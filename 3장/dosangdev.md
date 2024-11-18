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
