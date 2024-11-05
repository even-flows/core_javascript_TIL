# 실행 컨텍스트

## 2-1 실행 컨텍스트란?

- 데이터 구조

  - 스택(stack - LIFO): 후입선출형태의 자료구조

    - 특징

      - top으로 정한 곳을 통해서만 접근할 수 있다.
      - top에는 가장 위에 있는 자료는 가장 최근에 들어온 자료를 가리키고 있으며, 삽입되는 새 자료는 top이 가리키는 자료의 위에 쌓이게 된다.
      - 스택에서 자료를 삭제할 때도 top을 통해서만 가능하다.
      - 스택에서 top을 통해 삽입하는 연산을 'push', 삭제하는 연산을 'pop'이라고 한다.
      - **가장 마지막에 삽입된 자료가 가장 먼저 삭제된다**는 구조적 특징을 가지게 된다.

    - stack underflow : 비어있는 스택에서 원소를 추출하려고 할 때 발생
    - stack overflow : 스택이 넘치는 경우

      - javascript에선 재귀 호출이 너무 깊어질 때 발생하고 스택의 최대 크기를 직접적으로 설정하거나 확인할 수 있는 방법은 없다.

    - 스택의 활용예시
      - 웹 브라우저 방문기록(뒤로가기)
      - 역순 문자열 만들기
      - 실행 취소: 가장 나중에 실행된 것부터 실행을 취소한다.
      -

  - 큐(queue - FILO): 선입선출형태의 자료구조

    - 특징
      - 한쪽 끝에서 삽입 작업(rear), 다른 한쪽 끝에서 삭제 작업(front)이 양쪽으로 이루어진다.
      - 큐의 rear에서 이루어지는 삽입연산을 인큐(enQueue), front에서 이루어지는 삭제 연산을 디큐(dnQueue)

  - 큐, 스택 사진!!

- 실행 컨텍스트 : 실행할 코드에 제공할 환경 정보들을 모아놓은 객체
  - 하나의 실행 컨텍스트를 구성할 수 있는 방법
    - 자동 생성
      - 전역공간
      - eval()
    - 함수 실행

```js
    var a = 10;

function outer() {
    var a = 20;

    function inner() {
        console.log(a); // (1)
        var a = 30;
        console.log(a); // (2)
    }

    inner(); // (3)
    console.log(a); // (4)
}

outer(); // (5)
console.log(a); // (6)

출력 결과
(1) undefined
(2) 30
(4) 20
(6) 10

```

```
코드 실행 흐름
	1.	전역 컨텍스트 (Global Context)
	  •	var a = 10;으로 전역 변수 a가 선언되고, 10으로 설정
	2.	outer 함수 호출 (5)
    •	outer 함수가 호출되면 새로운 실행 컨텍스트가 생성된다.
    •	outer 함수 안에서는 새로운 지역 변수 a가 선언되고, 20으로 설정
    •	outer 함수 내에서 inner 함수가 선언 -> 호출.
	3.	inner 함수 호출 (3)
    •	inner 함수가 호출되면 inner 함수의 실행 컨텍스트가 생성된다.
    •	inner 함수의 첫 번째 console.log(a); (1)에서 a의 값이 출력되는데, inner 함수 내에서 a 변수가 호이스팅(hoisting)되었지만 아직 값이 할당되지 않았기 때문에 undefined가 출력된다.
    •	이후 var a = 30;을 통해 inner 함수의 지역 변수 a에 값 30이 할당된다.
    •	두 번째 console.log(a); (2)에서는 a의 값이 30으로 출력된다.
	4.	outer 함수로 돌아가기 (4)
    •	inner 함수가 종료되면 inner의 실행 컨텍스트가 콜 스택에서 제거되고, outer 함수로 돌아온다.
    •	outer 함수 내에서 console.log(a); (4)에서 a의 값이 출력되며, 이는 outer 함수의 지역 변수 a인 20이 출력된다.
	5.	전역 컨텍스트로 돌아가기 (6)
    •	outer 함수가 종료되면 outer의 실행 컨텍스트가 콜 스택에서 제거되고, 전역 컨텍스트로 돌아온다.
    •	마지막으로 console.log(a); (6)에서 전역 변수 a의 값인 10이 출력된다.

```

- 객체에 담기는 정보
  - js엔진이 활용할 목적으로 생성(개발자가 코드를 통해 확인 불가)
  - 정보
    - VariableEnvironment: 현재 컨텍스트 내의 식별자들에 대한 정보 + 외부 환경 정보. 선언 시점의 LexicalEnvironment의 스냅샷으로, 변경사항은 반영되지 않음.
    - LexicalEnvironment: 처음에는 VariableEnvironment와 같지만 변경 사항이 실시간으로 반영됨.
    - ThisBindings: this 식별자가 바라봐야 할 대상 객체

