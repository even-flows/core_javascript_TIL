# this
> 자바스크립트에서 this는 실행컨텍스트가 생성될 때(VariableEnvironment와 LexicalEnvironment)와 함께 결정된다. 콘텍스트는 함수를 호출할 때 생성되므로, 바꿔 말하면 this는 함수를 호출할 때 결정된다고 할 수 있다.
> 즉, this는 상황별로 값이 달라질 수 있다.

## 전역공간에서의 this
> 전역 공간에서 this는 전역 객체를 가리킨다.
> 전역 객체는 자바스크립트 런타임 환경에 따라 다른 이름과 정보를 가지고 있다. 브라우저 환경에서 전역 객체는 window이고 Node.js환경에서는 global이다.
> 자바스크립트의 모든 변수는 특정 객체의 프로퍼티로 동작한다. 특정 객체란, 실행 컨텍스트의 LexicalEnvironment. 전역 변수를 선언하면 자바스크립트 엔진은 이를 전역 객체의 프로퍼티로 할당한다.

### 전역 변수와 전역 객체
```javascript
var a = 1;
console.log(a); // 1
console.log(window.a); // 1
console.log(this.a); // 1
```
> 전역 공간에서 선언한 변수 a에 1을 할당하면 전역 객체와 전역 객체를 가리키는 this에서 모두 1이 출력된다.
> 이유는 자바스크립트의 모든 변수는 특정 객체의 프로퍼티로서 동작하기 때문.
> 객체의 프로퍼티인 a를 그냥 호출하여도 1이 나오는 이유는 변수 a에 접근하고자 하면 스코프 체인에서 a를 검색했다가 가장 마지막에 도달하는 전역 스코프의 LE 즉 전역 객체에서 해당 프로퍼티 a를 발견해서 그 값을 반환하기 때문.
> 또한 var로 변수를 선언하는 대신 window에 프로퍼티를 직접 할당하더라도 같은 결과를 얻을 수 있다. 하지만, 한 가지 차이점은 delete 연산자를 활용하면, window에 프로퍼티를 직접 할당한 경우 삭제가 되지만, var로 할당한 경우에는 그렇지 않다.
> 그 이유는 전역 변수를 선언하면 자바스크립트 엔진이 이를 자동으로 전역 객체의 프로퍼티로 할당하면서 추가적으로 해당 프로퍼티의 configurable 속성(변경 및 삭제 가능성)을 false로 정의하기 때문.
```javascript
var a = 1
delete window.a // false

var b = 2
delete b // false

window.c = 3
delete window.c // true

window.d = 4
delete d // true
```

## 메서드 호출과 내부의 this
> 함수를 실행하는 방법은 함수로서 호출하는 경우와 메서드로서 호출하는 경우가 있다.
> 함수는 그 자체로 독립적인 기능을 수행한다.
> 메서드는 자신을 호출한 대상 객체에 관한 동작을 한다.
```javascript
var func = function (x) {
  console.log(this, x)
}
func(1) // window {...}

var obj = {
  method: func
};

obj.methidA(2); // {method: ƒ} 2
```
> 함수로서 호출과 메서드로서의 호출을 구분하는법은 함수앞에 점으로 구분한다.
> 메서드 내부에서의 this - this에는 호출한 주체(함수명, 프로퍼티명 앞의 객체)에 대한 정보가 담긴다. 마지막 점 앞에 명시된 객체가 곧 this가 되는 것.

## 함수로서 호출할 때 함수 내부에서의 this
> 함수 내부에서의 this - 어떤 함수를 함수로서 호출할 경우에는 this가 지정되지 않는다. 함수에서의 this는 전역 객체를 가리다.
> 메서드의 내부 함수에서의 this - 내부 함수 역시 함수로서 호출했는지 메서드로서 호출했는지 파악하면 this의 값을 예측할 수 있다.

