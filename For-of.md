<style>
[lang="ar"] {
    direction: rtl;
    text-align: right;
}

[lang="en"] {
    direction: ltr;
    text-align: left;
}

code, pre {
    direction: ltr;
    text-align: left;
}

body {
    direction: rtl;
}
</style>

---

---

title: for...of
slug: Web/JavaScript/Reference/Statements/for...of
page-type: javascript-statement
browser-compat: javascript.statements.for_of
sidebar: jssidebar

---

تنفذ عبارة **`for...of`** حلقة تكرارية (loop) تعمل على سلسلة من القيم مصدرها [كائن قابل للتكرار (iterable object)](/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol). تشمل الكائنات القابلة للتكرار نُسخًا (instances) من الكائنات المضمنة (built-ins) مثل {{jsxref("Array")}}, {{jsxref("String")}}, {{jsxref("TypedArray")}}, {{jsxref("Map")}}, {{jsxref("Set")}}, {{domxref("NodeList")}} (وغيرها من مجموعات DOM)، بالإضافة إلى كائن {{jsxref("Functions/arguments", "arguments")}}, و [المولدات (generators)](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator) التي تنتجها [الدوال المولّدة (generator functions)](/en-US/docs/Web/JavaScript/Reference/Statements/function*)، والكائنات القابلة للتكرار المعرّفة من قبل المستخدم.

{{InteractiveExample("JavaScript Demo: for...of statement")}}

```js interactive-example
const array = ["a", "b", "c"];

for (const element of array) {
  console.log(element);
}

// المخرجات المتوقعة: "a"
// المخرجات المتوقعة: "b"
// المخرجات المتوقعة: "c"
```

## الصيغة (Syntax)

```js-nolint
for (variable of iterable)
  statement
```

- `variable`
  - : يستقبل قيمة من السلسلة في كل دورة تكرار. يمكن أن يكون إما تصريحًا باستخدام [`const`](/en-US/docs/Web/JavaScript/Reference/Statements/const)، أو [`let`](/en-US/docs/Web/JavaScript/Reference/Statements/let)، أو [`var`](/en-US/docs/Web/JavaScript/Reference/Statements/var)، أو [`using`](/en-US/docs/Web/JavaScript/Reference/Statements/using)، أو [`await using`](/en-US/docs/Web/JavaScript/Reference/Statements/await_using)، أو هدف [إسناد (assignment)](/en-US/docs/Web/JavaScript/Reference/Operators/Assignment) (على سبيل المثال، متغير تم التصريح عنه مسبقًا، أو خاصية كائن، أو [نمط تفكيك (destructuring pattern)](/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring)). المتغيرات المصرح عنها باستخدام `var` ليست محلية للحلقة التكرارية، أي أنها تكون في نفس النطاق (scope) الذي توجد فيه حلقة `for...of`.
- `iterable`
  - : كائن قابل للتكرار. وهو مصدر سلسلة القيم التي تعمل عليها الحلقة التكرارية.
- `statement`
  - : عبارة برمجية يتم تنفيذها في كل دورة تكرار. قد تشير إلى `variable`. يمكنك استخدام [عبارة كتلة (block statement)](/en-US/docs/Web/JavaScript/Reference/Statements/block) لتنفيذ عدة عبارات.

## الوصف

تعمل حلقة `for...of` على القيم المستمدة من كائن قابل للتكرار واحدة تلو الأخرى بترتيب تسلسلي. كل عملية للحلقة على قيمة تسمى _دورة تكرار (iteration)_، ويقال إن الحلقة _تتكرر على الكائن القابل للتكرار (iterate over the iterable)_. تنفذ كل دورة تكرار عبارات قد تشير إلى قيمة السلسلة الحالية.

عندما تتكرر حلقة `for...of` على كائن قابل للتكرار، فإنها تستدعي أولاً التابع `[Symbol.iterator]()` الخاص بالكائن، والذي يعيد [مُكرِّرًا (iterator)](/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterator_protocol)، ثم تستدعي بشكل متكرر التابع `next()` للمُكرِّر الناتج لإنتاج سلسلة القيم التي سيتم إسنادها إلى `variable`.

