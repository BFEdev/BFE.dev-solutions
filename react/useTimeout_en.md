Ravikumar Daivajna

import React, { useEffect, useState, useRef } from "react";

export function useTimeout(callback: () => void, delay: number) {
  const callbackRef = useRef(callback);
  const timeoutRef = useRef(0);

  useEffect(() => {
    callbackRef.current = callback;
  }, [callback]);

  useEffect(() => {
    // Clear the existing timer
    clearTimeout(timeoutRef.current);

    // Set a new timer with the updated delay
    timeoutRef.current = setTimeout(() => callbackRef.current(), delay);

    // Cleanup function to clear the timer on unmount or delay change
    return () => clearTimeout(timeoutRef.current);
  }, [delay]);
}

// if you want to try your code on the right panel
// remember to export App() component like below

export function App() {
  useTimeout(() => { console.log("hello") }, 2000)
  return <div>your app</div>
}
