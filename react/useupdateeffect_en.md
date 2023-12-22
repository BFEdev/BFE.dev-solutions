import React, {EffectCallback, DependencyList, useRef, useEffect} from 'react';

export function useUpdateEffect(effect: EffectCallback, deps?: DependencyList) {
  const initialRenderRef = useRef(true);

  useEffect(() => {
    let cleanupFunction: void | (() => void);

    if (initialRenderRef.current) {
      initialRenderRef.current = false;
    } else {
      cleanupFunction = effect();
    }

    return () => {
      if (cleanupFunction) {
        cleanupFunction();
      }
    }
  }, deps)
}
