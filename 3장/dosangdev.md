# 3. this

## 3-1 상황에 따라 달라지는 this

- this는 함수를 호출할 때 결정된다.

### 3-1-1 전역 공간에서의 this

- 전역 공간에서의 this는 전역 객체이다.
  - 전역 객체는 자바스크립트 런타임 환경에 따라 다른 이름과 정보를 갖고 있다.
    |브라우저|Node.js|
    |----|----|
    |window|global|

> ect. var로 변수 선언을 하면 전역객체에 프로퍼티로 추가되고, const나 let은 안된다.
>
> > var의 범위는 함수 스코프고 const, let의 범위는 블록 스코프이기 때문

- 전역 공간에서의 var을 이용한 할당은 `window.a = 1`처럼 전역객체에 할당하는 결과를 갖지만 전역객체의 프로퍼티를 삭제할 수 있는 delete 연산자로는 var을 이용한 할당은 삭제하지 못한다.
  - Why? 사용자가 의도치 않게 삭제하는 것을 방지(방어 전략). var을 이용한 선언은 전역객체에 추가할 때 configurable속성(변경 및 삭제 가능성)을 false로 정의한다.

### 3-1-2 메서드로서 호출할 때 그 메서드 내부에서의 this

- 함수 실행 방법

  - 함수로서 실행: 그 자체로 독립적이 기능을 수행
  - 메서드로서 실행: 자신을 호출한 대상 객체에 관한 동작을 수행

- js는 상황별로 this 키워드에 다른 값이 부여됨

```js
var func = function (x) {
  console.log(this, x);
};
func(1); // Window{...} 1

var obj = {
  method: func,
};
obj.method(2); // {method: f} 2
```

- 구분 방법: 메서드 앞에 .으로 구분(대괄호 표기법으로도 가능), 그 외의 호출은 함수로 호출
  - 어떤 함수를 메서드로서 호출하는 경우 호출 주체는 바로 함수명 앞의 객체이다.

```js
var obj = {
  methodA: function () {console.log(this)}
  inner: {
    methodB: function() { console.log(this);}
  }
}
obj.methodA();  //{methodA: f, inner: {...}}
obj['methodA'](); //{methodA: f, inner: {...}}

obj.inner.methodB(); //{methodB: f}
obj.inner['methodB'](); //{methodB: f}
obj['inner'].methodB(); //{methodB: f}
obj['inner']['methodB'](); //{methodB: f}
```

### 3-1-3 함수로서 호출할 떄 그 함수 내부에서의 this

- this에는 호출한 주체에 대한 정보가 담긴다고 했지만 함수로서 호출하는 것은 호출 주체를 명시하지 않고 개발자가 코드에 직접 관여해서 실행한 것이기 때문에 호출 주체의 정보를 알 수 없다.

```js
var obj = {
  outer: function () {
    console.log(this); // (1) {outer: f} // obj

    function innerFunc() {
      console.log(this); // (2) 전역 객체 (window) (3) obj2
    }
    innerFunc();

    var obj2 = {
      innerMethod: innerFunc,
    };
    obj2.innerMethod();
  },
};

obj.outer();
```

- 메서드의 내부 함수에서의 this를 우회하는 방법
  - 그 밖에도 call, apply 등의 메서드를 활용해서 명시적으로 this를 지정할 수 있다.

```js
// 상위 스코프의 this를 저장
var obj = {
  outer: function () {
    var self = this; // 상위 스코프의 this 저장

    function innerFunc() {
      console.log(self); // (1) obj
    }

    innerFunc();
  },
};

obj.outer();

// 화살표 함수 사용
var obj = {
  outer: function () {
    const innerFunc = () => {
      console.log(this); // (1) obj
    };

    innerFunc();
  },
};

obj.outer();
// 화살표 함수는 실행 컨텍스트를 생성할 때 this 바인딩 단계를 생략하고 this를 상위 스코프(렉시컬 환경)의 this와 동일하게 사용한다.
```

### 3-1-4 콜백 함수 호출 시 그 함수 내부에서의 this

```js
// (1) setTimeout에서의 this
setTimeout(function () {
  console.log(this); // 전역 객체 (window)
}, 300);

// (2) forEach에서의 this
[1, 2, 3, 4, 5].forEach(function (x) {
  console.log(this, x); // 전역 객체 (window), 각각의 배열 값 x
});

// (3) addEventListener에서의 this
document.body.innerHTML += '<button id="a">클릭</button>';
document.body.querySelector("#a").addEventListener("click", function (e) {
  console.log(this, e); // 클릭된 버튼 요소와 이벤트 객체
});
```

