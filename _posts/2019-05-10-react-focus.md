---
title: Tracking focus in React
excerpt: .focus() on conditional elements
draft: true
---

I've been using React a fair amount lately and really enjoy a lot of what it offers. The ability to easily think of state in terms of real _objects_ and not a paticular arrangement of DOM nodes or class names is a huge help. For example take the canonical example of managing a list of Todos. In HTML you may have,

```html
<ul>
    <li>First todo</li>
    <li>Second todo</li>
</ul>
```

With vanilla JS I can add a third todo by `todos.appendChild(document.createElement('li'))` and that works to a certain point. But once you start adding in the mayhem of state and you have to worry about whether a todo is completed things go off the rails. For example, if a todo is completed let's add a class name and a `<s>` element for semantic purposes. Now we've got,

```html
<ul>
    <li>First todo</li>
    <li class="complete"><s>Second todo</s></li>
</ul>
```

And our Javascript is so much worse. With each update (reorder, reload, etc…) we have to worry about whether our Todo is wrapped by an `<s>` element and what the class names are on the `li`. React cleans all this up by thinking of our code declaratively. We can, instead, think of our state in terms of business objects and let our HTML be a byproduct of that state. Something like this,

```jsx
function Todos (params) {
    const [todos, setTodos] = useState([])

    return <ul>
        {todos.map(todo => <li>{todo}</li>)}
    </ul>
}
```

Here the `return` is HTML but, again, that's a byproduct of the business logic that happens in and around real javascript objects. This makes everything easier to deal with. Adding in a "completed" state is also relatively easy,

```jsx
function Todos (params) {
    const [todos, setTodos] = useState([])

    return <ul>
        {todos.map(todo => <li className={`${todo.complete ? 'complete' : ''}`}>
            {todo.complete ? <s>{todo.text}</s> : todo.text}
        </li>)}
    </ul>
}
```

It's more code than (maybe) a vanilla JS approach but we've removed an entire class of bugs. Bugs like the class name being applied but not the strike through or the todos getting out of sync with the back-end. All that is removed because React promises us that the HTML will eventually become consitent with our state objects.

This is all well and good until you need to actually deal with DOM elements because you're trying to interact with the native features of the browser, like calling `.focus()`. Extending our Todo example, say we would like to offer a button to add todos and after a user clicks on the button we want to focus the browser on the newly added `<li>`.

Traditionally, we would have done this by adding the element to the DOM and then calling `.focus()` on the newly added LI. But because React separates our state from our rendering/HTML we don't actually have immediate access to the HTML. This all comes about like this:

```jsx
function Todos (props) {
    const addButton = React.useRef(null)
    const [todos, setTodos] = React.useState(Immutable.List([]))

    const addTodo = e => {
      setTodos(todos.push('Todo text...'))

      // AFTER THE TODO IS ADDED HERE IS WHERE I'D LIKE TO
      // THROW THE FOCUS TO THE <LI> CONTAINING THE NEW TODO
      // THIS WAY A KEYBOARD USER CAN CHOOSE WHAT TO DO WITH
      // THE NEWLY ADDED TODO
    }

    const updateTodo = (index, value) => {
      setTodos(todos.set(index, value))
    }

    const removeTodo = index => {
      setTodos(todos.delete(index))
      addButton.current.focus()
    }

    return <div>
      <button ref={addButton} onClick={addTodo}>Add todo</button>
      <ul>
        {todos.map((todo, index) => (
          <li tabIndex="0" aria-label={`Todo ${index+1} of ${todos.size}`}>
            <input type="text" value={todos[index]} onChange={e => updateTodo(index, e.target.value)}/>
            <a onClick={e => removeTodo(index)} href="#">Delete todo</a>
          </li>
        ))}
      </ul>
  </div>
}
```

The bug is in the `addTodo` event callback. When the button is clicked I'd _like_ to add the todo to my state and then immediately call `todo.focus()` but I can't do that because React is going to first update the state and _then_ batch together updates in to a single DOM modification. That means that _some_ amount of time is going to pass between me updating the state and the `<li>` actually appearing on the page (like 1 or 2ms of time, but still, I can't just write `.focus()` on the next line).

What we need to do is somehow update our state and then tell React, after you're done, please call `.focus()` for me. We can do that using two fancy new hooks.`useRef` allows us to retain a value object through React's render passes. When we're calling `addTodo` we can set a ref and it'll still be available to us the next time React renders the component. This answers the first half of "after you're done, please call `.focus()`" part of the equation. `useEffect` allows us to answer the second half of the question and provides us a place to interact with the rendered HTML/DOM. The updates look like this,

```jsx
function Todos (props) {
    const focusIndex = useRef(null) // we'll use this to track what should be focused on the next pass
    const [todos, setTodos] = React.useState(Immutable.List([])) // this is our state of todos
    const refs = todos.toArray().map(todo => React.createRef(null)) // each of our rendered todos are tracked here
    
    const addTodo = e => {
        setTodos(todos.push('Todo text...')) // update our state
        focusIndex.current = newTodos.size // track on the next render we want to focus the last todo
    }
    
    useEffect(() => {
        if (focusIndex.current !== null) { // if there is something to focus on
            refs[focusIndex.current].current.focus() // focus on the requested element
        }
        focusIndex.current = null // after the render forget the requested focus so we can do it all again
    }, [todos])
```