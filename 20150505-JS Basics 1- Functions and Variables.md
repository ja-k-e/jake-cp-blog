# Functions and Variables

### Math.random();

I guess I need to explain this if we are going to get anywhere. <code class="stx-js"><span class="var">Math</span>.<span class="mtd">random</span>()</code> gives us a random decimal between <code class="stx-js int">0</span></code> and <code class="stx-js int">1</code>. Got it? Great.

```js
// log a random decimal to the console
console.log(Math.random());
```

The above code would log a random decimal between <code class="stx-js int">0</span></code> and <code class="stx-js int">1</code> to the console.


### Storing Variables
We can extract our random decimal out and store it as a variable.

```js
// Math.random() as variable
var random_decimal = Math.random();
```

We can then <code class="stx-js mtd">log</code> the variable to the <code class="stx-js var">console</code> and use it like any decimal. In this case, let’s multiply it by itself.

```js
// log random_decimal * random_decimal to the console
console.log(random_decimal * random_decimal);
```

This would not return the product of two *different* random decimals, but the product of the *same* random decimal. <code class="stx-js"><span class="var">Math</span>.<span class="mtd">random</span>()</code> is only called one time while defining the variable, so it is the same value when it is multiplied and then logged to the console.

Now, what if we wanted to multiply two *different* random decimals together? We could define two different variables:

```js
// two Math.random() variables
var random_decimal_1 = Math.random(),
    random_decimal_2 = Math.random();
// log random_decimal_1 * random_decimal_2 to the console
console.log(random_decimal_1 * random_decimal_2);
```

This is *okay* in theory, but a better choice would be to use a <code class="stx-js key">function</code>.

### Functions

A function can handle our multiplication for us.

```js
// Math.random() wrapped in a function
function random_decimal() {
    return Math.random() * Math.random();
}
// log random_decimal() to the console
console.log(random_decimal());
```

This would return the product of two different random decimals. Notice the `()` at the end of <code class="stx-js"><span class="mtd">random_decimal</span>()</code> when we run it. This is because we want to run the <code class="stx-js key">function</code>. If we wrote <code class="stx-js"><span class="var">console</span>.<span class="mtd">log</span>(<span class="mtd">random_decimal</span>);</code> it would not run the <code class="stx-js key">function</code>, instead it would spit out the code for the <code class="stx-js key">function</code>.

```js
function random_decimal() {
    return Math.random() * Math.random();
}

console.log(random_decimal());
0.12345678 // a random decimal

console.log(random_decimal);
"function random_decimal() { return Math.random() * Math.random(); }" // a string of code
```


### Function or variable?

Now, you could write the <code class="stx-js key">function</code> in two ways.

```js
// a named function
function random_decimal() {
    return Math.random() * Math.random();
}
// an unnamed function stored in a variable
var random_decimal = function() {
    return Math.random() * Math.random();
}
```

“What’s the diff?”, you might ask. Well, a lot. They achieve the same end, but are actually two very different things. Our first is a named <code class="stx-js key">function</code>. The second is an *unnamed* <code class="stx-js key">function</code> stored inside a variable.

Javascript is evaluated by a browser on load. The way that evaluation works is two-fold. On load, the browser first evaluates declarations. Our first named <code class="stx-js key">function</code> is a declaration. After evaluating the declarations, the browser steps through the code character by character. Our second unnamed <code class="stx-js key">function</code> is an expression and evaluated at that time. This isn’t just a nerdy thing to know. In fact, the two have very different purposes.

```js
// calling the function first
console.log(random_decimal());
// expressing the function second
var random_decimal = function() {
    return Math.random() * Math.random();
}
```

This would return the following error `undefined is not a function` because the <code class="stx-js key">function</code> has not yet been defined at the time it is called in the <code class="stx-js"><span class="var">console</span>.<span class="mtd">log</span>()</code>. It is evaluated afterwards as the browser steps through the code. For you sass nerds, this is the  same thing as defining a variable after using it.


```js
// calling the function first
console.log(random_decimal());
// declaring the function second
function random_decimal() {
    return Math.random() * Math.random();
}
```

This would succeed, because the <code class="stx-js key">function</code> is evaluated prior to stepping through the code even though it is defined after it is called.

“So why the hell would I store it in a variable?” Great question.

