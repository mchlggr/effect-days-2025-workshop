# Agent Guidelines: Effect.Service

This repository defines several services using `Effect.Service`.
When creating a new service, follow these guidelines extracted from the existing examples.

## Service definition

- Declare a class extending `Effect.Service<MyService>()("MyService", { ... })` and export it.
- Use `effect: Effect.gen(function*() { ... })` for stateless or pure services.
- Use `scoped: Effect.gen(function*() { ... })` if the service acquires resources that need cleanup.
- Return an object with the service methods and values using `return { ... } as const`.

## Methods

- Define methods inside the generator and assign them to local constants.
- Prefer `Effect.fn("MyService.method")` for functions so that spans are named when tracing.
- Wrap synchronous operations with `Effect.try` or asynchronous operations with `Effect.tryPromise` and map errors to a custom error type.
- Use TypeScript generics to expose the return type and error type of each method.

## Errors

- Create domain errors with `Schema.TaggedError`.
- Include a `cause: Schema.Defect` field when converting unknown errors.

## Dependencies and layers

- List required layers in a `dependencies: [OtherService.Default, ...]` array if the service relies on other services.
- Each service exposes a `Default` layer. Provide it when running effects using `Effect.provide(MyService.Default)` or merge with other layers via `Layer.provideMerge`.

## Usage

```ts
Effect.gen(function*() {
  const svc = yield* MyService;
  // use svc.method
}).pipe(
  Effect.provide(MyService.Default),
  NodeRuntime.runMain
);
```

Follow these conventions when adding new services to maintain consistency.
