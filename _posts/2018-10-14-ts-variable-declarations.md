---
layout: post
title:  "[Handbook] 변수 선언(Variable Declarations)"
date:   2018-10-14 17:20:00 +0900
categories: TypesScript
---

# 변수 선언
## var 선언
var는 자바스크립트에서 사용하는 전통적인 변수 선언이다.
함수 단위의 스코프를 가진다.

```js
function f() {
    var a = 10;
    return function g() {
        var b = a + 1;
        return b;
    }
}

var g = f();
g(); // returns '11'
```
위의 예제의 경우 f() 실행하면 리턴되는 g() 함수가 var = g 변수에 담기게 되고 g() 실행하게 되면 함수가 실행되서 11 값이 리턴된다.

### 스코프 규칙
다른 언어와 다르게 특이한 스코프 규칙이 있다.

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
예제의 경우처럼 x 변수가 if 블록 안에 선언되었음에도 x값이 할당 된다.
일부 사람들은 var 스코프는 함수 스코프라고 부른다.

이런 규칙은 사람들을 실수하게 하는 값은 이름으로 변수를 선언하는 경우 에러를 발생 시키게 한다.
for 중첩되서 i를 덮어 쓰는 경우 무한 루프에 빠질수 있다.

```js
function sumMatrix(matrix: number[][]) {
    var sum = 0;
    for (var i = 0; i < matrix.length; i++) {
        var currentRow = matrix[i];
        for (var i = 0; i < currentRow.length; i++) {
            sum += currentRow[i];
        }
    }

    return sum;
}
```
### variable capturing quirks

```js
for (var i = 0; i < 10; i++) {
    setTimeout(function() { console.log(i); }, 100 * i);
}
```
결과는 10만 10번 찍힌다.
setTimeout 안에 있는 i는 for문 안에 있는 같은 i를 참조 하기 때문이다.
1,2 ~ 10 연속으로 찍으려면 이런식으로 구현해야 한다.

```js
for (var i = 0; i < 10; i++) {
    // capture the current state of 'i'
    // by invoking a function with its current value
    (function(i) {
        setTimeout(function() { console.log(i); }, 100 * i);
    })(i);
}
```

## let 선언
var와 유사하지만 다른점은 block 스코프를 가진다.
선언되기 전에 호출 하면 에러를 발생 시킨다.


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

### 선언과 쉐도잉
var의 경우 이미 선언된 변수를 중첩해서 선언할수 있다. let은 중첩해서 선언할수 없다.

```js
let x = 10;
let x = 20; // error: 같은 스코프 내에서 재선언 할수 없
```

중첩된 스코프 안에 동일한 이름으로 새로 선언하는 행동을 쉐도잉이라고 부릅니다.
양날의 검이라 지양하는게 좋습니다.

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
안쪽 루프 i는 바깥 루프 i를 쉐도우하고 있기때문

###  블록 스코프의 변수 캡쳐


## const 선언