# 코어자바스크립트(위키북스)

## 1장 - 데이터 타입

### 데이터 타입의 종류

데이터 타입에는 크게 두가지가 있다 : 기본형과 참조형  
기본형은 숫자, 문자열, 불리언, null, undefined 등 (+ ES6에서 symbol 추가됨)  
참조형은 객체, 배열, 함수, 날짜, 정규표현식 등 (+ ES6에서 Map, WeakMap, Set, WeakSet 추가됨)

기본형은 할당이나 연산시 복제되고 참조형은 참조된다.
둘 모두 복제를 하긴 하지만,  
기본형은 값이 담긴 주솟값을 바로 복제하는 반면 참조형은 값이 담긴 주솟값들로 이루어진 묶음을 가리키는 주솟값을 복제한다.

기본형은 불변성을 띈다?

```javascript
let a = 10;
a = 20;
console.log(a); // 20
```

이렇게 보면 값이 변하는 것 처럼 보이는 것이 아닌가?

> 불변성을 이해하기 위해서는 메모리와 데이터의 지식이 필요하고  
> 식별자와 변수의 개념을 구분할 수 있어야한다.

<br/>

---

### 데이터 타입에 관한 배경지식

- 비트 : 0 또는 1만 표현할 수 있는 하나의 메모리 조각<br/>
  메모리는 많은 비트로 구성되어 있으며 각 비트는 고유한 식별자를 통해 위치를 확인할 수 있다.

- 바이트 : 1바이트 = 8비트
  바이트 역시 시작하는 비트의 식별자로 위치를 파악할 수 있다.

모든 데이터는 바이터 단위의 식별자, 즉 주솟값으로 이루어져 있다.

- 변수 : 변할 수 있는 수<br/>
  값이 반드시 숫가 아니어도 된다. 문자열, 객체, 함수 등도 가능하다.

- 식별자 : 어떤 데이터를 식별하는데 사용하는 이름, 즉 변수명

---

### 변수 선언과 데이터 할당

```javascript
var a;
```

이를 풀어보면 '변할 수 있는 데이터를 만든다. 이 데이터의 식별자를 a로 한다.'는 뜻이다.

변수란 결국 변경 가능한 데이터가 담길 수 있는 공간 또는 그릇이다.  
var a; 라는 명령을 받은 컴퓨터는 메모리에서 비어있는 공간 하나를 확보한다. 그 공간의 이름(식별자)을 a로 지정한다 > **변수 선언 과정**

이후 사용자가 a에 접근하고자 하면 메모리에서 a라는 이름을 가진 주소를 찾아 그 공간에 담긴 값을 가져온다 > **데이터 할당 과정**

```javascript
var a = "abc"; // 변수 선언과 데이터 할당
```

a라는 이름을 가진 공간에 'abc'를 할당하는데 실제로는 해당 위치에 문자열 'abc'를 직접 저장하지는 않음.
데이터를 저장하기 위한 별도의 메모리 공간을 다시 확보해서 거기에 'abc'를 저장하고 그 주소를 변수 영역에 저장하는 식으로 이루어진다.

왜? 직접 대입하지 않고 별도의 공간을 확보해서 거기에 저장하는가?

- 데이터 변환을 자유롭게 하고
- 메모리를 효율적으로 사용하기 위함

미리 확보한 공간 내에서만 데이터 변환을 할 수 있다면 변환한 데이터를 다시 저장하기 위해서는 확보된 공간을 해제하고 새로운 공간을 확보하는 과정을 거쳐야 한다.  
컴퓨터가 처리해야할 연산이 많아질 수 밖에 없고,  
결국 효율적으로 문자열 데이터의 변환을 처리하려면 변수와 데이터를 별도의 공간에 나누어 저장하는 해야함.

---

### 기본형 데이터와 참조형 데이터

#### 불변값

변수(variable)과 상수(constant)를 구분하는 성질은 ‘변경 가능성’이다.
불변값은 상수가 아님.
변수 vs 상수 구분 짓는 변경 가능성의 대상은 변수 영역의 메모리
불변성 여부를 구분할 때의 변경 가능성은 데이터 영역의 메모리
기본형 데이터는 모두 불변값이다.

```javascript
var a = "abc"; // 변수 a에 'abc' 데이터 주소 할당
a = a + "def"; // 기본 'abc'에 'def' 추가가 아니라, 새로운 문자열 'abcdef'를 만들어 a에 주소 저장

// 즉, 'abc'와 'abcdef'는 완전 별개의 데이터

var b = 5; // 변수 b에 숫자 5 할당. 데이터 영역에서 5 찾고, 없으면 데이터 공간 만들어 저장
var c = 5; // b에 할당한 숫자 5의 주솟값 재활용
b = 7; // 기존 5값을 변경하지 않고, 데이터 영역에서 7을 찾거나 생성해서 주솟값 변경
```

