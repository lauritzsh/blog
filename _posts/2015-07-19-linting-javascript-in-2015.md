---
layout: post
title: Linting JavaScript in 2015
categories:
  - tutorial
tags:
  - vim
  - javascript
  - eslint
---

Better and more consistent JavaScript coding using a linter.

<!--more-->

When writing in whatever programming language, you will want to write something
that looks consistent. It doesn't really matter how you write your code but it's
important that you keep it consistent.

That may be easy when you're the only developer. It can be hard for a bigger
team though to write in the same style. Some may prefer four instead of two
spaces or even tabs over spaces (sigh)!

Linting helps you (and your team) setting up clear style guides for how the code
should be written. It's actually even more than that since it can help you
detect (mundane) errors while you code.


## Short lint history for JavaScript
It all started with [JSLint][jslint]. It was pretty easy configure, because
there is no configuration! The rules are defined by [Douglas
Crockford][crockford] from his [JavaScript: The Good Parts][goodparts]. You
install it and scan your files.

Next came [JSHint][jshint] which can be considered the successor to JSLint.
JSHint can be configured with a dotfile called `.jshintrc` unlike JSLint. Here
you can define how many spaces, tabs, globals etc. It has some different
[options][options] you can set yourself. It's still somewhat limited since you
can't write plugins for it.

What these two have in common is they both check for style and syntax. If you
are accessing an undefined variable they will tell you.

[JSCS][jscs] is only concerned about your style though. It will not check for
syntax errors and warn you about them. If you are only caring about your code
style this might be for you since it comes with a lot of [presets][presets].

![ESLint's logo](/images/eslint.png)

### Then came ESLint
What I recommend is [ESLint][eslint]. It offers more than JSHint and JSCS and
even performs faster.[^1] What's really great about ESLint and why I recommend
it is because you can use your own parser or extend it with plugins.

What does it mean to use your own parser? ESLint does most of ES6[^2] but not
everything. That's why [Babel][babel] wrote their own [parser][parser] to lint
for all valid Babel code This mean you can instead make use of
`babel-eslint`[^3] to lint ES6 code.

Using ESLint we can even lint JSX files which isn't supported out-of-the-box.
All it requires is to install the [ESLint-plugin-React][plugin] module and tell
ESLint to use it.


## Better code with ESLint
**Note:** We get a lot of rules enabled by default now but this is soon to
[change][v1] with `1.0.0` coming up. What we can do is pass `--reset` to disable
everything.  It might also help make it more clear to see what is enabled and in
use too.

So let's lint some files. Be sure to have [Node][node] and [npm][npm] (should
come with Node) installed.

We will need a new directory for our project, so let's create one: `mkdir test
&& cd $_`.

Rules can be defined as warnings or errors. The difference is the exit code.
Warnings will exit with `0` but errors will exit with `1`. This can be really
useful in combination with Git pre-commit or your build tools, as you can use
this to stop doing 'dirty' commits.

So let's define some rules and their severity:

* Error: We will indent with two spaces
* Error: No unused variables
* Warning: No alert

Keeping it simple for now.

Create `index.js` with your favorite text editor and write some ugly code:

```javascript
var unusued = 'I have no purpose!';

function greet() {
    var message = 'Hello, World!';
    alert(message);
}

greet();
```

Two wrongly indented lines, an unused variable and a message alert!
ESLint is not going to like that.

So now we need our `package.json` to install ESLint. Type `npm init` and just
keep hitting Enter. Let's install it as a dev dependency: `npm install
--save-dev eslint` or `npm i -D eslint`.

ESLint will look for a file called `.eslintrc`. That is where our rules will go
in, but we also have to define the environment in which our code will run. We
can assume this code will only run in the browser (because of `alert` since it
is only available in the browser) so we will set the env to browser.

Create `.eslintrc` and write:

```json
{
  "rules": {
    "indent": [2, 2],
    "no-unused-vars": [2, {"vars": "local", "args": "after-used"}],
    "no-alert": 1
  },
  "env": {
    "browser": true
  }
}
```

Some rules take an array. The first value is the severity of breaking the rule.
0 means it is disabled, 1 means a warning, and 2 is an error. In `"indent"` the
second 2 is the number of spaces we will allow.

The `"vars": "local"` has to do whether we only disallow unused local variables
or globals too. `"args": "after-used"` means only the last parameter in a
function is required to be used.

We set `"no-alert"` to 1 only since it has no options and we just want a
warning.

The browser env tells ESLint about `document` and `window`.

We can finally lint our file! Let's add it as a script to our `package.json` so
we can easily lint in the future.

```json
{
  ...
  "scripts": {
    "lint": "eslint . --reset; exit 0"
  },
  ...
}
```

**Note:** we use `; exit 0` because we get errors and npm thinks something bad
is going on. Try remove it and see. You might not use this if you want to use
the exit code of ESLint for anything. Consider writing a separate script for
that.  The `eslint .` means we will lint every file (by default files with the
`.js` extension) in this directory.

You can now lint easily using npm with `npm run lint`. You should now get a nice
list of the errors and the warning we specified:

```
> test@1.0.0 lint /Users/Lauritz/Downloads/test
> eslint . --reset; exit 0


