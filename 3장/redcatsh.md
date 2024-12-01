### 6주차 JavaScript TIL

### 📣 주제

1. this

---

## this 란?

> 대부분의 객체지향 언어에서 this는 클래스로 생성한 인스턴스 객체를 의미한다. 클래스에서만 사용할 수 있기에 혼란의 여지가 없지만 자바스크립트에서의 this는 어디서든 사용 가능하다. 함수와 객체(메서드)의 구분이 느슨한 자바스크립트에서 this는 실질적으로 이 둘을 구분하는 거의 유일한 기능이며, **this는 함수를 어떤 방식으로 호출하느냐에 따라 값이 달라진다.**

---

###  1. 전역 공간에서의 this

전역 공간에서 this는 전역 객체를 가리킨다. 전역 객체는 자바스크립트 런타임 환경에 따라 다르다.
- 브라우저 환경: `window`
- Node.js: `global`

#### 전역 공간에서의 this(브라우저 환경)
```js
console.log(this === window)  // true
```
#### 전역 공간에서의 this(Node.js 환경)
```js
console.log(this === global)  // true
```
---

✅ **자바스크립트의 모든 변수는 특정 객체의 프로퍼티로서 동작한다.** 따라서 전역 변수를 선언하면 자바스크립트 엔진은 이를 전역객체의 프로퍼티로 할당한다.

#### 💻 예제 코드:

```js
var a = 1;
console.log(a); // 1
delete window.a; // false
console.log(a, window.a, this.a) // 1 1 1
```

```js
window.c = 3;
delete window.c; // true
console.log(c, window.c, this.c) // Uncaught ReferenceError: c is not defined
```

처음부터 전역객체의 프로퍼티로 할당한 경우에는 삭제가 되는 반면, 전역변수로 선언한 경우에는 삭제가 되지 않는다. 이는 사용자가 의도치 않게 삭제하는 것을 방지하기 위한 나름의 방어 전략으로 해석되며, 전역변수를 선언하면 **자바스크립트 엔진이 해당 프로퍼티의 configurable 속성(변경 및 삭제 가능성)을 false로 정의**하는 것이다.

---

✅ 전역변수 선언과 전역객체의 프로퍼티 할당에서 **delete 명령**을 한다면?

#### 💻 예제 코드:

```js
var a = 1;
console.log(a); // 1
console.log(window.a); // 1
console.log(this.a) // 1
```
---

###  2. 메서드로서 호출할 때 그 메서드 내부에서의 this

> 함수를 실행하는 가장 일반적인 방법 두 가지는 함수로서 호출하는 경우와 메서드로서 호출하는 경우이다.<br/><br/> 
> **함수**: 그 자체로 독립적인 기능 수행<br/>
> **메서드**: 자신을 호출한 대상 객체에 관한 동작 수행

#### 함수로서 호출, 메서드로서 호출:

```js
var func = function (x) {
  console.log(this, x);
};
func(1);  // window { ... } 1

var obj = {
  method: func
};
obj.method(2); // { method: f } 2
```
'함수로서 호출'과 '메서드로서 호출' 구분 방법은 **함수 앞에 점(.)이 있는지 여부 판단이다. `func(1);`은 앞에 점이 없으니 함수로 호출한 것이고, `obj.method(2);`는 점이 있으니 메서드로서 호출한 것이다. 이는 대괄호 표기법에 따른 경우에도 똑같이 적용된다. (`obj['method'](2)`)

---

✅ **메서드 내부에서의 this**

> `this`에는 호출한 주체에 대한 정보가 담긴다. 어떤 함수를 메서드로서 호출하는 경우 호출 주제는 함수명(프로퍼티명) 앞의 객체이다.

---

###  3. 함수로서 호출할 떄 그 함수 내부에서의 this

✅ **함수 내부에서의 this**
> 함수로서 호출하는 것은 호출 주체를 명시하지 않고 개발자가 코드에 직접 관여해서 실행한 것이기 때문에 호출 주체의 정보를 알 수 없다. 따라서 this가 지정되지 않기 때문에 함수에서의 this는 전역 객체를 가리킨다.

✅ **메서드 내부함수에서의 this**
> this 바인딩에 관해서는 함수를 실행하는 당시의 주변 환경(메서드 내부인지, 함수 내부인지 등)은 중요하지 않고 해당 함수를 호출하는 구문 앞에 점 또는 대괄호 표기가 있는지 없는지가 관건이다. 