이처럼 문자열 값은 한번 만들면 변경할 수 없고 변경은 오로지 새로 만드는 동작을 통해서만 이뤄진다.<br/>
한번 만들어진 값은 가비지 컬렉팅을 하지 않은 한 영원히 변하지 않는다.

#### 가변값

참조형 데이터의 기본적인 성질은 가변값이 경우가 많다.
설정에 따라 변경 불가하게 활용할 수도 있음

```javascript
var obj1 = {
  a: 1,
  b: "bbb",
};
```

![image](https://user-images.githubusercontent.com/101851472/222895508-4b25689f-f572-4b43-ae50-2867e2be5895.png)

1. 컴퓨터는 변수 영역의 빈 공간(@1002)를 확보하고, 그 주소 이름을 obj1으로 지정
2. 임의의 데이터 저장공간 (@5001)에 저장하려고 보니 여러개의 프로퍼티로 이뤄진 데이터 그룹임.
   이 그룹 내부의 프로퍼티를 저장하기 위해 별도의 변수 영역 마련 후, 그 영역의 주소(@7103 ~?)를 @5001에 저장
3. @7103 및 @7104에 각각 a와b라는 프로퍼티 이름 지정
4. 데이터 영역에서 숫자 1 검색.
   검색 결과 없으므로 임의로 @5003에 저장하고 이 주소를 @7103에 저장.
   'bbb'역시 마찬가지로 작업
   기본형 데이터와의 차이는 객체의 변수(프로퍼티) 영역이 별도로 존재한다는 점이다.
   객체가 별도로 할애한 영역은 ‘변수 영역'일 뿐 ‘데이터 영역'은 기존의 메모리 공간 그대로 활용. 즉, 데이터 영역에 저장된 값은 불변값
   하디만 변수에는 언제든지 다른 값 대입할 수 있다. 이 점이 가변성.
   따라서 참조형 데이터는 불변(immutable)하지 않다고 한다.
   가비지 컬렉터:

<br/>
어떤 데이터에 대해 자신의 주소를 참조하는 변수의 갯수를 ‘참조 카운트' 라고 한다. (ex. 주소 @5003을 참조하는 주소는 @7103 하나 이므로 참조 카운트는 1)<br/>
문자열 재할당 후 사용하지 않는 데이터는 참조 카운트가 0이 된다.
<br/>
참조 카운트가 0인 메모리 주소는 가비지 컬렉터의 수거 대상이 된다.<br/>
가비지 컬렉터는 특정 시점이나 메모리 사용량이 포화 상태에 임박할 때마다 자동으로 수거 대상들을 수거하고,
수거된 메모리는 다시 새로운 값을 할당할 수 있는 빈 공간이 된다.

---

### 불변 객체
참조형 데이터에서의 가변은 데이터 자체가 아닌 내부 프로퍼티의 변화를 의미한다.

#### 불변 객체를 만드는 간단한 방법
기존 데이터는 변하지 않고 내부 프로퍼티를 변경할 필요가 있을 때마다 매번 새로운 객체를 만들어 재할당하기로 규칙을 정하거나 자동으로 새로운 객체를 만드는 도구 활용하면 불변성 확보 가능<br/>
불변 객체 → 값으로 전달받은 객체에 변경을 가하더라도 원본 객체는 변하지 않아야 하는 경우 필요!
```javascript
let a = 10;
let b = a;

let obj1 = {c:10, d:'ddd'};
let obj2 = obj1;
// console.log(obj1);
// console.log(obj2);

// 1) 객체의 프로퍼티 변경
obj2.d = 'eee';
console.log(obj1);   // {c: 10, d: 'eee'}
console.log(obj2);   // {c: 10, d: 'eee'}

// 2) 객체 자체의 변경
obj2 = {c:20, d:'ddd'};
console.log(obj1);      // {c: 10, d: 'ddd'}
console.log(obj2);      // {c: 20, d: 'ddd'}
```

```javascript
// 객체의 가변성으로 인한 문제점
let user = {
    name : 'yeni',
    age : 30,
    gender : 'female'
}

let changeName = (user, newName) => {
    let newUser = user;
    newUser.name = newName;
    return newUser;
}

let user2 = changeName(user, 'Kim');

if(user !== user2){
    console.log("유저 정보가 변경되었습니다!");
}


console.log(user.name, user2.name);  // Kim Kim
console.log(user === user2);         // true
```
위 예시처럼 user2 호출 시, 내부 프로퍼티만 변경함으로써 기존 user 객체를 동시에 변경하였다. (가변성)
user와 user2가 다른 값을 가질 수 있도록 하려면 아래와 같이 새로운 객체를 반환하도록 수정하면 된다.

```javascript
// 객체의 가변성에 따른 문제점의 해결방법
let user = {
    name : 'yeni',
    age : 30,
    gender : 'female'
}

let changeName = (user, newName) => {
    return {
        name : newName,
        age : user.age,
        gender : user.gender
    }
}

let user2 = changeName(user, "Kim");

if(user !== user2){
    console.log("유저 정보가 변경되었습니다.");   
    // 유저 정보가 변경되었습니다.
}

console.log(user.name, user2.name);  // yeni Kim
console.log(user === user2);         // false

```
하지만, 변경된 코드의 경우 변경할 필요가 없는 기존 객체의 ‘gender’속성도 하드코딩으로 입력했다. 객체에 정보가 많을수록 비효율적이게 되는데, 프로퍼티 갯수에 상관없이 모든 프로퍼티를 복사하는 함수를 만들 수 있다.

```javascript
// 얕은 복사
var copyObject = function (target) {
	var result = {};
	for (var prop in target) {
			result[prop] = target[prop]
	}
}
```
물론 협업하는 모든 개발자들이 copyObject라는 얕은 복사를 사용하면 문제가 되지 않는다. (=user 객체가 곧 불변 객체)
하지만, 그 규칙을 지키지 않을 수도 있으므로 아예 프로퍼티를 변경할 수 없게 제약을 거는게 더 안전하다.

---
### 얕은복사와 깊은복사

1) 얕은복사 : 바로 아래 단계의 값만 복사하는 방법
2) 깊은복사 : 내부의 모든 값들을 하나하나 전부 복사하는 방법

