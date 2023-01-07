## TL;DR

1. then() creates a new Promise, in which the return value of the callbacks are used in resolve() or reject() internally
   1. if a new Promise is returned, it will be chained.
2. [Promise.prototype.finally()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/finally) returns a new Promise, if the passed callback throws an error or returns a rejected promise, the promise returned by finally() will be rejected with that value instead, therwise the return value of the handler does NOT affect the state of the original promise.

```js
Promise.resolve(1)
  .then((val) => {
    console.log(val) // 1
    return val + 1
  })
  // since 2 is returned in above callback
  // Promise of this then() is fulfilled with value: 2
  .then((val) => {
    console.log(val) // 2
  })
  // since nothing is returned in above callback
  // Promise of this then() is fulfilled with value: undefined
  .then((val) => {
    console.log(val) // undefined
    return Promise.resolve(3).then((val) => {
      console.log(val) // 3
    })
    // since nothing is returned in above callback
    // Promise of this then() is fulfilled with value: undefined
  })
  // since a Promise is returned and it is fulfilled with value: undefined
  // Promise of this then() is fulfilled with value: undefined

  .then((val) => {
    console.log(val) // undefined
    return Promise.reject(4)
  })
  // since a rejected Promise with value 4 is returned
  // Promise of this then() is rejected with value: 4
  .catch((val) => {
    console.log(val) // 4
  })
  // since above callback returns undefined
  // Promise of this catch() is fulfilled with value: undefined
  .finally((val) => {
    console.log(val) // undefined
    return 10
  })
  // finally() returns a Promise, though 10 is returned in above callback, but it doesn't affect the original promise
  // so Promise of this finally() is fulfilled with value: undefined
  .then((val) => {
    console.log(val) // undefined
  })
```

Below is a longer version of the explanation based on ECMAScript Spec.

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
>    1. Append _fulfillReaction_ to _promise_.[[PromiseFulfillReactions]].
>    2. Append _rejectReaction_ to _promise_.[[PromiseRejectReactions]].
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
>     1. Return **undefined**.
> 14. Else,
>     1. Return _resultCapability_.[[Promise]].

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

## Promise.prototype.finally(onFinally)

Here are the steps involved in Promise.prototype.finally(), based on [ECMAScript Spec](https://tc39.es/ecma262/multipage/control-abstraction-objects.html#sec-promise.prototype.finally).

> 1. Let _promise_ be the this value.
> 2. If _promise_ is not an Object, throw a **TypeError** exception.
> 3. Let C be ? SpeciesConstructor(_promise_, %Promise%).
> 4. Assert: IsConstructor(C) is true.
> 5. If IsCallable(onFinally) is false, then
>    1. Let _thenFinally_ be _onFinally_.
>    2. Let _catchFinally_ be _onFinally_.
> 6. Else,
>    1. Let _thenFinallyClosure_ be a new Abstract Closure with parameters (value) that captures _onFinally_ and C and performs the following steps when called:
>       1. Let _result_ be ? Call(_onFinally_, undefined).
>       2. Let _promise_ be ? PromiseResolve(C, result).
>       3. Let _returnValue_ be a new Abstract Closure with no parameters that captures value and performs the following steps when called:
>          1. Return _value_.
>       4. Let valueThunk be CreateBuiltinFunction(_returnValue_, 0, "", « »).
>       5. Return ? Invoke(promise, "then", « valueThunk »).
>    2. Let _thenFinally_ be CreateBuiltinFunction(thenFinallyClosure, 1, "", « »).
>    3. Let _catchFinallyClosure_ be a new Abstract Closure with parameters (_reason_) that captures _onFinally_ and C and performs the following steps when called:
>       1. Let _result_ be ? Call(_onFinally_, undefined).
>       2. Let _promise_ be ? PromiseResolve(C, result).
>       3. Let _throwReason_ be a new Abstract Closure with no parameters that captures reason and performs the following steps when called:
>          1.Return ThrowCompletion(reason).
>       4. Let _thrower_ be CreateBuiltinFunction(throwReason, 0, "", « »).
>       5. Return ? Invoke(promise, "then", « thrower »).
>    4. Let _catchFinally_ be CreateBuiltinFunction(catchFinallyClosure, 1, "", « »).
> 7. Return ? Invoke(promise, "then", « thenFinally, catchFinally »).

From step 6, we can see that internally `finally()` use `then()` callbacks to create new Promises, also `thenFinallyClosure` always returns the value being passed, which means the return value of `onFinally` doesn't affect the fulfillment.
