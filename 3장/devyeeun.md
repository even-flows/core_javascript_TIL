# 3장 - this

## this

this는 자바스크립트에서 가장 혼란스럽고 어려운 개념 중 하나이다.  
자바스크립트에서의 this는 어디서든 사용할 수 있다.  
this는 함수가 호출될 때마다 다르게 바인딩된다.(상황에 따라 this가 바라보는 대상이 달라진다.)

자바스크립트에서 this는 기본적으로 실행 컨텍스트가 생성될 때 함께 결정된다.  
실행 컨텍스트는 함수를 호출할 때 생성되므로 즉, **this는 함수를 호출할 때 결정된다!**  
함수를 어떤 방식으로 호출하느냐에 따라 값이 달라지는 것.

### 전역공간에서의 this

전역공간에서 this는 전역 객체를 가리킨다.  
브라우저에서는 window 객체가 전역 객체이므로 this는 window를 가리킨다.  
(전역 객체는 자바스크립트 런타임 환경에 따라 다른 이름과 정보를 지닌다. 브라우저는 window, Node.js는 global)

```javascript
console.log(this); // window
console.log(window); // window
console.log(this === window); // true
```

---

> **_참고_**:  
> 전역변수를 선언하면 JS엔진은 이를 전역객체의 프로퍼티로도 만든다. 변수이면서 객체의 프로퍼티이기도 하다.

```javascript
var a = 1;
console.log(a); // 1
console.log(window.a); // 1
console.log(this.a); // 1
```

전역공간에서 선언한 변수 a에 1을 할당했을 뿐인데 window.a와 this.a 모두 1이 출력된다.  
자바스크립트의 모든 변수는 특정 객체의 프로퍼티로서 동작하기 때문에 전부 값이 1인 것.

전역공간에서는 var로 변수를 선언하는 대신 window에 프로퍼티를 직접 할당해도 같은 결과를 얻을 수 있다.(대부분의 경우)  
단, 삭제명령에 대해서는 차이가 있다.

```javascript
var a = 1;
delete window.a; // false
console.log(a, window.a, this.a);
// 1 1 1

var b = 2;
delete b; // false
console.log(b, window.b, this.b);
// 2 2 2

window.c = 3;
delete window.c; // true
console.log(c, window.c, this.c);
// Uncaught ReferenceError: c is not defined

window.d = 4;
delete d; // true
console.log(d, window.d, this.d);
// Uncaught ReferenceError: d is not defined
```

전역객체의 프로퍼티로 할당한 경우에는 삭제가 되지만, 전역변수로 선언한 경우에는 삭제가 되지 않는다.  
=> 사용자가 의도치 않게 삭제하는 것을 방지하기 위해 전역변수를 삭제할 수 없게 설정되어 있기 때문.

---

### 메소드로서 호출될 때의 this

함수를 실행하는 방법 :

1. 일반 함수로서 호출
2. 메소드로서 호출

이 둘을 구분하는 유일한 차이는 **독립성**이다.
함수는 그 자체로 독립적인 기능을 수행하는 반면, 메소드는 자신을 호출한 객체에 종속적이다.

메서드를 객체의 프로퍼티에 할당된 함수로 이해하지만, 사실은 어떤 함수를 객체의 프로퍼티에 할당한다고 해서 그 자체로서 무조건 메서드가 되는 것이 아니라 객체의 메서드로서 호출할 경우에만 메서드로 동작하고 그렇지 않으면 함수로 동작한다.

```javascript
var func = function (x) {
  console.log(this, x);
};

func(1); // window {...}, 1

var obj = {
  method: func,
};

obj.method(2); // {method: ƒ} 2
```

원래의 익명함수는 그대로인데 이를 변수에 담아서 호출한 경우와 obj 객체의 프로퍼티에 할당해서 호출한 경우에 this가 달라진다.

그렇다면 이걸 어떻게 구분할까?

> 함수 앞에 점(.)이 붙어있으면 메서드로서 호출된 것이다.  
> (대괄호 표기법에 따른 경우에도 메서드로 호출한 것은 마찬가지)

```javascript
var obj = {
  method: function (x) {
    console.log(this, x);
  },
};

obj.method(3); // {method: ƒ} 3
obj["method"](4); // {method: ƒ} 4
```

