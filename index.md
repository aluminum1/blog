# Bruce's coding blog


## Import-oriented programming in Javascript

Since `import` and `export` were introduced in ES6 into Javascript, I believe we now have a much simpler and cleaner way to implement classes, and object-oriented programming ideas in general. For want of a better word, I'm going to call it _`import-oriented programming'_.


### The old way: class notation

Consider this example of the old class notation (taken from the wonderful book _Eloquent Javascript_ by Marijn Haverbeke):

```javascript
class Rabbit {

  constructor(type) {
    this.type = type
  }

  speak(line) {
    console.log(`The ${this.type} rabbit says '${line}'`)
  }
}

let killerRabbit = new Rabbit("killer")
killerRabbit.speak("Hello") // The killer rabbit says Hello
let blackRabbit = new Rabbit("black")
blackRabbit.speak("Hi") // The black rabbit says Hi
console.log("The blackRabbit's type is " + blackRabbit.type) // The blackRabbit's type is black

```

It looks ok, but it's a bit messy, because the `this` keyword can be a bit confusing. Moreover, the `class` keyword is just syntactic sugar for a function called `Rabbit` which creates objects. It's complicated... Rabbit is a function, which itself is a kind of object. `Rabbit` has an object property called `prototype`, which is itself an object. Moreover, the objects that get created by `Rabbit` (namely `killerRabbit` and `blackRabbit`) each have a ``__proto__`` pseudo-property, which points to the `prototype` object on their parent factory function `Rabbit` (to which the `this` keyword binds). Complicated! Here is a diagram to illustrate what is going on:

![image](https://github.com/aluminum1/blog/assets/18632281/08fb37e5-cc1a-4777-9552-74073a92a835)

So, it's not really _object-oriented programming_ (which is ok, since OOP is a mess anyway). It's not really _protocol-oriented programming_ either. It's one thing, faking as if it's another type of thing, with a whole lot of muddle and confusion thrown in. 

Is there a cleaner way to do this? 

### The import-oriented way

Consider the following code:

```
// Inside Rabbit.js
function speak(line) {
  console.log(`The ${this.myType} rabbit indeed says '${line}'`)
}

export function createRabbit(type) {
  let myType = type
  return {
    type: myType,
    speak: speak
  }
}

// Inside main.js
import {createRabbit} from "./Rabbit.js"

let killerRabbit = createRabbit("killer")
killerRabbit.speak("Hello") // The killer rabbit says Hello

let blackRabbit = createRabbit("black")
blackRabbit.speak("Hi!")
console.log("The blackRabbit's type is " + blackRabbit.type) // The blackRabbit's type is black

```

I would argue this new code is conceptually cleaner. The diagram is as follows:

![image](https://github.com/aluminum1/blog/assets/18632281/70e4bc89-3ef5-46a4-b976-b45185cc57d8)

In the old class system, when you called `blackRabbit.speak`, the runtime _implicitly_ realized that `speak` is not a method on the `blackRabbit` object, so it 
went looking for it in its `__proto__` object. Note that `__proto__` is not strictly speaking an explicit property of the `blackRabbit` object (although it can be accessed in DevTools in the browser). So it's all a bit weird and implicit.

In the new system, `speak` is _explicitly_ a method on the `blackRabbit` object. It points to the `speak` function, which is only contained in the module scope of `Rabbit`. This, I would argue, is a cleaner and more explicit setup. 


```
