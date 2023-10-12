---
layout: "../../layouts/BlogPost.astro"
title: "How to reset React Component State by itself"
description: "Simple way to reset a component state on an event"
pubDate: "Oct 12 2023"
heroImage: "/placeholder-board.jpeg"
---
A react component state (defined by `useState`) remains unchanged during renders and is affected only when manually changed. One common way to reset the state is to manually modify them: 

```ts
const Demo = () => {
  const [name, setName] = useState('default');
  const [address, setAddress] = useState(null);

  const reset = () => {
    setName('default');
    setAddress(null);
  }

  return (
    ...
    <button onClick={()=>reset()} >Reset</button>
  );
}

```

This is not ideal for three reasons: 
1. The initial state is repeated and we may need to extract it to another variable to prevent bugs mismatch
2. Need to keep both in sync while updating
3. If the state is added from a custom hook, we have no way to reset it directly from the component

A better way is to reset by adding a special prop called `key`. It's well known we need to pass `key` while mapping list of components. But it can also be used to reset the component state. But this involves adding a new state to parent component just for resetting child component.

```ts
  const Demo = ({reset}: {reset:()=>void})=>{
    const [name, setName] = useState('default');
    const [address, setAddress] = useState(null);

    return (
      <button onClick={()=>reset()}>Reset</button>
    );
  }

  const Parent = ()=>{
    const [resetKey, setResetKey] = useState(0);

    return (
      ...
      <Demo key={resetKey} onReset={ ()=> setResetKey(resetKey+1)} />
    )
  }
```

What we are doing here is forcefully changing the value of prop `key` which resets the component state manually. Technically its not doing it by itself but taking parent component help, but this seems to be the cleanest way to reset.
This has the disadvantage of rerendering parent component and all its children. But since no other state is changed, ideally it should not be a big deal.


Codesandbox to play with : 
<iframe src="https://codesandbox.io/embed/black-dew-8t9vyv?fontsize=14&hidenavigation=1&theme=dark"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="black-dew-8t9vyv"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>

