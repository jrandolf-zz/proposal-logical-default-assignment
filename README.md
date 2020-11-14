# ECMAScript Logical Default Assignment

A proposal for using the logical assignments `||=` and `??=` in default assignment in function arguments and destructuring.

## Status

Author: [jun-sheaf](github.com/jun-sheaf)

## Use Cases

### Functions
```js
/**
 * With proposal
 */
function example1(arg ??= "some default value") {}
function example2(arg ||= "some default value") {}
var example3 = (arg ??= "some default value") => {}
var example4 = (arg ??= "some default value") => {}

/**
 * Without proposal
 */
function example1(arg) {
  arg = arg ?? "some default value"
}
function example2(arg) {
  arg = arg || "some default value"
}
var example3 = (arg ??= "some default value") => {
  arg = arg ?? "some default value"
}
var example4 = (arg ??= "some default value") => {
  arg = arg || "some default value"
}
```

### Destructuring

#### Arrays
```js
/**
 * With proposal
 */
const [arg1 ??= 0] = [null];

const [arg2 ||= 0] = [""];

const [arg3 ||= 0, arg4 ??= arg3] = ["", null];

/**
 * Without proposal
 */
var [_arg1] = [];
const arg1 = _arg1 ?? 0;

var [_arg2] = [];
const arg2 = _arg2 || 0;

const [arg3, arg4] = [""];
const arg3 = _arg3 || 0;
const arg4 = _arg4 || arg3
```

#### Objects
```js
/**
 * With proposal
 */
const { test1 ??= 0 } = { test1: null };

const { test2 ||= "default" } = { test2: "" };

/**
 * Without proposal
 */
var { test1: _test1 } = { test1: null };
const test1 = _test1 ?? 0;

var { test2: _test2 } = { test2: "" };
const test2 = _test2 || "default";
```

## Motivation

Similar to the [logical assignment proposal](https://github.com/tc39/proposal-logical-assignment), this further minimizes the syntax for well-known patterns such as function options:
```js
function search({
    depth ||= 1,
    limit ||= 1,
   } ??= {}) {
}
search({ depth: 0 }) // depth and limit are locally scoped in the function as 1
```
Currently, there is no simple, straightforward method for writing synonymous functionality for complicated objects. In fact, the current babel implementation requires reusing the logic from the [destructuring transform](https://babeljs.io/docs/en/babel-plugin-transform-destructuring) in [babe](https://babeljs.io/) with a small abstraction in assignment patterns.

The following is a pathological example:
```js
// With proposal
const { data: { courses: oldCourses ??= [] } = {} } = getState();

// Without proposal
const _getState = getState(),
      _getState$data = _getState.data,
      _getState$data2 = _getState$data === void 0 ? {} : _getState$data,
      _getState$data2$cours = _getState$data2.courses,
      oldCourses = _getState$data2$cours ?? [];
```

## Semantics

We defer the reader to the semantics provided in the [logical assignment proposal](https://github.com/tc39/proposal-logical-assignment).

## References

A babel plugin has been made, but will not be published until this proposal reaches stage 1. For those interested, you can test the proposal on this repo where the plugin resides: https://github.com/jun-sheaf/babel