Let’s say for whatever reason, you had a case where you wanted your <code class="stx-js key">function</code> to be one of two different things depending on a context. In this case, I will use a variable called <code class="stx-js var">cheating</code>. If <code class="stx-js var">cheating</code> is <code class="stx-js boo">true</code>, we will secretly return a “fake” random number instead of a truly random one.

```js
var cheating = true;
if (cheating) {
  var random_decimal = function() {
    return 0.123456789;
  }
} else {
  var random_decimal = function() {
    return Math.random() * Math.random();
  }
}
console.log(random_decimal());
```

As you can see, <code class="stx-js var">cheating</code> is being defined as <code class="stx-js boo">true</code> which sets the <code class="stx-js"><span class="var">random_decimal</span></code> variable as the first <code class="stx-js key">function</code> instead of the second;

### Simplification
We could achieve the same result in a more condensed form by putting the <code class="stx-js key">if</code> condition inside of a <code class="stx-js key">function</code>.

```js
var cheating = true;
function random_decimal() {
  if (cheating) {
    return 0.123456789;
  } else {
    return Math.random() * Math.random();
  }
}
console.log(random_decimal());
```

This works, but could be much better. The problem here is that we are referring to our <code class="stx-js var">cheating</code> variable inside of our <code class="stx-js key">function</code> and it is defined outside of our <code class="stx-js key">function</code>. This is a bad idea because we want our <code class="stx-js key">function</code> to be modular and run independently of any external data. So to fix it, we will pass a variable into the <code class="stx-js key">function</code>. 

```js
var cheating = true;
function random_decimal(c) {
  // if cheating
  if (c) {
    return 0.123456789;
  // if not cheating
  } else {
    return Math.random() * Math.random();
  }
}
console.log(random_decimal(cheating));
```

Here we are passing the <code class="stx-js var">cheating</code> variable into our <code class="stx-js key">function</code> where it is used inside of the <code class="stx-js key">function</code> as <code class="stx-js var">c</code>. The <code class="stx-js key">function</code> now handles the condition for us.

Using a <code class="stx-js key">function</code> instead of conditionally defining a variable is super efficient, but either approach can be appropriate depending on the implementation. If the <code class="stx-js key">function</code> is going to be called many times and with different <code class="stx-js var">cheating</code> cases throughout your script, you will want to go with the code above. If you are only calling the <code class="stx-js key">function</code> one time on load and your <code class="stx-js var">cheating</code> condition is permanently defined once, assigning the <code class="stx-js key">function</code> to a variable inside of an <code class="stx-js key">if</code> condition makes more sense.

### Cleaning Up
We can make our <code class="stx-js"><span class="mtd">random_decimal</span>()</code> even cleaner by using an inline <code class="stx-js key">if</code> condition.

```js
var cheating = true;
function random_decimal(c) {
  return (c) ? 0.123456789 : Math.random() * Math.random();
}
console.log(random_decimal(cheating));
```

Inside of the `()` is our condition, <code class="stx-js var">c</code>. The `?` indicates that we are running an <code class="stx-js key">if</code> condition. If <code class="stx-js var">c</code> is <code class="stx-js boo">true</code>, it will <code class="stx-js key">return</code> the value <code class="stx-js int">0.123456789</code>. If <code class="stx-js var">c</code> is <code class="stx-js boo">false</code> or <code class="stx-js boo">undefined</code>, the value after the `:` will be returned. In this case, that value would be <code class="stx-js"><span class="var">Math</span>.<span class="mtd">random</span>() * <span class="var">Math</span>.<span class="mtd">random</span>()</code>.

### Before and After

We started with three lines of code that could multiply two random decimals one time:

```js
var random_decimal_1 = Math.random(),
    random_decimal_2 = Math.random();
return random_decimal_1 * random_decimal_2;
```

Now we have created a <code class="stx-js key">function</code> in just as much code that can do the same thing infinitely, plus take a parameter that changes its output in a certain condition:

```js
function random_decimal(c) {
  return (c) ? 0.123456789 : Math.random() * Math.random();
}
return random_decimal(false);
```

Fun stuff, right? Keep learning! The sky is the limit!


---

I am Jake Albaugh and am going to write this bio in first person. These days, I write on CodePen because I care more about it and you than I do about my personal site. Read more articles via [my CodePen blog page](http://codepen.io/jakealbaugh/blog/). View my work on [my CodePen profile](http://codepen.io/jakealbaugh/). If you’re a hip millennial, “get at me” on my twitter [@jake_albaugh](http://twitter.com/jake_albaugh).