تخرج حلقة `for...of` عندما يكتمل المُكرِّر (عندما تكون نتيجة `next()` كائنًا يحتوي على `done: true`). مثل عبارات التكرار الأخرى، يمكنك استخدام [عبارات التحكم في التدفق (control flow statements)](/en-US/docs/Web/JavaScript/Reference/Statements#control_flow) داخل `statement`:

- توقف {{jsxref("Statements/break", "break")}} تنفيذ `statement` وتنتقل إلى أول عبارة بعد الحلقة.
- توقف {{jsxref("Statements/continue", "continue")}} تنفيذ `statement` وتنتقل إلى دورة التكرار التالية للحلقة.

إذا خرجت حلقة `for...of` مبكرًا (على سبيل المثال، عند مواجهة عبارة `break` أو إطلاق خطأ)، يتم استدعاء التابع `return()` للمُكرِّر لتنفيذ أي عمليات تنظيف.

يقبل الجزء `variable` من حلقة `for...of` أي شيء يمكن أن يأتي قبل عامل التشغيل `=`. يمكنك استخدام {{jsxref("Statements/const", "const")}} للتصريح عن المتغير طالما لم تتم إعادة إسناد قيمة له داخل جسم الحلقة (يمكن أن يتغير بين دورات التكرار، لأنها تعتبر متغيرين منفصلين). بخلاف ذلك، يمكنك استخدام {{jsxref("Statements/let", "let")}}.

````js
const iterable = [10, 20, 30];

for (let value of iterable) {
  value += 1;
  console.log(value);
}
// 11
// 21
// 31```

> [!NOTE]
> كل دورة تكرار تنشئ متغيرًا جديدًا. إعادة إسناد قيمة للمتغير داخل جسم الحلقة لا تؤثر على القيمة الأصلية في الكائن القابل للتكرار (مصفوفة، في هذه الحالة).

المتغيرات المصرح عنها باستخدام تصريح {{jsxref("Statements/using", "using")}} أو {{jsxref("Statements/await_using", "await using")}} يتم التخلص منها (disposed) في كل مرة تنتهي فيها دورة تكرار (و `await using` تسبب `await` ضمنيًا في نهاية دورة التكرار). ومع ذلك، إذا خرجت الحلقة مبكرًا، فإن أي قيم متبقية في المُكرِّر لم يتم الوصول إليها لا يتم التخلص منها (على الرغم من أن القيمة الحالية يتم التخلص منها).

```js
const resources = [dbConnection1, dbConnection2, dbConnection3];

for (using dbConnection of resources) {
  dbConnection.query("...");
  // يتم التخلص من dbConnection هنا
}
````

يمكنك استخدام [التفكيك (destructuring)](/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring) لإسناد متغيرات محلية متعددة، أو استخدام مُوصِّل خاصية (property accessor) مثل `for (x.y of iterable)` لإسناد القيمة إلى خاصية كائن.

ومع ذلك، توجد قاعدة خاصة تمنع استخدام `async` كاسم للمتغير. هذه صيغة غير صالحة:

```js-nolint example-bad
let async;
for (async of [1, 2, 3]); // SyntaxError: The left-hand side of a for-of loop may not be 'async'.
```

يهدف هذا إلى تجنب الغموض في الصيغة مع الكود الصحيح `for (async of => {};;)`، وهو حلقة [`for`](/en-US/docs/Web/JavaScript/Reference/Statements/for).

وبالمثل، إذا استخدمت تصريح `using`، فلا يمكن تسمية المتغير `of`:

````js-nolint example-bad
for (using of of []); // SyntaxError```

يهدف هذا إلى تجنب الغموض في الصيغة مع الكود الصحيح `for (using of [])`، قبل إدخال `using`.

## أمثلة

### التكرار على مصفوفة (Array)

```js
const iterable = [10, 20, 30];

for (const value of iterable) {
  console.log(value);
}
// 10
// 20
// 30```

### التكرار على سلسلة نصية (string)

يتم التكرار على السلاسل النصية [حسب نقاط ترميز يونيكود (Unicode code points)](/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/Symbol.iterator).

```js
const iterable = "boo";

for (const value of iterable) {
  console.log(value);
}
// "b"
// "o"
// "o"
````

### التكرار على مصفوفة مكتوبة (TypedArray)

```js
const iterable = new Uint8Array([0x00, 0xff]);

for (const value of iterable) {
  console.log(value);
}
// 0
// 255
```

### التكرار على خريطة (Map)

```js
const iterable = new Map([
  ["a", 1],
  ["b", 2],
  ["c", 3],
]);

for (const entry of iterable) {
  console.log(entry);
}
// ['a', 1]
// ['b', 2]
// ['c', 3]

for (const [key, value] of iterable) {
  console.log(value);
}
// 1
// 2
// 3
```

### التكرار على مجموعة (Set)

```js
const iterable = new Set([1, 1, 2, 2, 3, 3]);

for (const value of iterable) {
  console.log(value);
}
// 1
// 2
// 3
```

### التكرار على كائن arguments

يمكنك التكرار على كائن {{jsxref("Functions/arguments", "arguments")}} لفحص جميع المعاملات (parameters) التي تم تمريرها إلى دالة.

```js
function foo() {
  for (const value of arguments) {
    console.log(value);
  }
}

foo(1, 2, 3);
// 1
// 2
// 3
```

### التكرار على NodeList

يضيف المثال التالي صنف `read` إلى الفقرات التي هي أبناء مباشرين لعنصر [`<article>`](/en-US/docs/Web/HTML/Reference/Elements/article) عن طريق التكرار على مجموعة DOM من نوع [`NodeList`](/en-US/docs/Web/API/NodeList).

```js
const articleParagraphs = document.querySelectorAll("article > p");
for (const paragraph of articleParagraphs) {
  paragraph.classList.add("read");
}
```

### التكرار على كائن قابل للتكرار معرّف من قبل المستخدم

التكرار على كائن يحتوي على تابع `[Symbol.iterator]()` يعيد مُكرِّرًا مخصصًا:

```js
const iterable = {
  [Symbol.iterator]() {
    let i = 1;
    return {
      next() {
        if (i <= 3) {
          return { value: i++, done: false };
        }
        return { value: undefined, done: true };
      },
    };
  },
};

for (const value of iterable) {
  console.log(value);
}
// 1
// 2
// 3
```

التكرار على كائن يحتوي على تابع مولّد `[Symbol.iterator]()`:

```js
const iterable = {
  *[Symbol.iterator]() {
    yield 1;
    yield 2;
    yield 3;
  },
};

for (const value of iterable) {
  console.log(value);
}
// 1
// 2
// 3
```

_المُكرِّرات القابلة للتكرار_ (المُكرِّرات التي تحتوي على تابع `[Symbol.iterator]()` يعيد `this`) هي تقنية شائعة إلى حد ما لجعل المُكرِّرات قابلة للاستخدام في الصيغ التي تتوقع كائنات قابلة للتكرار، مثل `for...of`.

````js
let i = 1;

const iterator = {
  next() {
    if (i <= 3) {
      return { value: i++, done: false };
    }
    return { value: undefined, done: true };
  },
  [Symbol.iterator]() {
    return this;
  },
};

for (const value of iterator) {
  console.log(value);
}
// 1
// 2
// 3```

### التكرار على مولّد (generator)

```js
function* source() {
  yield 1;
  yield 2;
  yield 3;
}

const generator = source();

for (const value of generator) {
  console.log(value);
}
// 1
// 2
// 3
````

### الخروج المبكر

يؤدي تنفيذ عبارة `break` في الحلقة الأولى إلى خروجها مبكرًا. لم ينتهِ المُكرِّر بعد، لذا ستستمر الحلقة الثانية من حيث توقفت الأولى.

```js
const source = [1, 2, 3];

const iterator = source[Symbol.iterator]();

for (const value of iterator) {
  console.log(value);
  if (value === 1) {
    break;
  }
  console.log("This string will not be logged.");
}
// 1

// حلقة أخرى تستخدم نفس المُكرِّر
// تكمل من حيث توقفت الحلقة السابقة.
for (const value of iterator) {
  console.log(value);
}
// 2
// 3

// تم استهلاك المُكرِّر بالكامل.
// هذه الحلقة لن تنفذ أي دورات تكرار.
for (const value of iterator) {
  console.log(value);
}
// [لا يوجد مخرجات]
```

تنفذ المولّدات التابع `return()`، مما يؤدي إلى عودة الدالة المولّدة مبكرًا عند خروج الحلقة. هذا يجعل المولّدات غير قابلة لإعادة الاستخدام بين الحلقات.

```js example-bad
function* source() {
  yield 1;
  yield 2;
  yield 3;
}

const generator = source();

for (const value of generator) {
  console.log(value);
  if (value === 1) {
    break;
  }
  console.log("This string will not be logged.");
}
// 1

// تم استهلاك المولّد بالكامل.
// هذه الحلقة لن تنفذ أي دورات تكرار.
for (const value of generator) {
  console.log(value);
}
// [لا يوجد مخرجات]
```

### الفرق بين for...of و for...in

كل من عبارتي `for...in` و `for...of` تتكرر على شيء ما. الفرق الرئيسي بينهما هو في ما تتكرر عليه.

تتكرر عبارة {{jsxref("Statements/for...in", "for...in")}} على [الخصائص النصية القابلة للإحصاء (enumerable string properties)](/en-US/docs/Web/JavaScript/Guide/Enumerability_and_ownership_of_properties) لكائن، بينما تتكرر عبارة `for...of` على القيم التي يحددها [الكائن القابل للتكرار](/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) ليتم التكرار عليها.

يوضح المثال التالي الفرق بين حلقة `for...of` وحلقة `for...in` عند استخدامهما مع {{jsxref("Array")}}.

```js
Object.prototype.objCustom = function () {};
Array.prototype.arrCustom = function () {};

const iterable = [3, 5, 7];
iterable.foo = "hello";

for (const i in iterable) {
  console.log(i);
}
// "0", "1", "2", "foo", "arrCustom", "objCustom"

for (const i in iterable) {
  if (Object.hasOwn(iterable, i)) {
    console.log(i);
  }
}
// "0" "1" "2" "foo"

for (const i of iterable) {
  console.log(i);
}
// 3 5 7
```

يرث الكائن `iterable` الخاصيتين `objCustom` و `arrCustom` لأنه يحتوي على كل من `Object.prototype` و `Array.prototype` في [سلسلة prototype الخاصة به](/en-US/docs/Web/JavaScript/Guide/Inheritance_and_the_prototype_chain).

تسجل حلقة `for...in` فقط [الخصائص القابلة للإحصاء](/en-US/docs/Web/JavaScript/Guide/Enumerability_and_ownership_of_properties) للكائن `iterable`. لا تسجل _عناصر_ المصفوفة `3`، `5`، `7` أو `"hello"` لأنها ليست _خصائص_ — إنها _قيم_. تسجل _فهارس_ المصفوفة بالإضافة إلى `arrCustom` و `objCustom`، والتي هي خصائص فعلية. إذا لم تكن متأكدًا من سبب تكرار هذه الخصائص، فهناك شرح أكثر تفصيلاً لكيفية عمل [تكرار المصفوفات و `for...in`](/en-US/docs/Web/JavaScript/Reference/Statements/for...in#array_iteration_and_for...in).

تشبه الحلقة الثانية الحلقة الأولى، لكنها تستخدم {{jsxref("Object.hasOwn()")}} للتحقق مما إذا كانت الخاصية القابلة للإحصاء التي تم العثور عليها هي خاصية خاصة بالكائن، أي غير موروثة. إذا كانت كذلك، يتم تسجيل الخاصية. يتم تسجيل الخصائص `0`، `1`، `2` و `foo` لأنها خصائص خاصة. لا يتم تسجيل الخاصيتين `arrCustom` و `objCustom` لأنهما موروثتان.

تتكرر حلقة `for...of` وتسجل _القيم_ التي يحددها `iterable`، كمصفوفة (وهي [قابلة للتكرار](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Symbol.iterator))، ليتم التكرار عليها. تظهر _عناصر_ الكائن `3`، `5`، `7`، ولكن لا تظهر أي من _خصائص_ الكائن.
