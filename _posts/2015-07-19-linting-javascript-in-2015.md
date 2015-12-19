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
It all started with [JSLint][jslint]. It is pretty easy to configure, because
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

What does it mean to use your own parser? ESLint does all of ES6[^2] but if
you're using [Babel][babel] to transpile you might use some experimental
features. That's why Babel wrote their own [parser][parser] to lint for all
valid Babel code. This mean you can instead make use of `babel-eslint` to lint
your ES6/ES7 code.

We can even extend ESLint to better support JSX than what it currently does
out-of-the-box. All it requires is to install the [ESLint-plugin-React][plugin]
module and tell ESLint to use it. Now we have plenty of new specific JSX rules
we can use.

This is what I find really great about ESLint.


## Better code with ESLint
Should you have trouble following along then I have created a very simple
example [repository](https://github.com/lauritzsh/eslint-example) with the
different code examples from this post.

By default ESLint will have all rules disabled. This is different compared to
earlier versions where some rules would be enabled by default. We'll keep them
all disabled now and later show how to enable the most sane defaults easily.

So let's lint some files. Be sure to have [Node][node] and [npm][npm] (should
come with Node) installed.

We will need a new directory for our project, so let's create one: `mkdir test
&& cd $_`.

Rules can be defined as warnings or errors. The difference is the exit code.
Warnings will exit with `0` but errors will exit with `1`. This can be really
useful in combination with Git pre-commit or your build tools, as you can use
this to stop doing 'dirty' commits for example.

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
    "no-unused-vars": [2, {"vars": "all", "args": "after-used"}],
    "no-alert": 1
  },
  "env": {
    "browser": true
  }
}
```

Some rules take an array. The first value is the severity of breaking the rule.
0 means it is disabled, 1 is a warning, and 2 is an error. The second is
(optional) options for that rule.

In `"indent"` the second 2 is the number of spaces we will allow.

The `"vars": "all"` has to do whether we only disallow unused local variables or
globals too. Local here being in new scopes (such as functions for example).
`"args": "after-used"` means only the last parameter in a function is required
to be used.

We set `"no-alert"` to 1 only since it has no options and we just want a
warning.

The browser env tells ESLint about `document` and `window`.

We can finally lint our file! Let's add it as a script to our `package.json` so
we can easily lint in the future.

```json
{
  ...
  "scripts": {
    "lint": "eslint .; exit 0"
  },
  ...
}
```

You can now lint easily using npm with `npm run lint`. You should get a nice
list of the errors and the warning we specified:

```
> eslint-example@1.0.0 lint /Users/Lauritz/Developer/eslint-example
> eslint .; exit 0


index.js
  1:5  error    unusued is defined but never used                 no-unused-vars
  4:5  error    Expected indentation of 2 characters but found 4  indent
  5:5  error    Expected indentation of 2 characters but found 4  indent
  5:5  warning  Unexpected alert                                  no-alert

✖ 4 problems (3 errors, 1 warning)
```

**Note:** we use `; exit 0` because we get errors and npm thinks something bad
is going on. Try remove it and re-run. You might not use this if you want to use
the exit code of ESLint for anything. Consider writing a separate script for
that. The `eslint .` means we will lint every file (by default files with the
`.js` extension) in this directory.

Let's fix this. Open up `index.js` and make ESLint happy:

```javascript
function greet() {
  var message = 'Hello, World!';
  alert(message);
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
will need that as well (it is unfortunately also dependent on
`eslint-plugin-react` even if you don't care about React).

```bash
npm i -D babel-eslint eslint-config-airbnb eslint-plugin-react
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

### Sane defaults
Want to start using ESLint but with some sane defaults? You can enable the
recommended rules with:

```json
{
  "extends": "eslint:recommended"
}
```

It will turn all rules marked "(recommended)" from the [rules][rules] on.

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

Be sure to check out the
[repository](https://github.com/lauritzsh/eslint-example) for the different code
examples from this post.

The post can be discussed at [reddit][reddit].

**Updated 2015/08/08:** post has been updated to latest version of ESLint (v1.1
at the time of writing).

[reddit]:    https://www.reddit.com/r/javascript/comments/3f2x9l/how_to_properly_lint_javascript_in_2015/
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
[node]:      https://nodejs.org/
[npm]:       https://www.npmjs.com/
[airbnb]:    https://github.com/airbnb/javascript
[rules]:     http://eslint.org/docs/rules/
[airlint]:   https://github.com/airbnb/javascript/blob/master/packages/eslint-config-airbnb/.eslintrc

[^1]: [How does ESLint performance compare to JSHint and JSCS?](https://github.com/eslint/eslint/tree/4ea9a53044e598bb4f2c3e6b37625d735b4826af#how-does-eslint-performance-compare-to-jshint-and-jscs)
[^2]: Have we decided on ES6 or ES2015?
