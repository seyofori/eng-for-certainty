# Frontend-First API Mocks

Use this pattern when frontend implementation intentionally precedes the
backend. Adapt filenames and imports to the repository while preserving the
boundaries below.

## Boundary

```text
validated environment
        |
        v
domain API entrypoint
        |
        v
pure create[Domain]Api factory
        |
        +-- production adapter -> configured API client -> backend
        `-- mock adapter -------> typed mock scenario

hooks -> stable exported API functions
```

Only the adapter implementation that would call the backend changes. The API
operation contracts and every consuming frontend layer remain the same.

## Operation Contract

Define the stable callable surface before either implementation:

```ts
type OrdersApi = {
  createOrder: (
    input: CreateOrderInput,
  ) => ResultAsync<CreateOrderOutput, CreateOrderFailure>;
};
```

`ApiClient` below means the repository's configured low-level request client
used by production adapters. It owns transport concerns such as the base URL,
headers, cancellation, timeouts, and bounded safe retries according to the
repository's established architecture.

## Validated Environment

The environment exposes a closed production-or-mock configuration. This
Vite-style example defaults to production and makes mock mode invalid in a
production build:

```ts
const OrdersMockScenarioNameSchema = z.enum([
  "happy-path",
  "create-order-limit",
  "create-order-unavailable",
]);

const ApiEnvironmentSchema = z.discriminatedUnion("VITE_API_MODE", [
  z.object({
    MODE: z.enum(["development", "test", "production"]),
    VITE_API_MODE: z.literal("production"),
    VITE_API_BASE_URL: z.string().url(),
  }),
  z.object({
    MODE: z.enum(["development", "test"]),
    VITE_API_MODE: z.literal("mock"),
    VITE_ORDERS_MOCK_SCENARIO: OrdersMockScenarioNameSchema,
  }),
]);

const parsedEnvironment = ApiEnvironmentSchema.safeParse({
  MODE: import.meta.env.MODE,
  VITE_API_MODE: import.meta.env.VITE_API_MODE ?? "production",
  VITE_API_BASE_URL: import.meta.env.VITE_API_BASE_URL,
  VITE_ORDERS_MOCK_SCENARIO:
    import.meta.env.VITE_ORDERS_MOCK_SCENARIO ?? "happy-path",
});

if (!parsedEnvironment.success) {
  throw new Error("Invalid application environment");
}

export const env = match(parsedEnvironment.data)
  .with(
    { VITE_API_MODE: "production" },
    ({ MODE, VITE_API_BASE_URL }) =>
      ({
        appMode: MODE,
        api: {
          mode: "production",
          baseUrl: VITE_API_BASE_URL,
        },
      }) as const,
  )
  .with(
    { VITE_API_MODE: "mock" },
    ({ MODE, VITE_ORDERS_MOCK_SCENARIO }) =>
      ({
        appMode: MODE,
        api: {
          mode: "mock",
          ordersScenario: VITE_ORDERS_MOCK_SCENARIO,
        },
      }) as const,
  )
  .exhaustive();
```

Read environment values at startup. Do not change them after importing the
domain API entrypoint and expect the already-created functions to change.

## Mock Outcomes And Scenarios

A mock outcome says whether one operation succeeds or fails and may add a
delay. The `type` field makes the two shapes mutually exclusive and supports
exhaustive matching.

```ts
type MockOutcome<TSuccess, TFailure> =
  | {
      type: "success";
      value: TSuccess;
      delayMs?: number;
    }
  | {
      type: "failure";
      failure: TFailure;
      delayMs?: number;
    };

type OrdersMockScenario = {
  createOrderOutcome: MockOutcome<
    CreateOrderOutput,
    CreateOrderFailure
  >;
};

const MockDelaySchema = z.number().int().nonnegative().max(30_000).optional();

const OrdersMockScenarioSchema: z.ZodType<OrdersMockScenario> = z
  .object({
    createOrderOutcome: z.discriminatedUnion("type", [
      z
        .object({
          type: z.literal("success"),
          value: CreateOrderOutputSchema,
          delayMs: MockDelaySchema,
        })
        .strict(),
      z
        .object({
          type: z.literal("failure"),
          failure: CreateOrderFailureSchema,
          delayMs: MockDelaySchema,
        })
        .strict(),
    ]),
  })
  .strict();
```

Map each named development preset to a complete typed scenario:

```ts
function createOrdersMockScenario(
  name: z.infer<typeof OrdersMockScenarioNameSchema>,
): OrdersMockScenario {
  return match(name)
    .with("happy-path", () => ({
      createOrderOutcome: {
        type: "success",
        value: createOrderFixture(),
        delayMs: 300,
      },
    }))
    .with("create-order-limit", () => ({
      createOrderOutcome: {
        type: "failure",
        failure: orderLimitExceeded(),
        delayMs: 300,
      },
    }))
    .with("create-order-unavailable", () => ({
      createOrderOutcome: {
        type: "failure",
        failure: orderServiceUnavailable(),
        delayMs: 300,
      },
    }))
    .exhaustive();
}
```

Do not add callbacks or response sequences until a real UI behavior requires
them. When required, add one explicit scenario variant and handle it
exhaustively instead of replacing the closed model with arbitrary behavior.

## Production And Mock Implementations

The production adapter performs the real request and validates the real
operation contracts:

```ts
function createOrdersHttpApi(client: ApiClient): OrdersApi {
  return {
    createOrder: (input) =>
      validateCreateOrderInput(input)
        .asyncAndThen((validInput) =>
          client.post("/orders", validInput),
        )
        .andThen(validateCreateOrderResponse),
  };
}
```

The mock adapter still returns callable API functions with the exact same
signatures:

```ts
function wait(delayMs: number): Promise<void> {
  return new Promise((resolve) => {
    setTimeout(resolve, delayMs);
  });
}

