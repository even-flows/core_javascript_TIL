# 2장 - 실행 컨텍스트

실행 컨텍스트는 **실행할 코드에 제공할 환경 정보들을 모아놓은 객체**로, 자바스크립트 코드가 실행되는 환경을 의미한다.  
자바스크립트의 동적 언어로서의 성격을 가장 잘 파악할 수 있는 개념.

## 스택과 큐

> - 스택 : 출입구가 하나뿐인 깊은 우물같은 데이터구조, LIFO(Last In First Out)
> - 큐 : 양쪽이 모두 열려있는 파이프 같은 구조, FIFO(First In First Out)

1. 스택 :  
   스택에 순서대로 데이터 a, b, c, d를 저장했다면, 꺼낼때는 반대로 d, c, b, a 순서로 꺼낸다. 대부분의 프로그래밍 언어들은 스택이 넘치면 에러를 던진다.

2. 큐 :  
   양쪽 모두 입, 출력이 가능한 큐도 있지만 보통은 한쪽은 입력만, 다른 한쪽은 출력만을 담당한다. 순서대로 데이터 a, b, c, d를 저장했다면, 꺼낼때도 a, b, c, d 순서로 꺼낸다.

<br/>

![image](https://images.velog.io/images/combi_jihoon/post/a4c9affa-44b1-4deb-8590-11b155793ad1/image.png)

동일한 환경에 있는 코드들을 실행할 때 필요한 환경 정보들을 모아 컨텍스트를 구성하고, 이를 콜 스택(call stack)에 쌓아 올렸다가, 가장 위에 쌓여있는 컨텍스트와 관련 있는 코드들을 실행하는 방식으로 전체 코드의 환경과 순서를 보장
<br/>

## 실행 컨텍스트

하나의 실행 컨텍스트를 구성할 수 있는 방법으로 크게 3가지가 있다.

> 1. 전역 코드
> 2. 함수 코드
> 3. eval 함수

<br/>

전역 컨텍스트 : 자바스크립트 파일이 열리는 순간 자동으로 실행, **코드가 실행되기 전에 생성되는 컨텍스트**

어떤 실행 컨텍스트가 활성화될 때 자바스크립트 엔진은 해당 컨텍스트에 관련된 코드들을 실행하는 데 필요한 환경 정보들을 수집해서 실행 컨텍스트 객체에 저장한다. 이 객체는 자바스크립트 엔진이 활용할 목적으로 생성!

실행컨텍스트는 **_VariableEnvironment_**, **_LexicalEnvironment_**, **_ThisBinding_** 이라는 3가지 객체를 가지고 있다.
![image](https://user-images.githubusercontent.com/101851472/223682277-cfa9376a-1f78-4140-999d-a83993877cdf.png)

## VariableEnvironment

실행 컨텍스트가 생성될 때 현재 컨텍스트 내의 식별자들에 대한 정보를 담는 객체.  
LexicalEnvironment와 담기는 내용은 같지만, LexicalEnvironment는 함수 실행 도중에 변경되는 사항이 즉시 반영되는 반면, VariableEnvironment는 초기 상태를 유지한다.

실행 컨텍스트를 생성할 때, VariableEnvironment에 정보를 먼저 담은 다음, 이를 그대로 복사해서 LexicalEnvironment를 만들고, 이후에는 LexicalEnvironment를 주로 활용한다.

VariableEnvironment와 LexicalEnvironment의 내부는 environmentRecord와 outereEnvironmentReference로 구성되어 있다.
<br/><br/>

## LexicalEnvironment

실제로 식별자와 바인딩된 값을 관리하는 객체.<br/>
'현재 컨텍스트의 내부에는 어떤 식별자들이 있고, 그 외부 정보는 00을 참조하도록 구성되어있다.' 라는 느낌으로 모아놓은 것이다.

### environmentRecord와 호이스팅

environmentRecord에는 현재 컨텍스트와 관련된 코드의 식별자 정보들이 저장된다.  
컨텍스트를 구성하는 함수에 지정된 매개변수 식별자, 선언한 함수가 있을 경우 그 함수 자체, var로 선언된 변수의 식별자 등이 식별자에 해당한다.  
컨텍스트 낸부 전체를 처음부터 끝까지 쭉 훑어나가며 **순서대로** 수집한다.

> 전역 실행 컨텍스트는 자바스크립트 구동환경이 별도로 제공하는 객체, 즉 전역 객체를 활용한다.
> 브라우저의 경우 window, Node.js의 경우 global 객체

<br/>
변수 정보를 수집하는 과정을 모두 마쳤더라도, 아직 실행 컨텍스트가 관여할 코드들은 실행되기 전의 상태이다.
코드가 실행되기 전에, 자바스크립트 엔진은 이미 해당 환경에 속한 코드의 변수명들을 알고 있게 되는 것이다.

#### 호이스팅

흔히 호이스팅 개념을 '자바스크립트 엔진이 식별자들을 최상단으로 끌어올린 다음 코드를 실행한다' 로 설명하는데, 실제로 자바스크립트 엔진이 끌어올리지는 않지만, 편의상 끌어올린 것으로 간주한 가상의 개념이다.
(environment record의 수집 과정을 추상화한 개념이다.)

> 즉, 호이스팅은 함수선언/변수선언이 해당 스코프의 최상단으로 끌어올려진 것처럼 동작하는 것을 말한다.

#### 호이스팅 규칙

environmentRecord에는 매개변수의 이름, 함수 선언, 변수명 등이 담긴다.

> 매개변수와 변수에 대한 호이스팅(1) - 원본 코드

```javascript
function a(x) {
  console.log(x);
  var x;
  console.log(x);
  var x = 2;
  console.log(x);
}

a(1);
```

출력값 예상 : 1, undefined, 2

> 매개변수와 변수에 대한 호이스팅(2) - 매개변수를 변수 선언/할당과 같다고 간주해서 변환한 상태

```javascript
function a() {
  var x = 1;
  console.log(x);
  var x;
  console.log(x);
  var x = 2;
  console.log(x);
}

a();
```

environmentRecord는 현재 실행될 컨텍스트의 대상 코드 내에 어떤 식별자들이 있는지에만 관심이 있다.  
각 식별자에 어떤 값이 할당될 것인지는 관심이 없다.  
변수를 호이스팅할 때 변수명만 끌어올리고 할당 과정은 원래 자리에 그대로 남겨둔다.

> 매개변수와 변수에 대한 호이스팅(3) - 호이스팅을 마친 상태

```javascript
function a() {
  var x;
  var x;
  var x;

  x = 1;
  console.log(x);
  console.log(x);
  x = 2;
  console.log(x);
}

a();
```

이후 실제 코드를 출력하면 예상과 달리 1,1,2 가 나온다.

- 변수는 선언부와 할당부를 나누어 선언부만 끌어올림
- 함수 선언(문)은 함수 전체를 끌어올림

<br/>

---

1. 변수의 경우

- let으로 변수 선언

```javascript
console.log(hoisting);
let hoisting = "hoisting!";

//에러 발생
//Uncaught ReferenceError
//: hoisting is not defined
```

- var로 변수 선언

```javascript
console.log(hoisting);
var hoisting = "hoisting!";

//undefined
```

실제로는 위와 같이 작성했지만, 함수 호이스팅이 일어나서 아래와 같이 작성한 것처럼 작동한다.

```javascript
var hoisting;
console.log(hoisting);
hoisting = "hoisting";
```

변수를 let으로 선언한 경우에는 선언 전에 console.log를 찍게 되면 에러가 발생하지만,  
var 키워드는 아래처럼 호이스팅 되어 작동 하므로 undefined을 반환한다.
⇒ **_에러를 미연에 방지할 수 없다!_**

<br/>

---

2. 함수의 경우

- 함수 표현식

```javascript
fn2();
const fn2 = function () {
  console.log("error occurred");
};

// 에러 발생
// fn2 is not defined
```

- 함수 선언식

```javascript
fn1();
function fn1() {
  console.log("hoisting occurred");
}
```

실제로는 위와 같이 작성했지만, 호이스팅이 일어나서 아래와 같이 작성한 것처럼 작동한다.

```javascript
function fn1() {
  console.log("hoisting occurred");
}

fn1();

// hoisting occurred 값을 출력
```

**_함수 선언식은 호이스팅이 일어나서 에러 방지 할 수 없다!_**

---

#### 💡 함수 선언문과 함수 표현식

- 함수 선언문 : function 정의부만 존재하고 별도의 할당 명령이 없는 것. <br/>
  반드시 함수명 정의, '기명 함수 표현식'

- 함수 표현식 : function을 별도의 변수에 할당하는 것.<br/>
  '익명 함수 표현식'

##### 호이스팅 시 차이점

- 함수 선언문 : **전체**를 호이스팅
- 함수 표현식 : 변수는 **선언부**만 끌어올립니다. (할당부 이후부터 실행 가능) <br/>
  상대적으로 함수 표현식이 안전하다!

```javascript
console.log(sum(1, 2)); // 3
console.log(multiply(3, 4)); // TypeError: multiply is not a function

function sum(a, b) {
  return a + b;
}

var multiply = function (a, b) {
  return a * b;
};
```

위와 같은 코드가 있을 때 호이스팅이 일어나면 어떻게 될까?

```javascript
var sum = function (a, b) {
  return a + b;
};

var multiply;
console.log(sum(1, 2)); // 3
console.log(multiply(3, 4));

multiply = function (a, b) {
  return a * b;
};
```

### 스코프, 스코프 체인, outerEnvironmentReference

- 스코프 : **식별자에 대한 유효범위**를 말한다.
  어떤 경계 A의 외부에서 선언한 변수는 A의 외부와 내부에서 모두 접근할 수 있지만, A의 내부에서 선언한 변수는 A의 내부에서만 접근할 수 있다.

- 스코프 체인 : 식별자의 유효범위를 안에서부터 바깥으로 차례로 검색해나가는 것.  
  → LexicalEnvironment의 outerEnvironmentReference로 인해 가능!

<br/>

#### 스코프 체인

outerEnvironmentReference : 현재 호출된 함수가 선언될 당시의 LexicalEnvironment를 참조 (과거 시점) / 연결 리스트 형태 <br/>
→ 선언하는 행위 실제로 일어나는 시점 : 콜 스택 상에서 어떤 실행 컨텍스트가 활성화된 상태일 때뿐!!!

스코프 체인은 실행 컨텍스트가 생성될 때 만들어진다.
현재 실행 컨텍스트의 LexicalEnvironment를 참조하는 outerEnvironmentReference를 순차적으로 따라가며 검색한다.

선언 시점의 LexicalEnvironment를 계속 찾아 올라가면 마지막엔 전역 컨텍스트의 LexicalEnvironment까지 도달한다.  
각 outerEnvironmentReference는 자신이 선언된 시점의 LexicalEnvironment만 참조하고 있으므로 가장 가까운 요소부터 차례대로만 접근할 수 있고 다른 순서로 접근하는 것은 불가능하다.

이런 구조적 특성 덕분에 여러 스코프에서 동일한 식별자를 선언한 경우에는 **무조건 스코프 체인 상에서 가장 먼저 발견된 식별자에만 접근 가능**하게 된다.

```javascript
var a = 1;
var outer = function () {
  var inner = function () {
    console.log(a); //undefined
    var a = 3;
  };
  inner();
  console.log(a); // 1
};
outer();
console.log(a); // 1
```

![image](https://user-images.githubusercontent.com/101851472/223777396-7c7b699c-2d9b-4911-83bf-0acac46a8b6d.png)

전역 컨텍스트 → outer 컨텍스트 → inner 컨텍스트

- 점차 규모 작아짐
- 스코프 체인을 타고 접근 가능한 변수의 수 늘어남
  변수 은닉화 : inner 함수 내부에서 a 변수를 선언했기 때문에 전역 공간에서 선언한 동일한 이름의 a 변수에는 접근할 수 없음
  <br/>

**변수 은닉화** : 스코프 체인을 통해 외부에서 내부로 접근하는 것을 막는 것.  
식별자 a를 전역 공간에서도 선언하고 함수 내부에서도 선언하면,
함수 내부에서 a에 접근하려고 하면 무조건 스코프 체인 상의 첫 번째 인자, 즉 함수 스코프의 LexicalEnvironment에서 a를 찾아서 사용하게 된다.

함수 내부에서 a 변수를 선언했기 때문에 전역 공간에서 선언한 동일한 이름의 a 변수에는 접근할 수 없는 것

#### 전역변수와 지역변수

- 전역변수 : 전역 스코프에서 선언한 a, outer
- 지역변수 : 함수 내부에서 선언한 inner, a

코드의 안전성을 위해 가급적 전역변수 사용을 최소화하고자 하는 노력하기!

## this

thisBinding에는 this로 지정된 객체가 저장됨 → this가 지정되지 않은 경우 전역 객체가 저장됨