✅ **ES6에서 메서드 내부함수에서의 this를 우회하는 방법**
```js
var obj = {
  outer: function () {
    console.log(this)  // (1) { outer: f }
    var innerFunc = () => {
      console.log(this);  // (2) { outer: f }
    };
    innerFunc();
  }
};
obj.outer();
```
ES6에서는 함수 내부에서 this가 전역객체를 바라보는 문제를 보완하고자 this를 바인딩하지 않는 화살표 함수를 새로 도입했다. 실행 컨텍스트를 생성할 때 this 바인딩 과정 자체가 빠지고 상위 스코프의 this를 그대로 활용할 수 있다. 

---

###  4. 콜백 함수 호출 시 그 함수 내부에서의 this

 > 콜백 함수도 함수이기 때문에 기본적으로 this가 전역객체를 참조하지만, 콜백 함수의 제어권을 가지는 함수(메서드)가 콜백 함수에 별도로 this가 될 대상을 지정한 경우에는 그 대상을 참조한다.

---

###  5. 생성자 함수 내부에서의 this

 생성자 함수는 어떤 공통된 성질을 지니는 객체들을 생성하는 데 사용되는 함수이다. 생성자란 구체적인 인스턴스를 만들기 위한 일종의 틀이다. 자바스크립트는 함수에 생성자로서의 역할을 함께 부여했다. `new` 명령어와 함께 함수를 호출하면 해당 함수가 생성자로서 동작한다. 

 > 1. 생성자 함수 호출  
 >2. 생성자의 prototype 프로퍼티를 참조하는 __proto__라는 프로퍼티가 있는 객체(인스턴스) 만듦
 >3. 미리 준비된 공통 속성 및 개성을 해당 객체(this)에 부여 
 >4. 구체적인 인스턴스 만들어짐

#### 💻 예제 코드:
```js
var Cat = function (name, age) {    // Cat이라는 변수에 익명 함수 할당
  this.bark = '야옹';
  this.name = name;
  this.age = age;
};
var choco = new Cat('Choco', 7);  // 생성자 함수 내부에서의 this: choco 인스턴스를 가리킴
var nabi = new Cat('Nabi', 5);    // 생성자 함수 내부에서의 this: nabi 인스턴스를 가리킴
console.log(choco, nabi);

/* 결과
Cat { bark: '야옹', name: 'Choco', age: 7}
Cat { bark: '야옹', name: 'Nabi', age: 5}
*/
```
---

## 명시적으로 this를 바인딩하는 방법

---

## call 메서드

> 메서드의 호출 주체인 함수를 즉시 실행하도록 하는 명령

- call 메서드의 첫번째 인자를 this로 바인딩하고 이후의 인자들을 호출할 함수의 매개변수로 함
- 임의의 객체를 this로 지정할 수 있음

```js
var func = function (a, b, c) {
  console.log(this, a, b, c);
};

func(1, 2, 3); // window{...} 1 2 3  (그냥 호출하면 window 객체 호출)
func.call({ x: 1 }, 4, 5, 6); // { x: 1 } 4 5 6  (call 이용해서 임의의 객체를 지정)
```

> 그냥 실행하면 전역객체를 호출하지만 call 메서드를 이용하면 임의의 객체를 지정 가능

---

## apply 메서드

> call 메서드와 기능적으로 동일

- call 메서드는 첫번째 인자를 제외한 나머지 모든 인자들을 호출할 함수의 매개변수로 지정
- apply 메서드는 두번째 인자를 배열로 받아 그 배열의 요소들을 호출할 함수의 매개변수로 지정

```js
var func = function (a, b, c) {
  console.log(this, a, b, c);
};
func.apply({ x: 1 }, [4, 5, 6]);

var obj = {
  a: 1,
  method: function (x, y) {
    console.log(this.a, x, y);
  },
};
obj.method.apply({ a: 4 }, [5, 6]); // 4 5 6
```

---

## call / apply 메서드의 활용

### ✅ 유사배열객체에 배열 메서드 적용

- 객체에는 배열 메서드를 직접 적용할 수 없지만 배열의 구조와 유사한 객체의 경우 call 또는 apply 메서드를 이용해 배열 메서드를 차용할 수 있음
- 함수 내부에서 접근 가능한 arguments 객체는 유사배열객체이므로 배열로 전환해서 활용 가능
- querySelectorAll, getElementsByClass 등의 Node 선택자로 선택한 결과인 NodeList도 배열로 전환해서 활용 가능
- 배열처럼 인덱스와 length 프로퍼티를 지니는 문자열도 동일 **(단 `push`, `pop`, `shift`, `unshift`, `splice` 등 원본 문자열에 변경을 가하는 메서드는 에러를 던지며,
  `concat`처럼 대상이 반드시 배열이어야 하는 경우에는 에러는 X, 하지만 제대로 된 결과는 얻을 수 없음)**