어떤 함수를 호출할 때 그 함수 이름(프로퍼티명) 앞에 객체가 명시돼 있는 경우에는 메서드로 호출한 것이고, 그렇지 않은 모든 경우에는 함수로 호출한 것!

---

#### 메서드 내부에서의 this

this에는 호출한 객체가 바인딩된다.(호출한 주체에 대한 정보가 담긴다.)
호출 주체는 바로 함수명(프로퍼티명) 앞의 객체이다.

```javascript
var obj = {
  methodA: function () {
    console.log(this);
  },
  inner: {
    methodB: function () {
      console.log(this);
    },
  },
};

obj.methodA(); // {inner: {…}, methodA: ƒ}
obj["methodA"](); // {inner: {…}, methodA: ƒ}

obj.inner.methodB(); // {methodB: ƒ}
obj.inner["methodB"](); // {methodB: ƒ}
obj["inner"].methodB(); // {methodB: ƒ}
obj["inner"]["methodB"](); // {methodB: ƒ}
```

### 함수로서 호출될 때의 this

#### 함수 내부에서의 this

어떤 함수를 함수로서 호출할 경우에는 this가 지정되지 않는다.
this에는 호출한 주체에 대한 정보가 담기는데 함수로서 호출하는 것은 호출 주체의 정보를 알 수 없기 떄문이다.

#### 메서드의 내부 함수에서의 this

내부 함수 역시 함수로서 호출했는지 메서드로서 호출했는지에 따라 this의 값을 예측할 수 있다.

```javascript
var obj1 = {
  outer: function () {
    console.log(this); // (1)
    var innerFunc = function () {
      console.log(this); // (2) (3)
    };
    innerFunc();

    var obj2 = {
      innerMethod: innerFunc,
    };
  },
};

obj1.outer();

// (1) obj1
// (2) window 객체
// (3) obj2
```

같은 함수 임에도 불구하고 this가 가리키는 대상이 달라진다.
this 바인딩에 관해서는 함수를 실행하는 당시의 주변 환경은 중요하지 않고,  
해당 함수를 호출하는 구문 앞에 점 또는 대괄호 표기가 있는지 없는지가 중요하다.

#### 매서드의 내부 함수에서의 this를 우회하는 방법

#### this를 바인딩하지 않는 함수 : 화살표 함수

화살표 함수는 함수를 선언할 때 this가 바인딩되지 않는다.

```javascript
var obj = {
  outer: function () {
    console.log(this); // (1)
    var innerFunc = () => {
      console.log(this); // (2)
    };
    innerFunc();
  },
};

obj.outer();
// (1) { outer: ƒ }
// (2) { outer: ƒ }
```

### 콜백 함수 호출 시 그 함수 내부에서의 this

콜백 함수도 함수이기 때문에 기본적으로 this가 전역객체를 가리키지만,  
제어권을 받은 함수에서 콜백 함수에 별도로 this가 될 대상을 지정한 경우에는 그 대상을 참조하게 된다.

```javascript
setTimeout(function () {
  console.log(this); // window
}, 300);

[1, 2, 3, 4, 5].forEach(function (x) {
  console.log(this, x);
  // window 1, window 2, window 3, window 4, window 5
});

document.body.innerHTML += "<button id='a'>클릭</button>";
document.body.querySelector("#a").addEventListener("click", function (e) {
  console.log(this, e); // <button id="a">클릭</button>
});
```

### 생성자 함수 내부에서의 this

생성자 함수 : 어떤 공통된 성질을 지니는 객체들을 생성하는 데 사용하는 함수.

> 객체지향 언어에서의 생성자 : 클래스  
> 클래스를 통해 만든 객체 : 인스턴스

예를 들어 인간의 공통된 특성은 직립 보행, 언어 구사, 눈, 코, 입 등이 있는데,  
이런 공통 속성들을 모아 인간을 정의한 것이 클래스이고,  
각 사람들은 인간 클래스에 속하는 인스턴스인 것이다.

프로그래밍 적으로 생성자는 구체적인 인스턴스를 만들기 위한 일종의 틀이다.  
이 틀에는 해당 클래스의 공통 속성들이 미리 준비돼 있고,  
인스턴스를 만들 때마다 이 속성들을 복사해서 새로운 인스턴스를 만든다.

