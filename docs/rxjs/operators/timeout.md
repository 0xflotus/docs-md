---
kind: FunctionDeclaration
name: timeout
module: operators
---

# timeout

## description

Errors if Observable does not emit a value in given time span.

<span class="informal">Timeouts on Observable that doesn't emit values fast enough.</span>

![](timeout.png)

```ts
function timeout<T, R, M>(
  dueOrConfig: number | Date | TimeoutConfig<T, R, M>,
  scheduler?: SchedulerLike
): OperatorFunction<T, T | R>;
```

[Link to repo](https://github.com/ReactiveX/rxjs/blob/master/src/internal/operators/timeout.ts#L308-L398)

## Parameters

| Name        | Type            | Description |
| ----------- | --------------- | ----------- |
| dueOrConfig | `number         | Date        | TimeoutConfig<T, R, M>` |  |
| scheduler   | `SchedulerLike` |             |

## Overloads

### description

If `with` is provided, this will return an observable that will switch to a different observable if the source
does not push values within the specified time parameters.

<span class="informal">The most flexible option for creating a timeout behavior.</span>

The first thing to know about the configuration is if you do not provide a `with` property to the configuration,
when timeout conditions are met, this operator will emit a {@link TimeoutError}. Otherwise, it will use the factory
function provided by `with`, and switch your subscription to the result of that. Timeout conditions are provided by
the settings in `first` and `each`.

The `first` property can be either a `Date` for a specific time, a `number` for a time period relative to the
point of subscription, or it can be skipped. This property is to check timeout conditions for the arrival of
the first value from the source _only_. The timings of all subsequent values from the source will be checked
against the time period provided by `each`, if it was provided.

The `each` property can be either a `number` or skipped. If a value for `each` is provided, it represents the amount of
time the resulting observable will wait between the arrival of values from the source before timing out. Note that if
`first` is _not_ provided, the value from `each` will be used to check timeout conditions for the arrival of the first
value and all subsequent values. If `first` _is_ provided, `each` will only be use to check all values after the first.

### Example

Emit a custom error if there is too much time between values

```ts
import { interval, throwError } from "rxjs";
import { timeout } from "rxjs/operators";

class CustomTimeoutError extends Error {
  constructor() {
    super("It was too slow");
    this.name = "CustomTimeoutError";
  }
}

const slow$ = interval(900);

slow$
  .pipe(
    timeout({
      each: 1000,
      with: () => throwError(new CustomTimeoutError()),
    })
  )
  .subscribe({
    error: console.error,
  });
```

### Example

Switch to a faster observable if your source is slow.

```ts
import { interval, throwError } from "rxjs";
import { timeout } from "rxjs/operators";

const slow$ = interval(900);
const fast$ = interval(500);

slow$
  .pipe(
    timeout({
      each: 1000,
      with: () => fast$,
    })
  )
  .subscribe(console.log);
```

```ts
function timeout<T, R, M = unknown>(
  config: TimeoutConfig<T, R, M> & {
    with: (info: TimeoutInfo<T, M>) => ObservableInput<R>;
  }
): OperatorFunction<T, T | R>;
```

[Link to repo](https://github.com/ReactiveX/rxjs/blob/master/src/internal/operators/timeout.ts#L173-L175)

### Parameters

| Name   | Type                                                                                  | Description                        |
| ------ | ------------------------------------------------------------------------------------- | ---------------------------------- |
| config | `TimeoutConfig<T, R, M> & { with: (info: TimeoutInfo<T, M>) => ObservableInput<R>; }` | The configuration for the timeout. |

### description

Returns an observable that will error or switch to a different observable if the source does not push values
within the specified time parameters.

<span class="informal">The most flexible option for creating a timeout behavior.</span>

The first thing to know about the configuration is if you do not provide a `with` property to the configuration,
when timeout conditions are met, this operator will emit a {@link TimeoutError}. Otherwise, it will use the factory
function provided by `with`, and switch your subscription to the result of that. Timeout conditions are provided by
the settings in `first` and `each`.

The `first` property can be either a `Date` for a specific time, a `number` for a time period relative to the
point of subscription, or it can be skipped. This property is to check timeout conditions for the arrival of
the first value from the source _only_. The timings of all subsequent values from the source will be checked
against the time period provided by `each`, if it was provided.

The `each` property can be either a `number` or skipped. If a value for `each` is provided, it represents the amount of
time the resulting observable will wait between the arrival of values from the source before timing out. Note that if
`first` is _not_ provided, the value from `each` will be used to check timeout conditions for the arrival of the first
value and all subsequent values. If `first` _is_ provided, `each` will only be use to check all values after the first.

### Handling TimeoutErrors

If no `with` property was provided, subscriptions to the resulting observable may emit an error of {@link TimeoutError}.
The timeout error provides useful information you can examine when you're handling the error. The most common way to handle
the error would be with {@link catchError}, although you could use {@link tap} or just the error handler in your `subscribe` call
directly, if your error handling is only a side effect (such as notifying the user, or logging).

In this case, you would check the error for `instanceof TimeoutError` to validate that the error was indeed from `timeout`, and
not from some other source. If it's not from `timeout`, you should probably rethrow it if you're in a `catchError`.

### Example

Emit a {@link TimeoutError} if the first value, and _only_ the first value, does not arrive within 5 seconds

```ts
import { interval } from "rxjs";
import { timeout } from "rxjs/operators";

// A random interval that lasts between 0 and 10 seconds per tick
const source$ = interval(Math.round(Math.random() * 10000));

source$.pipe(timeout({ first: 5000 })).subscribe(console.log);
```

### Example

Emit a {@link TimeoutError} if the source waits longer than 5 seconds between any two values or the first value
and subscription.

```ts
import { timer } from "rxjs";
import { timeout, expand } from "rxjs/operators";

const getRandomTime = () => Math.round(Math.random() * 10000);

// An observable that waits a random amount of time between each delivered value
const source$ = timer(getRandomTime()).pipe(
  expand(() => timer(getRandomTime()))
);

source$.pipe(timeout({ each: 5000 })).subscribe(console.log);
```

### Example

Emit a {@link TimeoutError} if the the source does not emit before 7 seconds, _or_ if the source waits longer than
5 seconds between any two values after the first.

```ts
import { timer } from "rxjs";
import { timeout, expand } from "rxjs/operators";

const getRandomTime = () => Math.round(Math.random() * 10000);

// An observable that waits a random amount of time between each delivered value
const source$ = timer(getRandomTime()).pipe(
  expand(() => timer(getRandomTime()))
);

source$.pipe(timeout({ first: 7000, each: 5000 })).subscribe(console.log);
```

```ts
function timeout<T, M = unknown>(
  config: Omit<TimeoutConfig<T, any, M>, "with">
): OperatorFunction<T, T>;
```

[Link to repo](https://github.com/ReactiveX/rxjs/blob/master/src/internal/operators/timeout.ts#L270-L270)

### Parameters

| Name   | Type                                   | Description |
| ------ | -------------------------------------- | ----------- |
| config | `Pick<TimeoutConfig<T, any, M>, "each" | "first"     | "scheduler" | "meta">` |  |

### description

Returns an observable that will error if the source does not push its first value before the specified time passed as a `Date`.
This is functionally the same as `timeout({ first: someDate })`.

<span class="informal">Errors if the first value doesn't show up before the given date and time</span>

![](timeout.png)

```ts
function timeout<T>(
  first: Date,
  scheduler?: SchedulerLike
): MonoTypeOperatorFunction<T>;
```

[Link to repo](https://github.com/ReactiveX/rxjs/blob/master/src/internal/operators/timeout.ts#L284-L284)

### Parameters

| Name      | Type            | Description                                                                         |
| --------- | --------------- | ----------------------------------------------------------------------------------- |
| first     | `Date`          | The date to at which the resulting observable will timeout if the source observable |
| scheduler | `SchedulerLike` | The scheduler to use. Defaults to {@link asyncScheduler}.                           |

### description

Returns an observable that will error if the source does not push a value within the specified time in milliseconds.
This is functionally the same as `timeout({ each: milliseconds })`.

<span class="informal">Errors if it waits too long between any value</span>

![](timeout.png)

```ts
function timeout<T>(
  each: number,
  scheduler?: SchedulerLike
): MonoTypeOperatorFunction<T>;
```

[Link to repo](https://github.com/ReactiveX/rxjs/blob/master/src/internal/operators/timeout.ts#L298-L298)

### Parameters

| Name      | Type            | Description                                                                                |
| --------- | --------------- | ------------------------------------------------------------------------------------------ |
| each      | `number`        | The time allowed between each pushed value from the source before the resulting observable |
| scheduler | `SchedulerLike` | The scheduler to use. Defaults to {@link asyncScheduler}.                                  |
