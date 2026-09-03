# Result Mutation Hooks

Use this pattern for every TanStack mutation hook. Adapt filenames and imports
to the repository, but keep one shared settlement helper and one mutation
instance per hook.

## Contract

Every mutation adapter returns `ResultAsync<TData, TFailure>`. When an operation
has no expected failure, use `ResultAsync<TData, never>` instead of changing the
hook to a throwing contract. Awaiting the adapter gives TanStack Query a
resolved `Result`; only a genuinely unexpected exception rejects the mutation.

The two hook surfaces answer different questions:

- `state` describes the mutation hook's current reactive lifecycle.
- `run(input)` returns the settled outcome of the specific attempt the caller
  awaited.

Both must come from the same `useMutation` instance.

## Shared Settlement Helper

Define this once in the consuming project's established shared-library
location. For example, `src/lib/settle-mutation.ts`:

```ts
import type { Result } from "neverthrow";

export type SettledMutation<TData, TFailure> =
  | { status: "ok"; value: TData }
  | { status: "err"; error: TFailure }
  | { status: "thrown"; error: unknown };

export async function settleMutation<TData, TFailure>(
  attempt: () => Promise<Result<TData, TFailure>>,
): Promise<SettledMutation<TData, TFailure>> {
  try {
    const result = await attempt();

    return result.isOk()
      ? { status: "ok", value: result.value }
      : { status: "err", error: result.error };
  } catch (error: unknown) {
    return { status: "thrown", error };
  }
}
```

Passing the attempt as a callback lets the helper catch synchronous invocation
errors as well as promise rejections. Keep the helper free of telemetry, UI,
navigation, and domain-specific mapping. It preserves an unexpected value as
`unknown`; never render, serialize, or log that raw value.

## Domain Hook

For example, `orders/hooks/use-create-order.ts` owns the reactive state and the
awaitable operation while continuing to call the stable adapter directly:

```ts
import { useMutation, type UseMutationResult } from "@tanstack/react-query";
import type { Result } from "neverthrow";
import { match } from "ts-pattern";

import {
  settleMutation,
  type SettledMutation,
} from "#lib/settle-mutation";
import { createOrder } from "../orders.api";

type CreateOrderState =
  | { status: "idle" }
  | { status: "submitting" }
  | { status: "succeeded"; order: CreateOrderOutput }
  | {
      status: "failed";
      failure: CreateOrderFailure;
      retry: () => void;
    };

function mapCreateOrderState(
  mutation: UseMutationResult<
    Result<CreateOrderOutput, CreateOrderFailure>,
    unknown,
    CreateOrderInput
  >,
): CreateOrderState {
  return match(mutation)
    .returnType<CreateOrderState>()
    .with({ status: "idle" }, () => ({ status: "idle" }))
    .with({ status: "pending" }, () => ({ status: "submitting" }))
    .with({ status: "error" }, ({ error, variables, mutate }) => ({
      status: "failed",
      failure: mapUnexpectedCreateOrderFailure(error),
      retry: () => mutate(variables),
    }))
    .with({ status: "success" }, ({ data, variables, mutate }) =>
      data.match<CreateOrderState>(
        (order) => ({ status: "succeeded", order }),
        (failure) => ({
          status: "failed",
          failure,
          retry: () => mutate(variables),
        }),
      ),
    )
    .exhaustive();
}

export function useCreateOrder() {
  const mutation = useMutation<
    Result<CreateOrderOutput, CreateOrderFailure>,
    unknown,
    CreateOrderInput
  >({
    mutationFn: async (input) => await createOrder(input),
  });

  const run = (
    input: CreateOrderInput,
  ): Promise<
    SettledMutation<CreateOrderOutput, CreateOrderFailure>
  > => settleMutation(() => mutation.mutateAsync(input));

  return {
    state: mapCreateOrderState(mutation),
    run,
    reset: mutation.reset,
  };
}
```

The full `UseMutationResult` union makes `status` discriminative: failed and
successful states carry the variables used by that attempt, so retry does not
depend on a runtime assertion. `mapUnexpectedCreateOrderFailure` is an owned,
safe classifier for the reactive error state; it must not expose the raw caught
value.

Do not define `SettledMutation` or `settleMutation` beside this hook. Import the
shared definitions so all hooks preserve the same `ok | err | thrown` contract.

## Flow

For example, `orders/flows/CreateOrderFlow.tsx` owns rendering, navigation, and
safe reporting. Views receive plain application state and callbacks, never
TanStack Query flags, a `Result`, or a raw error:

```tsx
import { match } from "ts-pattern";

export function CreateOrderFlow() {
  const createOrder = useCreateOrder();

  const handleSubmit = async (input: CreateOrderInput): Promise<void> => {
    const outcome = await createOrder.run(input);

    match(outcome)
      .with({ status: "ok" }, ({ value }) => {
        navigateToOrder(value.id);
      })
      .with({ status: "err" }, () => {
        // The hook's reactive state renders the typed domain failure.
      })
      .with({ status: "thrown" }, ({ error }) => {
        reportUnexpectedCreateOrderFailure(error, {
          operation: "create_order",
        });
      })
      .exhaustive();
  };

  return match(createOrder.state)
    .with({ status: "idle" }, () => (
      <CreateOrderView onSubmit={handleSubmit} />
    ))
    .with({ status: "submitting" }, () => (
      <CreateOrderView submitting onSubmit={handleSubmit} />
    ))
    .with({ status: "succeeded" }, ({ order }) => (
      <OrderCreatedView order={order} />
    ))
    .with({ status: "failed" }, ({ failure, retry }) => (
      <CreateOrderErrorView failure={failure} onRetry={retry} />
    ))
    .exhaustive();
}
```

The `ok` branch owns attempt-specific orchestration such as navigation. The
`err` branch needs no duplicate local error state because the hook's reactive
state already carries the typed failure. The `thrown` branch passes `unknown`
only to an owned safe classifier or reporter, with safe operation and genuine
correlation context; it does not inspect, render, or log the raw value itself.

## Review And Test Invariants

- The adapter return type is `ResultAsync<TData, TFailure>`, including
  `ResultAsync<TData, never>` when there is no expected failure.
- `mutationFn` resolves expected failures as `Result`; it does not turn them
  into rejected promises.
- `state` and `run` use the same `useMutation` instance.
- Every hook imports the one shared settlement helper and type.
- Hook state and settled outcomes are matched exhaustively.
- Views receive only application-owned data, failures, and callbacks.
- Tests cover `ok`, `err`, and unexpected `thrown` settlement, plus every
  reactive state branch and retry with the original variables.
