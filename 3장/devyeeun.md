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

