## TL;DR

Once Promise is fulfilled or rejected, it cannot be fulfilled or rejected again.

So for the code in the question:

```js
new Promise((resolve, reject) => {
  resolve(1)
  resolve(2)
  reject('error')
}).then(
  (value) => {
    console.log(value)
  },
  (error) => {
    console.log('error')
  }
)
```

It is the same as below:

```js
new Promise((resolve, reject) => {
  resolve(1)
}).then(
  (value) => {
    console.log(value)
  },
  (error) => {
    console.log('error')
  }
)
```

So `1` is the result.

Below is detailed explanation based on ECMAScript spec

## Promise(_executor_)

According to [ECMAScript spec](https://tc39.es/ecma262/multipage/control-abstraction-objects.html#sec-promise-executor), what happens in `new Promise(...)` is defined as:

> 1. If NewTarget is undefined, throw a TypeError exception.
> 2. If IsCallable(_executor_) is false, throw a TypeError exception.
> 3. Let _promise_ be ? OrdinaryCreateFromConstructor(NewTarget, "%Promise.prototype%", « [[PromiseState]], [[PromiseResult]], [[PromiseFulfillReactions]], [[PromiseRejectReactions]], [[PromiseIsHandled]] »).
> 4. Set _promise_.[[PromiseState]] to pending.
> 5. Set _promise_.[[PromiseFulfillReactions]] to a new empty List.
> 6. Set _promise_.[[PromiseRejectReactions]] to a new empty List.
> 7. Set _promise_.[[PromiseIsHandled]] to false.
> 8. Let _resolvingFunctions_ be **CreateResolvingFunctions**(_promise_).
> 9. Let _completion_ be Completion(Call(executor, undefined, « resolvingFunctions.[[Resolve]], resolvingFunctions.[[Reject]] »)).
> 10. If _completion_ is an abrupt completion, then
>     1. a. Perform ? Call(resolvingFunctions.[[Reject]], undefined, « completion.[[Value]] »).
> 11. Return _promise_.

We can see that

1. it creates an empty promise object
2. creates the `resolve()` and `reject()` by CreateResolvingFunctions
3. the executor is called with above 2 functions passed.

## Properties of Promise Instances

According to [ECMAScript spec](https://tc39.es/ecma262/multipage/control-abstraction-objects.html#sec-properties-of-promise-instances), a promise have following internal data slot.

> | Internal Slot               | Type                                                                                                                                    | Description                                                                                                                  |
> | --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
> | [[PromiseState]]            | pending, fulfilled, or rejected                                                                                                         | Governs how a promise will react to incoming calls to its then method.                                                       |
> | [[PromiseResult]]           | an ECMAScript language value                                                                                                            | The value with which the promise has been fulfilled or rejected, if any. Only meaningful if [[PromiseState]] is not pending. |
> | [[PromiseFulfillReactions]] | a List of PromiseReaction Records                                                                                                       | Records to be processed when/if the promise transitions from the pending state to the fulfilled state.                       |
> | [[PromiseRejectReactions]]  | a List of PromiseReaction Records Records to be processed when/if the promise transitions from the pending state to the rejected state. |
> | [[PromiseIsHandled]]        | a Boolean                                                                                                                               | Indicates whether the promise has ever had a fulfillment or rejection handler; used in unhandled rejection tracking.         |

[[PromiseState]] is very important, it is internal state that we often refer to whether a Promise is rejected or fulfilled.

[[PromiseResult]] is the final value that it settles to

[[PromiseFulfillReactions]] and [[PromiseRejectReactions]] are the callbacks

[[PromiseIsHandled]] is why we often see unhandle rejection error.

## CreateResolvingFunctions ( promise )

According to [ECMAScript spec](https://tc39.es/ecma262/multipage/control-abstraction-objects.html#sec-createresolvingfunctions), `resolve()` and `reject()` are created as below

1. Let _alreadyResolved_ be the Record { [[Value]]: false }.
2. Let _stepsResolve_ be the algorithm steps defined in Promise Resolve Functions.
3. Let _lengthResolve_ be the number of non-optional parameters of the function definition in Promise Resolve Functions.
4. Let _resolve_ be CreateBuiltinFunction(stepsResolve, lengthResolve, "", « [[Promise]], [[AlreadyResolved]] »).
5. Set _resolve_.[[Promise]] to promise.
6. Set _resolve_.[[AlreadyResolved]] to alreadyResolved.
7. Let _stepsReject_ be the algorithm steps defined in Promise Reject Functions.
8. Let _lengthReject_ be the number of non-optional parameters of the function definition in Promise Reject Functions.
9. Let _reject_ be CreateBuiltinFunction(stepsReject, lengthReject, "", « [[Promise]], [[AlreadyResolved]] »).
10. Set _reject_.[[Promise]] to promise.
11. Set _reject_.[[AlreadyResolved]] to alreadyResolved.
12. Return the Record { [[Resolve]]: resolve, [[Reject]]: reject }.

We can see that

1. both `resolve()` and `reject()` holds internal reference to the Promise by `[[Promise]]`
2. both `resolve()` and `reject()` has a property `[[AlreadyResolved]]`, which points to a single state _alreadyResolved_, meaning `resolve()` and `reject()` are checking the same value

## resolve()

According to [ECMAScript spec](https://tc39.es/ecma262/multipage/control-abstraction-objects.html#sec-createresolvingfunctions),

> 1. Let _F_ be the active function object.
> 2. Assert: _F_ has a [[Promise]] internal slot whose value is an Object.
> 3. Let _promise_ be F.[[Promise]].
> 4. Let _alreadyResolved_ be F.[[AlreadyResolved]].
> 5. If _alreadyResolved_.[[Value]] is true, return undefined.
> 6. Set _alreadyResolved_.[[Value]] to true.
> 7. If SameValue(_resolution_, promise) is true, then
>    1. Let selfResolutionError be a newly created TypeError object.
>    2. Perform RejectPromise(promise, selfResolutionError).
>    3. Return undefined.
> 8. If _resolution_ is not an Object, then
>    1. Perform FulfillPromise(promise, resolution).
>    2. Return undefined.
> 9. Let _then_ be Completion(Get(resolution, "then")).
> 10. If _then_ is an abrupt completion, then
>     1. Perform RejectPromise(promise, then.[[Value]]).
>     2. Return undefined.
> 11. Let _thenAction_ be _then_.[[Value]].
> 12. If IsCallable(_thenAction_) is false, then
>     1. Perform FulfillPromise(_promise_, _resolution_).
>     2. Return undefined.
> 13. Let _thenJobCallback_ be HostMakeJobCallback(_thenAction_).
> 14. Let _job_ be NewPromiseResolveThenableJob(_promise_, _resolution_,_thenJobCallback_).
> 15. Perform HostEnqueuePromiseJob(job.[[Job]], job.[[Realm]]).
> 16. Return undefined.

There are quite some steps

1. it checks if promise is already resolved, return if so
2. it forbids `resolve()` to be called with promise itself, it creates infinite chaining
3. resolves to the value if the argument is not Object
4. if the argument is thenable, chain them

The step 5 clearly tells us that `resolve()` does nothing if promise is already resolved.

## reject()

According to [ECMASCript spec](https://tc39.es/ecma262/multipage/control-abstraction-objects.html#sec-promise-reject-functions), `reject()` internals are defined as:

> 1. Let _F_ be the active function object.
> 2. Assert: _F_ has a [[Promise]] internal slot whose value is an Object.
> 3. Let _promise_ be F.[[Promise]].
> 4. Let _alreadyResolved_ be F.[[AlreadyResolved]].
> 5. If _alreadyResolved_.[[Value]] is true, return undefined.
> 6. Set _alreadyResolved_.[[Value]] to true.
> 7. Perform RejectPromise(promise, reason).
> 8. Return undefined.

The step 5 clearly tells us that `reject()` does nothing if promise is already resolved.
