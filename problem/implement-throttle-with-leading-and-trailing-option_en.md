
## My proposed solution here:

```js
function trottleWithOption(fn, wait, option={leading: true, trailing: true}){
  let inCoolDown = false;
  let lastSavedArgs = null;
  let lastThis = null;

  return function inner(...args){
    if(inCoolDown){
      lastSavedArgs = args; // skip this last saved arg by overriding it with the latest
      lastThis = this;// save this context for the next call
      return null;
      // If in cooldown, the function updates lastSavedArgs with the current arguments and lastThis with the current this context, then exits by returning null.
      // This ensures that during the cooldown, the latest invocation's details are saved for possible execution later.
    }
    inCoolDown = true;
    if(option.leading || (option.trailing && lastSavedArgs)){
      fn.apply(this, args);
    }
    setTimeout(()=>{
      inCoolDown = false; // reset cooldown to false when timer runs out
      if(lastSavedArgs && option.trailing){
        inner.apply(lastThis, lastSavedArgs);
        lastSavedArgs = null;
        lastThis = null;
      }
    },wait)
  }
}
```