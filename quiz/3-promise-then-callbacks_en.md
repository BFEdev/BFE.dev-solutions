## TL;DR

For [Promise.prototype.then()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/then):

- if function is passed as callback, the return value of the function is used as the fulfillment value
- for non-function values, previous fulfillment value is used.

```js
Promise.resolve(1)            // fulfilled : 1
  .then(() => 2)              // function is passed, return value is 2 => fulfilled : 2
  .then(3)                    // non-function is passed, previous fulfillment value 2 is used => fulfilled : 2
  .then((value) => value * 3) // function is passed, value is 2, return value is 6 => fulfilled: 6
  .then(Promise.resolve(4))   // Promise object is not function, previous fulfillment value 6 is used => fulfilled : 6
  .then(console.log)          // 6 gets logged
```

Below is longer version of explanation.

## Promise.prototype.then()

From [ECMAScript Spec](https://tc39.es/ecma262/multipage/control-abstraction-objects.html#sec-promise.prototype.then), it does below things.

> 1. Let _promise_ be the this value.
> 2. If IsPromise(_promise_) is false, throw a TypeError exception.
> 3. Let C be ? SpeciesConstructor(_promise_, %Promise%).
> 4. Let _resultCapability_ be ? NewPromiseCapability(C).
> 5. Return PerformPromiseThen(_promise_, _onFulfilled_, _onRejected_, _resultCapability_).

Basically it says it creates a new Promise object, and more steps are in below function.

## PerformPromiseThen()

According to [ECMAScript Spec](https://tc39.es/ecma262/multipage/control-abstraction-objects.html#sec-performpromisethen),
`Promise.prototype.then()` works as below.

> 1. Assert: IsPromise(_promise_) is true.
> 2. If _resultCapability_ is not present, then
>    1. Set _resultCapability_ to undefined.
> 3. If IsCallable(_onFulfilled_) is false, then
>    1. Let _onFulfilledJobCallback_ be empty.
> 4. Else,
>    1. Let _onFulfilledJobCallback_ be HostMakeJobCallback(_onFulfilled_).
> 5. If IsCallable(_onRejected_) is false, then
>    1. Let _onRejectedJobCallback_ be empty.
> 6. Else,
>    1. Let onRejectedJobCallback be HostMakeJobCallback(_onRejected_).
> 7. Let _fulfillReaction_ be the PromiseReaction { [[Capability]]: _resultCapability_, [[Type]]: Fulfill, [[Handler]]: onFulfilledJobCallback }.
> 8. Let _rejectReaction_ be the PromiseReaction { [[Capability]]: _resultCapability_, [[Type]]: Reject, [[Handler]]: onRejectedJobCallback }.
> 9. If _promise_.[[PromiseState]] is pending, then
>     1. Append _fulfillReaction_ to _promise_.[[PromiseFulfillReactions]].
>     2. Append _rejectReaction_ to _promise_.[[PromiseRejectReactions]].
> 10. Else if _promise_.[[PromiseState]] is fulfilled, then
>     1. Let _value_ be _promise_.[[PromiseResult]].
>     2. Let _fulfillJob_ be NewPromiseReactionJob(_fulfillReaction_, _value_).
>     3. Perform HostEnqueuePromiseJob(_fulfillJob_.[[Job]], _fulfillJob_.[[Realm]]).
> 11. Else,
>     1. Assert: The value of _promise_.[[PromiseState]] is rejected.
>     2. Let _reason_ be _promise_.[[PromiseResult]].
>     3. If _promise_.[[PromiseIsHandled]] is false, perform HostPromiseRejectionTracker(promise, "handle").
>     4. Let _rejectJob_ be NewPromiseReactionJob(_rejectReaction_, _reason_).
>     5. Perform HostEnqueuePromiseJob(_rejectJob_.[[Job]], _rejectJob_.[[Realm]]).
> 12. Set _promise_.[[PromiseIsHandled]] to true.
> 13. If resultCapability is **undefined**, then
>      1. Return **undefined**.
> 14. Else,
>      1. Return _resultCapability_.[[Promise]].

Basically it says

1. create callbacks(fulfillReaction, rejectReaction) for fulfilment and rejection (7 and 8)
2. register callbacks if the promise is pending (9)
3. trigger the callbacks if promise is fulfilled or rejected (10 and 11)

Notice that in step 3 and 5, it is checking if argument is function or not (IsCallable). **If not function, the internal of fulfillReaction/rejectReaction is _empty_**

What happens in `_empty_` is defined in NewPromiseReactionJob(), which is how the fulfillReaction and rejectReaction are executed.

## NewPromiseReactionJob()

According to [ECMAScript Spec](https://tc39.es/ecma262/multipage/control-abstraction-objects.html#sec-newpromisereactionjob),

> 1. Let \_job be a new Job Abstract Closure with no parameters that captures reaction and argument and performs the following steps when called:
>    1. Let _promiseCapability_ be _reaction_.[[Capability]].
>    2. Let _type_ be _reaction_.[[Type]].
>    3. Let _handler_ be _reaction_.[[Handler]].
>    4. If _handler_ is **empty**, then
>       1. If type is Fulfill, let _handlerResult_ be NormalCompletion(argument).
>       2. Else,
>          1. Assert: _type_ is Reject.
>          2. Let handlerResult be ThrowCompletion(argument).
>    5. Else, let _handlerResult_ be Completion(HostCallJobCallback(handler, undefined, « argument »)).
>    6. If _promiseCapability_ is undefined, then
>       1. Assert: _handlerResult_ is not an abrupt completion.
>       2. Return **empty**.
>    7. Assert: _promiseCapability_ is a PromiseCapability Record.
>    8. If _handlerResult_ is an abrupt completion, then
>       1. Return ? Call(_promiseCapability_.[[Reject]], undefined, « handlerResult.[[Value]] »).
>    9. Else,
>       1. Return ? Call(_promiseCapability_.[[Resolve]], undefined, « handlerResult.[[Value]] »).
> 2. Let _handlerRealm_ be null.
> 3. If _reaction_.[[Handler]] is not empty, then
>    1. Let _getHandlerRealmResult_ be Completion(GetFunctionRealm(reaction.[[Handler]].[[Callback]])).
>    2. If _getHandlerRealmResult_ is a normal completion, set handlerRealm to getHandlerRealmResult.[[Value]].
>    3. Else, set _handlerRealm_ to the current Realm Record.
>    4. NOTE: _handlerRealm_ is never null unless the handler is undefined. When the handler is a revoked Proxy and no ECMAScript code runs, handlerRealm is used to create error objects.
> 4. Return the Record { [[Job]]: _job, [[Realm]]: \_handlerRealm_ }.

Let's focus on step 4 & 5, it says

1. step 4: if callback (handler) is empty, then the existing fulfillment value (argument) is used
2. step 5: otherwise, the return value of callback (handler) is used.

After this the value is used to resolve() or reject() the promise object.
