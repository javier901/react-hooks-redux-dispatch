# Persisting State and Displaying State

## Objectives

In this code-along, you will learn the following:

* How to persist changes to state.
* How to display changes to the state in the view.

An HTML page, `index.html`, is provided and already linked to `js/reducer.js`.
Open `index.html` in browser to access the functions in `reducer.js` and follow
along.

## Building our Counter Application

Previously, we've had a reducer that updates state. In the example we used, we
defined a `switch` statement with one `case` and a default:

```javascript
function changeState(state, action){
  switch (action.type) {
    case 'INCREASE_COUNT':
      return {count: state.count + 1}
    default:
      return state;
  }
}

let state = {count: 0}
let action = {type: 'INCREASE_COUNT'}

changeState(state, action)
// => {count: 1}
```

## Persisting State

We currently have a problem.  Let's call `changeState` multiple times:

```javascript
changeState(state, {type: 'INCREASE_COUNT'})
  // => {count: 1}
changeState(state, {type: 'INCREASE_COUNT'})
  // => {count: 1}
changeState(state, {type: 'INCREASE_COUNT'})
  // => {count: 1}
```

See that? Our state never increases beyond one.  It starts off as zero, and
while the `changeState` function returns one more than that, if we look at
state, it still returns `{count: 0}`.  Now, fixing this in the console isn't so
bad.  We just write

```javascript
state = changeState(state, {type: 'INCREASE_COUNT'})
state
  => {count: 1}
state = changeState(state, {type: 'INCREASE_COUNT'})
  => {count: 2}
```

Here, we're reassigning state to the return value of our reducer. This way, the
second time `changeState` is called, it is using the updated state in its
arguments.

Ok.  So let's encapsulate this procedure in a function so that we can just call
that method and it will persist our changes.  We'll name that function
`dispatch`.  

```javascript
let state = {count: 0};

function changeState(state, action){
    switch (action.type) {
      case 'INCREASE_COUNT':
        return {count: state.count + 1}
      default:
        return state;
    }
  }

function dispatch(action){
  state = changeState(state, action)
  return state
}

dispatch({type: 'INCREASE_COUNT'})
  // => {count: 1}
dispatch({type: 'INCREASE_COUNT'})
  // => {count: 2}
dispatch({type: 'INCREASE_COUNT'})
  // => {count: 3}
```

Ok, it's fixed!!  Previously our state was stuck at one, but now we just call
our `dispatch` function, and pass through our action.  Let's walk through it.

We declare our state to equal an object `{count: 0}`.  Then, we define our
reducer and our new function `dispatch`.  At the bottom, we call the `dispatch`
function and pass through our action, `{type: 'INCREASE_COUNT'}`.  When we call
`dispatch`, this calls our `changeState` reducer, and passes the action object
to the reducer.  When called, the `changeState` reducer also takes in `state`,
which has been declared up above.  

`state` is assigned the return value of `changeState`. Since the
`INCREASE_COUNT` type was used, the returned value of `changeState` contains a
`count` equal to the previous state's count plus one.

Thus, our state is updated. Each time `dispatch` is called, the current version of
`state` is passed into `changeState`, and then `state` is assigned a new value based
on what `changeState` returns.

## Rendering Our State

Ignoring React for a bit, how would we display something like this on a page?
And how would we make sure that our HTML updates every time we change our state?
Well, to render this on the page we can write a render function that changes our
HTML:

```javascript
function render(){
  document.body.textContent = state.count
}
```

Now if we want to call our render function, we'll see our state on the page:

```javascript
let state = {count: 0};

function changeState(state, action){
    switch (action.type) {
      case 'INCREASE_COUNT':
        return {count: state.count + 1}
      default:
        return state;
    }
  }

function dispatch(action){
  state = changeState(state, action)
  return state
}

function render(){
  document.body.textContent = state.count
}

// call the render function
render()
```

And presto! Our number is displayed on the page.  However, we want to call
`render` every time our state changes.  And it's safe to say our state will not
change unless we call the `dispatch` function.  So we can ensure that the
`render` function runs every time that our `dispatch` function is called by
changing our `dispatch` function to the following.

```javascript
function render(){
  document.body.textContent = state.count
}

function dispatch(action){
  state = changeState(state, action)
  render()
}

dispatch({type: 'INCREASE_COUNT'})
dispatch({type: 'INCREASE_COUNT'})
```

## Summary

Just to show everything together finally:

```js
let state = {count: 0};

function changeState(state, action){
    switch (action.type) {
      case 'INCREASE_COUNT':
        return {count: state.count + 1}
      default:
        return state;
    }
  }

function render(){
    document.body.textContent = state.count
}

function dispatch(action){
  state = changeState(state, action)
  render()
}

render()
```

If you copy the code above into `js/reducer.js` and open `index.html` in 
your browser, in the console, you can confirm everything is working by running `dispatch({type: 'INCREASE_COUNT'})` and watch as the displayed
count increases!

With just this set of functions, we could actually apply our own Redux pattern
to a regular ol' JavaScript and HTML webpage!

## Conclusion

In this lesson, we've introduced a new function called the `dispatch` function.
Our `dispatch` function solved two problems for us.

First, it persisted changes to our state.  This is because we called the
`dispatch` function, the `dispatch` function called our reducer, and then we
took the return value from the reducer and assigned it to be our new state.

Second, it ensured that each time our state updates, our HTML updates to reflect
these changes.  It does this by simply calling the `render` function.  Each time
we call `dispatch` it's as if we are then calling render.  Don't worry about
re-rendering too much.  Remember that when we integrate with React, React will
only be virtually re-rendering the DOM, and then updating the DOM with the
smallest number of changes to ensure a performant application.

<p class='util--hide'>View <a href='https://learn.co/lessons/redux-dispatch'>Redux Dispatch</a> on Learn.co and start learning to code for free.</p>
