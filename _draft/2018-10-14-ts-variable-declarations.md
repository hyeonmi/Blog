---
layout: post
title:  "[Handbook] 변수 선언(Variable Declarations)"
date:   2018-10-14 17:20:00 +0900
tags: [typescirpt, ts]
---

# 변수 선언
## var 선언
자바스크립트에 익숙한 사람에게는 꼭 필요한 내용이 아니라서 간단하게 특징만 짚고 넘어갑니다.

### 함수 단위의 스코프
이전의 자바스크립트의 var는 다른 언어와 다르게 블록 단위로 변수의 스코프가 정해지지 않습니다.
함수, 객체, 모듈, 네임스페이스, 글로벌 스코프가 존재합니다.

```js
function f(shouldInitialize: boolean) {
    if (shouldInitialize) {
        var x = 10;
    }

    return x;
}

f(true);  // returns '10'
f(false); // returns 'undefined'
```
예제의 경우처럼 x 변수가 if 블록 안에 선언되었음에도 x값이 할당됩니다.

### 변수 캡처의 특이점

```js
for (var i = 0; i < 10; i++) {
    setTimeout(function() { console.log(i); }, 100 * i);
}

//결과는 10이 10번 찍힌다.
```
이런 현상이 발생하는 이유는 for loop가 실행이 끝난 후에 setTimeout가 실행되는데
그때 캡쳐된 i 값이 10이기 때문입니다.

1 ~ 10 순차적으로 출력하려면 반복마다 i를 캡쳐하는 즉시실행함수(IIFE:Immediately Invoked Function Expression)사용 하면됩니다.
```js
for (var i = 0; i < 10; i++) {
    // 현재 상태의 i를 캡쳐
    // 현재 값과 함수가 호출 된다.
    (function(i) {
        setTimeout(function() { console.log(i); }, 100 * i);
    })(i);
}
```

## let 선언
대부분 var와 유사하지만 가장 큰 차이점은 let은 블록 스코프를 가진다는 점입니다.
let으로 선언하게 되면 렉스컬 스코프(lexical-scope), 블록 스코프(block-scope)로 불리는 스코프를 사용하게 됩니다.
블록 스코프는 var와 다르게 가까운 블록안의 스코프를 가집니다.

```js
function f(input: boolean) {
    let a = 100;

    if (input) {
        // 'a' 참조 가능
        let b = a + 1;
        return b;
    }

    // Error: 'b'는 여기에 존재하지 않음
    return b;
}
```
또한 블록 스코프를 벗어나서 호출하거나 변수가 선언되기 전에 호출 하면 에러를 발생시킵니다.

```js
try {
    throw "oh no!";
}
catch (e) {
    console.log("Oh well.");
}

// Error: 'e' doesn't exist here
console.log(e);


a++; // illegal to use 'a' before it's declared;
let a;
```

### 재선언과 쉐도잉
var의 경우 이미 선언된 변수를 중첩해서 선언할수 있어서 덮어쓸수 있습니다.
하지만 let은 재선언 할수 없고 그럴 경우 에러를 발생합니다.

```js
let x = 10;
let x = 20; // error: 같은 스코프 내에서 재선언 할수없다.
```

중첩된 스코프 안에 동일한 이름으로 새로 선언하는 행위를 쉐도잉이라고 부릅니다.
가독성에 좋지 않기 때문에 지양하는게 좋습니다.

```js
function sumMatrix(matrix: number[][]) {
    let sum = 0;
    for (let i = 0; i < matrix.length; i++) {
        var currentRow = matrix[i];
        for (let i = 0; i < currentRow.length; i++) {
            sum += currentRow[i];
        }
    }

    return sum;
}
```

###  블록 스코프의 변수 캡쳐
스코프가 실행될때마다 "환경(environment)" 변수를 생성하는데 실행을 마친후에도
매번 스코프가 실행될때 직관적으로 더 나은 방법은 그것이 "환경"변수로 생성되는 것입니다.
환경과 캡쳐된 변수는 그 스코프 실행이 끝난 후에도 존재할 수 있습니다.

```js
function theCityThatAlwaysSleeps() {
    let getCity;

    if (true) {
        let city = "Seattle";
        getCity = function() {
            return city;
        }
    }

    return getCity();
}
```