JS는 함수에 생성자로서의 역할을 함께 부여했다.  
new 명령어를 사용해 함수를 호출하면 해당 함수는 생성자 함수로 동작한다.

```javascript
var Person = function (name, age) {
  this.name = name;
  this.age = age;
};

var jay = new Person("jay", 30);
var dave = new Person("dave", 22);
console.log(jay, dave);
// Person {name: "jay", age: 30}
// Person {name: "dave", age: 22}
```

---

## 명시적으로 this를 바인딩하는 방법

### call 메서드

```javascript
Function.prototype.call(thisArg[, arg1[, arg2[, ...]]])
```

call 메서드는 메서드의 호출 주체인 함수를 즉시 실행하도록 하는 명령이다.  
메서드의 첫 번째 인자로 this로 바인딩할 대상을 지정하고, 이후의 인자로는 호출할 함수에 전달할 인자들을 나열한다.

```javascript
var func = function (a, b, c, d) {
  console.log(this, a, b, c, d);
};

func(1, 2, 3, 4); // window 1 2 3 4
func.call({ x: 1 }, 4, 5, 6, 7); // {x: 1} 4 5 6 7
```

함수를 그냥 실행하면 this는 전역객체를 참조하지만. call 메서드를 이용하면 임의의 객체를 this로 지정할 수 있다.

메서드로서 호출할 때도 마찬가지로 그냥 호추하면 this는 해당 객체를 참조하지만, call 메서드를 이용하면 다른 객체를 this로 지정할 수 있다.

```javascript
var obj = {
  a: 1,
  method: function (x, y) {
    console.log(this.a, x, y);
  },
};

obj.method(2, 3); // 1 2 3
obj.method.call({ a: 4 }, 5, 6); // 4 5 6
```

### apply 메서드

```javascript
Function.prototype.apply(thisArg[, argsArray])
```

apply 메서드는 call 메서드와 완전히 동일한 기능을 수행하지만,  
call 메서드는 인자를 하나씩 나열하는 방식이라면 apply 메서드는 인자를 배열로 받는다.

```javascript
var func = function (a, b, c, d) {
  console.log(this, a, b, c, d);
};

func(1, 2, 3, 4); // window 1 2 3 4
func.apply({ x: 1 }, [4, 5, 6, 7]); // {x: 1} 4 5 6 7

var obj = {
  a: 1,
  method: function (x, y) {
    console.log(this.a, x, y);
  },
};

obj.method(2, 3); // 1 2 3
obj.method.apply({ a: 4 }, [5, 6]); // 4 5 6
```

### call/apply 메서드 활용

#### 유사배열객체에 배열 메서드 적용

유사배열객체는 배열처럼 보이지만, 실제 배열은 아니기 때문에 배열 메서드를 직접 사용할 수 없다.  
ex) arguments 객체, NodeList 객체

배열처럼 인덱스를 통해 값에 접근하고, length 프로퍼티를 가지고 있지만, 실제 배열 메서드(push, pop, map 등)는 없다. 그래서 이 객체들에는 배열 메서드를 직접 사용할 수 없지만, **call**이나 **apply** 메서드를 사용해서 배열 메서드를 "차용"할 수 있다.

```javascript
var obj = {
  0: "a",
  1: "b",
  2: "c",
  length: 3,
};

Array.prototype.push.call(obj, "d");
console.log(obj); // {0: "a", 1: "b", 2: "c", 3: "d", length: 4}

var arr = Array.prototype.slice.call(obj);
console.log(arr); // ['a', 'b', 'c', 'd']
```

> slice 메서드는 원래 시작 인덱스값과 마지막 인덱스값을 받아 시작값부터 마지막값의 앞 부분까지의 배열 요소를 추출하는 메서드인데, 매개변수를 아무것도 넘기지 않을 경우에는 그냥 원본 배열의 얕은 복사본을 반환한다.

arguments와 querySelectorAll 등의 NodeList 객체도 유사배열객체이므로 배열로 전환해서 활용할 수 있다.

```javascript
function a() {
  var argv = Array.prototype.slice.call(arguments);
  console.log(argv); // [1, 2, 3]

  argv.forEach(function (arg) {
    console.log(arg); // 1, 2, 3
  });
}

a(1, 2, 3);

document.body.innerHTML = "<div>a</div><div>b</div><div>c</div>";
var nodeList = document.querySelectorAll("div");
var nodeArr = Array.prototype.slice.call(nodeList);
nodeArr.forEach(function (node) {
  console.log(node); // <div>a</div>, <div>b</div>, <div>c</div>
});
```