```javascript
var obj1 = {
            outer: function () {
                console.log(this);                       // (1)
                    var innerFunc = function () {
                            console.log(this);           // (2) (3)
                        }
                    innerFunc();

                    var obj2 = {
                            innerMethod: innerFunc
                    };
                    obj2.innerMethod();
                }
            };
obj1.outer();
```

## 메서드 내부 함수에서 this를 우회하는 방법
```javascript
var obj = {
  outer: function() {
    console.log(this);
    var innerFunc = () => {
      console.log(this);
    };
    innerFunc();
  }
};
obj.outer();  
```

## 콜백함수 호출 시 그 함수 내부에서의 this
> 콜백 함수도 함수이기 때문에 기본적으로 전역 객체를 참조하지만, 함수에서 콜백 함수에 별도로 this가 될 대상을 지정한 경우에는 그 대상을 참조하게 된다.
```javascript
setTimeout(function() {
  console.log(this);
}, 300); // (1)

[1, 2, 3, 4, 5].forEach(function(x) {
  // (2)
  console.log(this, x);
});

document.body.innerHTML += '<button id="a">클릭</button>';
document.body.querySelector('#a').addEventListener('click', function(e) {
  // (3)
  console.log(this, e);
});
```


## 생성자 함수 내부에서의 this 
> 생성자 함수란 어떤 공통된 성질을 지니는 객체들을 생성할 때 사용하는 함수이다.
> 프로그래밍적으로 '생성자'는 구체적인 인스턴스를 만들기 위한 일종의 틀.
> 틀에는 클래스의 공통 속성들이 미리 준비돼 있고, 여기에 구체적인 인스턴스의 개성을 더해 개별 인스턴스를 만들 수 있다.
> 자바스크립트는 함수에 생성자로서의 역할을 함께 부여했다. new 명령어와 함께 함수를 호출하면 해당 함수가 생성자로서 동작하게 된다.
> 어떤 함수가 생성자 함수로서 호출된 경우 내부에서의 this는 곧 새로 만들 구체적인 인스턴스 자신이 된다.
> 생성자 함수를 new 명령어와 함께 호출하면 우선 생성자의 prototype 프로퍼티를 참조하는 proto라는 프로퍼티가 있는 객체를 만들고, 미리 준비된 공통 속성 및 개성을 해당 객체(this)에 부여한다.
```javascript
var Cat = function(name, age) {
  this.bark = '야옹';
  this.name = name;
  this.age = age;
};
var choco = new Cat('초코', 7);
var nabi = new Cat('나비', 5);
console.log(choco, nabi);
```
# 명시적으로 this를 바인딩 하는 방법
아래 메서드들을 통해 this에 별도의 대상을 바인딩 할 수 있다. 
- call 메서드
```javascript
Function.prototype.call(thisArg[, arg1[, arg2[, ...]]])
```
> 메서드의 호출 주체인 함수를 즉시 실행하도록 명령
> call 메서드의 첫 번째 인자를 this로 바인딩하고, 이후의 인자들을 호출할 함수의 매개변수로 함
> 즉, 함수를 그냥 실행하면 this는 전역객체를 참조하지만, call 메서드를 이용하면 임의의 객체를 this로 지정 가능
```javascript
var func = function (a, b, c) {
	console.log(this, a, b, c);
}

func(1, 2, 3); // Window{ ... } 1 2 3
func.call({ x: 1}, 4, 5, 6); // { x:1 } 4 5 6
```
- apply 메서드
```javascript
Function.prototype.apply(thisArg[, argsArray])
```
> call 메서드와 기능적으로 동일. 다만, 두번째 인자를 배열로 받아 그 배열의 요소들을 호출할 함수의 매개변수로 지정
```javascript
var func = function (a, b, c) {
	console.log(this, a, b, c);
}

func(1, 2, 3); // Window{ ... } 1 2 3
func.apply({ x: 1}, [4, 5, 6]); // { x:1 } 4 5 6
```
- call / apply 메서드를 활용해 중복을 줄일 수 있다. 아래 예시는 생성자 내부에 다른 생성자와 공통된 내용이 있을 경우 call 또는 apply를 이용해 다른 생성자를 호출해 반복을 줄이는 예시:
```javascript
function Person(name, gender) {
  this.name = name;
  this.gender = gender;
}

function Student(name, gender, school) {
  Person.call(this, name, gender);
  this.school = school;
}

function Employee(name, gender, company) {
  Person.call(this, name, gender);
  this.company = company;
}

var by = new Student("보영", "female", "단국대");
var jn = new Employee("재난", "male", "구글");
```

