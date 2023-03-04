//Nitesh Jain
// This is a React coding question from BFE.dev

import React from 'react'

export function useClickOutside(callback: () => void) {
// creating ref
const ref:any = React.useRef(null)

// handling click
// ref.current will points to div in which it is assigned
const handleClick = (e: any) =>{
if(ref.current && !ref.current.contains(e.target))
callback()
}

// creating listner for capturing clicks
React.useEffect(() => {
window.addEventListener('click', handleClick )
return () => window.removeEventListener('click', handleClick )
}, [])

return ref
}

// to try your code on the right panel
// export App() component like below

// export function App() {
// return <div>your app</div>
// }