- (1),(2)는 콜백 함수를 호출할 때 대상이될 this를 지정하지 않고, (3)의 this는 `<button id="a">클릭</button>`로 지정된다.

### 3-1-5 생성자 함수 내부에서의 this

- 생성자(=class) 함수: 어떤 공통된 성질을 지니는 객체를 생성하는 데 사용하는 함수
- 인스턴스(instance): 클래스를 통해 만든 객체

```js
var Cat = function (name, age) {
  this.bark = "야옹";
  this.name = name;
  this.age = age;
};

var choco = new Cat("초코", 7);
var nabi = new Cat("나비", 5);
console.log(choco, nabi);

/* 결과
Cat {bar: '야옹',name: '초코', age:7}
Cat {bar: '야옹',name: '나비', age:5}
*/
```

- this는 생성자 함수 호출 시 새로 생성된 객체를 참조한다.
- 생성자 함수는 new 키워드를 통해 호출되며, 객체의 초기 상태를 설정하는 역할을 한다.
- `return`을 명시적으로 하지 않으면 생성된 객체가 자동으로 반환된다.

## 명시적으로 this를 바인딩하는 방법

> this에 별도의 대상을 바인딩 하는 방법

### 3-2-1 call 메서드

`Function.prototype.call(this[,arg1[,arg2[, ...]]])`

- call메서드는 메서드의 호출 주체인 함수를 즉시 실행하도록 하는 명령. 이때 call의 첫번쨰 인자를 this로 바인딩, 이후의 인자들을 호출할 함수의 매개변수로 한다.

```js
var func = function (a, b, c) {
  console.log(this, a, b, c);
};

func(1, 2, 3); // Window{...} 1 2 3
func.call({ x: 1 }, 4, 5, 6); // {x: 1} 4 5 6
```

- func의 this는 전역객체이지만 func.call의 this는 {x: 1}로 바인딩 된다.

```js
var obj = {
  a: 1,
  method: function (x, y) {
    console.log(this.a, x, y);
  },
};

obj.method(2, 3); // 1 2 3
obj.method.call({ a: 4 }, 5, 6); // 4 5 6
```

- `obj.method`엔 obj가 바인딩 되기 떄문에 this.a는 1이 들어가고 `obj.method.all`엔 4가 바인딩된다

### 3-2-2 apply 메서드

> apply는 call메서드와 기능적으로 동일하지만 두번째 매개변수를 배열로 받아 처리를 한다.

```js
var func = function (a, b, c) {
  console.log(this, a, b, c);
};

func(1, 2, 3); // Window{...} 1 2 3
func.apply({ x: 1 }, [4, 5, 6]); // {x: 1} 4 5 6
```

```js
var obj = {
  a: 1,
  method: function (x, y) {
    console.log(this.a, x, y);
  },
};

obj.method(2, 3); // 1 2 3
obj.method.apply({ a: 4 }, [5, 6]); // 4 5 6
```

### 3-2-3 call / apply 메서드의 활용

> 객체에 배열메서드는 직접 적용할 순 없지만, 키가 0 or 양의 정수인 프로퍼티가 존재하고 length프로퍼티의 값이 0 or 양의 정수인 객체, 즉 배열의 구조와 유사한 객체의 경우(유사배열객체) call 또는 apply메서드를 이용해 배열 메서드를 차용할 수 있다.

```js
var obj = {
  0: "a",
  1: "b",
  2: "c",
  length: 3,
};

Array.prototype.push.call(obj, "d");
console.log(obj); // { 0: 'a', 1: 'b', 2: 'c', 3: 'd',length: 4}

var arr = Array.prototype.slice.call(obj);
console.log(arr); // ['a', 'b', 'c', 'd']
// push할땐 length를 기준으로 만약 키가 0,1,2까지만 있고 length가 4인 경우 3은 건너뛰고 키가 4인 곳에 값이 들어간다.
// slice로 배열로 전환할 때 원랜 시작 인덱스부터 마지막 인덱스 앞부분까지 얕은 복사로 새로운 배열을 복사해 주는 메서드지만 유사배열객체에서 쓸때 length의 기준으로 복사를 해서 리턴한다. 그리고 중간 값이 없을때 예를들어 위에 키 중 0, 1, 3이 있다고 치고 length가 3일 경우 [a,b,empty]를 반환한다.
```

