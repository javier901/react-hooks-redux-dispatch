Persisting State and Displaying State
==============

In this lesson, you will learn the following:

* How to persist changes to state.
* How to display changes to the state in the view.

## Building our Counter Application
So far, we have a reducer that effectively updates the state.

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

We currently have a problem in our application.  Let's call `changeState` multiple times:

```javascript
changeState(state, {type: 'INCREASE_COUNT'})
	// => {count: 1}
changeState(state, {type: 'INCREASE_COUNT'})
	// => {count: 1}
changeState(state, {type: 'INCREASE_COUNT'})
	// => {count: 1}
```

See that? Our state never increases beyond one.  It starts off as zero, and while the `changeState` function returns one more than that, if we look at state, it still returns `{count: 0}`.  Now, fixing this in the console isn't so bad.  We just write

```javascript
state = changeState(state, {type: 'INCREASE_COUNT'})
state
	=> {counter: 1}
state = changeState(state, {type: 'INCREASE_COUNT'})
	=> {counter: 2}
```

Ok.  So let's encapsulate this procedure in a function so that we can just call that method and it will persist our changes.  We'll name that function `dispatch`.  

```javascript
let state = {counter: 0};

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
}

dispatch({type: 'INCREASE_COUNT'})
	// => 1
dispatch({type: 'INCREASE_COUNT'})
	// => 2
dispatch({type: 'INCREASE_COUNT'})
	// => 3
```

Ok, it's fixed!!  Previously our state was stuck at one, but now we just call our `dispatch` function, and pass through our action.  Let's walk through it.


We declare our state to equal an object `{counter: 0}`.  Then, we define our reducer and our new function `dispatch`.  At the bottom, we call the `dispatch` function and pass through our action, `{type: 'INCREASE_COUNT'}`.  When we call `dispatch`, this calls our `changeState` reducer, and passes the `changeState` reducer the action object.  The `changeState` reducer references the already declared state and passes that through.  And the `changeState` reducer has access to the state object because the state object is declared in global scope.  Then in the reducer we return a new object that has a property count, whose corresponding value is one plus the previous state's count -- in this case the state is changed to one.  Finally, back in the `dispatch` function we take the return value of `{counter: 1}` and assign it to our state variable.  Thus, our state is updated.


## Rendering our State

Now, how would we display something like this on a page?  And how would we make sure that our HTML updates every time we change our state?  Well, to render this on the page we can write a render function that changes our HTML.   

```javascript
function render(){
	document.setInnerHTML = state.counter
}
```

Now if we want to call our render function.  We'll see our state on the page.

```javascript
let state = {counter: 0};

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
}

function render(){
	document.setInnerHTML = state.counter
}

// call the render function
render()
```

And presto! Our number is displayed on the page.  However, we want to call `render` every time our state changes.  And it's safe to say our state will not change unless we call the `dispatch` function.  So we can ensure that the `render` function runs every time that our `dispatch` function is called by changing our `dispatch` function to the following.


```javascript
function render(){
	document.setInnerHTML = state.counter
}

function dispatch(action){
	state = changeState(state, action)
	render()
}

dispatch({type: 'INCREASE_COUNT'})
dispatch({type: 'INCREASE_COUNT'})
```

And there you have it, our HTML updates appropriately.

## Summary

We introduced a new function called the `dispatch` function.  Our `dispatch` function solved two problems for us.  

First, it persisted changes to our state.  This is because we called the `dispatch` function, the `dispatch` function called our reducer, and then we took the return value from the reducer and assigned it to be our new state.


Second, it ensured that each time our state updates, our HTML updates to reflect these changes.  It does this by simply calling the `render` function.  Each time we call `dispatch` it's as if we are then calling render.  Don't worry about re-rendering too much.  Remember that when we integrate with React, React will only be virtually re-rendering the DOM, and then updating the DOM with the smallest number of changes to ensure a performant application.


<p class='util--hide'>View <a href='https://learn.co/lessons/redux-dispatch'>Redux Dispatch</a> on Learn.co and start learning to code for free.</p>