```javascript
// 중첩된 객체에 대한 얕은 복사

let user = {
    name: 'Yeni',
    info: {
        age : 30,
        gender : 'female',
        address : 'Seoul'
    }
}

let copyobject = (user) => {
    let result = {};
    for (let prop in user){
        result[prop] = user[prop];
    }
    return result;
}

let user2 = copyobject(user);
user2.name = 'Kim';
console.log(user.name === user2.name);   // false

user.info.age = 35;
console.log(user.info.age === user2.info.age);  // true
console.log(user.info.age);  // 35

user2.info.address = 'Kyonggi';
console.log(user.info.address === user2.info.address);   // true
console.log(user.info.address);  // Kyonggi
```

> user 객체에 직접 속한 프로퍼티에 대해서는 복사해서 완전히 새로운 데이터가 만들어진 반면, 한 단계 더 들어간 info의 내부프로퍼티들은 기존 데이터를 그대로 참조 => user.info 프로퍼티에 대해서도 불변 객체로 만들어야함. 


```javascript
// 중첩된 객체에 대한 깊은 복사
let user2 = copyobject(user);
user2.info = copyobject(user.info);
user.info.age = 35;
console.log(user.info.age === user2.info.age);  // false
console.log(user.info.age);   // 35
console.log(user2.info.age);  // 30

user2.info.address = 'Kyonggi';
console.log(user.info.address === user2.info.address);   // false
console.log(user.info.address);   // Seoul
console.log(user2.info.address);  // Kyonggi
```

<br />

깊은 복사를 하는 여러가지 방법이 있다.
> 1) Object.assign()  
> 2) spread 연산자
> 3) JSON.stringify()와 JSON.parse()
> 4) 커스텀 재귀 함수를 구현한 복사

<br/>

1) object.assign()을 사용한 깊은 복사 : Object.assign(생성할 객체, 복사할 객체)  
<br/> 하지만 Object.assign()를 활용한 복사는 완벽한 깊은 복사가 아니다.
Object.assign()는 2차원 객체는 깊은 복사가 이루어지지 않는다.  
<br/> MDN에서는 "Object.assign()은 속성의 값을 복사하기때문에 다른 대안을 사용해야한다. 출처 값이 객체에 대한 참조인 경우, 참조 값만을 복사합니다."라고 설명한다.
<br/>
<br/>

2) spread 연산자를 사용한 깊은 복사 : const copyObj = {...obj};  
<br/>하지만 전개 연산자도 Object.assign()와 마찬가지로 2차원 객체는 얕은 복사가 되는 문제점이 있다.
<br/>
<br/>