```js
// call/apply 메서드의 활용 - arguments, NodeList에 배열메서드를 적용
function a() {
  var argv = Array.prototype.slice.call(arguments); // arguments를 배열로 변환
  argv.forEach(function (arg) {
    console.log(arg); // 각 인자를 출력
  });
}

a(1, 2, 3);

document.body.innerHTML = "<div>a</div><div>b</div><div>c</div>"; // HTML 추가
var nodeList = document.querySelectorAll("div"); // NodeList를 선택
var nodeArr = Array.prototype.slice.call(nodeList); // NodeList를 배열로 변환
nodeArr.forEach(function (node) {
  console.log(node); // <div>a</div><div>b</div><div>c</div>
});
```

```js
// 원본을 수정하려는 메서드는 에러가 나오고 concat처럼 대상이 반드시 배열이어야 하는 경우에는 에러는 나지 않지만 제대로 된 결과를 얻을 수 없다.
var str = "abc def";

Array.prototype.push.call(str, ",", "pushed string");
// 문자열은 읽기 전용이기 때문에 push를 적용하려 하면 오류 발생
// Error: Cannot assign to read only property 'length' of object '[object String]'

Array.prototype.concat.call(str, "string");
// 결과: ["abc def", "string"]
// concat은 원본을 수정하지 않고 새로운 배열을 반환, 여기서 str은 유사배열로 취급하며 'string'이 추가된 배열로 반환

Array.prototype.every.call(str, function (char) {
  return char !== " ";
});
// 결과: false
// every는 배열의 모든 요소가 조건을 만족하면 true, 그렇지 않으면 false를 반환한다.
// 이 코드에서는 모든 문자가 공백(' ')이 아닌지 검사한다.
// 문자열 str에는 공백이 포함되어 있으므로 false가 반환된다.

Array.prototype.some.call(str, function (char) {
  return char === " ";
});
// 결과: true
// some은 배열의 요소 중 하나라도 조건을 만족하면 true, 그렇지 않으면 false를 반환한다.
// 이 코드에서는 문자열 str에 공백(' ')이 하나라도 있는지 검사한다.
// str에 공백이 포함되어 있으므로 true가 반환된다.

var newArr = Array.prototype.map.call(str, function (char) {
  return char + "!";
});
console.log(newArr);
// 결과: ["a!", "b!", "c!", " !", "d!", "e!", "f!"]
// map은 배열의 각 요소를 변환한 결과를 새 배열로 반환한다.

var newStr = Array.prototype.reduce.apply(str, [
  function (string, char, i) {
    return string + char + i;
  },
  "",
]);
console.log(newStr);
// 결과: "a0b1c2 3d4e5f6"
// reduce는 배열의 각 요소를 누적 처리해 단일 값을 반환한다.
// 여기서 각 문자(char)와 해당 인덱스(i)를 문자열로 결합한다.
// 초기값('')에서 시작해 결과 문자열을 생성한다.
```

> call과 apply로 형변환 하는 것은 `this를 원하는 값으로 지정해서 호출한다`라는 본래 메서드의 의도와는 다소 동떨어진 활용법이기 떄문에 ES6에선 유사배열 객체 또는 순회 가능한 모든 종류의 데이터 타입을 배열로 전환하는 Array.from메서드를 새로 도입했다.

```js
var obj = {
  0: "a",
  1: "b",
  2: "c",
  length: 3,
};

var arr = Array.from(obj);
console.log(arr); // ['a', 'b', 'c]
```

#### 생성자 내부에서 다른 생성자를 호출

> 생성자 내부에 다른 생성자와 공통된 내용이 있을 경우 call 또는 apply를 이용해 다른 생성자를 호출하면 반복을 줄일 수 있다

```js
function Person(name, gender) {
  this.name = name;
  this.gender = gender;
}

function Student(name, gender, school) {
  Person.call(this, name, gender);
  this.school = school;
}

function Employee(name, gender, company) {
  Person.apply(this, [name, gender]);
  this.company = company;
}

var by = new Student("보영", "female", "단국대");
var jn = new Employee("재난", "male", "구글");
```

#### 여러 인수를 묶어 하나의 배열로 전달하고 싶을떄 - apply 활용