- bind 메서드
> 함수 바인딩이란 특정한 this값과 특정한 매개변수를 넘기면서 다른 함수를 호출하는 함수. 많은 자바스크립트
> 라이브러리들은 함수를 특정한 컨텍스트에 묶는 함수를 만들었다. 일반적으로 이런 함수를 bind()라고 부른다.
> 기본 구조는 아래와 같다.
```javascript
function bind(fn, context){
    return function(){
        return fn.apply(context, arguments);
    }
}
```
> ECMAScript5에서 bind()메서드가 도입되었으며 아래와 같이 사용한다.
```javascript
Function.prototype.bind(thisArg[, arg1[, arg2[, ...]]])
```
> ES5에서 추가된 기능. call 과 비슷하지만, 즉시 호출하지는 않고 넘겨 받은 this 및 인수들을 바탕으로 새로운
> 함수 반환.
> 즉, bind 메서드는 함수에 this를 미리 적용하는 것과 부분 적용 함수를 구현하는 두 가지 목적을 지님
```javascript
var func = function (a, b, c, d){
  console.log(this, a, b, c, d);
}
func(1, 2, 3, 4);   // Window{...} 1 2 3 4

var bindFunc1 = func.bind({ x: 1 });
bindFunc1(5, 6, 7, 8);  // { x: 1 } 5 6 7 8

var bindFunc2 = func.bind({ x: 1 }, 4, 5);
bindFunc2(6, 7);     // { x: 1} 4 5 6 7
bindFunc2(8, 9);     // { x: 1} 4 5 8 9
```
- 화살표 함수의 예외사항
> ES6에 새롭게 도입된 화살표 함수는 실행 컨텍스트 생성 시 this를 바인딩 하는 과정이 제외됨.
> 즉, 이 함수 내부에는 this가 아예 없으며, 접근하고자 하면 스코프체인상 가장 가까운 this에 접근
```javascript
var obj = {
  outer: function() {
    console.log(this);  // { outer: [Function: outer] }
    var innerFunc = () => {
      console.log(this);  // { outer: [Function: outer] }
    }
    innerFunc();
  }
}
obj.outer();
```
- 별도의 인자로 this를 받는 경우 (콜백 함수 내에서의 this)

> 콜백 함수를 인자로 받는 메서드 중 일부는 추가로 this로 지정할 객체(thisArg)를 인자로 지정할 수 있는 경우> 가 있음.
> 배열 메서드에 이러한 경우가 많이 있으며, 대표적인 예로 forEach가 있음 (ex. foreach, map, filter, > some, every, find, findIndex, flatMap, from, 그리고 ES6의 Set, Map)

foreach 메서드 예시:
```javascript
var report = {
  sum: 0,
  count: 0,
  add: function () {
		// arguments 를 배열로 변환해서 args 변수에 담는다
    var args = Array.prototype.slice.call(arguments); 
		// 해당 배열(args)를 순회하면서 콜백 함수 실행
    args.forEach(function (entry) {
      this.sum += entry;
      ++this.count;
    }, this); // 콜백 함수 내부에서의 this가 해당 this로 바인딩 됨! 
  },
  average: function () {
    return this.sum / this.count;
  },
};
report.add(60, 85, 95);
console.log(report.sum, report.count, report.average()); // 240 3 80
```
