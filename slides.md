---
# You can also start simply with 'default'
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: Welcome to Slidev
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# apply unocss classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
# take snapshot for each slide in the overview
overviewSnapshots: true
---

# React Components

Cooper Duan


<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---
transition: fade-out
---

# Guide

React components are independent, reusable units of code for building user interfaces.


- **React component type classification**

- **Design principles**

<br>
<br>

<!--
Here is another comment.
-->

---
layout: two-cols
layoutClass: gap-16
---

- Function component

<div style="font-size: 13px"> React@16.8</div>
<span style="font-size: 13px">Function-based components use Hooks to implement state management and side effects. </span>

```tsx
function Counter() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState('');

  useEffect(() => {
    fetch('/api/userinfo').then((userInfo)=> { setName(userInfo.name) })
  }, [])

  return (
    <div>
      <p>{name}</p>
      <p>{count}</p>
      <button 
        onClick={() => setCount(count + 1)}
      >
        Increase
      </button>
    </div>
  );
}
```


::right::

- Class component

```tsx
import React, { Component } from 'react';
class MyComponent extends Component {
  constructor(props) {
    super(props);
    this.state = { count: 0, name: '' };
  }
  increaseCount = () => this.setState({ count: this.state.count + 1 });
  componentDidMount() {
    fetch('/api/userinfo')
    .then((userInfo) => { 
      this.setState((state) => ({ ...state, name: userInfo.name })) 
    })
  }

  render() {
    return (
      <div>
        <p>Name: {this.state.name}</p>
        <p>Count: {this.state.count}</p>
        <button onClick={this.increaseCount}>Increase</button>
      </div>
    );
  }
}
```

---
layout: two-cols
layoutClass: gap-16
---

-  Presentational Component

<span style="font-size: 13px">Only responsible for UI display, usually stateless, mainly relying on props, such as buttons, cards and other pure UI components. </span>

```tsx
function Button({ label, onClick }) {
  return (
    <div onClick={onClick}>
      <img className="prefix" />
      <button>{label}</button>
      <img className="suffix" />
    </div>
  )
}
```

::right::

- Container Component

<span style="font-size: 13px">for logic and state management, usually not directly displaying UI. </span>

```tsx
import { useState } from 'react';
import Button from './Button';

function ButtonContainer() {
  const [label, setLabel] = useState('Click Me');
  const [isLoading, setIsLoading] = useState(false)
  const onSubmit = () => {
    if(isLoading) return;
    setIsLoading(true)
    fetch('/api/form')
      .then(() => { })
      .finally(() => { setIsLoading(false) })
  }
  
  return (
    <Spin spinning={isLoading}>
      <Button label={label} onClick={onSubmit} />
    </Spin>
  )
}

```


---
layout: two-cols
layoutClass: gap-16
---

- Controlled Component

<span style="font-size: 13px">The state of the component is controlled by React and is often used for form input. The state is managed through `props` or `state`. </span>

```tsx
function ControlledInput() {
  const [value, setValue] = useState('');
  return <input value={value} onChange={(e) => setValue(e.target.value)} />;
}

```

::right::

- Uncontrolled Component

<span style="font-size: 13px">The state is managed by the DOM itself, and the value of the DOM node is directly obtained through ref. </span>

```tsx

function UncontrolledInput() {
  const inputRef = useRef(null);
  return <input ref={inputRef} />;
}

```


---
transition: fade-out
---

### Design principles


- ***Documentation and Instructions***
  - Component API and Type definitions 

- ***Single Responsibility Principle***
  - A component should only be responsible for a specific function or task

- ***State management***s
  - Controlled `props` and `state`

- ***Reusability***
  - Small and independent modular components

- ***Style and appearance***
  - Style Isolation & Customizability

- ***testing***

---
layout: two-cols
layoutClass: gap-16
---

- Controlled state

```tsx
import * as React from 'react';
import { useCallbackRef } from './react-use-callback-ref';

function useControllableState<T>({
  prop,
  defaultProp,
  onChange = () => {},
}: UseControllableStateParams<T>) {
  const [uncontrolledProp, setUncontrolledProp] = useUncontrolledState({ defaultProp, onChange });
  const isControlled = prop !== undefined;
  const value = isControlled ? prop : uncontrolledProp;
  const handleChange = useCallbackRef(onChange);

  const setValue: React.Dispatch<React.SetStateAction<T | undefined>> = React.useCallback(
    (nextValue) => {
      if (isControlled) {
        const setter = nextValue as SetStateFn<T>
        const value = typeof nextValue === 'function' ? setter(prop) : nextValue;
        if (value !== prop) handleChange(value as T);
      } else {
        setUncontrolledProp(nextValue);
      }
    },
    [isControlled, prop, setUncontrolledProp, handleChange]
  );

  return [value, setValue] as const;
}

```

::right::

- Uncontrolled state

```tsx

function useUncontrolledState<T>({
  defaultProp,
  onChange,
}: Omit<UseControllableStateParams<T>, 'prop'>) {
  const uncontrolledState = React.useState<T | undefined>(defaultProp);
  const [value] = uncontrolledState;
  const prevValueRef = React.useRef(value);
  const handleChange = useCallbackRef(onChange);

  React.useEffect(() => {
    if (prevValueRef.current !== value) {
      handleChange(value as T);
      prevValueRef.current = value;
    }
  }, [value, prevValueRef, handleChange]);

  return uncontrolledState;
}
link
```
---
layout: center
class: text-center
---

# Thank you


