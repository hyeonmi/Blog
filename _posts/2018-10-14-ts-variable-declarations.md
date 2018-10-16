---
layout: post
title:  "[Handbook] 변수 선언(Variable Declarations)"
date:   2018-10-14 17:20:00 +0900
categories: TypesScript
---

# 변수 선언
## var 선언
자바스크립트에 익숙한 사람에게는 꼭 필요한 내용이 아니라서 간단하게 특징만 짚고 넘어간다.

### 함수 단위의 스코프
이전의 자바스크립트의 var는 다른 언어와 다르게 블록 단위로 변수의 스코프가 정해지지 않는다.
함수, 객체, 모듈, 네임스페이스, 글로벌 스코프가 존재한다.

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
예제의 경우처럼 x 변수가 if 블록 안에 선언되었음에도 x값이 할당된다.


### 변수 캡처의 특이점

```js
for (var i = 0; i < 10; i++) {
    setTimeout(function() { console.log(i); }, 100 * i);
}

//결과는 10이 10번 찍힌다.
```

IIFE(Immediately Invoked Function Expression)로 하면 i 값이 정상으로 출력된다.

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
대부분 var와 유사하지만 가장 큰 차이점은 let은 블록 스코프를 가진다.
블록 스코프를 벗어나서 호출하거나 변수가 선언되기 전에 호출 하면 에러를 발생시킨다.

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
var의 경우 이미 선언된 변수를 중첩해서 선언할수 있다.
하지만 let은 중첩해서 선언할수 없다. 에러가 발생한다.

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


## const 선언