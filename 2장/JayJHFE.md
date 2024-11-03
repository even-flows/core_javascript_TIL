## 실행 컨텍스트 예제
```javascript
function first() {
    console.log('first')
    second();
}

function second() {
    console.log('second');
}

first();
```
![image](https://github.com/user-attachments/assets/94d929b5-7ac9-408a-b4c8-4e7ada550513)

## VariableEnvironment
> VariableEnvironment의 내용은 LexicalEnvironment와 같지만 최초 실행의 스냅샷을 유지한다는 점이 다름. VariableEnvironment는에 정보를 담은 다음 그대로 복사해서 LexicalEnvironment에를 만들고
> 이후 LexicalEnvironment를 사용함
> VariableEnvironment 와 LexicalEnvironment는 environmentRecord와 outer-EnvironmentReference로 구성되어있음

### environmentRecord
> 코드의 식별자 정보를 저장, 함수에 지정된 매개변수 식별자, 선언한 함수, 변수의 식별자 등을 일컫음# 실행 컨텍스트 예제
```javascript
function first() {
    console.log('first')
    second();
}

function second() {
    console.log('second');
}

first();
```
![image](https://github.com/user-attachments/assets/94d929b5-7ac9-408a-b4c8-4e7ada550513)

## VariableEnvironment
> VariableEnvironment의 내용은 LexicalEnvironment와 같지만 최초 실행의 스냅샷을 유지한다는 점이 다름. VariableEnvironment는에 정보를 담은 다음 그대로 복사해서 LexicalEnvironment에를 만들고
> 이후 LexicalEnvironment를 사용함
> VariableEnvironment 와 LexicalEnvironment는 environmentRecord와 outer-EnvironmentReference로 구성되어있음

### environmentRecord
> 코드의 식별자 정보를 저장, 함수에 지정된 매개변수 식별자, 선언한 함수, 변수의 식별자 등을 일컬음
- 내장 객체 (Native object)
> 내장 객체는 String, Math, Object, Function 과 같은 자바스크립트 언어의 일부에 해당하는 객체들을 의미함.

- 호스트 객체 (Host object)
> 호스트 객체는 window와 같은 BOM, XMLHTTPRequest와 같은 노드나 브라우저 등 런타임, 실행 환경에 의해 제공되는 객체들을 의미
### 호이스팅
> 변수 정보를 수집하는 과정에서 엔진이 실제로 끌어올리지는 않지만 편의상 끌어올린 것으로 간주

### 매개변수와 변수에 대한 호이스팅
```javacript
function a(x){
    console.log(x);
    var x;
    console.log(x);
    var x = 2;
    console.log(x);
};

a(1);
```

호이스팅 결과 - 변수의 선언부만 끌어올리고 할당부는 원래 자리에 둠

```javacript
function a(x){
    var x; //매개변수 x의 선언부
    var x; //var x 선언부
    var x; //var x = 2 선언부
    
    x = 1; //매개변수 할당부
    console.log(x);
    console.log(x);
    x = 2; //var x = 2 할당부
    console.log(x);
};

a(1);
```
### 함수 선언의 호이스팅
```javacript
function a() {
    console.log(b);
    var b = 'bbb';
    console.log(b);
    function b() {}
    console.log(b);
}
a();
```
호이스팅 결과 - 함수선언은 함수 전체를 끌어올리고 변수의 할당부는 원래자리에 남겨둠
```javacript
function a() {
    var b;
    var b = function b() {}
    console.log(b);
    b = 'bbb';
    console.log(b);
    console.log(b);
}
a();
```

## 함수 선언문과 함수 표현식
> 함수 선언문은 function의 정의만 내린 뒤 별도 할당을 하지않은 것. 함수 표현식은 정의 후 별도의 변수에 할달 한 것.
> * 기명 함수 표현식은 외부에서 함수명으로 함수 호출 불가능.

- 함수 선언문과 함수 표현식
```javacript
console.log(sum(1, 2));  // 3
console.log(multiply(3, 4));  // TypeError: multiply is not a function

function sum(a, b) {
  return a + b;
}

var multiply = function (a, b) {
  return a * b;
};
```

이렇게 호이스팅 된다

```javascript

var sum = function sum(a, b) {
  return a + b;
};

var multiply;

console.log(sum(1, 2)); // 3
console.log(multiply(3, 4)); // TypeError: multiply is not a function

// multiply 함수 (by. 함수 표현식)
multiply = function (a, b) {
  return a * b;
};
```
- 1. 메모리공간 확보 후 주솟값을 변수 sum에 연결
  2. 메모리 공간 확보 후 주솟값을 multiply에 연결
  3. sum함수를 다른 메모리에 저장 후 변수 sum 에 할당
  4. sum 함수를 실행
  5. multiply는 할당되어 않으므로 multiply is not a function 에러메시지 출력

## 스코프, 스코프체인, outerEnviromentReference
### 스코프(Scope) : 식별자에 대한 유효범위

> 자바스크립트 ES5 까지는 전역공간을 제외하면 오직 함수에 의해서만 스코프가 형성되었음
> ES6 부터는 블록 에서도 스코프 경계가 발생하게 함으로써 다른 언어와 비슷한 스코프 형성이 가능해짐
> 단, var 변수가 아닌 let, const, class, strict mode 에서의 함수 선언 등에 대해서만 범위로서의 역할을 수행
> ES6 에서는 둘을 구분하기 위해 함수 스코프(var), 블록 스코프(let, const ...) 라는 용어를 사용

### 스코프 체인(Scope Chanining) : 이러한 식별자의 유효범위를 안에서부터 바깥으로 차례로 검색해나가는 것

> 스코프 체인을 가능케 하는 것이 바로 LexicalEnviroment 의 두 번째 수집 자료인 outerEnviromentReference

### outerEnviromentReference

현재 호출된 함수가 선언될 당시의 LexicalEnviroment 를 참조한다.
“선언하다." 라는 행위가 실제로 일어날 수 있는 시점 = 콜 스택 상에서 어떤 실행 컨텍스트가 활성화된 상태일 때
함수를 선언(정의)하는 행위 자체도 하나의 코드에 지나지 않으며, 모든 코드는 실행 컨텍스트가 활성화 상태일 때 실행되기 때문
outerEnviromentReference 는 연결리스트(Linked List) 형태를 띈다.
선언 시점의 LexicalEnviroment 를 계속 찾아 올라가면 마지막엔 전역 컨텍스트의 LexicalEnviroment 가 있을 것이다.
또한, 각 outerEnviromentReference 는 오직 자신이 선언된 시점의 LexicalEnviroment 만 참조하고 있으므로 가장 가까운 요소부터 차례대로만 접근할 수 있고 다른 순서로 접근하는 것은 불가능
이런 구조적 특성 덕분에 여러 스코프에서 동일한 식별자를 선언한 경우에는 무조건 스코프 체인 상에서 가장 먼저 발견된 식별자에만 접근 가능
변수 은닉화(variable shadowing) : 내부 함수에 선언된 식별자와 내부 함수 외에 선언된 식별자(like. 전역변수) 이름이 같을 때, 스코프 체인 상 가장 가까운 위치 상의 식별자로 접근하므로, 외부에 존재하는 식별자에는 접근할 수 없는 셈인 것

## 전역변수와 지역변수
- 전역변수(global variable) = 전역 공간에서 선언한 변수는 전역 변수
- 지역변수(local variable) = 함수 내부에서 선언한 변수는 무조건 지역변수
