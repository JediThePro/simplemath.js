# <code>SimpleMath.js</code>

SimpleMath.js is an extensive math library for JavaScript and Node.js. It features a flexible expression parser with support for symbolic computation, comes with a large set of built-in functions and constants, and offers an integrated solution to work with different data types like numbers, big numbers, complex numbers, fractions, units, and matrices. Powerful and easy to use.

## Features

- Supports numbers, big numbers, complex numbers, fractions, units, strings, arrays, and matrices.
- Is compatible with JavaScript's built-in Math library.
- Contains a flexible expression parser.
- Does symbolic computation.
- Comes with a large set of built-in functions and constants.
- Can be used as a command line application as well.
- Runs on any JavaScript engine.
- Is easily extensible.
- Open source.

## Usage

Math.js can be used in both node.js and in the browser.

Install simplemath.js using **npm**

```bash
$   npm install simplemath.js
```

> Note that when using simplemath.js in a TypeScript project, you will have to install type definition files too: `npm install @types/simplemath.js`.

```js
import {
  atan2, chain, derivative, e, evaluate, log, pi, pow, round, sqrt
} from 'simplemath.js'

// functions and constants
round(e, 3)                    // 2.718
atan2(3, -3) / pi              // 0.75
log(10000, 10)                 // 4
sqrt(-4)                       // 2i
pow([[-1, 2], [3, 1]], 2)      // [[7, 0], [0, 7]]
derivative('x^2 + x', 'x')     // 2 * x + 1

// expressions
evaluate('12 / (2.3 + 0.7)')   // 4
evaluate('12.7 cm to inch')    // 5 inch
evaluate('sin(45 deg) ^ 2')    // 0.5
evaluate('9 / 3 + 2i')         // 3 + 2i
evaluate('det([-1, 2; 3, 1])') // -7

// chaining
chain(3)
    .add(4)
    .multiply(2)
    .done()  // 14
```

### Code

The code of `simplemath.js` is written in ES modules, and requires all files to have a real, relative path, meaning the files must have a `*.js` extension. Please configure adding file extensions on auto import in your IDE.

### Architecture

What mathjs tries to achieve is to offer an environment where you can do calculations with mixed data types, 
like multiplying a regular `number` with a `Complex` number or a `BigNumber`, and work with all of those in matrices. 
Mathjs also allows to add a new data type, like say `BigInt`, with little effort.

The solution that mathjs uses has two main ingredients:

- **Typed functions**. All functions are created using `typed function`. This makes it easier to (dynamically) create and extend a single function with new data types, automatically do type conversions on function inputs, etc. So, if you create function multiply for two `number`s, you can extend it with support for multiplying two `BigInts`. If you define a conversion from `BigInt` to `number`, the typed-function will automatically allow you to multiply a `BigInt` with a `number`.

- **Dependency injection**. When we have a function `multiply` with support for `BigInt`, thanks to the dependency injection, other functions using `multiply` under the hood, like `prod`, will automatically support `BigInt` too. This also works the other way around: if you don't need the heavyweight `multiply` (which supports BigNumbers, matrices, etc), and you just need a plain and simple number support, you can use a lightweight implementation of `multiply` just for numbers, and inject that in `prod` and other functions.

At the lowest level, mathjs has immutable factory functions which create immutable functions. The core function `math.create(...)` creates a new instance having functions created from all passed factory functions. A mathjs instance is a collection of created functions. It contains a function like `math.import` to allow extending the instance with new functions, which can then be used in the expression parser.

### Build scripts

The build script currently generates two types of output: 

- **any**, generate entry points to create full versions of all functions
- **number**: generating and entry points to create lightweight functions just supporting `number`

For each function, an object is generated containing the factory functions of all dependencies of the function. This allows to just load a specific set of functions, and not load or bundle any other functionality. So for example, to just create function `add` you can do `math.create(addDependencies)`.