index.js
  4:2  error    Expected indentation of 2 characters  indent
  5:2  error    Expected indentation of 2 characters  indent
  5:4  warning  Unexpected alert                      no-alert

✖ 3 problems (2 errors, 1 warning)
```

Let's fix this. Open up `index.js` and make ESLint happy:

```javascript
function greet() {
  var message = 'Hello, World!';
  console.log(message);
}

greet();
```

Type `npm run lint` again and you should see no messages! That's good, it means
ESLint isn't complaining anymore.

### Be lazy and use others' style
You may have heard of [Airbnb's style guide][airbnb] for JavaScript. It is a
pretty decent style guide for writing ES6 (and they also have for ES5).

If you (mostly) agree with their guide you can use it as a base. You might go
through the [rules][rules] or realize they already have written an
[`.eslintrc`][airlint] file but it is filled with comments.

You can actually install their guide as a module for ESLint. Their module is
dependent on another parser though, the `babel-eslint` I mentioned earlier so we
will need that as well.

```bash
npm i -D babel-eslint eslint-config-airbnb
```

Now rewrite your `.eslintrc` file with:

```json
{
  "extends": "eslint-config-airbnb"
}
```

That is it really. Now you will use their guide to lint and style check your own
projects. You can type `npm run lint` again but now you will see an error and
warning.

This is because we are using `var` that is discouraged in ES6 and should instead
be a `const`. By default we shouldn't really keep `alert` as it is mostly used
for debugging but it is only a warning.

Say you want to keep using `var` (for whatever reason) and `alert`, you can just
override their rules:

```json
{
  "extends": "eslint-config-airbnb",
  "rules": {
    "no-var": 0,
    "no-alert": 0
  }
}
```

Now ESLint should stop complaining.

## Automatically lint as you write
It was my plan to show how you can setup a text editor (Vim) to automatically
lint your files as you write them, but I think this guide is already longer than
I planned it to be.

Instead I will split it up into another post and hopefully be able to cover more
text editors (in the future).

## Ending notes
This tutorial showed you how linting can be done for JavaScript. This isn't new
or even exclusive to JavaScript though. Linting started with C with `lint` and a
linter is available for most languages.

I am sure you can find a configurable linter for your other languages, that will
as well help you write cleaner and more consistent code.

Setup a linter once and stop having arguments about code style with your team
members. Agree on a standard and use ESLint to abide them.

[jslint]:    http://www.jslint.com/help.html
[crockford]: http://www.crockford.com/
[goodparts]: http://www.amazon.com/exec/obidos/ASIN/0596517742/wrrrldwideweb
[jshint]:    http://jshint.com/about/
[options]:   http://jshint.com/docs/options/
[jscs]:      http://jscs.info/
[presets]:   https://github.com/jscs-dev/node-jscs/tree/master/presets
[eslint]:    http://eslint.org/
[babel]:     https://github.com/babel/babel
[parser]:    https://github.com/babel/babel-eslint
[plugin]:    https://github.com/yannickcr/eslint-plugin-react
[v1]:        http://eslint.org/blog/2015/07/eslint-1.0.0-rc-1-released/#reset-is-now-the-default
[node]:      https://nodejs.org/
[npm]:       https://www.npmjs.com/
[airbnb]:    https://github.com/airbnb/javascript
[rules]:     http://eslint.org/docs/rules/
[airlint]:   https://github.com/airbnb/javascript/blob/master/linters/.eslintrc

[^1]: [How does ESLint performance compare to JSHint and JSCS?](https://github.com/eslint/eslint/tree/4ea9a53044e598bb4f2c3e6b37625d735b4826af#how-does-eslint-performance-compare-to-jshint-and-jscs)
[^2]: Have we decided on ES6 or ES2015?
[^3]: Using `babel-eslint` should soon not be necessary to use though: [New Language Features](http://eslint.org/blog/2015/07/eslint-1.0.0-rc-1-released/#new-language-features)
