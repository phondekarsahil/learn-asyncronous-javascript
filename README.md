# Asyncronous JavaScript

## Introduction

-   Asynchronous javascript is one of the most important parts of the language because it governs how we perform tasks that take some time to do like requesting data from a database or from an api.
-   So in a very simplistic nutshell `asynchronous code is code that can start now and then finish later`.
-   Before we talk more about asynchronous code let's first of all understand its counterpart synchronous code.
-   So javascript by its very nature is a synchronous language and that basically means that javascript can execute only one statement at a time from top to bottom.

```js
console.log(1);
console.log(2);
console.log(3);
/* prints
1
2
3
*/
```

-   Javascript is also being called a `single threaded language` and that essentially means the same thing. A thread is like an ordered sequence of statements and only one of those statements can run at a time.
-   Imagine a scenario where we want to call a bunch of javascript functions in a file. Some of these functions do quick little things like log something to a console but one of them wants to make a network request to a database on another server somewhere to get some data. Now this could take two or three seconds to complete maybe less maybe more. So in a synchronous programming world because only one statement can run at a time this fetching of data stalls the program this is known as `blocking code`, because it blocks the next line of code from running until it gets the data back and this function is complete.
-   So this is a downfall of synchronous code and this is where asynchronous code comes into play.
-   Hence to request data from network requests for data to a database or an api we use an asynchronous function instead. And this means the function can start now and then finish later.
-   Now since this function is finishing later what we typically do is pass this function or this statement some kind of `callback function` as a parameter and then that `callback function` is the thing that runs and finishes later on once the request is complete and the data comes back.
-   Now remember at all times javascript can only execute one thing at a time but this time when we get to the request function here, we are using an `asynchronous function` to request our external data.
-   What this means is that the browser takes that request and it handles it outside of the scope of this single thread in another part of the browser.
-   It also takes a callback function and puts it to one side too so that it knows to execute this later on when the data comes back.
-   So because this network request has been taken out of this thread and is now running in a different part of the browser javascript can carry on down the queue and run the remaining functions, while this is still going on the request for data.
-   So it continues through these functions and then when it receives the data back from the network request and once the rest of the functions have been executed then we're allowed to call this callback function and finish this original function.
-   It makes our code `non-blocking` because the rest of the functions here, they can run while the request is being made.
-   There are other things at play such as the `event loop` and the `call stack` for asynchronous operations.

## Http Requests

-   Let's now turn our attention to http requests which are the things that are ultimately going to be the actions which take some time to complete in your code.
-   So first of all what is an http request and why would you want to make one.
-   We make HTTP requests to get data from another server.
-   We make these requests to API endpoints.
-   HTTP request methods:
    1. `GET` => Used to get a record or records
    2. `POST` => Used to create or update a record
    3. `PUT` => Used to update a record
    4. `DELETE` => Used to delete a record

## Status Codes

-   When we send a http request, so typically we get `codes` in the ranges of the 100's, 200's, 300's, 400's and 500's. And in each range it means something.
-   `100–199` => All of the status codes in the 100 range, they are to do with information responses
-   `200–299` => Anything to do with success responses is in the 200 range
-   `300–399` => 300 range is any kind of redirection messages
-   `400–499` => 400 range is client error responses. So this is when there's an error in the browser such as when we formatted something incorrectly in the code or if we use a wrong endpoint etc.
-   `500–599` => 500 is when there's a server error. Over here so it's not our fault we've made the correct type of request but there's some kind of error on the server which prevents us from getting the data.

## Callback Functions

-   `Callback functions` are functions that are passed as arguments to a function

```js
const getTodos = (callback) => {
    const request = new XMLHttpRequest();

    request.addEventListener("readystatechange", () => {
        if (request.readyState === 4 && request.status === 200) {
            callback(undefined, request.responseText);
        } else if (request.readyState === 4) {
            callback("cound not fetch data", undefined);
        }
    });

    request.open("GET", "https://jsonplaceholder.typicode.com/todos/");
    request.send();
};

getTodos((err, data) => {
    console.log("callback fired");
    if (err) {
        console.log(err);
    } else {
        console.log(data);
    }
});
```

## Callback Hell

-   Until one request is done to go out and do another request is quite common when you're making requests to different apis.
-   Often you might need to make a request to one api to get some data and then use that data to make a request to another api.
-   So we have to do them in turn. So how would we do this? When would we make a request for the second one? and how do we know that the first one is done? so that we can make a request for that second one.
-   Well we know right, when the callback function fires, at that point we know we have the data or an error but generally speaking we know we have the data at this point so this would be a good time to then go out and do
    our second request because we know the first one is complete.
-   This is called `callback hell` i.e nesting callback within callback.

```js
const getTodos = (resource, callback) => {
    const request = new XMLHttpRequest();

    request.addEventListener("readystatechange", () => {
        if (request.readyState === 4 && request.status === 200) {
            callback(undefined, request.responseText);
        } else if (request.readyState === 4) {
            callback("cound not fetch data", undefined);
        }
    });

    request.open("GET", resource);
    request.send();
};

// callback hell
getTodos("API_URL_1", (err, data) => {
    console.log(data);
    getTodos("API_URL_2", (err, data) => {
        console.log(data);
    });
});
```

-   We perform this kind of one request at a time methodology in a more nicer readable way by using something called `promises`.

## Promises

