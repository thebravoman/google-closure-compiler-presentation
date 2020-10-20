# Advanced compilation with Google Closure Compiler

 or How do we maintain our mental stability when developing in JavaScript.

Kiril Mitov (kmitov [at] axlessoft [dot] com), CTO Axlessoft, October 2020 

Get in touch or follow me:

[twitter/thebravoman](https://twitter.com/thebravoman)

[linkedin/kirilmitov](https://www.linkedin.com/in/kirilmitov/)

# Quote to begin with 

> "If the human body was making a new organ every week, doctors would be googling this shit."

(On the state of the JavaScript community; author Unknown; date: beginning of 21 century)

# Organization Context

We used Google Closure Compiler in the development of the Instructions Steps (IS) framework. IS helped us build buildin3d.com and visualize 3D assembly instructions to end clients. IS has an event-driven plug-in architecture. It consists of 68 plugins. Every plugin is a separate repo with separate release cycle, build and version. Every plugin is written in JavaScript. In total we've released more than 3000 versions of the plugins. The framework is written vanilla JS. It is not even dependent on DOM not to mention jQuery or others. Some extensions depend on Babylon.js, Three.js, Mustache.js and others because of the services they provide.

The tools that we used every day:

1. Google Closure Compiler for compilation - no code reaches production if it is not compiled in an ADVANCED_OPTIMIZATION mode.
2. Sprockets for packging (deprecated currently, but is working very nice.)
3. Teaspoon for running specs (the project is looking for maintainers, probably we would take it)
4. Jasmine for developing specs (a healthy specs framework)

# Personal context

In about a year between 2019-2020 I went from occasionally working with JavaScript when I had to, to one of the very best JavaScript programmers I know of. I hated most steps of the way. At least most steps until I decided to start version 6 of the framework where I completely rewrote it all dropping a lot and basing the framework on vanilla JS with ES6 classes and no depending even on the DOM. In retrospective GCC was the tool that helped me the most.

# Table of contents

<!-- MarkdownTOC autolink="true" autoanchor="true" -->

- [Demonstration on how GCC works and how we protect our Intellectual property](#demonstration-on-how-gcc-works-and-how-we-protect-our-intellectual-property)
    - [Example 1 - 'output'](#example-1---output)
        - [SIMPLE_OPTIMIZATION](#simple_optimization)
            - [SIMPLE_OPTIMIZATION compiled](#simple_optimization-compiled)
            - [SIMPLE_OPTIMIZATION compiled & formatted](#simple_optimization-compiled--formatted)
        - [ADVANCED_OPTIMIZATION](#advanced_optimization)
            - [ADVANCED_OPTIMIZATION compiled](#advanced_optimization-compiled)
        - [Summary](#summary)
    - [Example 2 - 'classes'](#example-2---classes)
        - [ADVANCED_OPTIMIZATION compiled](#advanced_optimization-compiled-1)
        - [Improve example 2](#improve-example-2)
        - [ADVANCED_OPTIMIZATION compiled again](#advanced_optimization-compiled-again)
        - [ADVANCED_OPTIMIZATION compiled again & beautified](#advanced_optimization-compiled-again--beautified)
        - [Summary](#summary-1)
    - [Example 3 - inheritance](#example-3---inheritance)
        - [ADVANCED_OPTIMIZATION compiled](#advanced_optimization-compiled-2)
        - [ADVANCED_OPTIMIZATION compiled & beautified](#advanced_optimization-compiled--beautified)
        - [Summary](#summary-2)
- [How did the errors disappear](#how-did-the-errors-disappear)
    - [Types](#types)
    - [Missing methods](#missing-methods)
    - [Invalid syntax](#invalid-syntax)
    - [A lot of other checks even before reaching the browser.](#a-lot-of-other-checks-even-before-reaching-the-browser)
- [How the size of the output is reduced by GCC](#how-the-size-of-the-output-is-reduced-by-gcc)
- [Real world compiled code](#real-world-compiled-code)
    - [Example 1 from IS](#example-1-from-is)
    - [Example 2 from IS](#example-2-from-is)
    - [Example 3 from IS](#example-3-from-is)
    - [Conclusion](#conclusion)
- [How we build the Instructions Steps \(IS\) Framework](#how-we-build-the-instructions-steps-is-framework)
    - [Building an SDK](#building-an-sdk)
        - [@export the classes](#export-the-classes)
        - [@export the methods](#export-the-methods)
    - [Drawbacks of using @export](#drawbacks-of-using-export)

<!-- /MarkdownTOC -->

<a id="demonstration-on-how-gcc-works-and-how-we-protect-our-intellectual-property"></a>
# Demonstration on how GCC works and how we protect our Intellectual property

Google closure compiler is a compiler. It takes a source and compiles an output. Using the wikipedia definition 

> 'In computing, a compiler is a computer program that translates computer code written in one programming language (the source language) into another language (the target language). ' 

You can take a program in one language and produce a different language. With Google Closure Compiler we compile JavaScript to JavaScript. The compiler has three modes of compilation - WHITESPACE, SIMPLE, ADVANCED

To successfully run GCC you must first download it. 
It is available at: https://mvnrepository.com/artifact/com.google.javascript/closure-compiler

In this presentation I am using closure-compiler-v20200830. 

You must have java to run the GCC compiler, but there is also a JavaScript implementation so you can use GCC without Java.

<a id="example-1---output"></a>
## Example 1 - 'output'

The code writes 7 to the browser console

```javascript
// example1.js
function output(n) {
  console.log(`called with ${n}`)
}
output(7);
```

<a id="simple_optimization"></a>
### SIMPLE_OPTIMIZATION

```bash
#
$ java -jar closure-compiler-v20200830.jar example1.js 
```

<a id="simple_optimization-compiled"></a>
#### SIMPLE_OPTIMIZATION compiled

The output of the compilation is:

```javascript
var $jscomp=$jscomp||{};$jscomp.scope={};$jscomp.createTemplateTagFirstArg=function(a){return a.raw=a};$jscomp.createTemplateTagFirstArgWithRaw=function(a,b){a.raw=b;return a};function output(a){console.log("called with "+a)}output(7);
```

By default GCC uses SIMPLE_OPTIMIZATION

<a id="simple_optimization-compiled--formatted"></a>
#### SIMPLE_OPTIMIZATION compiled & formatted

```javascript
var $jscomp = $jscomp || {};
$jscomp.scope = {};
$jscomp.createTemplateTagFirstArg = function(a) {
    return a.raw = a
};
$jscomp.createTemplateTagFirstArgWithRaw = function(a, b) {
    a.raw = b;
    return a
};

function output(a) {
    console.log("called with " + a)
}
output(7);
```

1. The result from the compiler is minified. There are no comments and whitespaces.
2. The variable 'n' was renamed to 'a'
3. The code could be executed on older browsers that do not support ``
4. There is a little extra code in the beginning, but it is not that interesting
5. The compiler is started with java. The version of the compiler is 20200830. https://mvnrepository.com/artifact/com.google.javascript/closure-compiler is the repository with first version in 'Dec, 2010'. Currently a new version is released every 3 weeks.

<a id="advanced_optimization"></a>
### ADVANCED_OPTIMIZATION

To start the advanced compilation use '-O ADVANCED'

```bash
$ java -jar closure-compiler-v20200830.jar -O ADVANCED example1.js
```

<a id="advanced_optimization-compiled"></a>
#### ADVANCED_OPTIMIZATION compiled

The result contains just that:

```javascript
console.log("called with 7")
```

<a id="summary"></a>
### Summary

1. The code is not simply minified. It is completely changed, but the behavior is the same.
2. The 'output' function is missing. We might need it as developers, but the browser does not need it at runtime.
3. There is no definition, no param, no call. There is directly the 'job done'. So something that is 'developer friendly' is compiled to something that is 'browser and machine friendly'. 
4. How do we protect the Intellectual property - the compiled code does not contain our code. It contains our behaviour, but not the code.
5. Technically speaking the call is a little faster - there is no function and no operation to sum the string with the number. So we are a few thousands of CPU cycles faster (at least). 
6. Technically the program takes less memory - there is one function less.
7. Despite all that, the behavior is preserved.

<a id="example-2---classes"></a>
## Example 2 - 'classes'

We wanted to use vanilla JS and classes for Instructions Steps Framework, because of the problems we wanted to track down with the IS framework. So here is an example.

```javascript
// example2.js
/**
 * Process all the elements with element name and add the css class to this element.
 * Used as an example in a Google Closure Compiler presentation
 *
 * @example
 * Given an page that contains
 * <div></div>
 *
 * When the processor is constructed and executed as
 *
 * const processor = new Processor("div", "my-class")
 * processor.process()
 *
 * <div class="my-class"></div>
 *
 * @author Kiril Mitov
 */
class Processor {
  constructor(elementName, cssClassToAdd) {
    this._elementName = elementName;
    this._cssClassToAdd = cssClassToAdd;
  }

  process() {
    const elements= document.querySelectorAll(this._elementName)
    elements.forEach((element)=> {
      element.classList.add(this._cssClassToAdd)
    })
  }
}
```

```bash
$ java -jar closure-compiler-v20200830.jar -O ADVANCED example2.js 
```

<a id="advanced_optimization-compiled-1"></a>
### ADVANCED_OPTIMIZATION compiled

The file is empty:

```javascript

```

The resulting JavaScript file is empty, because we've never called the Processor and its functions. So the browser will never execute this code, and the compiler also does not execute it and produces an empty file.

This means that any dead code that is not reached is removed from production, which could have great benefits.

<a id="improve-example-2"></a>
### Improve example 2

We add

```javascript
// add this fragment at the bottom at example2.js
const processor = new Processor("div", "my-class")
processor.process()
```

and execute 

```bash
$ java -jar closure-compiler-v20200830.jar -O ADVANCED example2.js 
```

<a id="advanced_optimization-compiled-again"></a>
### ADVANCED_OPTIMIZATION compiled again

The result is:

```javascript
document.querySelectorAll("div").forEach(function(a){a.classList.add("my-class")});
```

<a id="advanced_optimization-compiled-again--beautified"></a>
### ADVANCED_OPTIMIZATION compiled again & beautified

When the code is beautified it is:

```javascript
document.querySelectorAll("div").forEach(function(a) {
    a.classList.add("my-class")
});
```

<a id="summary-1"></a>
### Summary

```javascript
// original
class Processor {
  constructor(elementName, cssClassToAdd) {
    this._elementName = elementName;
    this._cssClassToAdd = cssClassToAdd;
  }

  process() {
    const elements= document.querySelectorAll(this._elementName)
    elements.forEach((element)=> {
      element.classList.add(this._cssClassToAdd)
    })
  }
}
const processor = new Processor("div", "my-class")
processor.process()

// compiled & beautified
document.querySelectorAll("div").forEach(function(a) {
    a.classList.add("my-class")
});
```

What are the changes introduced by the compiler

1. Classes are not used
2. Variable 'element' is now called 'a'. That is a 7 times reduction in the size required for the name of this one variable.
3. The compiler knows about the DOM and the DOM APIs and it knows that 'a' is an 'Element' and it has a '.classList' method.

<a id="example-3---inheritance"></a>
## Example 3 - inheritance

We needed a structure of classes in IS, so another example with classes.

```javascript
/**
 * Two processors with inheritance along with a function that is calling them.
 *
 */
/**
 * Base class for all processors
 * @author Kiril Mitov
 */
class Processor {
  constructor(elementName, cssClassToAdd) {
    this._elementName = elementName;
    this._cssClassToAdd = cssClassToAdd;
  }

}

/**
 * Adds a css class to DOM elements
 *
 * @example
 * Given an page that contains
 * <div></div>
 *
 * When the processor is constructed and executed as
 *
 * const processor = new AddProcessor("div", "my-class")
 * processor.process()
 *
 * <div class="my-class"></div>
 *
 */
class AddProcessor extends Processor {
  constructor(elementName, cssClassToAdd) {
    super(elementName, cssClassToAdd)
  }

  process() {
    const elements= document.querySelectorAll(this._elementName)
    elements.forEach((element)=> {
      element.classList.add(this._cssClassToAdd)
    })
  }
}

/**
 * Removes a css class from DOM elements
 *
 * @example
 * Given an page that contains
 * <div class='my-class'></div>
 *
 * When the processor is constructed and executed as
 *
 * const processor = new RemoveProcessor("div", "my-class")
 * processor.process()
 *
 * <div></div>
 */
class RemoveProcessor extends Processor {

  constructor(elementName, cssClassToAdd) {
    super(elementName, cssClassToAdd)
  }

  process() {
    const elements= document.querySelectorAll(this._elementName)
    elements.forEach((element)=> {
      element.classList.remove(this._cssClassToAdd)
    })
  }
}

let processor = null;
if(document.getElementById("addProcessor")) {
  processor = new AddProcessor("div", "my-class")
} else {
  processor = new RemoveProcessor("div", "my-class")
}
processor.process()

```

```bash
$ java -jar closure-compiler-v20200830.jar -O ADVANCED example3.js
```

<a id="advanced_optimization-compiled-2"></a>
### ADVANCED_OPTIMIZATION compiled

```javascript
var d="function"==typeof Object.create?Object.create:function(a){function b(){}b.prototype=a;return new b},e;if("function"==typeof Object.setPrototypeOf)e=Object.setPrototypeOf;else{var f;a:{var g={f:!0},h={};try{h.__proto__=g;f=h.f;break a}catch(a){}f=!1}e=f?function(a,b){a.__proto__=b;if(a.__proto__!==b)throw new TypeError(a+" is not extensible");return a}:null}var k=e;
function l(a,b){a.prototype=d(b.prototype);a.prototype.constructor=a;if(k)k(a,b);else for(var c in b)if("prototype"!=c)if(Object.defineProperties){var p=Object.getOwnPropertyDescriptor(b,c);p&&Object.defineProperty(a,c,p)}else a[c]=b[c];a.g=b.prototype}function m(a,b){this.c=a;this.b=b}function n(a,b){m.call(this,a,b)}l(n,m);n.prototype.a=function(){var a=this;document.querySelectorAll(this.c).forEach(function(b){b.classList.add(a.b)})};function q(a,b){m.call(this,a,b)}l(q,m);
q.prototype.a=function(){var a=this;document.querySelectorAll(this.c).forEach(function(b){b.classList.remove(a.b)})};var r=null;document.getElementById("addProcessor")?r=new n("div","my-class"):r=new q("div","my-class");r.a();
```

<a id="advanced_optimization-compiled--beautified"></a>
### ADVANCED_OPTIMIZATION compiled & beautified

```javascript
var d = "function" == typeof Object.create ? Object.create : function(a) {
        function b() {}
        b.prototype = a;
        return new b
    },
    e;
if ("function" == typeof Object.setPrototypeOf) e = Object.setPrototypeOf;
else {
    var f;
    a: {
        var g = {
                f: !0
            },
            h = {};
        try {
            h.__proto__ = g;
            f = h.f;
            break a
        } catch (a) {}
        f = !1
    }
    e = f ? function(a, b) {
        a.__proto__ = b;
        if (a.__proto__ !== b) throw new TypeError(a + " is not extensible");
        return a
    } : null
}
var k = e;

function l(a, b) {
    a.prototype = d(b.prototype);
    a.prototype.constructor = a;
    if (k) k(a, b);
    else
        for (var c in b)
            if ("prototype" != c)
                if (Object.defineProperties) {
                    var p = Object.getOwnPropertyDescriptor(b, c);
                    p && Object.defineProperty(a, c, p)
                } else a[c] = b[c];
    a.g = b.prototype
}

function m(a, b) {
    this.c = a;
    this.b = b
}

function n(a, b) {
    m.call(this, a, b)
}
l(n, m);
n.prototype.a = function() {
    var a = this;
    document.querySelectorAll(this.c).forEach(function(b) {
        b.classList.add(a.b)
    })
};

function q(a, b) {
    m.call(this, a, b)
}
l(q, m);
q.prototype.a = function() {
    var a = this;
    document.querySelectorAll(this.c).forEach(function(b) {
        b.classList.remove(a.b)
    })
};
var r = null;
document.getElementById("addProcessor") ? r = new n("div", "my-class") : r = new q("div", "my-class");
r.a();
```

There are a couple of fragments in the beginning about protytopes. JavaScript  is based on prototypes, but we are not going to discuss prototypes here.

The main part of the compiled code is:

```javascript
function m(a, b) {
    this.c = a;
    this.b = b
}

function n(a, b) {
    m.call(this, a, b)
}
l(n, m);
n.prototype.a = function() {
    var a = this;
    document.querySelectorAll(this.c).forEach(function(b) {
        b.classList.add(a.b)
    })
};

function q(a, b) {
    m.call(this, a, b)
}
l(q, m);
q.prototype.a = function() {
    var a = this;
    document.querySelectorAll(this.c).forEach(function(b) {
        b.classList.remove(a.b)
    })
};
var r = null;
document.getElementById("addProcessor") ? r = new n("div", "my-class") : r = new q("div", "my-class");
r.a();
```

<a id="summary-2"></a>
### Summary

1. 'Processor' is now called 'm', 'elementName' is called 'a' and 'cssClassName' is called 'b'.

```javascript
// original
class Processor {
  constructor(elementName, cssClassToAdd) {
    this._elementName = elementName;
    this._cssClassToAdd = cssClassToAdd;
  }
}
// compiled
function m(a, b) {
    this.c = a;
    this.b = b
}
```

2. AddProcessor

'AddProcessor' is now called 'n', '_elementName' is called 'c' and 'this.cssClassToAdd' is called 'a.b'.

```javascript
// original
class AddProcessor extends Processor {
  constructor(elementName, cssClassToAdd) {
    super(elementName, cssClassToAdd)
  }

  process() {
    const elements= document.querySelectorAll(this._elementName)
    elements.forEach((element)=> {
      element.classList.add(this._cssClassToAdd)
    })
  }
}
// compiled
function n(a, b) {
    m.call(this, a, b)
}
l(n, m);
n.prototype.a = function() {
    var a = this;
    document.querySelectorAll(this.c).forEach(function(b) {
        b.classList.add(a.b)
    })
};
```

3. RemoveProcessor

```javascript
// original 
class RemoveProcessor extends Processor {

  constructor(elementName, cssClassToAdd) {
    super(elementName, cssClassToAdd)
  }

  process() {
    const elements= document.querySelectorAll(this._elementName)
    elements.forEach((element)=> {
      element.classList.remove(this._cssClassToAdd)
    })
  }
}

// compiled
function q(a, b) {
    m.call(this, a, b)
}
l(q, m);
q.prototype.a = function() {
    var a = this;
    document.querySelectorAll(this.c).forEach(function(b) {
        b.classList.remove(a.b)
    })
};
```

4. If statement

- instead of _let_ the compiler uses _var_
- instead of _if/else_ it uses _' ? : '_
- instead of calling '_processor.process()_' it calls '_r.a()_'
 
```javascript
// original 
let processor = null;
if(document.getElementById("addProcessor")) {
  processor = new AddProcessor("div", "my-class")
} else {
  processor = new RemoveProcessor("div", "my-class")
}
processor.process()

// compiled
var r = null;
document.getElementById("addProcessor") ? r = new n("div", "my-class") : r = new q("div", "my-class");
r.a();
```

<a id="how-did-the-errors-disappear"></a>
# How did the errors disappear

Compared to JS code written by us that we are delivering in other platforms most of the errors in our IS framework disappeared with the use of GCC.

The compiler allow for setting types. In the comments, as an additional meta in the form of an annotation we could set the types of the params and the compiler would check for them. 

JavaScript is a dynamic language which is a great advantage, but could also be the source of a lot of challenges. With Google Closure Compiler we found the right balance. We develop in vanilla JS and set the types were we benefit from it.

<a id="types"></a>
## Types

```javascript
class RemoveProcessor extends Processor {

  // We add a type for the params
  /**
   * @param  {string} elementName
   * @param  {string} cssClassToAdd
   */
  constructor(elementName, cssClassToAdd) {
    super(elementName, cssClassToAdd)
  }

  process() {
    const elements= document.querySelectorAll(this._elementName)
    elements.forEach((element)=> {
      element.classList.remove(this._cssClassToAdd)
    })
  }
}

// we call the constructor method with the wrong type.
processor = new RemoveProcessor("div", 4)

```

If we try to compile there will be a warning by the compiler.

```bash
$ java -jar closure-compiler-v20200830.jar -O ADVANCED example4.js 
example4.js:81:41: WARNING - [JSC_TYPE_MISMATCH] actual parameter 2 of RemoveProcessor does not match formal parameter
found   : number
required: string
  81|   processor = new RemoveProcessor("div", 4)
                                               ^

0 error(s), 1 warning(s), 93.2% typed
```

There are a lot of types in GCC. 
https://github.com/google/closure-compiler/wiki/Types-in-the-Closure-Type-System

<a id="missing-methods"></a>
## Missing methods

What would happen if we try to call a method that does not exist?

```javascript
// we call .anotherMethod and .process()
// .anotherMethod does not exist and is not defined
processor.anotherMethod()
```

The compiler will warn us and this method is not defined. Is it really defined?

```bash
$ java -jar closure-compiler-v20200830.jar -O ADVANCED example5.js 
example5.js:83:10: WARNING - [JSC_INEXISTENT_PROPERTY] Property anotherMethod never defined on (AddProcessor|RemoveProcessor)
  83| processor.anotherMethod()
                ^^^^^^^^^^^^^
```

<a id="invalid-syntax"></a>
## Invalid syntax

It could happen. To submit a code with invalide syntax. Generally it our case it happens because we are handling a stack with Ruby, JavaScript, Python, C#. Sometimes in a day you could make three different commits in three different languages. 
Ruby does not care about '()'. JavaScript cares.
Ruby does not care about this like .count, .size, .length. They all work.  JavaScript cares. Only one of them works.

```javascript
// this code can not be compiled. It is not a valid JavaScript
  process() {
    const elements= document.querySelectorAll(this._elementName)
    elements.forEach((element)=> {
      element.classList.remove this._cssClassToAdd
    })
  }
```

Compiling gives an error as the syntax is not valid. 

```bash
$ java -jar closure-compiler-v20200830.jar -O ADVANCED example6.js 
example6.js:72:31: ERROR - [JSC_PARSE_ERROR] Parse error. Semi-colon expected
  72|       element.classList.remove this._cssClassToAdd
                                     ^

1 error(s), 0 warning(s)
```

<a id="a-lot-of-other-checks-even-before-reaching-the-browser"></a>
## A lot of other checks even before reaching the browser.

> More sweat in the training less blood on the ring

I like the dynamically typed environment of JavaScript. I look at it as a tool that could be used for good causes, but it could also lead to a lot of errors. Balance is what is important.

<a id="how-the-size-of-the-output-is-reduced-by-gcc"></a>
# How the size of the output is reduced by GCC

1. The size of the whole IS Framework  before it is compiled as it is packed in a single file.

```bash
$ du is-release_pack-b65ffd509871e122b72cbdfa93e96c47cea2ac0c64b83016bf3b0df14cd99a86.js -b
586230  is-release_pack-b65ffd509871e122b72cbdfa93e96c47cea2ac0c64b83016bf3b0df14cd99a86.js
```

**586230 bytes**

2. Number of lines 

It give you an example of large the framework is.

```bash
$ wc -l is-release_pack-b65ffd509871e122b72cbdfa93e96c47cea2ac0c64b83016bf3b0df14cd99a86.js
20646 is-release_pack-b65ffd509871e122b72cbdfa93e96c47cea2ac0c64b83016bf3b0df14cd99a86.js
```

**20646 lines of code.** 

3. Uglify JS

By using Uglify JS we are minimizing and uglifying the code. 

```bash
$ du is-release_pack-1ce088d0e0f412a627779c10d1248ac0481ea4f687f18e712261c8d29630b7cc.js
204 is-release_pack-1ce088d0e0f412a627779c10d1248ac0481ea4f687f18e712261c8d29630b7cc.js
```

**196964 bytes**

4. Google Closure Compiler ADVANCED_OPTIMIZATION

```bash
$ du -b is-release_pack-1.1.209.js
121857  is-release_pack-1.1.209.js
```

**121857 bytes** 

5. Results

| Original  | Uglify    | GCC
| --------- | --------- | ---------
| 586230    |  196964   |   121857
| 100%      |  33.59%   |   20,78% of original
|           |           |   61,86% of Uglify

We deliver JavaScript, compiled with GCC in ADVANCED_OPTIMIZATION mode

 - it is difficult to base a product on it as it is difficult to modify it.
 - it works
 - it works and many browsers with 0 efforts from our side
 - it has 40% smaller size than Uglify
 - GCC is supported by Google and will probably not disappear soon.
 - GCC is not complex, it is just a compiler. No configuration, no files, just a command line call.
 - there is a new GCC version every 3 weeks
 - the compiler is called 'gcc'. It is pleasing and fun to work with JavaScript and talk about 'gcc' and to remember the glorious times :)

<a id="real-world-compiled-code"></a>
# Real world compiled code

<a id="example-1-from-is"></a>
## Example 1 from IS

I have no idea what this code does and what is the original code.

```javascript
    function Sa(b) {
        var c = {
            stepsTreeLoaded: !0,
            Cb: !1
        };
        v(c);
        var d = [];
        b.a.forEach(function(e) {
            a: {
                for (f in c)
                    if (1 != c[f] || !Ta(b.c, e, f))
                        if (0 != c[f] || void 0 != Ta(b.c, e, f)) {
                            var f = !1;
                            break a
                        }
                f = !0
            }
            f && d.push(e)
        });
        return d
    }
```

<a id="example-2-from-is"></a>
## Example 2 from IS

I also have no idea what whis code does, but I love the call 'this.a.a' :)

```javascript
n.Rb = function(b) {
    b = b.a;
    var c = y(this.a),
        d = new x(this.c);
    c && d.next(za(this.c, c.s()) + 1);
    c = 0 < b ? 1 : -1;
    for (var e = Math.abs(b), f = this.a.a; d.W(c) && 0 < e;) d.next(c), Bb(this, y(d)) || (e--, f = d.a);
    d = f - this.a.a;
    c = y(this.a);
    b = 0 < b ? 1 : -1;
    0 < Math.abs(d) && (this.a.next(d), b = new tb(y(this.a), c, d, b), D(this.l(), {
        ha: b
    }))
};
```

<a id="example-3-from-is"></a>
## Example 3 from IS

What a nice for statement

```javascript
function Fb(b) {
    var c = b.i.a();
    if (0 == b.j.length) return c;
    var d = 0,
        e = new x(b.i);
    b.c = [];
    for (var f = 0; e.W(1);) c = b.Ia(e.next(1)), d += c ? 0 : 1, c && f++, 0 < f && !c && (b.c.push([e.a, f]), f = 0);
    return d
}
```

<a id="conclusion"></a>
## Conclusion

Don't look at the GCC code and try to write your code in an "pseudo-optimized" way because the compiler does so. Develop the code as readable, understandable and maintainable, and leave the optimization to GCC.

<a id="how-we-build-the-instructions-steps-is-framework"></a>
# How we build the Instructions Steps (IS) Framework

<a id="building-an-sdk"></a>
## Building an SDK

<a id="export-the-classes"></a>
### @export the classes

GCC will remove all the methods that are not called. But if you are building an SDK and would like to provide others with your GCC compiled library most of the methods will not be called. You are providing a library and this library will be used by the clients. Not all the methods will be called by every clients.

This is what @export annotation is for. 

Let's provide our Add and Remove Processors as a library for others to use.

We add the @export annotation

```javascript
// example3_with_exports.js
// 
// Adding @export to the classes
// 
/**
 * @export
 * @author Kiril Mitov
 */
class Processor {
  constructor(elementName, cssClassToAdd) {
    this._elementName = elementName;
    this._cssClassToAdd = cssClassToAdd;
  }

  process() {
    throw new Error("Unimplemented")
  }
}

/**
 * @export
 */
class AddProcessor extends Processor {
  constructor(elementName, cssClassToAdd) {
    super(elementName, cssClassToAdd)
  }

  process() {
    const elements= document.querySelectorAll(this._elementName)
    elements.forEach((element)=> {
      element.classList.add(this._cssClassToAdd)
    })
  }
}

/**
 * @export
 */
class RemoveProcessor extends Processor {

  constructor(elementName, cssClassToAdd) {
    super(elementName, cssClassToAdd)
  }

  process() {
    const elements= document.querySelectorAll(this._elementName)
    elements.forEach((element)=> {
      element.classList.remove(this._cssClassToAdd)
    })
  }
}
let processor = null;
if(document.getElementById("addProcessor")) {
  processor = new AddProcessor("div", "my-class")
} else {
  processor = new RemoveProcessor("div", "my-class")
}
processor.process()
```

To compile we must include two new options:

1. --generate_exports
2. --js closure/goog/base.js - we must download closure/goog/base to the current folder

```bash
$ java -jar closure-compiler-v20200830.jar -O ADVANCED --generate_exports --js closure/goog/base.js example3_with_exports.js
```

The compiled file is:

```javascript
//...more
function q(a, b) {
    this.c = a;
    this.b = b
}
q.prototype.a = function() {
    throw Error("Unimplemented");
};
p("Processor", q);

function r(a, b) {
    q.call(this, a, b)
}
m(r, q);
r.prototype.a = function() {
    var a = this;
    document.querySelectorAll(this.c).forEach(function(b) {
        b.classList.add(a.b)
    })
};
p("AddProcessor", r);

function t(a, b) {
    q.call(this, a, b)
}
m(t, q);
t.prototype.a = function() {
    var a = this;
    document.querySelectorAll(this.c).forEach(function(b) {
        b.classList.remove(a.b)
    })
};
p("RemoveProcessor", t);
var u = null;
document.getElementById("addProcessor") ? u = new r("div", "my-class") : u = new t("div", "my-class");
u.a();
```

What you could wee is that we have the names 'Processor', 'AddProcessor' and 'RemoveProcessor' available as a form of 'named variables' in the file and we could give this file to someone and they can use it to construct new instances of the classes like;

```javascript
const processor = new AddProcessor("div", "my-class")
processor.process()
```

The problem is that the method 'process' is not exported. So it is not an API. It's name was mangled by GCC and was changed to "a()" 

```javascript
//  original
processor.process()
//  compiled
u.a();
```

<a id="export-the-methods"></a>
### @export the methods

If we provide the library to another person they will not be able to call 
'process()' as the name of the method was changed. 

We must @export also the method.

```javascript
// example3_with_exports_and_exported_process_method.js
// 
// Adding @export to the classes and to the process method
// 
/**
 * @export
 * @author Kiril Mitov
 */
class Processor {
  constructor(elementName, cssClassToAdd) {
    this._elementName = elementName;
    this._cssClassToAdd = cssClassToAdd;
  }

  process() {
    throw new Error("Unimplemented")
  }
}

/**
 * @export
 */
class AddProcessor extends Processor {
  constructor(elementName, cssClassToAdd) {
    super(elementName, cssClassToAdd)
  }

  process() {
    const elements= document.querySelectorAll(this._elementName)
    elements.forEach((element)=> {
      element.classList.add(this._cssClassToAdd)
    })
  }
}

/**
 * @export
 */
class RemoveProcessor extends Processor {

  constructor(elementName, cssClassToAdd) {
    super(elementName, cssClassToAdd)
  }

  process() {
    const elements= document.querySelectorAll(this._elementName)
    elements.forEach((element)=> {
      element.classList.remove(this._cssClassToAdd)
    })
  }
}
let processor = null;
if(document.getElementById("addProcessor")) {
  processor = new AddProcessor("div", "my-class")
} else {
  processor = new RemoveProcessor("div", "my-class")
}
processor.process()

```

```bash
$ java -jar /home/kireto/local/closure-compiler-v20200830.jar -O ADVANCED --generate_exports --js /home/kireto/axles/code/is-gcc_tools/vendor/assets/javascripts/closure/goog/base.js example3_with_exports_and_exported_process_method.js

```

The compiled file is:

```javascript
// ... more
function q(a, b) {
    this.b = a;
    this.a = b
}
q.prototype.process = function() {
    throw Error("Unimplemented");
};
p("Processor", q);
q.prototype.process = q.prototype.process;

function r(a, b) {
    q.call(this, a, b)
}
m(r, q);
r.prototype.process = function() {
    var a = this;
    document.querySelectorAll(this.b).forEach(function(b) {
        b.classList.add(a.a)
    })
};
p("AddProcessor", r);

function t(a, b) {
    q.call(this, a, b)
}
m(t, q);
t.prototype.process = function() {
    var a = this;
    document.querySelectorAll(this.b).forEach(function(b) {
        b.classList.remove(a.a)
    })
};
p("RemoveProcessor", t);
var u = null;
document.getElementById("addProcessor") ? u = new r("div", "my-class") : u = new t("div", "my-class");
u.process();
```

What you can see are two new things in this file

```javascript
p("Processor", q);
q.prototype.process = q.prototype.process;

// ...
document.getElementById("addProcessor") ? u = new r("div", "my-class") : u = new t("div", "my-class");
u.process();
```

1. There is a method 'q.prototype.process'
2. The call is no londer 'u.a()', but is 'u.process()'

<a id="drawbacks-of-using-export"></a>
## Drawbacks of using @export

> 'Ideally, the ratio of external symbols to internal code in a binary should be as small as possible. The compiler is much more effective as the "volume" of code increases in comparison to its "surface".'

(Nick Reid, Closure Compiler Discuss, April 2020)

Here is the stats for our Instructions Steps Framework with and without SDK for the size. 

```bash
$ du -b is-release_pack-sdk-1.1.209.js 
158442  is-release_pack-sdk-1.1.209.js
```

**158442 bytes**

| IS production | IS SDK    |
| ------------- | ----------|
| 121857        | 158442    |
| 100%          | 130,02%   |

With all the exports the size of the code grows to to about 30% more, but still less than the Uglify JS code.