```js
var obj = {
  0: "a",
  1: "b",
  2: "c",
  length: 3,
};
Array.prototype.push.call(obj, "d");
console.log(obj); // { 0: 'a', 1: 'b', 2: 'c', 3: 'd', length: 4 }

var arr = Array.prototype.slice.call(obj);
console.log(arr); // [ 'a', 'b', 'c', 'd' ]
```

#### ❗`length`를 명시적으로 설정하는 이유

> `Array.prototype.push`는 `this.length`를 기반으로 작동한다.
> `obj`의 초기 상태에 `length` 속성이 없는 상태에서 `Array.prototype.push`를 호출하면 `this.length`는 `0`으로 간주되어 `obj[0]`이 `d`로 변경된다.

> ES6에서는 유사배열객체 또는 순회 가능한 모든 종류의 데이터 타입을 배열로 전환하는 `Array.from` 메서드를 새로 도입함

```js
var obj = {
  0: "a",
  1: "b",
  2: "c",
  length: 3,
};
var arr = Array.from(obj);
console.log(arr); // [ 'a', 'b', 'c' ]
```

### ✅ 생성자 내부에서 다른 생성자 호출

- 생성자 내부에 다른 생성자아ㅘ 공통된 내용이 있을 경우 call 또는 apply를 이용해 다른 생성자를 호출하면 반복을 줄일 수 있음

### ✅ 여러 인수를 묶어 하나의 배열로 전달하고 싶을 때 apply 사용

- 여러 개의 인수를 받는 메서드에게 하나의 배열로 인수들을 전달하고 싶을 떄 apply 메서드를 사용하면 좋지만 ES6에서는 **spread 연산자** 를 사용하면 apply를 적용하는 것보다 더욱 간편함

```js
const numbers = [10, 20, 3, 16, 45];
const max = Math.max(...numbers);
const min = Math.min(...numbers);
console.log(max, min); // 45 3
```

> 🚨 call/apply 메서드는 명시적으로 별도의 this를 바인딩하면서 함수 또는 메서드를 실행하는 훌륭한 방법이지만 이로 인해 this를 예측하기 어렵게 만들어 코드 해석을 방해한다는 단점 존재하지만 ES5 이하 환경에서는 대안이 없어 광범위하게 활용됨

---

## bind 메서드

> ES5에서 추가된 기능으로, call과 비슷하지만 즉시 호출하지는 않고 넘겨 받은 this 및 인수들을 바탕으로 새로운 함수를 반환하기만 하는 메서드

- 함수에 this를 미리 적용하는 것과 부분 적용 함수를 구현하는 두 가지 목적을 지님

```js
var func = function (a, b, c, d) {
  console.log(this, a, b, c, d);
};
func(1, 2, 3, 4); // window{ ... } 1 2 3 4

var bindFunc1 = func.bind({ x: 1 }); // this만을 지정
bindFunc1(5, 6, 7, 8); // { x: 1 } 5 6 7 8

var bindFunc2 = func.bind({ x: 1 }, 4, 5); // this 지정과 함께 부분 적용 함수 구현
bindFunc2(6, 7); // { x: 1 } 4 5 6 7
bindFunc2(8, 9); // { x: 1 } 4 5 8 9
```

### ✅ 상위 컨텍스트의 this를 콜백 함수에 전달하기

- 콜백 함수 내에서의 this에 관여하는 함수 또는 메서드에 대해서 bind 메서드를 이용하면 this 값을 사용자가 원하는대로 바꿀 수 있음

---

## 화살표 함수의 예외 사항

> ES6에 도입된 화살표 함수는 실행 컨텍스트 생성 시 this를 바인딩하는 과정이 제외됨. 이 함수 내부에는 this가 아예 없고, 접근 시 스코프체인상 가장 가까운 this에 접근하게 됨

```js
var obj = {
  outer: function () {
    console.log(this);
    var innerFunc = () => {
      console.log(this);
    };
    innerFunc();
  },
};
obj.outer();
```

> 이렇게 하면 별도의 변수로 this를 우회하거나 call/apply/bind를 적용할 필요가 없어 더욱 간결하고 편리함

---

## 콜백 함수 내에서의 this

> 콜백 함수를 인자로 받는 메서드 중 일부는 추가로 this로 지정할 객체(thisArg)를 인자로 지정할 수 있는 경우가 있는데, 이러한 메서드의 thisArg 값을 지정하면 콜백 함수 내부에서 this 값을 원하는 대로 변경할 수 있음

- 내부 요소에 대해 같은 동작을 반복 수행해야 하는 **배열 메서드**에 많이 포진되어 있음
- `Set`, `Map` 등의 메서드에도 일부 존재