`city` 변수의 경우 if 블록의 실행이 끝났지만 접근이 가능합니다.
앞서 봤던 setTimeout 예제를 let으로 변경하면 IIFE를 사용하지 않아도 됩니다.

```js
for (let i = 0; i < 10 ; i++) {
    setTimeout(function() { console.log(i); }, 100 * i);
}
```

## const 선언
const 이름에서 의미하듯 한번 선언하면 변경할 수 없습니다.
참조는 변경할수 없지만 참조하는 객체의 값은 변경할 수 있습니다.

```js
const numLivesForCat = 9;
const kitty = {
    name: "Aurora",
    numLives: numLivesForCat,
}

// Error
kitty = {
    name: "Danielle",
    numLives: numLivesForCat
};

// "okay"
kitty.name = "Rory";
kitty.name = "Kitty";
kitty.name = "Cat";
kitty.numLives--;
```
타입스크립트에서 객체 멤버를 redonly로 지정할 수 있습니다.
인터페이스장에서 자세히 설명되어 있습니다.

## let vs.const
두개의 선언 방식중에 어떤것을 사용할지 선택해야 합니다.
[최소 특권의 원칙](https://en.wikipedia.org/wiki/Principle_of_least_privilege)에 따르면 수정할 경우가 아니면 모두 const로 사용해야 합니다.

## 비구조화(destructuring)
ECMAScript 2015 특색인 destructuring를 타입스크립트에서 가지고 있습니다.
[MDN Destructuring](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)에 자세히 나와 있습니다.

### 배열 비구조화(Array destructuring)

```js
let input = [1, 2];
let [first, second] = input;
console.log(first); // outputs 1
console.log(second); // outputs 2

//swap
[first, second] = [second, first];

//remaining items
let [first, ...rest] = [1, 2, 3, 4];
console.log(first); // outputs 1
console.log(rest); // outputs [ 2, 3, 4 ]

//other elements
let [, second, , fourth] = [1, 2, 3, 4];

```

### 객체 비구조화(Object destructuring)
```js
let o = {
    a: "foo",
    b: 12,
    c: "bar"
};
let { a, b } = o;

//without declaration with surround
({ a, b } = { a: "baz", b: 101 });

let { a, ...passthrough } = o;
let total = passthrough.b + passthrough.c.length;

let { a, b }: { a: string, b: number } = o;
```

기본값이 undefined 이면 기본 값을 지정할수 있습니다.

```js
function keepWholeObject(wholeObject: { a: string, b?: number }) {
    let { a, b = 1001 } = wholeObject;
}
```
wholeObject는 a,b 프로퍼티를 가지고 있는데 b가 undefined경우 1001로 기본 값을 지정한예입니다.

### 함수 선언(Function declarations)
함수 선언에도 비구조화를 사용할수 있습니다.

```js
type C = { a: string, b?: number }
function f({ a, b }: C): void {
    // ...
}
```

트릭을 써서 기본값을 설정할 수 있습니다.

```js
function f({ a, b = 0 } = { a: "" }): void {
    // ...
}
f({ a: "yes" }); // ok, default b = 0
f(); // ok, default to { a: "" }, which then defaults b = 0
f({}); // error, 'a' is required if you supply an argument
```
깊은 중첩은 이해하기 어렵기때문에 최소한으로 단순하게 사용하는게 좋습니다.

### 스프레드(Spread)
스프레드 연산자는 비구조화의 반댓말입니다. 배열을 다른 배열로 객체를 다른 객체로 퍼트릴수 있습니다.

```js
let first = [1, 2];
let second = [3, 4];
let bothPlus = [0, ...first, ...second, 5];

// result
// [0, 1, 2, 3, 4, 5]
```

```js

let defaults = { food: "spicy", price: "$$", ambiance: "noisy" };
let search = { ...defaults, food: "rich" };

// result
// { food: "rich", price: "$$", ambiance: "noisy" }
```

스프레드 연산자의 제한 사항이 2가지가 있습니다.
첫번째, 스프레드시 객체가 가진 불변 속성만 포함합니다.
즉 함수가 포함된 객체를 스프레드하면 함수는 제외됩니다.

```js
class C {
  p = 12;
  m() {
  }
}
let c = new C();
let clone = { ...c };
clone.p; // ok
clone.m(); // error!
```
두번째, 타입스크립트 컴파일러는 일반함수의 매개 변수 스프레이드를 허용하지 않습니다.