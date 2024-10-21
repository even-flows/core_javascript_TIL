# 데이터 타입

## 데이터 타입의 종류

### 데이터 저장

    0과 1로 포현되는 데이터는 메모리에 저장될때 1비트의 크기로 저장된다.
    비트단위를 묶어 한 단위로 표기하게 되는데 8개의 비트를 묶어 1바이트로 표기한다. ( 8비트 = 1바이트 )

### 기본형과 참조형

    기본형은 불변성, 참조형은 가변성을 띈다.
    기본형은 숫자, 문자열, boolean, null, undefined, symbol등의 값이다.
    참조형은 객체와 배열이다.

    사실 자바스크립트의 데이터타입은 모두 참조형이다.

## 변수선언과 메모리 영역
![image](https://github.com/user-attachments/assets/9138617f-ee74-439c-b77f-af5a558a21ce)
  
    let 또는 var 이후에오는 변수명이 식별자로써 동작한다.
    
    메모리영역은 위 그림 처럼 표기되며 변수영역과 데이터 영역이 나뉘어 저장되게 된다.
    데이터를 변수영역, 데이터 영역없이 구분지어 저장하는 이유는 메모리의 '효율적 사용' 이다.
    
    자바의 경우 정수형 타입에대해 메모리 크기를 2바이트, 4바이트 등으로 나누어 정의를 해놓았다. 반면 문자열의 경우     정해진 규격이 없기때문에 글자수에 따라 메모리 용량이 가변적으로 변하게된다.
    하지만 변수영역과 데이터영역을 구분 짓는다면 이를 통해 값이 변경될 때 데이터영역의 주솟값만 바꿔주면
    데이터 구조상 변경이 일어날때 컴퓨터의 연산을 줄이고 효율이 증대된다.
    변수자체에 값이 할당되어있다면 값 변경 시 메모리의 용량을 늘리고 줄이면서 옆메모리로 옮기는 작업까지 진행해야 
    하기 때문이다.
    

### 가변값과 불변값

    불변값은 데이터 영역의 데이터가 선언된 순간, 식별자를 통해 수정하여도 수정되지않고 다른 주솟값에 수정된 값이 
    저장된다.
    가변값은 데이터 영역에 데이터가 선언되더라도, 주솟값에 대한 변수영역의 값이 다시 설정된다.

### 변수의 복사
    
    변수를 복사 하였을 때 식별자는 달라지지만 데이터에대한 주솟값은 같은 곳을 바라본다.
    이는 가변값과 불변값 둘다 공통으로 적용되는 사항이다.
    하지만 복사 후 변수를 변경하였을 때는 다르게 작동하게된다.
    위에 언급했듯이 자바스크립트의 데이터타입은 모두 참조형이지만, 기본형과 참조형으로 나누는 이유는
    복사에서 비롯된다. 변수 복사 시 기본형의 경우 참조를 한번, 참조형의 경우 참조를 두번 하게되는 차이 인 것이다.

    참조형 데이터가 가변값이라고 설명할 때 가변은 참조형 데이터 자체를 변경할 경우가 아닌, 그 내부의 프로퍼티를 변경
    할때만 성립한다. 참조형 데이터의 내부 프로퍼티를 변경하게되면, 데이터영역의 주솟값에 대한 변수영역과 데이터 영역
    이 새로 추가된다는 뜻이다.

## 객체 가변성의 문제점

```javascript
    var user = {
	name: 'Jaenam',
  	gender: 'male'
    };
    
    var changeName = function (user, newName) {
    	var newUser = user;
      	newUser.name = newName;
      	return newUser;
    };
    
    var user2 = changeName(user, 'Jung');
    
    if (user !== user2) {
    	console.log('유저 정보가 변경되었습니다.');
    }
    console.log(user.name, user2.name); // Jung Jung
    console.log(user === user2); // true
```
## 해결법
```javascript
var user = {
	name: 'Jaenam',
  	gender: 'male'
};

var changeName = function (user, newName) {
  	return {
    	name: newName,
      	gender: user.gender
    };
};

var user2 = copyObject(user); // 얕은 복사만 수행한다.
user2.name = 'jung';

if (user !== user2) {
	console.log('유저 정보가 변경되었습니다.'); // 유저 정보가 변경되었습니다.
}
console.log(user.name, user2.name); // Jaenam Jung
console.log(user === user2); // false
```

