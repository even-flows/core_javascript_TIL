# 콜백 함수란?
> 콜백 함수는 다른 코드의 인자로 넘겨주는 함수. 콜백 함수를 넘겨받은 코드는 이 콜백 함수를 필요에 따라 적절한 시점에 실행한다.
 
 
## 제어권
```javascript
var count = 0;
var timer = setInterval(function() {
	console.log(count);
    if(++count > 4) clearInterval(timer);
}, 300);
```
 
위 함수를 풀어 설명한다면
```javascript
var count = 0;
var cbFunc = function() {
	console.log(count);
	if(++count > 4) clearInterval(timer);
}
var timer = setInterval(cbFunc, 300);
```
|Code|호출 주체|제어권|
|------|---|---|
|cbFunc()|사용자|사용자|
|setInterval(cbFunc, 300)|setInterval|setInterval|

setInterval이라고 하는 '다른 코드'에 첫 번째 인자로서 cbFunc 함수를 넘겨주면 제어권을 넘겨받은 setInterval이 스스로의 판단에 따라 적절한 시점에 이 익명 함수를 실행한다. 콜백 함수의 제어권을 넘겨받은 코드는 콜백 함수 호출 시점에 대한 제어권을 가진다. 

## 콜백 함수는 함수
> 콜백 함수로 어떤 객체의 메서드를 전달하더라도 그 메서드는 메서드가 아닌 함수로서 호출된다. 
```javascript
var obj = {
	vals: [1, 2, 3],
    logValues: function(v, i) {
    	console.log(this, v, i);
    }
};
obj.logValues(1, 2); // { vals: [1, 2, 3], logValues: logValues()} 1 2
[4, 5, 6].forEach(obj.logValues);  // Window { ... } 4 0
				   // Window { ... } 5 1
                                   // Window { ... } 6 2
```

obj 객체의 logValues는 메서드로 정의되고 메서드로서 호출한 것이다. 따라서 this는 obj를 가리키고, 인자로 넘어온 1, 2가 출력된다. 
 
forEach를 실행하는 곳에서는 이 메서드를 forEach 함수의 콜백 함수로서 전달했다.

obj를 this로 하는 메서드를 그대로 전달한 것이 아니라, obj.logValues가 가리키는 함수만 전달한 것.

이 함수는 메서드로서 호출할 때가 아닌 한 obj와의 직접적인 연관이 없어진다. forEach에 의해 콜백이 함수로서 호출되고, 별도로 this를 지정하는 인자를 지정하지 않았으므로 함수 내부에서의 this는 전역 객체를 바라보게 된다. 
 
어떤 함수인자에 객체의 메서드를 전달하더라도 이는 결국 메서드가 아닌 함수이다.

## 콜백 함수 내부의 this에 다른 값 바인딩하기
콜백 함수 내부에서 this가 객체를 바라보게 하고 싶다면 전통적으로는 this를 다른 변수에 담아 콜백 함수로 활용할 함수에서는 this 대신 그 변수를 사용하게 하고, 이를 클로저로 만드는 방식이 많이 쓰였다.
 
```javascript
var obj1 = {
	name: 'obj1',
    func: function() {
    	var self = this;
        return function () {
        	console.log(self.name);
        };
    }
};
var callback = obj1.func();
setTimeout(callback, 1000);

var obj2 = {
	name: 'obj2',
    func: obj1.func;
};
var callback2 = obj2.func();
setTimeout(callback2, 1500);

var obj3 = { name: 'obj3' };
var callback3 = obj1.func.call(obj3);
setTimeout(callback3, 2000);
```

obj1.func 메서드 내부에서 self 변수에 this를 담고, 익명 함수를 선언과 동시에 반환. 

obj1.func를 호출하면 앞서 선언한 내부 함수가 반환되어 callback 변수에 담긴다. 

이 callback을 setTimeout 함수에 인자로 전달하면 1초(1000ms) 뒤 callback이 실행되면서 'obj1'을 출력된다.
 
또한 callback2에는 obj1의 func를 복사한 obj2의 func를 실행한 결과를 담아 이를 콜백으로 사용했다. 

callback3의 경우 obj1의 func를 실행하면서 this를 obj3가 되도록 지정해 이를 콜백으로 사용했다.

이렇게 this를 우회적으로나마 활용함으로써 다양한 상황에서 원하는 객체를 바라보는 콜백 함수를 만들 수 있는 방법이 있다. 

다른방법으로 개선하려면 bind 메서드를 활용하면 된다. 