> 배열에서 최대/최솟값을 구해야할 경우

```js
// 1. 코드 직접 구현

var numbers = [10, 20, 3, 16, 45];
var max = (min = numbers[0]);
numbers.forEach(function (number) {
  if (number > max) {
    max = number;
  }
  if (number < min) {
    min = number;
  }
});

// 2. call/apply 메서드 활용

var numbers = [10, 20, 3, 16, 45];
var max = Math.max.apply(null, numbers);
var min = Math.min.apply(null, numbers);

// 3. spread operator 사용

var numbers = [10, 20, 3, 16, 45];
var max = Math.max(...numbers);
var min = Math.min(...numbers);

console.log(max, min); // 45 3
```

### 3-2-4 bind 메서드

> `Function.prototype.bind(thisArg[,arg1[, arg2[, ...]]])`
> bind 메서드는 ES5에서 추가된 기능으로 call과 비슷하지만 넘겨받은 this 및 인수들을 바탕으로 새로운 함수를 반환하기만 하는 메서드이다.

- 목적
  - 함수에 this를 미리 적용하는 것
  - 부분 적용 함수를 구현

```js
var func = function (a, b, c, d) {
  console.log(this, a, b, c, d);
};
func(1, 2, 3, 4); // window{....} 1 2 3 4

var bindFunc1 = func.bind({ x: 1 });
bindFunc1(5, 6, 7, 8); // {x: 1} 5 6 7 8

var bindFunc2 = func.bind({ x: 1 }, 4, 5);
bindFunc2(5, 6); // {x: 1} 4 5 5 6
bindFunc2(7, 8); // {x: 1} 4 5 7 8
```

#### name 프로퍼티

- 특징으로 bind 메서드로 만들어진 함수는 bound라는 접두어가 붙는다 -> 기존의 call, apply보다 코드를 추적하기 더 수월
- 사진

#### 상위 컨텍스트의 this를 내부함수나 콜백 함수에 전달하기

```js
// call
var obj = {
  outer: function () {
    console.log(this); // {outer: f}

    var innerFunc = function () {
      console.log(this); // {outer: f}
    };

    innerFunc.call(this);
  },
};
obj.outer();

// bind
var obj = {
  outer: function () {
    console.log(this); // {outer: f}

    var innerFunc = function () {
      console.log(this); // {outer: f}
    }.bind(this);

    innerFunc();
  },
};

obj.outer();
```

```js
var obj = {
  logThis: function () {
    console.log(this);
  },

  logThisLater1: function () {
    setTimeout(this.logThis, 500);
  },
  logThisLater2: function () {
    setTimeout(this.logThis.bind(this), 1000);
  },
};

obj.logThisLater1(); // Window {...}
obj.logThisLater2(); // obj {logThis: f...}
```

### 3-2-5 화살표 함수의 예외사항

- 화살표함수는 실행컨텍스트 생성 시 this를 바인딩하는 과정이 제외됐다. 이 함수 내부에는 this가 아예 없고 접근하고자 하면 스코프체인상 가장 가까운 this에 접근하게 된다.

### 3-2-6 별도의 인지로 this를 받는 경우(콜백 함수 내에서의 this)

```js
var report = {
  sum: 0,
  count: 0,
  add: function () {
  var args = Array.prototype.slice.call(arguments); // arguments를 배열로 변환
  args.forEach(function (entry) {
    this.sum += entry;   // this는 report를 참조
    ++this.count;        // this는 report를 참조
  }, this); // this를 forEach의 두 번째 인자로 전달
},
average: function (){
  return this.sum / this.count;
}
report.add(60,85,95);
console.log(report.sum, report.count, report.average()); // 240 3 80
}
```

- 그 이외에도 콜백함수와 함께 thisArg를 인자로 받는 메서드
  - Array.prototype.forEach(callback[, thisArg])
  - Array.prototype.map(callback[, thisArg])
  - Array.prototype.filter(callback[, thisArg])
  - Array.prototype.some(callback[, thisArg])
  - Array.prototype.every(callback[, thisArg])
  - Array.prototype.find(callback[, thisArg])
  - Array.prototype.findIndex(callback[, thisArg])
  - Array.prototype.flatMap(callback[, thisArg])
  - Array.prototype.from(arrayLike[, callback[, thisArg]])
  - Set.prototype.forEach(callback[, thisArg])
  - Map.prototype.forEach(callback[, thisArg])
