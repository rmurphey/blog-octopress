---
layout: post
title: Two Things about Conditionals in JavaScript
date: 2012-12-10 21:40
comments: true
---

Just a quick post, inspired by [Laura Kalbag's post](http://laurakalbag.com/display-none/), where she reminded me:

> We shouldn’t be fearful of writing about what we know. Even if you write from the most basic point of view, about something which has been ‘around for ages’, you’ll likely be saying something new to someone.

## One: There is no `else if`

When you write something like this ...

```javascript
function saySomething( msg ) {
  if ( msg === 'Hello' ) {
    console.log('Hello there');
  } else if ( msg === 'Yo' ) {
    console.log('Yo dawg');
  }
}
```

... then what you're actually writing is this ...

```javascript
function saySomething( msg ) {
  if ( msg === 'Hello' ) {
    console.log('Hello there');
  } else {
    if ( msg === 'Yo' ) {
      console.log('Yo dawg');
    }
  }
}
```

That's because there is no `else if` in JavaScript. You know how you can write an `if` statement without any curly braces?

```javascript
if ( foo ) bar() // please don't do this if you want your code to be legible
```

You're doing the same thing with the `else` part of the initial `if` statement when you write `else if`: you're skipping the curly braces for the second `if` block, the one you're providing to `else`. There's nothing *wrong* with `else if` per se, but it's worth knowing about what's actually happening.

## Two: `return` Means Never Having to Say `else`

Consider some code like this:

```javascript
function howBig( num ) {
  if ( num < 10 ) {
    return 'small';
  } else if ( num >- 10 && num < 100 ) {
    return 'medium';
  } else if ( num >= 100 ) {
    return 'big';
  }
}
```

Of course, we might be inclined to write this using the `else if` construct that we just discussed above; however, there's an even better way. If the number we pass to `howBig` is less than 10, then our function will return `'small'`. As soon as it returns, none of the rest of the function will run -- this means we can skip the `else` part entirely, which means our code could look like this:

```javascript
function howBig( num ) {
  if ( num < 10 ) {
    return 'small';
  }

  if ( num >= 10 && num < 100 ) {
    return 'medium';
  }

  if ( num >= 100 ) {
    return 'big';
  }
}
```

But wait -- if the first `if` statement isn't true, and the second `if` statement isn't true, then we will *always* return `'big'`. That means the third `if` statement isn't even required:

```javascript
function howBig( num ) {
  if ( num < 10 ) {
    return 'small';
  }

  if ( num >= 10 && num < 100 ) {
    return 'medium';
  }

  return 'big';
}
```