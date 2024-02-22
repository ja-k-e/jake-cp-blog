In [JS Basics 1](http://codepen.io/jakealbaugh/blog/js-basics-1-functions-and-variables/) we discussed `functions` and `variables` and how they relate to each other. In this post, we will focus on arrays and how we can iterate over them with `for` and `while` loops.

## The problem
We have a series of numbers.

```js
// our numbers
var num_1 = 10, num_2 = 20, num_3 = 30, num_4 = 40, num_5 = 50;
```

We need to get the sum of these numbers. To do this, we can simply add the variables.

```js
// adding our numbers
var sum = num_1 + num_2 + num_3 + num_4 + num_5; // = 150
```

This works, but could be much more dynamic. Here we are explicitly adding up all of our numbers. What happens when we have six numbers? Four? Zero? We would need to change our code drastically because it has no flexibility.


## Arrays
Arrays are how we store collections of data in Javascript. Our series of numbers would be best described and most efficiently stored in an array:

```js
// verbose numbers
var num_1 = 10, num_2 = 20, num_3 = 30, num_4 = 40, num_5 = 50;
// concise array
var nums = [10, 20, 30, 40, 50];
```

Items in an array are referred to on a zero-index. `0` = first position, `1` = second position, etc. So if we were to say `nums[0]`, we would be referring to `10`.

```js
// adding our array’s numbers
var sum = nums[0] + nums[1] + nums[2] + nums[3] + nums[4]; // = 150
```

As you can see, we can run the same expression with our array as we did with the individual variables. This is a *slight* improvement because we are storing our data more efficiently, but there is still a hard-coded reference to each value in the array.

## “For” Loops

We want to be able to add the contents of our `nums` array, no matter how many values are in it. To do this, we can use a `for` loop.

```js
var nums = [10, 20, 30, 40, 50], // our nums array
    sum = 0; // put our sum variable up here and initially set it to 0

// incrementing for loop
for (var i = 0; i < nums.length; i++) {
    // add the value to sum
    sum += nums[i];
}

// sum now equals 150 down here
```

Our `for` contains three statements.

The first statement `var i = 0;` executes prior to the loop running.

The second statement `i < nums.length;` is a condition that keeps the loop running. In this case it is saying, “while variable `i` is less than the `length` of (amount of items in) `nums`”.

The third statement `i++` executes at the end of each loop. `i++` is a shortcut for `i = i + 1`. This makes variable `i` increase by `1` each loop.

Since its initial value is `0`, it will run `5` times. Each time it loops, `i` is the incremental value (`0`, `1`, `2`, `3`, `4`). If  we set our second statement to be “less than or equal to”, `i <= nums.length;`, it will run `6` times.

If we acccidentally set our second statement to be “grater than”, `i > nums.length;`, our loop would not run since the first time around it would already have not met the condition.

There is another way we could do this `for` loop:

```js
// decrementing for loop
for (var i = (nums.length - 1); i >= 0; i--) {
    // add the value to sum
    sum += nums[i];
}

// sum still equals 150 down here
```

Can you see the difference? We are decrementing instead of incrementing. Note that when we set variable `i`, we set it to be `1` less than the `length` of `nums`. Why? Our array is zero-indexed. The value of `i` is fed through the loop as follows: `4`, `3`, `2`, `1`, `0`.

The result of these two loops is exactly the same, but the sequence in which the numbers are processed is different. Depending on what you are doing inside of the loop, decrementing or incrementing may make more sense.


## Complexity

What if we wanted to multiply the last item by the sum of all the other items?

```js
var nums = [10, 20, 30, 40, 50],
    sum = 0,
    product = undefined;

// incrementing for loop
for (var i = 0; i < nums.length; i++) {
    if (i < nums.length - 1) {
        // add the value to sum
        sum += nums[i];
    } else {
        // subtract the last value from the ongoing sum which is 100
        product = sum * nums[i];
    }
}

// product equals 5000 and sum equals 100 down here
```

Inside of the loop, we detect if the `i` value is less than the amount of numbers - 1. More simply, whether or not it is not the last number. Remember that the loop starts at `0` because of the zero index, so we need to say `<` instead of `<=`. If the number is not the last number, increase the value of `sum` by the number's value. By the time the `if` condition is `false` and we hit the `else`, we have a total `sum` of our first four numbers. Then we set `product` to be the product of that `sum` by that last number.

If we used our *decrementing* loop, the multiplication would happen in the first loop, while `sum` is still equal to `0`

```js
var nums = [10, 20, 30, 40, 50],
    sum = 0,
    product = undefined;

// decrementing for loop
for (var i = (nums.length - 1); i >= 0; i--) {
    // i == 4 the first time around making this if condition false
    if (i < nums.length - 1) {
        // add the value to sum
        sum += nums[i];
    } else {
        // subtract the last value from the ongoing sum which is 0
        product = sum * nums[i];
    }
}

// product equals 0 and sum equals 100 down here
```

In this case, whether or not we are decrementing or incrementing is incredibly important. This illustrates the basic logic behind why you may have a need for a decrementing loop instead of an incrementing one.


## Bringing it all together

Pairing these principles with the array `push` method, we can generate an array of 10 random numbers, and get the average of them.

```js
var nums = [], // an empty nums array
    count = 10, // we want to generate 10 numbers
    sum = 0;

// incrementing for loop to create numbers
for (var i = 0; i < count; i++) {
    // create a random number between 0 and 100
    var num = Math.random() * 100;
    // push num into nums array
    nums.push(num);
    // add the random number to the ongoing sum
    sum += num;
}

// get the average number out of the array
var avg = sum / nums.length;
```

We start with an empty `nums` array to fill with our numbers, define how many numbers we want to generate with `count`, and set the initial `sum` to `0`. Inside of our loop, we generate a random number, add it to the array, and increase the `sum` by its value. This repeats `10` times. When we define the `avg` after the loop, `sum` will equal the sum of the array of random `nums`. So we divide it by the length of the `nums` array (we could also divide by `count`) to get the average.


## "While" Loops

If you can wrap your head around the logic of `for` loops, `while` loops should come fairly easily.

```js
var nums = [10, 20, 30, 40, 50],
    looping = true,
    i = 0;

while (looping) {
  console.log(nums[i++]);
  if (i == nums.length) looping = false;
}
```

A common case for a `while` loop is a boolean value that is `true` until a certain point. In this case, we have named that value `looping`. It is set to `true` by default. This value will remain `true` until our `i` value is increased to the amount of items in the `nums` array.

What is interesting here is that the `i++` which you may recognize from our `for` loop is the reference to the index of `nums`. Essentially, `nums[i++]` is equivalent to `nums[i]; i++;` The increment happens after using the current value of `i` as a reference. The added benefit is that we are writing less code.

By the time our loop hits the `if` condition, it has been incremented to the value used in the next loop. Here, once `i` is equal to `5`, it kills the loop by setting `looping` to `false`. Can you guess what would happen if the `if` statement came *before* the `console.log`?

The correct answer is that it would run an extra time and log `undefined` for the last value. Can you figure out why?


## Manipulating arrays

I can’t finish this post without saying something about array manipulation. I couldn’t find a better place to put it, so it’s going here.

We can mainpulate arrays in many ways. We can sort from lowest to highest...

```js
var nums = [20, 40, 30, 10, 50];
nums.sort( function(a, b) {
  return a - b;
});

// [10, 20, 30, 40, 50]
```

...or sort from highest to lowest.

```js
var nums = [10, 20, 30, 40, 50];
nums.sort( function(a, b) {
  return b - a;
});

// [50, 40, 30, 20, 10]
```

We can add a number to an array...

```js
var nums = [10, 20, 30, 40, 50];
nums.push(60);

// [10, 20, 30, 40, 50, 60]
```


...and then remove a number from the array.

```js
var nums = [10, 20, 30, 40, 50, 60],
    index = nums.indexOf(10);
nums.splice(index, 1); // starting at index, remove 1 item

// [20, 30, 40, 50, 60]
```

We can even get a string of the array data, with a `|` between each number:

```js
var nums = [10, 20, 30, 40, 50];
nums.join(" | ");

// "10 | 20 | 30 | 40 | 50"
```

As you can see, manipulating arrays with loops is quite useful even though it can get fairly complicated. I know that this stuff can seem out of reach at times; this was the biggest hurdle for me when I was first digging into Javascript. Rest assured, once you get it, *everything* starts to make a lot more sense very quickly.

Keep at it!

---

I am Jake Albaugh and am going to write this bio in first person. These days, I write on CodePen because I care more about it and you than I do about my personal site. Read more articles via [my CodePen blog page](http://codepen.io/jakealbaugh/blog/). View my work on [my CodePen profile](http://codepen.io/jakealbaugh/). Or if you’re a hip millennial, “get at me” on my twitter [@jake_albaugh](http://twitter.com/jake_albaugh).