| 특성                     | `var`                                      | `let` (또는 `const`)                        |
| ------------------------ | ------------------------------------------ | ------------------------------------------- |
| 스코프                   | 함수 스코프                                | 블록 스코프                                 |
| 초기화                   | `undefined`로 초기화                       | 초기화되지 않음 (Temporal Dead Zone에 놓임) |
| 초기화 전 접근 가능 여부 | 가능 (값은 `undefined`)                    | 불가능 (접근 시 ReferenceError 발생)        |
| 사용 가능한 시점         | 선언 이전에도 접근 가능 (값은 `undefined`) | 선언과 초기화가 이루어진 이후부터 접근 가능 |

## 2-2 VariableEnvironment

- 담기는 내용: 함수가 선언될 당시의 LexicalEnvironment의 스냅샷
- 실행 컨텍스트 생성 -> VariableEnvironment에 정보 저장 -> VariableEnvironment의 정보를 복사해서 LexicalEnvironment 생성
- VariableEnvironment와 LexicalEnvironment의 구성
  - environmentRecord
  - outer-EnvironmentReference
- 주요 사용 용도: 클로저 생성 시 참조
- 변경x

## 2-3 LexicalEnvironment

### 2-3-1 environmentRecord와 호이스팅

- environmentRecord: 현재 컨텍스트와 관련된 코드의 식별자 정보들이 저장
  - 컨텍스트를 구성하는 함수에 지정된 매개변수 식별자
  - 선언한 함수가 있을 경우 그 함수 자체
  - var로 선언된 변수의 식별자 등

```
전역 실행 컨텍스트
변수 객체를 생성하는 대신 js구동 환경이 별도로 제공하는 객체, 즉 전역 객체를 활용한다.
ex) 브라우저의 window, Node.js의 global 객체 등 -> 호스트 객체로 분류
```

-> 코드가 실행되기 전 변수 정보를 수집, 즉 코드 최상단에 변수나 함수들이 제일 위에 선언 돼 있다는 것과 동일

- hoisting

```js
function a(){
    console.log(b);
    var b = 'bbb';
    console.log(b);
    function b(){
    console.log("qqq");
    }
    console.log(b)
}

// 호이스팅 시
function a(){
  var b;
  function b() {
    console.log("qqq");
  }

  console.log(b);
  b = 'bbb';
  console.log(b);
  console.log(b);
}

// 출력 결과
ƒ b(){
    console.log("qqq");
    }
bbb
bbb
```

#### 함수 선언문과 함수 표현식

- 함수 선언 방법

```js
function a() {} // 함수 선언문

var b = function () {}; // (익명) 함수 표현식. b가 함수명

var c = function d() {}; // 기명 함수 표현식. 변수명은 c, 함수명은 d
c(); //실행OK
d(); //에러
```

- 예시

```js
// 1. 원본 코드
console.log(sum(1, 2));
console.log(multiply(3, 4));

function sum(a, b) {
  // 함수 선언문 sum
  return a + b;
}

var multiply = function (a, b) {
  // 함수 표현식 multiple
  return a * b;
};

// 2. 호이스팅 마친 상태
function sum(a, b) {
  // 함수 선언문은 전체를 호이스팅
  return a + b;
}
var multiply; //변수는 선언부만 끌어올림

console.log(sum(1, 2));
console.log(multiply(3, 4));

multiply = function (a, b) {
  // 변수의 할당부는 원래 자리에 남겨둔다.
  return a * b;
};
```

- var로 선언을 했을때 함수 선언문으로 했다가 같은 함수명으로 선언해서 실행을 해도 정상 작동하기 때문에 함수 표현식으로 작성하는 것이 안전하고 let이나 const로 선언하면 중복이름으로 할 시 SyntaxError: Identifier 함수명 has already been declared 에러가 뜨기 때문에 걱정 안해도 된다.

### 2-3-2 스코프, 스코프 체인, outer EnvironmentReference

- 스코프: 식별자에 대한 유효범위

  - 전역 스코프
    - 어느 위치에서든 접근 가능
    - js파일 전체에서 사용가능
  - 함수 스코프
    - 함수 내부에서 선언된 변수는 그 함수 내부에서만 접근 가능. 이 범위가 함수 스코프
    - 함수 내부에서만 유효하고 함수가 종료되면 사라짐
  - 블록 스코프
    - 블록(`{}`)으로 감싸진 코드 안에서만 유효한 스코프
    - ex) let, const로 선언된 변수, if, for, while 등의 코드 블록

- 스코프 체인: 자신의 스코프 안에 찾고자하는 변수가 없다면 자신을 포함하고 있는 더 넓은 범위의 스코프에 변수를 검색해나가는 것
- outer EnvironmentReference

  - 현재 스코프에서 상위 스코프를 참조하는 링크
  - js가 스코프 체인을 통해 변수를 탐색할 수 있도록 경로를 제공

- 스코프 체인 사진!

## 2-4 this

- this로 지정된 객체가 저장
- 실행 컨텍스트 활성화 당시 this가 지정되지 않은 경우 this에는 전역 객체가 저장된다.
- 함수를 호출하는 방법에 따라 this에 저장되는 대상이 다르다.