```javascript
var obj1 = {
	name: 'obj1',
    func: function() {
        	console.log(this.name);
    }
};

setTimeout(obj1.func.bind(obj1), 1000);

var obj2 = { name: 'obj2' };
setTimeout(obj1.func.bind(obj2), 1500);
```
## 콜백 지옥과 비동기 제어
> 콜백 지옥은 콜백 함수를 익명 함수로 전달하는 과정이 반복되어 코드의 들여 쓰기 수준이 감당하기 힘들 정도로 깊어지는 현상으로, 자바스크립트에서 흔히 발생하는 문제이다.
> 주로 이벤트 처리나 서버 통신과 같이 비동기적인 작업을 수행하기 위해 이런 형태가 자주 등장하는데, 가독성이 떨어질뿐더러 코드를 수정하기도 어렵다.
> 비동기는 동기의 반대말로. 동기적인 코드는 현재 실행 중인 코드가 완료된 후에야 다음 코드를 실행하는 방식.
```javascript
setTimeout(function (name){
	var coffeeList = name;
    console.log(coffeeList);
    
    setTimeout(function(name) {
    	coffeeList += ', ' + name;
        console.log(coffeeList);
        
        setTimeout(function(name) {
        	coffeeList += ', ' + name;
            console.log(coffeeList);
            
            setTimeout(function(name){
            	coffeeList += ', ' + name;
                console.log(coffeeList);
            }, 500, '카페라떼');
        }, 500, '카페모카');
    }, 500, '아메리카노');
}, 500, '에스프레소');
```
위 방식은 깊이가 과도하게 깊어지고 순서가 아래에서 위로 향하고있다. 이를 바꾸는 방법으로는 아래와같이 기명함수로 전환하는 것이다.

```javascript
var coffeeList = "";

var addEspresso = function (name) {
  coffeeList = name;
  console.log(coffeeList);
  setTimeout(addAmericano, 500, "아메리카노");
};

var addAmericano = function (name) {
  coffeeList += ", " + name;
  console.log(coffeeList);
  setTimeout(addMocha, 500, "카페모카");
};

var addMocha = function (name) {
  coffeeList += ", " + name;
  console.log(coffeeList);
  setTimeout(addLatte, 500, "카페라떼");
};

var addLatte = function (name) {
  coffeeList += ", " + name;
  console.log(coffeeList);
};

setTimeout(addEspresso, 500, "에스프레소");
```
 
이 방식은 변수를 최상단으로 끌어올림으로써 외부에 노출되게 됐지만 전체를 즉시 실행 함수 등으로 감싸면 해결될 문제이다. 

## ES6에서 도입된 Promise, Generator, ES2017에서는 도입된 async/await 
### Promise
```javascript
new Promise(function(resolve) {
  setTimeout(function() {
    var name = '에스프레소';
    console.log(name);
    resolve(name);
  }, 500);
}).then(function(prevName) {
  return new Promise(function(resolve) {
    setTimeout(function() {
      var name = prevName + ', 아메리카노';
      console.log(name);
      resolve(name);
    }, 500);
  });
})
```

new 연산자와 함께 호출한 Promise의 인자로 넘겨주는 콜백 함수는 호출할 때 바로 실행되지만 그 내부에 resolve 또는 reject 함수를 호출하는 구문이 있을 경우 둘 중 하나가 실행되기 전까지는 다음(then) 또는 오류 구문(catch)으로 넘어가지 않는다. 
따라서 비동기 작업이 완료될 때 비로소 resolve 또는 reject를 호출하는 방법으로 비동기 작업의 동기적 표현이 가능하다. 
### Generator
```javascript
var addCoffee = function(prevName, name) {
  setTimeout(function() {
    coffeeMaker.next(prevName ? prevName + ', ' + name : name);
  }, 500);
};

var coffeeGenerator = function* () {
  var espresso = yield addCoffee('', '에스프레소');

  // addCoffee('', 에스프레소)의 내부
  // var addCoffee = function('', 에스프레소) {
  // setTimeout(function() {
  //   coffeeMaker.next(prevName ? prevName + ', ' + name : name);
  // }, 500);
  // };
  // 에스프레소 출력
  
  console.log(espresso);
  var americano = yield addCoffee(espresso, '아메리카노');
  console.log(americano);
};

var coffeeMaker = coffeeGenerator();
coffeeMaker.next();
```
'*'이 붙은 함수가 Generator 함수이다. 이 함수를 실행하면 Iterator가 반환되는데, Iterator는 next라는 메서드를 갖고 있다. 
이 next 메서드를 호출하면 Generator 함수 내부에서 가장 먼저 등장하는 yield에서 함수의 실행을 멈춘다. 
이후 다시 next 메서드를 호출하면 앞서 멈췄던 부분부터 시작해서 그다음에 등장하는 yield에서 함수의 실행을 멈춘다. 
비동기 작업이 완료되는 시점마다 next 메서드를 호출해준다면 Generator 함수 내부의 소스가 위에서부터 아래로 순차적으로 진행된다. 

### async/await
```javascript
var addCoffee = function(name){
  return new Promise(function (resolve){
    setTimeout(function() {
      resolve(name);
    }, 500);
  });
};

var coffeeMaker = async function() {
  var coffeeList = '';
  var _addCoffee = async function (name) {
    coffeeList += (coffeeList ? ', ' : '') + await addCoffee(name);
  };
  await _addCoffee('에스프레소');
  console.log(coffeeList);
  await _addCoffee('아메리카노');
  console.log(coffeeList);
}
coffeeMaker();
```  
 
비동기 작업을 수행하고자 하는 함수 앞에 async를 표기하고, 함수 내부에서 실질적인 비동기 작업이 필요한 위치마다 await를 표기하는 것만으로 뒤의 내용을 Promise로 자동 전환하고, 해당 내용이 resolve 된 이후에야 진행한다.
즉 Promise의 then과 흡사한 효과를 얻을 수 있다. 
