# Intro to Debugging

## Overview
In this lesson, we'll look at a few different ways to print data to the JavaScript console as a means of debugging our code.

## Objectives
1. Use the built-in `console` object for debugging.
2. Explain the use cases for the various logging methods.

## Faulty vending machines
We've already used `console.log()` to print out data to the console, but we haven't really discussed why you'd want to do that. In short, it's one of the simplest, best tools in a JavaScript programmer's debugging toolkit.

>As soon as we started programming, we found to our surprise that it wasn’t as easy to get programs right as we had thought. We had to discover debugging. I can remember the exact instant when I realized that a large part of my life from then on was going to be spent in finding mistakes in my own programs.
— Maurice Wilkes, 1949

To understand how `console.log()` fits in to our debugging workflow, let's talk again about our vending machine. We don't really know how the machine works under the hood — we just know that we can provide it some inputs (money and a snack selection) and it will return an output (our snack). However, what happens when the machine breaks? Well, we'll obviously wait patiently and calmly until it's fixed.

<picture>
  <source srcset="https://curriculum-content.s3.amazonaws.com/web-development/js/basics/intro-to-debugging-readme/hangry.webp" type="image/webp">
  <source srcset="https://curriculum-content.s3.amazonaws.com/web-development/js/basics/intro-to-debugging-readme/hangry.gif" type="image/gif">
  <img src="https://curriculum-content.s3.amazonaws.com/web-development/js/basics/intro-to-debugging-readme/hangry.gif" alt="WHERE'S MY MAC N' CHEESE?">
</picture>