3) JSON.stringify()와 JSON.parse()를 사용한 깊은 복사 : JSON.parse(JSON.stringify(obj));  
<br/>JSON.stringify()을 사용해서 JSON 포맷으로 변경(문자열형태로) ⇒ 복사 후 JSON.parse()로 다시 객체 형태로 만들어준다.  
이 방법이 가장 간단하고 쉽지만 다른 방법에 비해 느리다는 것과 객체가 function일 경우, undefined로 처리한다는 것이 단점이다.

```javascript
// 복사된 newObj는 func가 없고 undefined로 출력되고 있다.
const obj = {
  a: 1,
  b: {
    c: 2,
  },
  func: function() {
    return this.a;
  }
};

const newObj = JSON.parse(JSON.stringify(obj));

console.log(newObj.func); // undefined
```
<br/>
<br/>

4) 커스텀 재귀 함수를 구현한 복사  
이 문제를 원칙적으로 해결하려면 직접 깊은 복사를 구현하는 커스텀 재귀 함수를 사용하는 것인데 복잡하다는 것이 단점이다.

```javascript
// 깊은 복사가 가능하도록 함수 수정
let copyobjectDeepCopy = (user) => {
    let result = {};
    if(typeof user === Object && user !== null){
        for (let prop in user){
            result[prop] = copyobjectDeepCopy(user[prop]);
        }
    } else {
        result = user;
    }
    return result;
}
```

<br/>
<br/>

> 어떤 객체를 복사할 때, 객체 내부의 모든 값을 복사해서 완전히 새로운 데이터를 만들고자 할 때, 객체의 프로퍼티 중에서 그 값이 기본형 데이터일 경우에는 그대로 복사하면 되지만 **참조형 데이터는 다시 그 내부의 프로퍼티들을 복사**해야한다.   
참조형 데이터가 있을 때마다 재귀적으로 수행해야만 비로소 깊은 복사가 되는 것!

---
### undefined와 null
- undefined : 값이 할당되지 않은 상태   
- null : 값이 없음을 의도적으로 명시한 상태

1) undefined  
undefined는 사용자가 명시적으로 지정할 수도 있지만 값이 존재하지 않을 때 자바스크립트 엔진이 자동으로 부여하는 경우가 있다.  
사용자가 어떤 값을 지정할 것이라고 예상되는 상황에서 값이 지정되지 않았을 때 undefined가 부여된다.  
>- 값을 대입하지 않은 변수에 접근할 때
>- 객체 내부에 존재하지 않는 프로퍼티에 접근할 때
>- return 문이 없거나 호출되지 않은 함수의 실행 결과

```javascript
var a;
console.log(a);   // undefined


var obj = { a : 1 };
console.log(obj.a);  // 1
console.log(obj.b);  // undefined
console.log(b);      // ReferenceError: b is not defined


var func = function(){ };
var c = func();
console.log(c);      // undefined
```

> 비어있는 요소와 undefined을 할당한 요소는 다르다.

<br/>

```javascript
var arr1 = [undefined, 1];
var arr2 = [];
arr2[1] = 1;

arr1.forEach((v, i)=> console.log(v, i));
// undefined 0
// 1 1
arr2.forEach((v, i)=> console.log(v, i)); // 1 1

arr1.map((v, i)=> v+i);  // [NaN, 2]
arr2.map((v, i)=> v+i);  // [비어 있음, 2]

arr1.filter((v)=> !v);   // [undefined]
arr2.filter((v)=> !v);   // []

arr1.reduce((p, c, i)=> p+c+i, '');   // 'undefined011'
arr2.reduce((p, c, i)=> p+c+i, '');   // '11'
```

> 직접 undefined를 할당한 arr1은 배열의 모든 요소를 순회해서 결과를 출력하지만,  
비어있는 요소를 가진 arr2는 비어있는 요소를 무시하고 결과를 출력한다.

배열도 객체와 마찬가지로 특정 인덱스에 값을 지정할 때 비로소 빈 공간을 확보하고 인덱스를 이름으로 지정하고 데이터의 주솟값을 저장하는 등의 동작을 한다.  
즉, 값을 지정하지 않은 인덱스는 **아직은 존재하지 않는 프로퍼티**

undefined가 '비어있음'을 의미하긴 하지만 하나의 값으로 동작   
=> 프로퍼티나 배열의 요소는 고유의 키값(프로퍼티 이름)이 존재, 순회의 대상이 된다.

그러나 js 엔진이 반환하는 undefined는 해당 프로퍼티, 배열의 키값(인덱스) 자체가 존재하지 않음을 의미.


2) null  
비어있음을 명시적으로 표현하는 값  
null의 type은 object이다.
```javascript
var n = null;
console.log(null == undefined);   // true
console.log(null === undefined);  // false
```