배열처럼 인덱스와 length 프로퍼티를 지니는 문자열에 대해서도 마찬가지이지만,  
문자열의 경우 length 프로퍼티가 읽기 전용이기 때문에 원본 문자열에 변경을 가하는 메서드(push, pop, shift, unshift, splice 등)는 에러를 던지며,  
concat 처럼 대상이 반드시 배열이어야 하는 경우에는 에러는 나지 않지만 제대로 된 결과를 얻을 수 없다.

```javascript
var str = "abc";
Array.prototype.push.call(str, "def");
console.log(str); // TypeError: Cannot assign to read only property 'length' of object '[object String]'

Array.prototype.concat.call(str, "def"); // [String {"abc"}, "def"]

var newArr = Array.prototype.map.call(str, function (char) {
  return char + "!";
});
console.log(newArr); // ["a!", "b!", "c!"]
```

ES6에서는 유사배열객체 또는 순회 가능한 모든 종류의 데이터 타입을 배열로 전환하는 Array.from 메서드를 제공한다.

```javascript
var obj = {
  0: "a",
  1: "b",
  2: "c",
  length: 3,
};

var arr = Array.from(obj);
console.log(arr); // ["a", "b", "c"]
```

#### 생성자 내부에서 다른 생성자를 호출

생성자 내부에 다른 생성자와 공통된 내용이 있을 경우 call 또는 apply 메서드를 사용해서 다른 생성자를 호출하면 반복을 줄일 수 있다.

```javascript
var Person = function (name, age) {
  this.name = name;
  this.age = age;
};

function Student(name, age, school) {
  Person.call(this, name, age);
  this.school = school;
}

function Employee(name, age, company) {
  Person.apply(this, [name, age]);
  this.company = company;
}

var jay = new Student("jay", 30, "Seoul");
var dave = new Employee("dave", 22, "Google");
```

#### 여러 인수를 묶어 하나의 배열로 전달하고 싶을 때 - apply 활용

Math.max/Math.min 메서드에 apply를 적용하면 간단하게 최대값과 최소값을 구할 수 있다.

```javascript
var numbers = [10, 20, 3, 16, 45];
var max = Math.max.apply(null, numbers);
var min = Math.min.apply(null, numbers);

console.log(max, min); // 45, 3
```

ES6에서는 확산 연산자(spread operator)를 사용해 간단하게 최대값과 최소값을 구할 수 있다.

```javascript
var numbers = [10, 20, 3, 16, 45];
var max = Math.max(...numbers);
var min = Math.min(...numbers);

console.log(max, min); // 45, 3
```

### bind 메서드

```javascript
Function.prototype.bind(thisArg[, arg1[, arg2[, ...]]])
```

bind 메서드는 call과 비슷하지만 call/apply 메서드와 달리 함수를 즉시 실행하지 않고,  
넘겨 받는 this와 인자들을 바인딩한 새로운 함수를 반환한다.

```javascript
var func = function (a, b, c, d) {
  console.log(this, a, b, c, d);
};

func(1, 2, 3, 4); // window 1 2 3 4

var bindFunc1 = func.bind({ x: 1 });
bindFunc1(5, 6, 7, 8); // {x: 1} 5 6 7 8

var bindFunc2 = func.bind({ x: 1 }, 4, 5);
bindFunc2(6, 7); // {x: 1} 4 5 6 7
bindFunc2(8, 9); // {x: 1} 4 5 8 9
```

#### name 프로퍼티

bind 메서드를 적용해서 새로 만든 함수는 name 프로퍼티에 동사 bind의 수동태인 'bound'가 붙는다.  
어떤 함수의 name 프로퍼티가 'bound qqq'라면 이는 함수명이 qqq인 원본 함수에 bind 메서드를 적용한 새로운 함수라는 의미이다.

```javascript
var func = function (a, b, c, d) {
  console.log(this, a, b, c, d);
};

var bindFunc = func.bind({ x: 1 }, 4, 5);
console.log(func.name); // func
console.log(bindFunc.name); // bound func
```

#### 상위 컨텍스트의 this를 내부함수나 콜백 함수에 전달하기

