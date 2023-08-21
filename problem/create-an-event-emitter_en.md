
// implementation using a map for tracking callbacks
// eventName => callbackFunction[]
class EventEmitter {
  events = new Map();
  subscribe(eventName, callback) {
    if (!this.events.has(eventName)) {
      // Initialize a new event type
      this.events.set(eventName, []);
    }
    // push the callback onto the event listeners array
    let newArr = this.events.get(eventName);
    let callbackIndex = newArr.push(callback);
  	this.events.set(eventName, newArr);
    return {
      release: () => {
        // we extract the callback and remove it
        let events = this.events.get(eventName);
        events.splice(callbackIndex - 1, 1);
        this.events.set(events);
      }
    }
  }
  
  emit(eventName, ...args) {
    if (!this.events.has(eventName)) {
      return;
    }
  	this.events.get(eventName).forEach(c => c(...args))
  }
}
