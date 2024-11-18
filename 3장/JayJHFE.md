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
