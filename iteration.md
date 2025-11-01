<style>
[lang="ar"] {
  direction: rtl;
  text-align: right;
}

[lang="en"] {
  direction: ltr;
  text-align: left;
}

/* جميع عناصر الكود */
code, pre, kbd, samp {
  direction: ltr;
  text-align: left;
  unicode-bidi: isolate-override;
}

/* عناصر inline بالإنجليزية داخل النص العربي */
[lang="ar"] :is(code, strong, em, var, cite) {
  unicode-bidi: isolate;
  direction: ltr;
}

/* التأكد من محاذاة القوائم */
[lang="ar"] :is(ul, ol, dl) {
  direction: rtl;
  text-align: right;
}

[lang="ar"] :is(li, dd, dt) {
  direction: rtl;
  text-align: right;
  text-align-last: right;
}

/* للنصوص المختلطة */
[lang="ar"] p,
[lang="ar"] blockquote {
  direction: rtl;
  text-align: right;
  unicode-bidi: embed;
}
</style>

**بروتوكولات التكرار (Iteration protocols)** ليست كائنات مدمجة (built-ins) أو صيغاً (syntax) جديدة، بل هي _بروتوكولات_. يمكن لأي كائن تطبيق هذه البروتوكولات باتباع بعض الأعراف.

هناك بروتوكولان: [بروتوكول الكائن القابل للتكرار (iterable protocol)](#the_iterable_protocol) و[بروتوكول المُكرِّر (iterator protocol)](#the_iterator_protocol).

## بروتوكول الكائن القابل للتكرار (The iterable protocol)

**بروتوكول الكائن القابل للتكرار (The iterable protocol)** يسمح لكائنات JavaScript بتعريف أو تخصيص سلوك التكرار الخاص بها، مثل تحديد القيم التي يتم المرور عليها في حلقة التكرار {{jsxref("Statements/for...of", "for...of")}}. بعض الأنواع المدمجة هي [كائنات قابلة للتكرار مدمجة](#built-in_iterables) ولها سلوك تكرار افتراضي، مثل {{jsxref("Array")}} أو {{jsxref("Map")}}، بينما أنواع أخرى (مثل {{jsxref("Object")}}) ليست كذلك.

لكي يكون الكائن **قابلاً للتكرار (iterable)**، يجب أن يطبق التابع **`[Symbol.iterator]()`**، وهذا يعني أن الكائن (أو أحد الكائنات في [سلسلة الوراثة (prototype chain)](/en-US/docs/Web/JavaScript/Guide/Inheritance_and_the_prototype_chain)) يجب أن يحتوي على خاصية بمفتاح `[Symbol.iterator]` يمكن الوصول إليه عبر الثابت {{jsxref("Symbol.iterator")}}:

- `[Symbol.iterator]()`
  - : دالة لا تأخذ أي وسائط (zero-argument function) وتُرجع كائنًا يتوافق مع [بروتوكول المُكرِّر (iterator protocol)](#the_iterator_protocol).

عندما يحتاج كائن ما إلى التكرار (كما يحدث في بداية حلقة {{jsxref("Statements/for...of", "for...of")}})، يتم استدعاء التابع `[Symbol.iterator]()` الخاص به بدون وسائط، ويُستخدم **المُكرِّر (iterator)** المُرجع للحصول على القيم التي سيتم التكرار عليها.

لاحظ أنه عند استدعاء هذه الدالة التي لا تأخذ وسائط، يتم استدعاؤها كتابع (method) على الكائن القابل للتكرار. لذلك، داخل الدالة، يمكن استخدام الكلمة المفتاحية `this` للوصول إلى خصائص الكائن القابل للتكرار، لتحديد ما سيتم توفيره أثناء التكرار.

يمكن أن تكون هذه الدالة دالة عادية، أو يمكن أن تكون دالة مولِّدة (generator function)، بحيث عند استدعائها، يتم إرجاع كائن مُكرِّر (iterator object). داخل هذه الدالة المولِّدة، يمكن توفير كل إدخال باستخدام `yield`.

## بروتوكول المُكرِّر (The iterator protocol)

**بروتوكول المُكرِّر (The iterator protocol)** يُعرّف طريقة قياسية لإنتاج سلسلة من القيم (سواء كانت محدودة أو لا نهائية)، وإمكانية إرجاع قيمة عند انتهاء توليد جميع القيم.

يكون الكائن مُكرِّرًا (iterator) عندما يطبق تابعًا باسم **`next()`** بالدلالات التالية:

- `next()`
  - : دالة تقبل صفرًا أو وسيطًا واحدًا وتُرجع كائنًا يتوافق مع واجهة `IteratorResult` (انظر أدناه). إذا تم إرجاع قيمة ليست كائنًا (مثل `false` أو `undefined`) عند استخدام ميزة لغوية مدمجة (مثل `for...of`) للمُكرِّر، فسيتم إلقاء خطأ من نوع {{jsxref("TypeError")}} (`"iterator.next() returned a non-object value"`).

من المتوقع أن تُرجع جميع توابع بروتوكول المُكرِّر (`next()` و `return()` و `throw()`) كائنًا يطبق واجهة `IteratorResult`. يجب أن يحتوي على الخصائص التالية:

- `done` {{optional_inline}}

  - : قيمة منطقية (boolean) تكون `false` إذا كان المُكرِّر قادرًا على إنتاج القيمة التالية في السلسلة. (هذا يعادل عدم تحديد خاصية `done` على الإطلاق).

    تكون قيمتها `true` إذا كان المُكرِّر قد أكمل سلسلته. في هذه الحالة، تحدد `value` اختياريًا القيمة المرجعة للمُكرِّر.

- `value` {{optional_inline}}
  - : أي قيمة JavaScript يُرجعها المُكرِّر. يمكن حذفها عندما تكون `done` قيمتها `true`.

عمليًا، لا تعد أي من الخاصيتين مطلوبة بشكل صارم؛ فإذا تم إرجاع كائن بدون أي من الخاصيتين، فإنه يعادل `{ done: false, value: undefined }`.

إذا أرجع مُكرِّر نتيجة تحتوي على `done: true`، فمن المتوقع أن تُرجع أي استدعاءات لاحقة لـ `next()` القيمة `done: true` أيضًا، على الرغم من أن هذا غير مفروض على مستوى اللغة.

يمكن أن يستقبل التابع `next` قيمة ستكون متاحة داخل جسم التابع. لا توجد ميزة لغوية مدمجة تمرر أي قيمة. القيمة التي يتم تمريرها إلى التابع `next` في [الدوال المولِّدة (generators)](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator) ستصبح قيمة تعبير `yield` المقابل.

اختياريًا، يمكن للمُكرِّر أيضًا تطبيق التابعين **`return(value)`** و **`throw(exception)`**، والتي عند استدعائها، تخبر المُكرِّر أن المستدعي قد انتهى من التكرار ويمكنه إجراء أي عمليات تنظيف ضرورية (مثل إغلاق اتصال قاعدة البيانات).

- `return(value)` {{optional_inline}}
  - : دالة تقبل صفرًا أو وسيطًا واحدًا وتُرجع كائنًا يتوافق مع واجهة `IteratorResult`، وعادة ما تكون `value` مساوية للقيمة `value` التي تم تمريرها و`done` مساوية لـ `true`. استدعاء هذا التابع يخبر المُكرِّر بأن المستدعي لا ينوي إجراء المزيد من استدعاءات `next()` ويمكنه تنفيذ أي إجراءات تنظيف. عندما تستدعي الميزات اللغوية المدمجة `return()` للتنظيف، تكون `value` دائمًا `undefined`.
- `throw(exception)` {{optional_inline}}
  - : دالة تقبل صفرًا أو وسيطًا واحدًا وتُرجع كائنًا يتوافق مع واجهة `IteratorResult`، وعادة ما تكون `done` مساوية لـ `true`. استدعاء هذا التابع يخبر المُكرِّر بأن المستدعي قد اكتشف حالة خطأ، ويكون `exception` عادةً نسخة من {{jsxref("Error")}}. لا توجد ميزة لغوية مدمجة تستدعي `throw()` لأغراض التنظيف — إنها ميزة خاصة بالدوال المولِّدة (generators) لتحقيق التناظر مع `return`/`throw`.

> [!NOTE]
> لا يمكن معرفة ما إذا كان كائن معين يطبق بروتوكول المُكرِّر بشكل انعكاسي (reflectively) (أي، دون استدعاء `next()` فعليًا والتحقق من النتيجة المرجعة).

من السهل جدًا جعل المُكرِّر قابلاً للتكرار أيضًا: فقط قم بتطبيق تابع `[Symbol.iterator]()` الذي يُرجع `this`.

```js
// يفي ببروتوكول المُكرِّر (Iterator Protocol) وبروتوكول الكائن القابل للتكرار (Iterable)
const myIterator = {
  next() {
    // …
  },
  [Symbol.iterator]() {
    return this;
  },
};
```

يُطلق على هذا الكائن اسم _مُكرِّر قابل للتكرار (iterable iterator)_. يسمح القيام بذلك باستهلاك المُكرِّر من قبل مختلف الصيغ التي تتوقع كائنات قابلة للتكرار — لذلك، نادرًا ما يكون من المفيد تطبيق بروتوكول المُكرِّر دون تطبيق بروتوكول الكائن القابل للتكرار أيضًا. (في الواقع، تتوقع جميع الصيغ وواجهات برمجة التطبيقات تقريبًا _كائنات قابلة للتكرار (iterables)_، وليس _مُكرِّرات (iterators)_). [كائن الدالة المولِّدة (generator object)](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator) هو مثال على ذلك:

```js
const generatorObject = (function* () {
  yield 1;
  yield 2;
  yield 3;
})();

console.log(typeof generatorObject.next);
// "function" — لديه تابع next (الذي يُرجع النتيجة الصحيحة)، لذا فهو مُكرِّر (iterator)

console.log(typeof generatorObject[Symbol.iterator]);
// "function" — لديه تابع [Symbol.iterator] (الذي يُرجع المُكرِّر الصحيح)، لذا فهو قابل للتكرار (iterable)

console.log(generatorObject[Symbol.iterator]() === generatorObject);
// true — التابع [Symbol.iterator] الخاص به يُرجع نفسه (مُكرِّر)، لذا فهو مُكرِّر قابل للتكرار (iterable iterator)
```

جميع المُكرِّرات المدمجة ترث من {{jsxref("Iterator", "Iterator.prototype")}}، الذي يطبق التابع `[Symbol.iterator]()` كإرجاع `this`، بحيث تكون المُكرِّرات المدمجة قابلة للتكرار أيضًا.

ومع ذلك، عندما يكون ذلك ممكنًا، من الأفضل أن يقوم `iterable[Symbol.iterator]()` بإرجاع مُكرِّرات مختلفة تبدأ دائمًا من البداية، كما يفعل [`Set.prototype[Symbol.iterator]()`](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set/Symbol.iterator).

## بروتوكولات المُكرِّر غير المتزامن (async iterator) والكائن القابل للتكرار غير المتزامن (async iterable)

هناك زوج آخر من البروتوكولات المستخدمة للتكرار غير المتزامن، يُطلق عليهما **بروتوكول المُكرِّر غير المتزامن (async iterator)** و **بروتوكول الكائن القابل للتكرار غير المتزامن (async iterable)**. لديهما واجهات مشابهة جدًا لبروتوكولات الكائن القابل للتكرار والمُكرِّر، باستثناء أن كل قيمة مُرجعة من استدعاءات توابع المُكرِّر تكون مغلفة في Promise.

يطبق الكائن بروتوكول الكائن القابل للتكرار غير المتزامن عندما يطبق التوابع التالية:

- [`[Symbol.asyncIterator]()`](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/asyncIterator)
  - : دالة لا تأخذ وسائط وتُرجع كائنًا يتوافق مع بروتوكول المُكرِّر غير المتزامن.

يطبق الكائن بروتوكول المُكرِّر غير المتزامن عندما يطبق التوابع التالية:

- `next()`
  - : دالة تقبل صفرًا أو وسيطًا واحدًا وتُرجع Promise. يتم الوفاء بالـ Promise بكائن يتوافق مع واجهة `IteratorResult`، والخصائص لها نفس دلالات المُكرِّر المتزامن.
- `return(value)` {{optional_inline}}
  - : دالة تقبل صفرًا أو وسيطًا واحدًا وتُرجع Promise. يتم الوفاء بالـ Promise بكائن يتوافق مع واجهة `IteratorResult`، والخصائص لها نفس دلالات المُكرِّر المتزامن.
- `throw(exception)` {{optional_inline}}
  - : دالة تقبل صفرًا أو وسيطًا واحدًا وتُرجع Promise. يتم الوفاء بالـ Promise بكائن يتوافق مع واجهة `IteratorResult`، والخصائص لها نفس دلالات المُكرِّر المتزامن.

## التفاعلات بين اللغة وبروتوكولات التكرار

تحدد اللغة واجهات برمجة التطبيقات (APIs) التي تنتج أو تستهلك الكائنات القابلة للتكرار والمُكرِّرات.

### الكائنات القابلة للتكرار المدمجة (Built-in iterables)

{{jsxref("String")}}، {{jsxref("Array")}}، {{jsxref("TypedArray")}}، {{jsxref("Map")}}، {{jsxref("Set")}}، و [`Segments`](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/Segmenter/segment/Segments) (التي يتم إرجاعها بواسطة [`Intl.Segmenter.prototype.segment()`](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/Segmenter/segment)) كلها كائنات قابلة للتكرار مدمجة، لأن كائنات `prototype` الخاصة بها تطبق تابع `[Symbol.iterator]()`. بالإضافة إلى ذلك، فإن كائن [`arguments`](/en-US/docs/Web/JavaScript/Reference/Functions/arguments) وبعض أنواع مجموعات DOM مثل {{domxref("NodeList")}} هي أيضًا قابلة للتكرار.
لا يوجد كائن في لغة JavaScript الأساسية قابل للتكرار غير المتزامن. بعض واجهات برمجة تطبيقات الويب، مثل {{domxref("ReadableStream")}}، لديها التابع `Symbol.asyncIterator` معينًا بشكل افتراضي.

[الدوال المولِّدة (Generator functions)](/en-US/docs/Web/JavaScript/Reference/Statements/function*) تُرجع [كائنات مولِّدة (generator objects)](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator)، وهي مُكرِّرات قابلة للتكرار. [الدوال المولِّدة غير المتزامنة (Async generator functions)](/en-US/docs/Web/JavaScript/Reference/Statements/async_function*) تُرجع [كائنات مولِّدة غير متزامنة (async generator objects)](/en-US/docs/Web/JavaScript/Reference/Global_Objects/AsyncGenerator)، وهي مُكرِّرات قابلة للتكرار غير متزامنة.

المُكرِّرات التي يتم إرجاعها من الكائنات القابلة للتكرار المدمجة ترث جميعها من صنف مشترك هو {{jsxref("Iterator")}}، والذي يطبق التابع المذكور أعلاه `[Symbol.iterator]() { return this; }`، مما يجعلها جميعًا مُكرِّرات قابلة للتكرار. يوفر الصنف `Iterator` أيضًا [توابع مساعدة إضافية](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Iterator#iterator_helper_methods) بالإضافة إلى التابع `next()` المطلوب بواسطة بروتوكول المُكرِّر. يمكنك فحص سلسلة الوراثة (prototype chain) للمُكرِّر عن طريق طباعتها في وحدة تحكم رسومية.

```plain
console.log([][Symbol.iterator]());

Array Iterator {}
  [[Prototype]]: Array Iterator     ==> هذا هو الـ prototype المشترك بين جميع مُكرِّرات المصفوفات
    next: ƒ next()
    Symbol(Symbol.toStringTag): "Array Iterator"
    [[Prototype]]: Object           ==> هذا هو الـ prototype المشترك بين جميع المُكرِّرات المدمجة
      Symbol(Symbol.iterator): ƒ [Symbol.iterator]()
      [[Prototype]]: Object         ==> هذا هو Object.prototype
```

### واجهات برمجة التطبيقات المدمجة التي تقبل الكائنات القابلة للتكرار (Built-in APIs accepting iterables)

هناك العديد من واجهات برمجة التطبيقات التي تقبل الكائنات القابلة للتكرار. بعض الأمثلة تشمل:

- {{jsxref("Map/Map", "Map()")}}
- {{jsxref("WeakMap/WeakMap", "WeakMap()")}}
- {{jsxref("Set/Set", "Set()")}}
- {{jsxref("WeakSet/WeakSet", "WeakSet()")}}
- {{jsxref("Promise.all()")}}
- {{jsxref("Promise.allSettled()")}}
- {{jsxref("Promise.race()")}}
- {{jsxref("Promise.any()")}}
- {{jsxref("Array.from()")}}
- {{jsxref("Object.groupBy()")}}
- {{jsxref("Map.groupBy()")}}

```js
const myObj = {};

new WeakSet(
  (function* () {
    yield {};
    yield myObj;
    yield {};
  })()
).has(myObj); // true
```

### الصيغ التي تتوقع الكائنات القابلة للتكرار (Syntaxes expecting iterables)

بعض العبارات والتعبيرات تتوقع كائنات قابلة للتكرار، على سبيل المثال حلقات {{jsxref("Statements/for...of", "for...of")}}، [نشر المصفوفات والمعاملات (array and parameter spreading)](/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)، {{jsxref("Operators/yield*", "yield*")}}، و [تفكيك المصفوفات (array destructuring)](/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring):

```js
for (const value of ["a", "b", "c"]) {
  console.log(value);
}
// "a"
// "b"
// "c"

console.log([..."abc"]); // ["a", "b", "c"]

function* gen() {
  yield* ["a", "b", "c"];
}

console.log(gen().next()); // { value: "a", done: false }

[a, b, c] = new Set(["a", "b", "c"]);
console.log(a); // "a"
```

عندما تقوم الصيغ المدمجة بالتكرار على مُكرِّر، وتكون `done` في النتيجة الأخيرة `false` (أي أن المُكرِّر قادر على إنتاج المزيد من القيم) ولكن لا حاجة لمزيد من القيم، سيتم استدعاء التابع `return` إذا كان موجودًا. يمكن أن يحدث هذا، على سبيل المثال، إذا تمت مواجهة `break` أو `return` في حلقة `for...of`، أو إذا تم ربط جميع المعرفات بالفعل في تفكيك مصفوفة.

```js
const obj = {
  [Symbol.iterator]() {
    let i = 0;
    return {
      next() {
        i++;
        console.log("Returning", i);
        if (i === 3) return { done: true, value: i };
        return { done: false, value: i };
      },
      return() {
        console.log("Closing");
        return { done: true };
      },
    };
  },
};

const [a] = obj;
// Returning 1
// Closing

const [b, c, d] = obj;
// Returning 1
// Returning 2
// Returning 3
// تم الوصول بالفعل إلى النهاية (الاستدعاء الأخير أرجع `done: true`)،
// لذلك لا يتم استدعاء `return`
console.log([b, c, d]); // [1, 2, undefined]; القيمة المرتبطة بـ `done: true` لا يمكن الوصول إليها

for (const b of obj) {
  break;
}
// Returning 1
// Closing
```

حلقة [`for await...of`](/en-US/docs/Web/JavaScript/Reference/Statements/for-await...of) و [`yield*`](/en-US/docs/Web/JavaScript/Reference/Operators/yield*) في [الدوال المولِّدة غير المتزامنة](/en-US/docs/Web/JavaScript/Reference/Statements/async_function*) (وليس [الدوال المولِّدة المتزامنة](/en-US/docs/Web/JavaScript/Reference/Statements/function*)) هي الطرق الوحيدة للتفاعل مع الكائنات القابلة للتكرار غير المتزامنة. استخدام `for...of`، نشر المصفوفات، إلخ. على كائن قابل للتكرار غير متزامن ليس أيضًا قابلاً للتكرار متزامنًا (أي لديه `[Symbol.asyncIterator]()` ولكن ليس لديه `[Symbol.iterator]()`) سيؤدي إلى إلقاء TypeError: x is not iterable.

## معالجة الأخطاء (Error handling)

نظرًا لأن التكرار يتضمن نقل التحكم ذهابًا وإيابًا بين المُكرِّر والمستهلك، تحدث معالجة الأخطاء في كلا الاتجاهين: كيف يتعامل المستهلك مع الأخطاء التي يلقيها المُكرِّر، وكيف يتعامل المُكرِّر مع الأخطاء التي يلقيها المستهلك. عند استخدام إحدى طرق التكرار المدمجة، قد تلقي اللغة أيضًا أخطاء لأن الكائن القابل للتكرار يكسر بعض {{Glossary("invariant", "الثوابت")}}. سنصف كيف تولد الصيغ المدمجة الأخطاء وتتعامل معها، والتي يمكن استخدامها كدليل إرشادي للكود الخاص بك إذا كنت تتقدم في المُكرِّر يدويًا.

### الكائنات القابلة للتكرار غير جيدة التكوين (Non-well-formed iterables)

قد تحدث أخطاء عند الحصول على المُكرِّر من الكائن القابل للتكرار. الثابت اللغوي المفروض هنا هو أن الكائن القابل للتكرار يجب أن ينتج مُكرِّرًا صالحًا:

- لديه تابع `[Symbol.iterator]()` قابل للاستدعاء.
- التابع `[Symbol.iterator]()` يُرجع كائنًا.
- الكائن الذي يتم إرجاعه بواسطة `[Symbol.iterator]()` لديه تابع `next()` قابل للاستدعاء.

عند استخدام صيغة مدمجة لبدء التكرار على كائن قابل للتكرار غير جيد التكوين، يتم إلقاء TypeError.

```js example-bad
const nonWellFormedIterable = { [Symbol.iterator]: 1 };
[...nonWellFormedIterable]; // TypeError: nonWellFormedIterable is not iterable
nonWellFormedIterable[Symbol.iterator] = () => 1;
[...nonWellFormedIterable]; // TypeError: [Symbol.iterator]() returned a non-object value
nonWellFormedIterable[Symbol.iterator] = () => ({});
[...nonWellFormedIterable]; // TypeError: nonWellFormedIterable[Symbol.iterator]().next is not a function
```

بالنسبة للكائنات القابلة للتكرار غير المتزامنة، إذا كانت قيمة الخاصية `[Symbol.asyncIterator]()` هي `undefined` أو `null`، فإن JavaScript تعود لاستخدام الخاصية `[Symbol.iterator]` بدلاً من ذلك (وتغلف المُكرِّر الناتج في مُكرِّر غير متزامن عن طريق [إعادة توجيه](#forwarding_errors) التوابع). خلاف ذلك، يجب أن تتوافق الخاصية `[Symbol.asyncIterator]` مع الثوابت المذكورة أعلاه أيضًا.

يمكن منع هذا النوع من الأخطاء عن طريق التحقق أولاً من الكائن القابل للتكرار قبل محاولة التكرار عليه. ومع ذلك، هذا نادر الحدوث لأنك عادةً تعرف نوع الكائن الذي تتكرر عليه. إذا كنت تتلقى هذا الكائن القابل للتكرار من كود آخر، فيجب عليك فقط ترك الخطأ ينتشر إلى المستدعي حتى يعرف أنه تم توفير إدخال غير صالح.

### الأخطاء أثناء التكرار (Errors during iteration)

تحدث معظم الأخطاء عند التقدم في المُكرِّر (استدعاء `next()`). الثابت اللغوي المفروض هنا هو أن التابع `next()` يجب أن يُرجع كائنًا (بالنسبة للمُكرِّرات غير المتزامنة، كائنًا بعد انتظار `await`). خلاف ذلك، يتم إلقاء TypeError.

إذا تم كسر الثابت أو ألقى التابع `next()` خطأ (بالنسبة للمُكرِّرات غير المتزامنة، قد يُرجع أيضًا Promise مرفوضًا)، يتم نشر الخطأ إلى المستدعي. بالنسبة للصيغ المدمجة، يتم إحباط التكرار قيد التقدم دون إعادة المحاولة أو التنظيف (على افتراض أنه إذا ألقى التابع `next()` الخطأ، فإنه قد قام بالتنظيف بالفعل). إذا كنت تستدعي `next()` يدويًا، يمكنك التقاط الخطأ وإعادة محاولة استدعاء `next()`، ولكن بشكل عام يجب أن تفترض أن المُكرِّر مغلق بالفعل.

إذا قرر المستدعي الخروج من التكرار لأي سبب آخر غير الأخطاء في الفقرة السابقة، مثل عندما يدخل في حالة خطأ في الكود الخاص به (على سبيل المثال، أثناء معالجة قيمة غير صالحة ينتجها المُكرِّر)، فيجب عليه استدعاء التابع `return()` على المُكرِّر، إذا كان موجودًا. هذا يسمح للمُكرِّر بإجراء أي تنظيف. يتم استدعاء التابع `return()` فقط للخروج المبكر — إذا أرجع `next()` القيمة `done: true`، فلن يتم استدعاء التابع `return()`، على افتراض أن المُكرِّر قد قام بالتنظيف بالفعل.

قد يكون التابع `return()` غير صالح أيضًا! تفرض اللغة أيضًا أن التابع `return()` يجب أن يُرجع كائنًا وتلقي TypeError خلاف ذلك. إذا ألقى التابع `return()` خطأ، يتم نشر الخطأ إلى المستدعي. ومع ذلك، إذا تم استدعاء التابع `return()` لأن المستدعي واجه خطأ في الكود الخاص به، فإن هذا الخطأ يتجاوز الخطأ الذي ألقاه التابع `return()`.

عادةً ما يطبق المستدعي معالجة الأخطاء بهذا الشكل:

```js
try {
  for (const value of iterable) {
    // …
  }
} catch (e) {
  // Handle the error
}
```

ستكون `catch` قادرة على التقاط الأخطاء التي يتم إلقاؤها عندما لا يكون `iterable` كائنًا صالحًا للتكرار، وعندما يلقي `next()` خطأ، وعندما يلقي `return()` خطأ (إذا خرجت حلقة `for` مبكرًا)، وعندما يلقي جسم حلقة `for` خطأ.

يتم تنفيذ معظم المُكرِّرات باستخدام دوال مولِّدة، لذلك سنوضح كيف تتعامل الدوال المولِّدة عادةً مع الأخطاء:

```js
function* gen() {
  try {
    yield doSomething();
    yield doSomethingElse();
  } finally {
    cleanup();
  }
}
```

عدم وجود `catch` هنا يؤدي إلى انتشار الأخطاء التي يلقيها `doSomething()` أو `doSomethingElse()` إلى مستدعي `gen`. إذا تم التقاط هذه الأخطاء داخل الدالة المولِّدة (وهو أمر يُنصح به أيضًا)، يمكن للدالة المولِّدة أن تقرر الاستمرار في إنتاج القيم أو الخروج مبكرًا. ومع ذلك، فإن كتلة `finally` ضرورية للمولدات التي تبقي الموارد مفتوحة. من المضمون تشغيل كتلة `finally`، إما عند استدعاء `next()` الأخير أو عند استدعاء `return()`.

### إعادة توجيه الأخطاء (Forwarding errors)

بعض الصيغ المدمجة تغلف مُكرِّرًا في مُكرِّر آخر. وهي تشمل المُكرِّر الذي ينتجه {{jsxref("Iterator.from()")}}، [توابع المساعدة للمُكرِّر](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Iterator#iterator_helper_methods) (`map()`، `filter()`، `take()`، `drop()`، و `flatMap()`)، [`yield*`](/en-US/docs/Web/JavaScript/Reference/Operators/yield*)، ومغلف مخفي عند استخدام التكرار غير المتزامن (`for await...of`، `Array.fromAsync`) على المُكرِّرات المتزامنة. يكون المُكرِّر المغلف مسؤولاً بعد ذلك عن إعادة توجيه الأخطاء بين المُكرِّر الداخلي والمستدعي.

- جميع المُكرِّرات المغلفة تعيد توجيه التابع `next()` للمُكرِّر الداخلي مباشرةً، بما في ذلك قيمته المرجعة والأخطاء التي يلقيها.
- تعيد المُكرِّرات المغلفة بشكل عام توجيه التابع `return()` للمُكرِّر الداخلي مباشرةً. إذا لم يكن التابع `return()` موجودًا على المُكرِّر الداخلي، فإنه يُرجع `{ done: true, value: undefined }` بدلاً من ذلك. في حالة مساعدات المُكرِّر: إذا لم يتم استدعاء التابع `next()` لمساعد المُكرِّر، بعد محاولة استدعاء `return()` على المُكرِّر الداخلي، يُرجع المُكرِّر الحالي دائمًا `{ done: true, value: undefined }`. هذا يتوافق مع الدوال المولِّدة حيث لم يدخل التنفيذ بعد في تعبير `yield*`.
- `yield*` هي الصيغة المدمجة الوحيدة التي تعيد توجيه التابع `throw()` للمُكرِّر الداخلي. للحصول على معلومات حول كيفية إعادة توجيه `yield*` للتابعين `return()` و `throw()`، راجع مرجعه الخاص.

## أمثلة (Examples)

### كائنات قابلة للتكرار مُعرَّفة من قبل المستخدم (User-defined iterables)

يمكنك إنشاء كائنات قابلة للتكرار خاصة بك بهذا الشكل:

```js
const myIterable = {
  *[Symbol.iterator]() {
    yield 1;
    yield 2;
    yield 3;
  },
};

console.log([...myIterable]); // [1, 2, 3]
```

### مُكرِّر أساسي (Basic iterator)

المُكرِّرات ذات حالة بطبيعتها. إذا لم تقم بتعريفها [كدالة مولِّدة](/en-US/docs/Web/JavaScript/Reference/Statements/function*) (كما يوضح المثال أعلاه)، فمن المحتمل أنك سترغب في تغليف الحالة في إغلاق (closure).

```js
function makeIterator(array) {
  let nextIndex = 0;
  return {
    next() {
      return nextIndex < array.length
        ? {
            value: array[nextIndex++],
            done: false,
          }
        : {
            done: true,
          };
    },
  };
}

const it = makeIterator(["yo", "ya"]);

console.log(it.next().value); // 'yo'
console.log(it.next().value); // 'ya'
console.log(it.next().done); // true
```

### مُكرِّر لا نهائي (Infinite iterator)

````js
function idMaker() {
  let index = 0;
  return {
    next() {
      return {
        value: index++,
        done: false,
      };
    },
  };
}

const it = idMaker();

console.log(it.next().value); // 0
console.log(it.next().value); // 1
console.log(it.next().value); // 2
// …```

### تعريف كائن قابل للتكرار باستخدام دالة مولِّدة (Defining an iterable with a generator)

```js
function* makeGenerator(array) {
  let nextIndex = 0;
  while (nextIndex < array.length) {
    yield array[nextIndex++];
  }
}

const gen = makeGenerator(["yo", "ya"]);

console.log(gen.next().value); // 'yo'
console.log(gen.next().value); // 'ya'
console.log(gen.next().done); // true

function* idMaker() {
  let index = 0;
  while (true) {
    yield index++;
  }
}

const it = idMaker();

console.log(it.next().value); // 0
console.log(it.next().value); // 1
console.log(it.next().value); // 2
// …
````

### تعريف كائن قابل للتكرار باستخدام صنف (Defining an iterable with a class)

يمكن القيام بتغليف الحالة باستخدام [الحقول الخاصة](/en-US/docs/Web/JavaScript/Reference/Classes/Private_elements) أيضًا.

```js
class SimpleClass {
  #data;

  constructor(data) {
    this.#data = data;
  }

  [Symbol.iterator]() {
    // استخدم فهرسًا جديدًا لكل مُكرِّر. هذا يجعل التكرارات المتعددة
    // على الكائن القابل للتكرار آمنة للحالات غير البسيطة،
    // مثل استخدام break أو التكرار المتداخل على نفس الكائن القابل للتكرار.
    let index = 0;

    return {
      // ملاحظة: استخدام دالة سهمية (arrow function) يسمح لـ `this` بالإشارة إلى
      // `this` الخاصة بـ `[Symbol.iterator]()` بدلاً من `next()`
      next: () => {
        if (index >= this.#data.length) {
          return { done: true };
        }
        return { value: this.#data[index++], done: false };
      },
    };
  }
}

const simple = new SimpleClass([1, 2, 3, 4, 5]);

for (const val of simple) {
  console.log(val); // 1 2 3 4 5
}
```

### تجاوز الكائنات القابلة للتكرار المدمجة (Overriding built-in iterables)

على سبيل المثال، {{jsxref("String")}} هو كائن قابل للتكرار مدمج:

```js
const someString = "hi";
console.log(typeof someString[Symbol.iterator]); // "function"
```

[المُكرِّر الافتراضي](/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/Symbol.iterator) لـ `String` يُرجع نقاط الكود (code points) الخاصة بالنص واحدة تلو الأخرى:

```js
const iterator = someString[Symbol.iterator]();
console.log(`${iterator}`); // "[object String Iterator]"

console.log(iterator.next()); // { value: "h", done: false }
console.log(iterator.next()); // { value: "i", done: false }
console.log(iterator.next()); // { value: undefined, done: true }
```

يمكنك إعادة تعريف سلوك التكرار عن طريق توفير `[Symbol.iterator]()` الخاص بنا:

```js
// نحتاج إلى إنشاء كائن String بشكل صريح لتجنب التحويل التلقائي (auto-boxing)
const someString = new String("hi");

someString[Symbol.iterator] = function () {
  return {
    // هذا هو كائن المُكرِّر، يُرجع عنصرًا واحدًا (النص "bye")
    next() {
      return this._first
        ? { value: "bye", done: (this._first = false) }
        : { done: true };
    },
    _first: true,
  };
};
```

لاحظ كيف تؤثر إعادة تعريف `[Symbol.iterator]()` على سلوك التركيبات المدمجة التي تستخدم بروتوكول التكرار:

```js
console.log([...someString]); // ["bye"]
console.log(`${someString}`); // "hi"
```

### التعديلات المتزامنة أثناء التكرار (Concurrent modifications when iterating)

تقريبًا جميع الكائنات القابلة للتكرار لها نفس الدلالة الأساسية: لا تقوم بنسخ البيانات في وقت بدء التكرار. بدلاً من ذلك، تحتفظ بمؤشر وتحركه. لذلك، إذا قمت بإضافة أو حذف أو تعديل عناصر في المجموعة أثناء التكرار عليها، فقد تغير عن غير قصد ما إذا كانت العناصر الأخرى _غير المتغيرة_ في المجموعة ستتم زيارتها. هذا مشابه جدًا لكيفية عمل [توابع المصفوفات التكرارية](/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array#mutating_initial_array_in_iterative_methods).

لنأخذ الحالة التالية باستخدام {{domxref("URLSearchParams")}}:

```js
const searchParams = new URLSearchParams(
  "deleteme1=value1&key2=value2&key3=value3"
);

// حذف المفاتيح غير المرغوب فيها
for (const [key, value] of searchParams) {
  console.log(key);
  if (key.startsWith("deleteme")) {
    searchParams.delete(key);
  }
}

// الناتج:
// deleteme1
// key3
```

لاحظ كيف أنه لم يطبع `key2` أبدًا. هذا لأن `URLSearchParams` هي في الأساس قائمة من أزواج المفاتيح والقيم. عندما تتم زيارة `deleteme1` وحذفها، يتم إزاحة جميع الإدخالات الأخرى إلى اليسار بمقدار واحد، لذلك يحتل `key2` الموضع الذي كان يحتله `deleteme1`، وعندما ينتقل المؤشر إلى المفتاح التالي، فإنه يصل إلى `key3`.

تتجنب بعض تطبيقات الكائنات القابلة للتكرار هذه المشكلة عن طريق تعيين قيم "شاهدة" (tombstone) لتجنب إزاحة القيم المتبقية. لننظر في كود مشابه باستخدام `Map`:

```js
const myMap = new Map([
  ["deleteme1", "value1"],
  ["key2", "value2"],
  ["key3", "value3"],
]);

for (const [key, value] of myMap) {
  console.log(key);
  if (key.startsWith("deleteme")) {
    myMap.delete(key);
  }
}

// الناتج:
// deleteme1
// key2
// key3
```

لاحظ كيف أنه يطبع جميع المفاتيح. هذا لأن `Map` لا تقوم بإزاحة المفاتيح المتبقية عند حذف أحدها. إذا كنت ترغب في تنفيذ شيء مشابه، فإليك كيف قد يبدو:

```js
const tombstone = Symbol("tombstone");

class MyIterable {
  #data;
  constructor(data) {
    this.#data = data;
  }
  delete(deletedKey) {
    for (let i = 0; i < this.#data.length; i++) {
      if (this.#data[i][0] === deletedKey) {
        this.#data[i] = tombstone;
        return true;
      }
    }
    return false;
  }
  *[Symbol.iterator]() {
    for (const data of this.#data) {
      if (data !== tombstone) {
        yield data;
      }
    }
  }
}

const myIterable = new MyIterable([
  ["deleteme1", "value1"],
  ["key2", "value2"],
  ["key3", "value3"],
]);
for (const [key, value] of myIterable) {
  console.log(key);
  if (key.startsWith("deleteme")) {
    myIterable.delete(key);
  }
}
```

> [!WARNING]
> التعديلات المتزامنة، بشكل عام، عرضة للأخطاء ومربكة جدًا. ما لم تكن تعرف بدقة كيف يتم تنفيذ الكائن القابل للتكرار، فمن الأفضل تجنب تعديل المجموعة أثناء التكرار عليها.
