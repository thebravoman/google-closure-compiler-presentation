# Advanced compilation with Google Closure Compiler

 or How do we maintain our mental stability when developing in JavaScript.

Kiril Mitov, CTO Axlessoft, October 2020 

> "If the human body was making a new organ every week, doctors would be googling this shit."

(On the state of the JavaScript community; author Unknown; date: beginning of 21 century)

<!-- MarkdownTOC autolink="true" autoanchor="true" -->

- [Demonstration on how it works and how we protect our Intellectual property](#demonstration-on-how-it-works-and-how-we-protect-our-intellectual-property)
    - [Example 1 - output](#example-1---output)
    - [SIMPLE_OPTIMIZATION](#simple_optimization)
        - [SIMPLE_OPTIMIZATIONS compiled](#simple_optimizations-compiled)
        - [SIMPLE_OPTIMIZATION compiled & formatted](#simple_optimization-compiled--formatted)
    - [ADVANCED_OPTIMIZATION](#advanced_optimization)
        - [ADVANCED_OPTIMIZATION compiled](#advanced_optimization-compiled)
    - [Example 2 - classes](#example-2---classes)
        - [ADVANCED_OPTIMIZATION compiled](#advanced_optimization-compiled-1)
        - [Improve example 2](#improve-example-2)
        - [ADVANCED_OPTIMIZATION compiled again](#advanced_optimization-compiled-again)
        - [ADVANCED_OPTIMIZATION compiled again & beautified](#advanced_optimization-compiled-again--beautified)
        - [What was changed](#what-was-changed)
    - [Example 3 - inheritance](#example-3---inheritance)
        - [ADVANCED_OPTIMIZATION compiled](#advanced_optimization-compiled-2)
        - [ADVANCED_OPTIMIZATION compiled & beautified](#advanced_optimization-compiled--beautified)
        - [Summary](#summary)
- [How did the errors disappear](#how-did-the-errors-disappear)
    - [Types](#types)
    - [Missing methods](#missing-methods)
    - [Invalid syntax](#invalid-syntax)
    - [A lot of other checks even before reaching the browser.](#a-lot-of-other-checks-even-before-reaching-the-browser)
- [How the size of the output reduced](#how-the-size-of-the-output-reduced)
- [How we build the Instructions Steps \(IS\) Framework](#how-we-build-the-instructions-steps-is-framework)

<!-- /MarkdownTOC -->

<a id="demonstration-on-how-it-works-and-how-we-protect-our-intellectual-property"></a>
# Demonstration on how it works and how we protect our Intellectual property

Google closure compiler is a compiler. It takes a source and compiles a target. Using the wikipedia definition 
'In computing, a compiler is a computer program that translates computer code written in one programming language (the source language) into another language (the target language). ' 

You can take a program in one language and produce a different language. Here is an example. 
We compile Javascript to Javascript. The compiler has two modes. SIMPLE and ADVANCED.

Ето един Javascript фрагмет

<a id="example-1---output"></a>
## Example 1 - output

```javascript
function output(n) {
  console.log(`called with ${n}`)
}
output(7);
```

<a id="simple_optimization"></a>
## SIMPLE_OPTIMIZATION

```bash
$ java -jar /home/kireto/local/closure-compiler-v20200830.jar --help
```

<a id="simple_optimizations-compiled"></a>
### SIMPLE_OPTIMIZATIONS compiled

```javascript
var $jscomp=$jscomp||{};$jscomp.scope={};$jscomp.createTemplateTagFirstArg=function(a){return a.raw=a};$jscomp.createTemplateTagFirstArgWithRaw=function(a,b){a.raw=b;return a};function output(a){console.log("called with "+a)}output(7);
```

<a id="simple_optimization-compiled--formatted"></a>
### SIMPLE_OPTIMIZATION compiled & formatted

```javascript
function output(a) {
 console.log("called with " + a)
}
output(7);
```

1. Резултатът е минифициран
2. Променливата 'n' вече е с друго име - 'a'
3. Кодът може да се изпълнява и на по-стари браузъри
4. Има малко повече код служебен код в началато, който не е толкова интересен
5. Компилаторът се пуска с java. Има java версия има и javascript версия на компилатора. Версията която аз използваме е 20200830.
6. https://mvnrepository.com/artifact/com.google.javascript/closure-compiler с първа версия  Dec, 2010 като последните години имат нова версия на всеки 3 седмици

<a id="advanced_optimization"></a>
## ADVANCED_OPTIMIZATION

```bash
$ java -jar /home/kireto/local/closure-compiler-v20200830.jar -O ADVANCED example1.js
```

<a id="advanced_optimization-compiled"></a>
### ADVANCED_OPTIMIZATION compiled

```javascript
console.log("called with 7")
```

1. Кодът не е просто минифиран.
2. Липсва фунцията output. Тя не ни трябва. За браузъра няма значение дали ще има такава функция или не. И в двата случая той ще изкара изпише стринг в конзолата
3. Няма дефиниця, няма параметър, няма извикване. Директно е свършена работата.
4. Как сте запазили интелектуалната собственост - ами в компилирания код отсъства разработеният от вас код.
5. Извикването е по-бързо - няма извикване на функция, няма събиране на низ с число
6. Взима по-малко памет - една функция по-малко
7. Въпреки това поведението е запазено.

<a id="example-2---classes"></a>
## Example 2 - classes

```javascript
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
$ java -jar /home/kireto/local/closure-compiler-v20200830.jar -O ADVANCED example2.js 
```

<a id="advanced_optimization-compiled-1"></a>
### ADVANCED_OPTIMIZATION compiled
```javascript

```

Причината е, че никъде не сме извикали Processor и неговите функции и компиларотът вижда, че браузърът никога няма да ги изпълни и затова не ги включва в компилирания код.

<a id="improve-example-2"></a>
### Improve example 2

We add

```javascript

// add this fragment at the bottom at the second example
const processor = new Processor("div", "my-class")
processor.process()
```

And execute 

```bash
$ java -jar /home/kireto/local/closure-compiler-v20200830.jar -O ADVANCED example2.js 
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

<a id="what-was-changed"></a>
### What was changed

Какви са промените въведени от компилатора
1. Не използваме класове
2. Променливата 'element' вече се казва 'a'
3. Компилаторът разбира от DOM и знае, че променливата 'а' има метод, който е classList. 

<a id="example-3---inheritance"></a>
## Example 3 - inheritance

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
$ java -jar /home/kireto/local/closure-compiler-v20200830.jar -O ADVANCED example3.js
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

В началото има няколко служебни фрагменти свързани с прототипи и наследяване. Javascript е базиран на прототипи, но това не е лекцията на която да разглеждаме прототипи, класове и разликите между тях. 

Същината на кода е:

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

<a id="summary"></a>
### Summary

Какви промени е направил компилаторът?

1. 'Processor' вече се казва 'm', 'elementName' е казва 'a' и 'cssClassName' се казва 'b'

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

Компилаторът предоставя възможност за типизиране чрез коментари. Може да кажем в коментари на кода какви са типовете на получаваните параметри. Тъй като JavaScript по подразбиране е силно нетипизиран и динамичен език това е едно от големите му предимства, но е източник и на доста недостатъци. С Google Closure Compiler намерихме точния баланс. Пишем на vanilla JS, и типизираме където трябва

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

// we call the constructor method with a wrong type.
processor = new RemoveProcessor("div", 4)

```

Получаваме забележка от компилатора, че типът ни не е коректен.

```bash
$ java -jar /home/kireto/local/closure-compiler-v20200830.jar -O ADVANCED example4.js 
example4.js:81:41: WARNING - [JSC_TYPE_MISMATCH] actual parameter 2 of RemoveProcessor does not match formal parameter
found   : number
required: string
  81|   processor = new RemoveProcessor("div", 4)
                                               ^

0 error(s), 1 warning(s), 93.2% typed
```

Съществуват много типове 
https://github.com/google/closure-compiler/wiki/Types-in-the-Closure-Type-System

<a id="missing-methods"></a>
## Missing methods

Какво ще стане ако се опитаме да извикаме метод който вече не съществува

```javascript
// we call .anotherMethod and .process()
// .anotherMethod does not exist and is not defined
processor.anotherMethod()
```

```bash
$ java -jar /home/kireto/local/closure-compiler-v20200830.jar -O ADVANCED example5.js 
example5.js:83:10: WARNING - [JSC_INEXISTENT_PROPERTY] Property anotherMethod never defined on (AddProcessor|RemoveProcessor)
  83| processor.anotherMethod()
                ^^^^^^^^^^^^^
```

<a id="invalid-syntax"></a>
## Invalid syntax

```javascript
// this code can not be compiled. It is not a valid JavaScript
  process() {
    const elements= document.querySelectorAll(this._elementName)
    elements.forEach((element)=> {
      element.classList.remove this._cssClassToAdd
    })
  }
```

```bash
$ java -jar /home/kireto/local/closure-compiler-v20200830.jar -O ADVANCED example6.js 
example6.js:72:31: ERROR - [JSC_PARSE_ERROR] Parse error. Semi-colon expected
  72|       element.classList.remove this._cssClassToAdd
                                     ^

1 error(s), 0 warning(s)
```

<a id="a-lot-of-other-checks-even-before-reaching-the-browser"></a>
## A lot of other checks even before reaching the browser.

> More sweat in the training less blood on the ring

Харесва ми динамичната, не типизирана среща, която JavaScript ти предоставя. Това е инструмент, който може да се използва за добри каузи, но също така може да е причина за доста грешки и важното е да се намери баланса.  

<a id="how-the-size-of-the-output-reduced"></a>
# How the size of the output reduced

1. The size of the whole IS Framework 

```bash
$ du is-release_pack-b65ffd509871e122b72cbdfa93e96c47cea2ac0c64b83016bf3b0df14cd99a86.js -b
586230  is-release_pack-b65ffd509871e122b72cbdfa93e96c47cea2ac0c64b83016bf3b0df14cd99a86.js
```

**586230 bytes**

2. Number of lines 

```bash
$ wc -l is-release_pack-b65ffd509871e122b72cbdfa93e96c47cea2ac0c64b83016bf3b0df14cd99a86.js
20646 is-release_pack-b65ffd509871e122b72cbdfa93e96c47cea2ac0c64b83016bf3b0df14cd99a86.js
```

**20646 lines of code.**

3. Uglify JS

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

Доставяме JavaScript, компилиран с GCC, ADVANCED_OPTIMIZATION
 - който и да ни откраднеш, ще ти е трудно да изградиш продукт върху него
 - който работи
 - работи на повече браузъри с 0 усилия от наша страна
 - и е 40% по-малък
 - GCC се поддържа от Google и вероятно няма да изчезне скоро
 - не е сложен за използване, а е просто един компилатор 
 - на всеки 3 седмици излиза нова версия
 - казва се gcc. Какъв по-голям кеф от това да пишеш gcc и да си спомняш за славните времена.

<a id="how-we-build-the-instructions-steps-is-framework"></a>
# How we build the Instructions Steps (IS) Framework