---
layout: post
title:      "You Thunk, huh?"
date:       2019-12-06 02:49:49 +0000
permalink:  you_thunk_huh
---


Thunk was something that I didn't completely understand. I knew that I needed it to make things happen buy why? What does it add to my application?

As a review, in Redux, one changes state by calling an Action which in turn calls a function called an Action Creator. Action Creator creates the change and sends the revision to the Reducer. The Reducer then is the one which will perform changes to the state stored in the store. We use the `<Provider />` component to wrap our app which passes the `store` to all the children components. 

Redux can change state in a well defined pattern as described above and rewritten:
Action Creator -*produces*-> Action -*flows into*-> Middleware -*passes action*-> Reducers -*produces*-> State --> React

`dispatch` method handles everything between Middleware and State. The `dispatch` takes our action and passes to all relevant reducers.  However, it is only capable of calling 1 action synchronously.

Many applications require asynchronous processes such as fetching data. Redux Thunk allows a user to return a plain Javascript function instead of an action. This function can then make asynchronous requests and then call the appropriate dispatch action(s) when the request is completed.

What does the name "Thunk" mean? [Techsith](https://www.youtube.com/watch?v=Sqkm39rqmEg) explains it as the "passed tense of think". Once the function finishes the "thinking", the asynchronous request or logic, which is more involved, then it calls the action. In other words, the action is dependent on something, its waiting on something. Thus, the [Redux Thunk Docs](https://github.com/reduxjs/redux-thunk) calls "A **thunk** is a function that wraps an expression to delay its evaluation." The thunk allows for thought to be inputted into our application before calling an action.

Not only does Thunk allow us to fetch data or delay dispatching an action, it also allows us to dispatch multiple actions within the function which we would not be able to do otherwise. 

In summary, Redux Thunk allows two great features in React-Redux applications: 
1) Allows for asychronous requests such as fetching data
2) Allows for mutiple actions to be called at once