call, apply, bind 메서드를 이용하면 메서드의 this를 그대로 바라보게 하기 위한 방법으로 self 등의 변수를 활용한 것을 대체할 수 있다.

```javascript
// 1) call 메서드를 이용한 방법
var obj = {
  outer: function () {
    console.log(this); // (1) {outer: ƒ}
    var innerFunc = function () {
      console.log(this); // (2) {outer: ƒ}
    };
    innerFunc.call(this);
  },
};

obj.outer();

// 2) bind 메서드를 이용한 방법
var obj = {
  outer: function () {
    console.log(this); // (1) {outer: ƒ}
    var innerFunc = function () {
      console.log(this); // (2) {outer: ƒ}
    }.bind(this);

    innerFunc();
  },
};

obj.outer();
```

### 화살표 함수의 예외사항

화살표 함수는 함수를 실행할 때 this가 바인딩되지 않는다. 이 함수 내부에는 this가 없으며, 접근하려고 하면 스코프상 가장 가까운 this에 접근하게 된다.  
call/apply/bind를 적용할 필요없이 간결하고 편리하게 this를 사용할 수 있다.

```javascript
var obj = {
  outer: function () {
    console.log(this); // (1) {outer: ƒ}
    var innerFunc = () => {
      console.log(this); // (2) {outer: ƒ}
    };
    innerFunc();
  },
};

obj.outer();
```

### 별도의 인자로 tihs를 받는 경우(콜백 함수 내에서의 this)

콜백 함수를 인자로 받는 메서드 중 일부는 추가로 this로 지정할 객체(thisArg)를 인자로 지정할 수 있는 경우가 있다.  
이러한 메서드의 thisArg 값을 지정하면 콜백 함수 내부에서 this 값을 원하는 대로 변경할 수 있다.

```javascript
var report = {
  sum: 0,
  count: 0,
  add: function () {
    var args = Array.prototype.slice.call(arguments);
    console.log(args); // [60, 85, 95];
    args.forEach(function (entry) {
      console.log(this);
      // {sum: 0, count: 0, add: ƒ, average: ƒ}
      // {sum: 60, count: 1, add: ƒ, average: ƒ}
      // {sum: 145, count: 2, add: ƒ, average: ƒ}
      this.sum += entry;
      ++this.count;
    }, this);
  },
  average: function () {
    return this.sum / this.count;
  },
};

report.add(60, 85, 95);
console.log(report.sum, report.count, report.average());
// 240 3 80
```

> 콜백 함수와 함께 thisArgs를 인자로 받는 메서드
>
> - Array.prototype.forEach(callback[, thisArg])
> - Array.prototype.map(callback[, thisArg])
> - Array.prototype.filter(callback[, thisArg])
> - Array.prototype.reduce(callback[, initialValue])
> - Array.prototype.some(callback[, thisArg])
> - Array.prototype.every(callback[, thisArg])
> - Set.prototype.forEach(callback[, thisArg])
> - Map.prototype.forEach(callback[, thisArg])
> - ...

---

### this 정리!!

다음 규칙은 명시적 this 바인딩이 없는 한 늘 성립한다.

- 전역공간에서의 this는 전역객체(브라우저에서는 window, Node.js에서는 global)를 참조합니다.
- 어떤 함수를 메서드로서 호출한 경우 this는 메서드 호출 주체(메서드명 앞의 객체)를 참조합니다.
- 어떤 함수를 함수로서 호출한 경우 this는 전역객체를 참조합니다. 메서드의 내부함수에서도 같습니다.
- 콜백 함수 내부에서의 this는 해당 콜백 함수의 제어권을 넘겨받은 함수가 정의한 바에 따르며, 정의하지 않은 경우에는 전역객체를 참조합니다.
- 생성자 함수에서의 this는 생성될 인스턴스를 참조합니다.

다음은 명시적 this 바인딩이다.

- call, apply 메서드는 this를 명시적으로 지정하면서 함수 또는 메서드를 호출합니다.
- bind 메서드는 this 및 함수에 넘길 인수를 일부 지정해서 새로운 함수를 만듭니다.
- 요소를 순회하면서 콜백 함수를 반복 호출하는 내용의 일부 메서드는 별도의 인자로 this를 받기도 합니다.

```

```