-   When we use promises the first thing we do is return a new promise.
-   A promise is basically something which is going to take some time to do.
-   A promise is ultimately going to lead to one of two outcomes either a promise is going to be `resolved` meaning we get the data we want and it's resolved or it's going to be `rejected` meaning we get an error at some point and we reject the promise.
-   So there's the two outcomes of a promise.
-   Now this promise takes as a parameter a function. In a promise we automatically get access to two parameters inside this function and that is a `resolve` parameter and a `reject` parameter.
-   These two are functions and they are built into the promise api in javascript and we automatically receive these as parameters in this function. \* So in here we typically fetch some data make a network request and then if we get some kind of data back and it's a success we'd call the `resolve` function and we'd pass the data in that `resolve` method.
-   Now if there's an error instead we'd call `reject` and we'd pass the error through.
-   So the `promise` us either going to `resolve` or `reject` at some point.
-   Now when we get a promise back from a function, we can tack on a `.then()` method and pass a callback function into it.
-   So a promise is basically saying okay well if you pass a function in here then i will fire that function when we resolve the promise.
-   The callback function takes the data that is passed when we `resolve` something in a promise.
-   Now if we `reject` a promise we also get a second callback function as a second argument inside this `then` method.

```js
const getSomething = () => {
    return new Promise((resolve, reject) => {
        // fetch something
        if (data) {
            resolve("Received Data");
        } else {
            reject("Error");
        }
    });
};

getSomething().then(
    (data) => {
        console.log(data);
    },
    (err) => {
        console.log(err);
    }
);
```

-   This is promises in action, we either resolve something or reject something and then fire one of two functions depending on that.
-   We can also use the `.catch()` method to get the rejected promise data rather that passing a second callback function to the `.then()` method.

```js
getSomething()
    .then((data) => console.log(data))
    .catch((err) => console.log(err));
```

-   The best things about promises is that we can chain them together so we can perform one asynchronous task after another in order.
-   `Promise Chaining`:

```js
const getTodos = (resource) => {
    return new Promise((resolve, reject) => {
        const request = new XMLHttpRequest();

        request.addEventListener("readystatechange", () => {
            if (request.readyState === 4 && request.status === 200) {
                const data = JSON.parse(request.responseText);
                resolve(data);
            } else if (request.readyState === 4) {
                reject("cound not fetch data");
            }
        });

        request.open("GET", resource);
        request.send();
    });
};

// Primise Chaining
getTodos("API_URL_1")
    .then((data) => {
        console.log("Promise 1 Resolved: ", data);
        return getTodos("API_URL_2");
    })
    .then((data) => {
        console.log("Promise 2 Resolved: ", data);
    })
    .catch((err) => console.log(err));
```

## The Fetch API

-   There is a newer and quicker way to make http requests using the native `fetch` api which is now built right into the language.
-   This is a fairly modern addition to the language and it's going to require us to write much less code than the older way using the `XMLHttpRequest` object and it's also going to implement the `promise` api under the hood which is going to make handling success and error cases easy too.

*   The fetch api returns to us a promise and it will either resolve if we have a success or reject if there's an error.
*   Now when a promise is returned to us like this we can tack on the dot then method to fire a function when we have a success case when the promise is resolved. And in the reject case in catch we can take an error object in `.catch()` method.
*   So the way the fetch api works is that the promise is only ever rejected when we get some kind of network error. For example if we're offline or we can't reach the api for some reason so that's when we get a rejection.
*   The response we get using the fetch api is not in readable json format. So we could say `response.json()`
*   `response.json` - this returns a promise. So remember a promise is something that typically takes a little bit of time to do and it can either be rejected or resolved.

```js
fetch("API_URL")
    .then((response) => {
        console.log("resolved", response);
        return response.json();
    })
    .then((data) => {
        console.log(data);
    })
    .catch((err) => {
        console.log("rejected", err);
    });
```

## Async and Await

-   `async` and `await` are two key words in javascript that were recently introduced to the language so they're quite modern features.
-   They basically allow us to chain promises together in a clean and much more readable way.
-   Using `async` and `await` what we can do is section off all of our asynchronous code into a single function i.e `an asynchronous function` and then use the `await` keyword inside to chain promises together in a much more readable and logical way.
-   An asynchronous function always return a promise.

```js
const response = await fetch("API_URL");
```

-   The `await` keyword stops javascript from assigning a value to
    the variable until the promise has resolved. Once the promise has resolved
    we can take the value from that resolve function i.e the response
    and assign it to this variable so that now looks like a cleaner way to do this.

```js
const getTodos = async () => {
    const response = await fetch("todos");
    const data = await response.json();
    return data;
};

getTodos()
    .then((data) => console.log("resolved", data))
    .catch((err) => console.log("rejected", err));
```

-   Using async and await, firstly it bundles up all of our asynchronous code inside a function.
-   Secondly it's an asynchronous function so it's not going to block the rest of the code in our application.
-   Thirdly it gives us a much cleaner way to chain promises together.

## Throwing Errors

-   We can throw our own custom errors inside the asynchronous function if we want to reject the promise that this returns.
-   If we want to throw our own error at some point this is the syntax we use

```js
throw new Error("Something went wrong");
```

-   We use the `throw` keyword then we use `new Error()`. We're just creating a new error object.
-   So this throws a new error and when we throw a new error inside an asynchronous function the promise returned by this asynchronous function is rejected.

```js
const getTodos = async () => {
    const response = await fetch("todos");
    if (response.status !== 200) {
        throw new Error("Cannot Fetch Data");
    }
    const data = await response.json();
    return data;
};

getTodos()
    .then((data) => console.log("resolved", data))
    .catch((err) => console.log("rejected", err.message));
```
