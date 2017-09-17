# JavaScript Logging

## Overview
In this lesson, we'll look at a few different ways to print data to the JavaScript console as a means of debugging our code.

## Objectives
1. Explore the `console` object.
2. Use different logging methods.

## Logging
Journals give us a window into the past, helping us discern what happened when and what the outcome was. We keep journals, traditionally, by writing things down with a time stamp (tracking the date at which we wrote the entry).

In programming, logging to the console is like journaling. It records a history of a running application that we can revisit, giving us insight into what was happening at a given point in time. Logging out data is a useful tool for tracking bugs, performance, and generally ensuring that our applications are chugging along.

In this lesson, we'll learn how to leverage JavaScript's main logging construct to quickly and simply help us debug our code. Let's dive in!

![dive in](https://i.giphy.com/LlPGmmhr0GcKs.gif)

## `console`

Open the console in your browser of choice.

**Top Tip**: If you already have the console open but you find that it's getting a little cluttered, you can clear it out by clicking the "Clear console" button — it looks like this in Chrome:

![clear console](https://curriculum-content.s3.amazonaws.com/skills-based-js/clear_console.png)

Alternatively, you can press `cmd` + `k`" or `ctrl` + `l` to make your console look fresh and new.

Now enter `console` in the console (that's a little funky to say, huh?) and press "enter". In Chrome, you'll see something like

Object {}

This is all probably a bit mystifying at the moment. You can explore that console thing (psst: it's an _object_, which we'll learn more about later), but feel free to move on to learn about how we use it.

### `log()`

The venerable `console.log()` is an all-purpose logging _method_. (A **method** or a **function** is a bit of code that _does_ something. We _call_ them when we want them to act.) In programming, _logging_ refers to the process of printing information about the program as it runs. Try it out!

``` javascript
console.log("I'm logging! I'm a regular lumberjack!")
```

You can pass any number of messages to `console.log()` by separating them with commas; when printed, they'll be separated by a space:

``` javascript
console.log('one', 'two', 'three')
```

When you enter the above in your console, you'll see "one two three".

And you don't just have to pass strings to `console.log()` — try this:

``` javascript
console.log("I must have logged", 1000, "times today.")
```

You should see "I must have logged 1000 times today."

Note that when you use strings, the comma must come _after_ the end quotation mark — this is because it's not punctuation like in English writing, but a way of telling JavaScript, "Hey, I'm going to give you something else!"

### `error()`

`console.error()` prints an error and usually includes a _stack trace_. A "stack trace" is a report of code that was executed at a certain time (in this case, starting from when the error occurred and working backwards). Enter the following in your console:

``` javascript
console.error('Danger, Will Robinson!')
```

You should see something like

![console.error](https://curriculum-content.s3.amazonaws.com/skills-based-js/console_error.png)

If you click on the arrow, you can see the stack trace. When you're debugging, you can click on the linked line numbers in the stack trace to jump to the relevant line in the source code. That won't be particularly useful here, since it will just take you to some of Chrome's internal JavaScript — but it can be incredibly useful when you're writing your own code!

You can pass several messages at once, separated by a comma:

``` javascript
console.error("Danger!", "Something bad happened!", "Time to debug!")
```

When printed, there will be a space after each message: `"Danger! Something bad happened! Time to debug!"`.

You might ask why we'd ever need to use this — isn't the goal of writing good code to avoid errors? Well, sure, but sometimes errors are out of our control: the network could go down, data could change, or a user could enter something invalid. In these cases, it's helpful to report not only what happened (which logging _generally_ is good for) but also what kind of thing happened — in these cases, it was an error, so we use `console.error()`.

### `warn()`

`console.warn()` does as its name suggests: it prints a warning. We can use `console.warn()` to, well, warn developers that an action they've taken _might_ not be wise — one common use-case is giving developers a heads-up that some of the code they've written might no longer be supported.

``` javascript
console.warn('Hm, you might not want to do that.')
```

It might be a while before you find yourself needing to use `console.warn()` — but you should think of it every time you see those yellow messages in the browser's console!

![console.warn()](https://curriculum-content.s3.amazonaws.com/skills-based-js/console_warn.png)

As with `console.error()`, we use `console.warn()` to indicate in our log history that something undesirable happened, but it _shouldn't_ have broken anything (unlike an error, which could break things). Warnings, as mentioned above, could give us insight into things that might break in the future or actions that _worked_ but maybe shouldn't have — just like in real life!

## Wrap-up

`console` has _tons_ (or _tonnes_ for some folks) of useful functionality — be sure to explore! Logging info, errors, warnings, and stack traces is essential to staying on top of your application's functionality.

## Resources

- [MDN: Console](https://developer.mozilla.org/en-US/docs/Web/API/Console)
- [Mastering The Developer Tools Console](http://blog.teamtreehouse.com/mastering-developer-tools-console)

<p class='util--hide'>View <a href='https://learn.co/lessons/javascript-logging'>JavaScript Logging</a> on Learn.co and start learning to code for free.</p>
