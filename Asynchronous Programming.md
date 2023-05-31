# Asynchronous programming
Asynchronous actions are the actions that we initiate now and they finish later. eg, setTimeout(). 
Synchronuous actions are the actions that initiate and finish one-by-one.

## setTimeout()
The ```setTimeout()``` method executes a block of code after the specified time. The met hod executes the code only once.\

Syntax : 
```js
let timerId = setTimeout(function, milliseconds, parameter1, ....paramenterN);
```

 If you want to stop this function call, you can use the ```clearTimeout()``` method.
 
Syntax:
```js
clearTimeout(timerId);
```

Commonly used example: 
```js
setTimeout(()=>{
	alert('Hello!')
}, 2000); 
```

Example with arguments: 
```js
function greet(name, lastName) {
    console.log('Hello' + ' ' + name + ' ' + lastName);
}

let id = setTimeout(greet, 1000, 'John', 'Doe'); 
clearTimeout(id)
```


## setInterval()
The ```setInterval()``` method repeats a block of code at every given timing event.
Commonly used example: 
```js
setTimeout(()=>{
	alert('Hello!')
}, 2000); 
```
If you want to stop this function call, then you can use the ```clearInterval()``` method.

Syntax:
```js
clearInterval(intervalID);
```

## Callbacks
A callback function is a function passed into another function as an argument which is then invoked inside the outer function to complete an action.

Syntax:
```js
const run = (param, callback) =>{
	// do something asynchronously and after its complete, call the callback function
	callback()
}
const a_callback_function = () =>{
	// do something
}
run(arg, a_callback_function);
```
This is called "callback-based" style of asynchronous programming. A function that does something asynchronously should provide a callback argument where we put the function to run after its complete. 

Example:
```js
const loadScript = (src, callback) =>{
	let script = document.createElement("script");
	script.src = src;
	script.onload = callback(src);
	document.body.appendChild(script);
}

const message = (src) =>{
	alert("Script has loaded : " + src)
}

loadScript("https://cdn.jsdelivr.net/npm/bootstrap@5.2.1/dist/js/bootstrap.bundle.min.js", message)
```

### Handling errors with callback
This can be done by supplying error argument to callback function.

Syntax:
```js
const run = (param, callback) =>{
	// do something asynchronously and after its complete, call the callback function
	callback(abc)
		// or 
	callback(new Error("some error"))
}
const a_callback_function = (error,abc) =>{
	// do something
	if(error){
		// handle it
	}
	else{
		// all good
	}
}
run(arg, a_callback_function);
```

Example of Callback with error handling :

```js
const loadScript = (src, callback) => {
	let script = document.createElement("script")
	script.src = src
	script.onload = () => {
		callback(null, src)
	}
	script.onerror = () => {
		callback(new Error("Src can not be loaded!"))
	}
	document.body.appendChild(script)
}

const message = (error, src) => {
	if (error) {
		console.log(error)
		return
	}
	console.log("Script has loaded : " + src)
}

loadScript("https://cdn.jsdelivr.net/npm/bootstrap@5.2.1/dist/js/bootstrap.bundle.min.js", message)
```


### Callback hell or pyramid of doom 
Callback Hell is essentially nested callbacks stacked below one another forming a pyramid structure. Every callback depends/waits for the previous callback, thereby making a pyramid structure that affects the readability and maintainability of the code. 

## Promises 
A promise is a good way to handle asynchronous operations. It is used to find out if the asynchronous operation is successfully completed or not. A promise may have one of three states : 
Pending, Fulfilled, Rejected.

Syntax:
```js
let promise = new Promise((resolve, reject)=>{
     //do something
});
```


If the promise returns successfully, the ```resolve()``` function is called. And, if an error occurs, the ```reject()``` function is called. Both resolve and reject are callback functions.

The promise object returned by the new Promise constructor has these properties:

1. state: initially pending, then changes to "fulfilled" when "resolve" is called else "rejected" when reject is called
2. result: initially undefined then changes to value, resolve(value) or reject(value)

### then() and catch()
The consuming code can receive the final result of a promise through then and catch.

The ```then()``` method is used with the callback when the promise is successfully fulfilled or resolved.

Syntax:
```js
promiseObject.then(
	(value)=>{
	// handle result
	}, 
	(error)=>{
	// handle error
	}
);
```

If we are interested in only successful completions: 
```js 
promiseObject.then((value)=>{
	// handle result
})
```


If we are interested in only errors ```catch()``` is used.
```js 
promiseObject.catch((error)=>{
	// handle error
})
```

Example of Promises: 

```js
let p1 = new Promise((resolve, reject) => {
        console.log("Promise is pending")
        setTimeout(() => {
                console.log("I am a promise and I am resolved after 5 seconds ")
                resolve(true)
        }, 5000)
})

let p2 = new Promise((resolve, reject) => {
        console.log("Promise is pending")
        setTimeout(() => {
                console.log("I am a promise and I am rejected after 5 seconds")
                reject(new Error("I am an error"))
        }, 5000)
})

// To get the value
p1.then((value) => {
        console.log(value)
})

// To catch the errors
// p2.catch((error) => {
//         console.log("some error occurred in p2")
// })

p2.then((value)=>{
        console.log(value) 
},(error)=>{
        console.log(error)
})
```


### Promise Chaining
We can chain promises and make them pass the resolved values to another promise. 

Flow of execution : 

1. the initial promise resolves.
2. the next .then() is called, which returns a new promise which is resolved. If the promise simply returns a value, it is treated as a revolved promise with that value
3. the next .then() is called with the previous one and this keeps going.

