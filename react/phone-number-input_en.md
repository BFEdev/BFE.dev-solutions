Submission by @gouravjeet
```typescript
import React, { useState } from 'react'
export function PhoneNumberInput() {
  const [inputValue, setInputValue] = useState('');

  const handleInputChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    let value = event.target.value.replace(/\D/g,''); // Replace non-digit number with empty string
    if(value.length > 10) value = value.slice(0, 10);
    if(value.length > 6) value = `${value.slice(0,6)}-${value.slice(6)}`
    if(value.length > 3) value = `(${value.slice(0,3)})${value.slice(3)}`
    setInputValue(value)
  }
  return <input onInput={handleInputChange} value={inputValue} type="string" data-testid="phone-number-input"/>
}

export function App() {
  return <div><PhoneNumberInput/></div>
}
```