function resolveMockOutcome<TSuccess, TFailure>(
  outcome: MockOutcome<TSuccess, TFailure>,
): ResultAsync<TSuccess, TFailure> {
  const resolveOutcome = (): ResultAsync<TSuccess, TFailure> =>
    match(outcome)
      .with({ type: "success" }, ({ value }) =>
        okAsync<TSuccess, TFailure>(value),
      )
      .with({ type: "failure" }, ({ failure }) =>
        errAsync<TSuccess, TFailure>(failure),
      )
      .exhaustive();

  const delayMs = outcome.delayMs ?? 0;

  return delayMs === 0
    ? resolveOutcome()
    : ResultAsync.fromSafePromise(wait(delayMs)).andThen(resolveOutcome);
}

function createOrdersMockApi(
  scenario: OrdersMockScenario,
): OrdersApi {
  const parsedScenario = OrdersMockScenarioSchema.safeParse(scenario);

  if (!parsedScenario.success) {
    throw new Error("Invalid orders mock scenario");
  }

  return {
    createOrder: (input) =>
      validateCreateOrderInput(input).asyncAndThen(() =>
        resolveMockOutcome(parsedScenario.data.createOrderOutcome),
      ),
  };
}
```

Invalid mock configuration is a startup or test-construction defect, not an
operation failure, so failing fast here does not widen `CreateOrderFailure`.
Reuse the canonical success and exact failure schemas; do not create looser
mock-only validators.

## Pure Factory And Domain Entrypoint

The pure factory matches its explicit options exhaustively:

```ts
type CreateOrdersApiOptions =
  | { mode: "production"; client: ApiClient }
  | { mode: "mock"; scenario: OrdersMockScenario };

function createOrdersApi(options: CreateOrdersApiOptions): OrdersApi {
  return match(options)
    .with({ mode: "production" }, ({ client }) =>
      createOrdersHttpApi(client),
    )
    .with({ mode: "mock" }, ({ scenario }) =>
      createOrdersMockApi(scenario),
    )
    .exhaustive();
}
```

The domain entrypoint reads validated configuration once, calls the factory,
and exports normal API functions:

```ts
const ordersApi = match(env.api)
  .with({ mode: "production" }, ({ baseUrl }) =>
    createOrdersApi({
      mode: "production",
      client: createApiClient({ baseUrl }),
    }),
  )
  .with({ mode: "mock" }, ({ ordersScenario }) =>
    createOrdersApi({
      mode: "mock",
      scenario: createOrdersMockScenario(ordersScenario),
    }),
  )
  .exhaustive();

export const createOrder = ordersApi.createOrder;
```

Use the repository's existing shared `ApiClient` instance instead of
constructing one here when its composition boundary already provides one.

## Hook And Flow

Hooks import the stable function directly and expose application-owned state:

```ts
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
  mutation: Pick<
    UseMutationResult<
      Result<CreateOrderOutput, CreateOrderFailure>,
      unknown,
      CreateOrderInput
    >,
    | "data"
    | "error"
    | "isError"
    | "isPending"
    | "mutate"
    | "variables"
  >,
): CreateOrderState {
  const failedState = (
    failure: CreateOrderFailure,
  ): CreateOrderState => {
    const failedInput = mutation.variables;

    if (failedInput === undefined) {
      throw new Error("Failed create-order mutation has no input");
    }

    return {
      status: "failed",
      failure,
      retry: () => mutation.mutate(failedInput),
    };
  };

  if (mutation.isError) {
    return failedState(mapUnexpectedCreateOrderFailure(mutation.error));
  }

  if (mutation.data?.isErr()) {
    return failedState(mutation.data.error);
  }

  if (mutation.data?.isOk()) {
    return { status: "succeeded", order: mutation.data.value };
  }

  return mutation.isPending
    ? { status: "submitting" }
    : { status: "idle" };
}

export function useCreateOrder() {
  const mutation = useMutation({ mutationFn: createOrder });

  return {
    state: mapCreateOrderState(mutation),
    submit: mutation.mutateAsync,
    reset: mutation.reset,
  };
}
```

The flow, not the hook, decides which UI to render and matches every state:

```tsx
return match(createOrder.state)
  .with({ status: "idle" }, () =>
    <CreateOrderView onSubmit={createOrder.submit} />,
  )
  .with({ status: "submitting" }, () =>
    <CreateOrderView submitting onSubmit={createOrder.submit} />,
  )
  .with({ status: "succeeded" }, ({ order }) =>
    <OrderCreatedView order={order} />,
  )
  .with({ status: "failed" }, ({ failure, retry }) =>
    <CreateOrderErrorView
      failure={failure}
      onRetry={retry}
    />,
  )
  .exhaustive();
```

## Tests

Isolated tests construct the pure factory directly:

```ts
const ordersApi = createOrdersApi({
  mode: "mock",
  scenario: {
    createOrderOutcome: {
      type: "failure",
      failure: orderLimitExceeded(),
    },
  },
});
```

Application-level tests may select mock mode before loading the application.
Do not mutate environment after importing the domain entrypoint and depend on
module-cache resets as the normal isolation strategy.