```js
let p1 = new Promise((resolve,reject)=>{
	setTimeout(()=>{
		console.log("promise resolved after 2 seconds")
		resolve("value1")
	}, 4000)
})

p1.then((value)=>{
	console.log("inside 1st then", value)
	return new Promise((resolve,reject)=>{
		setTimeout(()=>{
			 resolve("value2")
		}, 6000)
	})
}).then((value)=>{
	console.log("inside 2nd then", value)
	return 2
})

.then((value)=>{
	console.log("inside 3rd then", value)
})
```

### Multiple handlers
We can attach multilpe .then() to one promise. They don’t pass the result to each other, instead they process it independently.

```js
let p1 = new Promise((resolve, reject) => {
        setTimeout(() => {
                resolve(1);
        }, 2000)
})

p1.then((value) => {
        console.log("Hurray, ", value)
        return new Promise((resolve, reject) => {
                setTimeout(() => {
                        resolve(4)
                }, 4000)
        })
}).then((value) => { console.log(value) })

p1.then(() => {
        console.log("Congratulations this promise is now resolved")
})
````

### Promise API 
There are 6 methods of Promise class.

```js 
let p1 = new Promise((resolve, reject) => {
        setTimeout(() => {
                resolve("Value 1");
        }, 2000);
});

let p2 = new Promise((resolve, reject) => {
        setTimeout(() => {
                reject(new Error("Error"));
        }, 4000);
});

let p3 = new Promise((resolve, reject) => {
        setTimeout(() => {
                resolve("Value 3");
        }, 6000);
});
```

1. ```Promise.all([promises])``` - wait for all promises to get resolved and returns an array of results. If any one promise fails, error is given and all other results are ignored.
```js
let promise = Promise.all([p1,p3])
promise.then((value)=>{
	console.log(value)
})
```

2. ```Promise.allSettled([promises])``` - waits for all the promises to settle and then returns an array of object with status and value.
```js
let promise = Promise.allSettled([p1,p2,p3])
promise.then((value)=>{
	console.log(value)
})
```
   
3. ```promiseObj.race([promises])``` - waits for the first promise to settle and its result/error becomes the output
```js
let promise = Promise.race([p1,p2,p3])
promise.then((value)=>{
	console.log(value)
})
```

4. ```Promise.any([promises])``` - waits for the first promise to fulfill (and not reject) and its result becomes the output. It throws AggregateError if all the promises are rejected.
```js
let promise = Promise.any([p1,p2,p3])
promise.then((value)=>{
	console.log(value)
})
```

5. ```Promise.resolve(value)``` - makes resolved promise with given value
```js
let promise = Promise.resolve(10)
promise.then((value)=>{
	console.log(value)
})
```

6. ```Promise.resolve(value)``` - makes rejected promise with given error
```js
let promise = Promise.reject(10)
promise.then((value)=>{
	console.log(value)
})
```

## Async/await
Special syntax to work with promises in Js. Any function can be made asynchronous using ```async``` keyword.
An async function always returns a promise and other values are wrapped inside promise automatically.

Syntax:
```js
const fun = async () =>{
	return "anything (a promise)"
}
fun()
fun().then(alert) // can also be done
```
### await keyword
The await keyword is used inside the async function to wait for the asynchronous operation.
The use of await pauses the async function until the promise returns a result (resolve or reject) value.

Syntax:
```js
let result = await promise;
```

For example: 
```js

// a promise
let delhiWeather = new Promise((resolve, reject) => {
	setTimeout(() => {
		resolve("26 deg")
	}, 2000)
})

// an async function
const weather = async () => {
	// a promise inside async function
	let bangaloreWeather = new Promise((resolve, reject) => {
		setTimeout(() => {
			resolve("21 deg")
		}, 5000)
	})

	console.log("Fetching Delhi Weather Please wait ...")
	let delhiW = await delhiWeather
	console.log("Fetched Delhi Weather: " + delhiW)

	console.log("Fetching Bangalore Weather Please wait ...")
	let bangaloreW = await bangaloreWeather
	console.log("Fetched Bangalore Weather: " + bangaloreW)
	return [delhiW, bangaloreW]
}

// calling async function 
weather().then((value) => {
	console.log(value)
})
```


## Error handling
```try...catch``` allows us to catch errors so the script can do something instead of halting. 

Syntax:
```js
try{
	// try some code
}
catch(err){
	// error handling , err variable contains error object
	console.log(err.name, err.message, err.stack)
}
```

```try..catch``` works synchronously. But It will not work in scheduled code, like setTimeout().
Example:
```js
try{
	setTimeout(()=>{
		// code with error
	})
}catch(err){}
```
Here the script dies and catch doesn't work beacuse the function itself is executed later, when the engine has already left the try..catch construct	

### Throwing custom error
The ```throw``` operator generates an error.
```js
let error = new Error(message);
// or
let error = new SyntaxError(message);
let error = new ReferenceError(message);
throw error;
```

### finally clause
The ```finally``` clause is often used when we start doing something and want to finalize it in any case of outcome.
If finally exists it will run in all cases:
1. after try, if there were no errors
2. after catchs, if there were errors

If there is a return in try, finally is executed just before the controls return to the outer code.


Example:
```js
const loadScript = async (src) => {
	return new Promise((resolve, reject) => {
		let script = document.createElement("script")
		script.src = src;
		script.onload = () => {
			resolve("Script ready : " + src)
		}
		script.onerror = () => {
			reject(new Error("Script has some error"))
		}
		document.body.appendChild(script)
	})
}
const main1 = async () => {
	try{
		let a = await loadScript("https://cdn.jsdelivr.net/npm/bootstrap@5.2.2/dist/js/bootstrap.bundle.min.js")
		console.log(a)
	}
	catch(err){
		console.log(err)
	}
	finally{
		console.log("done")
	}
}
main1()
```