At some point (probably after we've resorted to eating the interns), a mechanic will show up with the necessary tools, open the machine, and get to work diagnosing the problem. If the overall machinery is composed of a series of small, self-contained parts that could be tested in isolation, locating the malfunctioning piece should be a breeze. *Cough cough*. **An architecture comprised of many small, compartmentalized functions is easier to debug and maintain than a single monolithic function**. But we digress...

The mechanic surveys the machine's internals and doesn't see any obvious breakages. Then, with the machine still open, they insert some money, make a selection, and watch the motors turn. With some luck, the issue may reveal itself, but this sort of observational debugging is pretty simplistic. If the bug remains hidden, the next step is to put the machine into its 'repair' mode. This makes the machine provide more verbose feedback of what's happening as it runs through its processes. Instead of just displaying the standard transactional fare, success (`"Your snack was dispensed. Thank you!"`) and error (`"Please insert more money."`) messages, the vending machine will now provide much more granular information and alerts about every step of the vending process.

## Tracing
Like the mechanic, we can't always diagnose what's malfunctioning from just watching the code run. The longer you stare at the same piece of code, the more familiar it becomes and the less likely you are to notice any typos or faulty logic. When we run a faulty function, we have three pieces of information:
- The inputs we provide to it.
- What the code in the function body looks like it should be doing.
- The function's output.

Somewhere in the function body, something's going awry, but we don't have enough information to figure out where or how. Enter: _tracing_.

Tracing — more commonly known as _`console.log()` debugging_ — is our equivalent of the mechanic's 'repair' mode: it involves placing logging statements in our code that don't have any effect on functionality but provide information on the values of variables at various points during our program. The name comes from helping us **trace** what's happening while our code executes.

Let's look at our final `vendingMachine()` function as an example. We made one small change that broke the code:
```js
function validateSelection (selection) {
  switch (selection) {
    case 'Pretzels':
    case 'Chips':
    case 'Water':
      return true;
    default:
      return null;
  }
}

function getPrice (selection) {
  switch (selection) {
    case 'Pretzels':
      return 100;
    case 'Chips':
      return 75;
    case 'Water':
      return 50;
  }
}

function vendingMachine (snackSelection, moneyInserted) {
  if (validateSelection(snackSelection) === false) {
    return 'Please select a valid snack.';
  }

  const price = getPrice(snackSelection);

  if (price > moneyInserted) {
    return `Please insert more to purchase ${snackSelection}.`;
  }

  const change = moneyInserted - price;

  return `${snackSelection} dispensed. Your change is ${change}. Thank you!`;
}

vendingMachine('Ice cream', 100);
// => "Ice cream dispensed. Your change is NaN. Thank you!"
```

Uh oh!

![L train `NaN`](https://curriculum-content.s3.amazonaws.com/web-development/js/basics/intro-to-debugging-readme/L_train_NaN.JPG)

At first glance, it looks like our `change` variable is the problem. Let's throw a pair of `console.log()`s right before `change` is calculated:
```js
function validateSelection (selection) {
  // Code unchanged
}

function getPrice (selection) {
  // Code unchanged
}

function vendingMachine (snackSelection, moneyInserted) {
  if (validateSelection(snackSelection) === false) {
    return 'Please select a valid snack.';
  }

  const price = getPrice(snackSelection);

  if (price > moneyInserted) {
    return `Please insert more to purchase ${snackSelection}.`;
  }

  console.log("In vendingMachine(), 'moneyInserted' contains:", moneyInserted);
  console.log("In vendingMachine(), 'price' contains:", price);

  const change = moneyInserted - price;

  return `${snackSelection} dispensed. Your change is ${change}. Thank you!`;
}
```

Now we get some additional information when running the code:
```js
vendingMachine('Ice cream', 100);
// LOG: In vendingMachine(), 'moneyInserted' contains: 100
// LOG: In vendingMachine(), 'price' contains: undefined
// => "Ice cream dispensed. Your change is NaN. Thank you!"
```

So `moneyInserted` correctly contains the number `100`, but why the heck does `price` contain `undefined`? Well, looks like it gets assigned the return value from invoking the `getPrice()` function, so that function must be `return`ing `undefined`. We can use `console.log()` for more than simply checking the value of variables. This is a good opportunity to use it as a flag of whether or not the JavaScript engine reaches a particular line of code:
```js
function validateSelection (selection) {
  // Code unchanged
}

function getPrice (selection) {
  switch (selection) {
    case 'Pretzels':
      return 100;
    case 'Chips':
      return 75;
    case 'Water':
      return 50;
  }

  console.log("This logging statement will only run if none of the RETURN statements are hit.");
}

function vendingMachine (snackSelection, moneyInserted) {
  // Code unchanged
}

vendingMachine('Ice cream', 100);
// LOG: This logging statement will only run if none of the RETURN statements are hit.
// LOG: In vendingMachine(), 'moneyInserted' contains: 100
// LOG: In vendingMachine(), 'price' contains: undefined
// => "Ice cream dispensed. Your change is NaN. Thank you!"
```

So none of the `return` statements in `getPrice()` are getting hit. Let's see what the `selection` variable contains:
```js
function validateSelection (selection) {
  // Code unchanged
}

function getPrice (selection) {
  console.log("In getPrice(), 'selection' contains:", selection);

  switch (selection) {
    case 'Pretzels':
      return 100;
    case 'Chips':
      return 75;
    case 'Water':
      return 50;
  }

  console.log("This logging statement will only run if none of the RETURN statements are hit.");
}

function vendingMachine (snackSelection, moneyInserted) {
  // Code unchanged
}

vendingMachine('Ice cream', 100);
// LOG: In getPrice(), 'selection' contains: Ice cream
// LOG: This logging statement will only run if none of the RETURN statements are hit.
// LOG: In vendingMachine(), 'moneyInserted' contains: 100
// LOG: In vendingMachine(), 'price' contains: undefined
// => "Ice cream dispensed. Your change is NaN. Thank you!"
```

Ah right! How silly of us. Of course `'Ice cream'` isn't going to match our `'Pretzels'`, `'Chips'`, or `'Water'` cases. But hmm, we implemented the call to `validateSelection()` as a guard against invalid snack selections. What's going wrong? It's pretty clear that `validateSelection()` is not `return`ing `false`, but what **is** it returning? Wouldn't it be awesome if we could just check the value returned by invoking that function? Well, buckle up partners:
```js
function validateSelection (selection) {
  // Code unchanged
}

function getPrice (selection) {
  // Code unchanged
}

function vendingMachine (snackSelection, moneyInserted) {
  console.log('Return value of validateSelection(snackSelection):', validateSelection(snackSelection));

  if (validateSelection(snackSelection) === false) {
    return 'Please select a valid snack.';
  }

  // Code unchanged
}

vendingMachine('Ice cream', 100);
// LOG: Return value of validateSelection(snackSelection): null
// LOG: In getPrice(), 'selection' contains: Ice cream
// LOG: This logging statement will only run if none of the RETURN statements are hit.
// LOG: In vendingMachine(), 'moneyInserted' contains: 100
// LOG: In vendingMachine(), 'price' contains: undefined
// => "Ice cream dispensed. Your change is NaN. Thank you!"
```

Neat — we can `console.log()` out the result of any expression! Through our awesome debugging powers, we've discovered that our `validateSelection()` function is `return`ing `null` instead of `false`. Sure enough:
```js
function validateSelection (selection) {
  switch (selection) {
    case 'Pretzels':
    case 'Chips':
    case 'Water':
      return true;
    default:
      return null; // Found the bug!
  }
}
```

If we change that back to `return false;`, everything's back to working normally:
```js
function validateSelection (selection) {
  switch (selection) {
    case 'Pretzels':
    case 'Chips':
    case 'Water':
      return true;
    default:
      return false; // Changed from 'null' to 'false'
  }
}

function getPrice (selection) {
  console.log("In getPrice(), 'selection' contains:", selection);

  switch (selection) {
    case 'Pretzels':
      return 100;
    case 'Chips':
      return 75;
    case 'Water':
      return 50;
  }

  console.log("This logging statement will only run if none of the RETURN statements are hit.");
}

function vendingMachine (snackSelection, moneyInserted) {
  console.log('Return value of validateSelection(snackSelection):', validateSelection(snackSelection));

  if (validateSelection(snackSelection) === false) {
    return 'Please select a valid snack.';
  }

  const price = getPrice(snackSelection);

  if (price > moneyInserted) {
    return `Please insert more to purchase ${snackSelection}.`;
  }

  console.log("In vendingMachine(), 'moneyInserted' contains:", moneyInserted);
  console.log("In vendingMachine(), 'price' contains:", price);

  const change = moneyInserted - price;

  return `${snackSelection} dispensed. Your change is ${change}. Thank you!`;
}

vendingMachine('Ice cream', 100);
// LOG: Return value of validateSelection(snackSelection): false
// => "Please select a valid snack."
```

Woohoo! Trace debugging for the win!

After you've successfully located the bug, you can delete the `console.log()`s, comment them out, or simply leave them in place. They provide additional insight and information about your code as it runs, and they shouldn't affect the functioning of your code.

## `console` in the console
So what is this `console.log()` thing, anyway? Well, it turns out that **`console` is not actually part of the JavaScript specification**. Instead, it's what's called a _Web API_ — a piece of extra functionality provided by the browser. For our purposes, `console` is an object that we can reference just like any other JavaScript object. Go ahead and type `console` into the browser's JavaScript console:
```js
console;
// => console {debug: ƒ, error: ƒ, info: ƒ, log: ƒ, warn: ƒ, …}
```

The `console` object has a number of functions stored in it that we can invoke. We'll learn a ton more about this later (so don't worry too much about the distinction now), but when a function is stored in an object, we call it a _method_. So `console.log()` is invoking the `log()` method on the `console` object, and `Number.parseInt()` is invoking the `parseInt()` method on the `Number` object. It's fine if you accidentally call `console.log()` a function or `alert()` a method — it's a nuanced distinction. Just know the difference!

Let's take a look at a few of the more common `console` methods.

### `console.log()`
The `console` object's `log()` method is for logging out general information to the console. It can take any number of arguments. If more than one argument is provided, the arguments will be printed out on the same line with a space in between:
```js
console.log('Hello,', 'world!');
// LOG: Hello, world!
```

When we use `console.log()` in code snippets, we'll preface the output statements with `LOG:`, such as in the above example. This is to differentiate messages logged out to the console from values `return`ed by an expression, which are represented with `=>`, e.g.:
```js
function logReturner () {
  console.log(false);

  return true;
}

logReturner();
// LOG: false
// => true
```

At this stage of your programming career, `console.log()` is really the only tool you need for debugging with the `console` object. However, you'll probably encounter the following two `console` methods, `error()` and `warn()`, in the wild.

### `console.error()`
The `console` object's `error()` method is for printing out an error to the console, and it can also take multiple arguments. Most browsers will style the error message differently from a regular message output with `log()`:

![`console.error()`](https://curriculum-content.s3.amazonaws.com/web-development/js/basics/intro-to-debugging-readme/console_error_log.png)

You might ask why we'd ever need to use this — isn't the goal of writing good code to **avoid** errors? Well, sure, but sometimes errors are out of our control: the network could go down, data could change, or a user could enter something invalid. In these cases, it's helpful to use the specialized `console.error()` method. That way, you're letting future engineers (including yourself) know that this particular message is more important than the average logged message.

When we use `console.error()` in code snippets, we'll preface the output statements with `ERROR:` to differentiate them from other logged messages:
```js
console.error('Uh oh, you done goofed.');
// ERROR: Uh oh, you done goofed.
```

You probably won't use `console.error()` very often — `console.log()` is by far the best tool for simple debugging — but now at least you'll know what's happening when you open up the JS console on a random site and see a bunch of bright red warnings.

### `console.warn()`
Ditto for the `console` object's `warn()` method, which provides an intermediate step between a regular `log()` message and a more dire `error()` message and is the source of those bright yellow messages you'll occasionally see in the browser's JS console:

![`console.warn()`](https://curriculum-content.s3.amazonaws.com/web-development/js/basics/intro-to-debugging-readme/console_error_log_warn.png)

The `warn()` method is often used to alert developers looking at the JS console that they've done something inadvisable but not reaching the level of a full-blown error. For example, we could highlight the usage of a soon-to-be-deprecated feature:
```js
console.log("Hmmm, you probably don't want to do that...");
// WARN: Hmmm, you probably don't want to do that...
```

When we use `console.warn()` in code snippets, we'll preface the output statements with `WARN:` to differentiate them from other logged messages.

## Conclusion
Over the course of your programming career, you'll probably spend **significantly** more time debugging than actually writing new code. Just as your coding skills will improve with practice, so too will your debugging skills.

Debugging can sometimes feel very demoralizing. You'll fix one bug and ten new ones appear:

<picture>
  <source srcset="https://curriculum-content.s3.amazonaws.com/web-development/js/basics/intro-to-debugging-readme/bugs.webp" type="image/webp">
  <source srcset="https://curriculum-content.s3.amazonaws.com/web-development/js/basics/intro-to-debugging-readme/bugs.gif" type="image/gif">
  <img src="https://curriculum-content.s3.amazonaws.com/web-development/js/basics/intro-to-debugging-readme/bugs.gif" alt="WHERE'S MY MAC N' CHEESE?">
</picture>

If it's any `console`-ation, we **all** make mistakes. Treat debugging as a learning opportunity. Often, looking at your code critically and trying to figure out why something isn't working will afford you a much deeper understanding of how some feature of the language actually works. We'll continue to use the `console` object and other tools throughout this course. By the end, you'll be on your way to being a debugging master!

## Resources
- [MDN — Console][console]
- [Wikipedia — Tracing (software)][tracing]
- [Nick Parlante (Stanford CS) — Debugging Zen][debugging zen]

[console]: https://developer.mozilla.org/en-US/docs/Web/API/console
[tracing]: https://en.wikipedia.org/wiki/Tracing_(software)
[debugging zen]: https://curriculum-content.s3.amazonaws.com/web-development/js/basics/intro-to-debugging-readme/nick_parlante_debugging_zen_1996.pdf

<p class='util--hide'>View <a href='https://learn.co/lessons/js-basics-intro-to-debugging-readme'>Intro to Debugging</a> on Learn.co and start learning to code for free.</p>
