# 데이터 타입

## 1-1 데이터 타입의 종류

1. 기본형(Primitive Type)

   - number
   - string
   - boolean
   - null
   - undefined
   - Symbol(ES6에 추가)

2. 참조형(Reference Type)

   - object
   - Array
   - Function
   - Date
   - RegExp

## 1-2 데이터 타입에 관한 배경지식

### 1-2-1 메모리와 데이터

- 비트(bit)
  - 0 or 1을 표현할 수 있는 **하나의 메모리 조각**
  - 각 비트는 고유한 식별자(unique identifier)를 통해 위치를 확인할 수 있다.
- 바이트(byte)

  - 8개의 비트로 구성
  - 256(2⁸)개의 값을 표현할 수 있음(2byte는 2¹⁶ 표현 가능).

- 자바스크립트트의 숫자의 경우 정수형인지 부동소수형인지 구분하지 않고 64비트, 즉 8바이트를 확보한다.
- 모든 데이터는 메모리 주솟값을 통해 서로 구분하고 연결할 수 있다(예를 들어 기가바이트의 메모리 주소도 첫번째 바이트의 메모리 주소가 기가바이트의 메모리 주소가 된다).

### 1-2-2 식별자와 변수

- 변수(variable) : 변할 수 있는 무언가(모든 데이터 종류)
- 식별자 : 어떤 데이터를 식별하는 데 사용하는 이름. 즉, **변수명**

## 1-3 변수 선언과 데이터 할당

### 1-3-1 변수 선언

```js
var a;
```

### 1-3-2 데이터 할당

```js
var a;
a = "abc";

var a = "abc";
```

- 변수 영역에 있는 값 부분엔 데이터 영역의 주솟값이 들어간다.
  - why? 숫자와 string같은 데이터의 메모리 할당량이 다르기 때문에 변환하는 작업을 없애려고 새로운 공간에 저장을 하고 그 데이터영역의 주솟값을 변수영역의 값 부분에 주솟값을 넣어준다.

## 1-4 기본형 데이터와 참조형 데이터

### 1-4-1 불변값

- 변수와 상수를 구분 짓는 변경 가능성의 대상은 변수 영역 메모리이다.
- 불변성 여부를 구분할 때의 변경 가능성의 대상은 데이터 영역 메모리이다.
- 변경되는 부분은 변수 메모리에 할당된 데이터 값에 대한 주소 메모리가 계속해서 변해간다는 것이고, 데이터 메모리에 있는 값은 'abc'에 'def'를 더 하면 'abc'가 있던 주솟값과 별개로 다른 주소에 'abcdef'가 저장되기 때문에 이런 부분에서 불변값이라고 하는 것이다.
- 한 번 만들어진 값은 Garbage Collection를 당하지 않는 한 영원히 변하지 않는다.

- 종류(기본형, 원시형 데이터)
  - 숫자
  - 문자열
  - boolean
  - null
  - undefined
  - Symbol

```
Garbage Collection
프로그래밍 언어에서 자동 메모리 관리를 위한 기법

프로그램이 필요 없는 데이터를 자동으로 정리하여 메모리 누수를 방지하고, 메모리 사용을 최적화하는 역할, 참조 카운트가 0이되면 수거를 한다.

자주쓰는 Garbage Collection 언어 : Java, Python, javascript 등
```

### 1-4-2 가변값

```js
var obj1 = {
  a: 1,
  b: "bbb",
};
```

![image](https://github.com/user-attachments/assets/0cbd7fb5-c740-423e-8380-1826c5faaad7)


- 기본형 데이터와의 차이는 '객체의 변수 영역'이 별도로 존재한다.
- 객체가 별도로 할애한 영역은 변수 영역이고 데이터 영역은 기존의 메모리 공간을 그대로 활용한다.
- 데이터 영역에 저장된 값은 모두 불변값이다.
- 배열도 똑같은 방식으로 변수 영역엔 그 배열에 대한 주솟값이 할당되고 배열의 변수 영역엔 이름과 값이 저장 되는데 값은 데이터값에 대한 주솟값이 저장된다.

### 1-4-3 변수 복사 비교

```js
var a = 10;
var b = a;

var obj1 = { c: 10, d: "ddd" };
var obj2 = obj1;

b = 15;
obj2.c = 20;
```

<img width="456" alt="image" src="https://github.com/user-attachments/assets/0b2a38f2-34a0-476d-aeb5-0b1a4bad31ff">


```js
a !== b;
obj1 === obj2;
```

- 이렇게 나오는데 기본형과 참조형 둘다 값에 대한 주솟값을 저장하는 거지만 기본형은 데이터가 저장되있는 주솟값이 바로 저장돼서 주솟값이 변하기 때문에 !==로 나오고 참조형은 객체 안에 값들이 저장될때 한번 더 변수가 있기 때문에 결국 한번 더 들어가야해서 저런 식으로 복사를 하면 다른 값이더라도 ===로 나온다.

## 1-5 불변 객체

- 사용 이유
  - 예측 가능한 상태 관리
  - 버그 방지
  - 성능 최적화
  - 디버깅 용이
- 불변 객체를 사용하면 코드의 가독성, 유지보수성, 안정성을 높일 수 있으며, 복잡한 애플리케이션일수록 불변성의 장점이 두드러진다.

### 1-5-1 불변 객체를 만드는 간단한 방법

```js
const human = {
  name: "AlphaGo",
  gender: "male",
};

function createHuman(human, name, gender) {
  return {
    ...human,
    name,
    gender,
  };
}

const newHuman = createHuman(human, "BetaGo", "female");
```

```js
const copyObject = function (target) {
  var result = {};
  for (var prop in target) {
    result[prop] = target[prop];
  }
  return result;
};

const ai = {
  name: "John",
  age: 25,
  city: "Seoul",
};

// ai 객체를 copyObject 함수를 사용하여 복사
var copiedAi = copyObject(ai);

console.log(copiedAi); // { name: 'John', age: 25, city: 'Seoul' }
console.log(copiedAi === ai); // (값은 같지만 객체의 주솟값은 다른 메모리에 저장되기 때문에 false)
```

-> 얕은 복사만 수행

- 협업하는 모든 개발자들이 ai 객체 내부의 변경이 필요할 때는 무조건 copyObject함수를 사용하기로 합의하면 ai객체는 불변 객체라고 볼 수 있다.
- 그렇지만 모두가 그 규칙을 지키리라는 법은 없기 때문에 그 규칙을 따르지 않고는 프로퍼티 변경을 할 수 없게끔 시스템적으로 제약을 거는 편이 안전하다.
- 관련된 라이브러리 : immutable.js, baobab.js, immer.js. Mori.js, deep-freeze.js

### 1-5-2 얕은 복사와 깊은 복사

- 얕은 복사는 위에서 사용방법을 말했기 때문에 생략
- 깊은복사

  - 사용 이유
    - **객체 안에 객체 또는 배열**이 있을 경우 복사를 위해 사용
    - 독립적인 객체가 필요할 때 사용 : 데이터 변경이 서로 영향을 미치지 않도록 보장해야 할 때, 복사된 객체와 원본 객체가 완전히 분리된 상태여야한다.
      - 예시) 두 객체가 동일한 참조를 가리킨다면, 하나의 객체를 수정할 때 다른 객체도 영향을 받게 되므로, 깊은 복사로 분리된 객체 생성
  - 사용 방법

    - 1. 재귀적 복사(Recursive Copy)

      - 재귀적으로 객체의 속성을 복사하는 방식
      - 장점
        - 모든 객체 타입에 대해 작동
        - 배열 및 객체를 재귀적으로 처리
      - 단점
        - 순환 참조가 있는 객체는 스택 오버 플로우가 발생할 수 있음.

      ```js
      function deepCopy(obj) {
        if (obj === null || typeof obj !== "object") {
          return obj; // 기본형 값은 그대로 반환
        }

        // 배열인 경우 빈 배열로, 객체인 경우 빈 객체로 초기화
        const copy = Array.isArray(obj) ? [] : {};

        for (let key in obj) {
          if (obj.hasOwnProperty(key)) {
            copy[key] = deepCopy(obj[key]); // 재귀적으로 복사
          }
        }

        return copy;
      }

      const original = {
        name: "John",
        address: {
          city: "Seoul",
          zip: "12345",
        },
      };

      const deepCopied = deepCopy(original);
      deepCopied.address.city = "Busan";

      console.log(original.address.city); // 'Seoul'
      console.log(deepCopied.address.city); // 'Busan'
      ```

    - 2. Json.parse(Json.stringify())

      - 객체를 JSON 문자열로 변환한 다음 다시 객체를 변환하는 방식
      - 장점
        - 간단하고 직관적
        - 대부분의 기본 데이터 타입에 대해 잘 작동
      - 단점
        - 함수, 날짜, 정규 표현식 같은 비직렬화 가능한 속성은 처리를 못한다.
          - 이것들은 js내에서 코드 자체와 실행 환경을 나타내기 때문에 실행 가능한 코드는 JSON 포맷에서 지원하지 않는다. JSON은 순수한 데이터 포맷이므로 코드 실행과 관련된 부분은 배제된다.
        - 순환 참조가 있는 객체를 처리하지 못한다.

      ```js
      const original = {
        name: "John",
        address: {
          city: "Seoul",
          zip: "12345",
        },
      };

      const deepCopy = JSON.parse(JSON.stringify(original));

      deepCopy.address.city = "Busan";

      console.log(original.address.city); // 'Seoul'
      console.log(deepCopy.address.city); // 'Busan'
      ```

    - 3. Lodash 라이브러리 사용

      - Lodash의 \_.cloneDeep 함수 사용
      - 장점
        - 신뢰할 수 있는 싶은 복사 기능 제공
        - 복잡한 객체 구조와 순환 참조까지 처리 가능

      ```js
      const _ = require("lodash");

      const original = {
        name: "John",
        address: {
          city: "Seoul",
          zip: "12345",
        },
      };

      const deepCopied = _.cloneDeep(original);
      deepCopied.address.city = "Busan";

      console.log(original.address.city); // 'Seoul'
      console.log(deepCopied.address.city); // 'Busan'
      ```

    - 4. 구조분해와 전개 연산자(ES6)

      - Spread Operator는 얕은 복사를 수행하지만, 배열이나 객체 안의 중첩된 객체를 수동으로 깊은 복사를 할 수 있다.
      - 장점
        - 간단한 구조에 대해 간편하게 사용
        - 직관적이고 코드가 간결
      - 단점
        - 중첩된 객체가 많을 경우 비효율적일 수 있고, 수동으로 모든 단계의 객체를 복사해야한다.

      ```js
      const original = {
        name: "John",
        address: {
          city: "Seoul",
          zip: "12345",
        },
      };

      const deepCopied = {
        ...original,
        address: {
          ...original.address,
        },
      };

      deepCopied.address.city = "Busan";

      console.log(original.address.city); // 'Seoul'
      console.log(deepCopied.address.city); // 'Busan'
      ```

- 순환 참조 : 객체가 자기 자신을 직접 or 간접적으로 참조하는 경우로 직렬화나 복사 과정에서 무한 루프를 일으킬 수 있다.
- 비 직렬화: JSON으로 변환할 수 없는 데이터 타입(함수, 날짜, 정기 표현식 등)을 포함한 객체는 직렬화 과정에서 데이터를 잃을 수도 있다.
- 스택오버플로우: 재귀 호출을 멈추지 않고 계속되어 메모리 초과가 발생하는 오류(순환 참조를 포함한 객체를 잘못 처리할 때 발생할 수 있다.).

## 1-6 undefined와 null

- 값이나 데이터가 '없음'을 나타낼 때 자바스크립트가 표현할 수 있는 방법

- undefined가 나오는 경우

  - 사용자가 명시적으로 지정
  - 값이 존재하지 않을 때 js엔진이 자동으로 부여

    - 사용자가 응당 어떤 값을 지정할 것이라고 예상되는 상황임에도 실제로는 그렇게 하지 않았을 때 반환

      - 값을 대입하지 않은 변수, 즉 데이터 영역의 메모리 주소를 지정하지 않은 식별자에 접근할 때

      - 객체 내부의 존재하지 않는 프로퍼티에 접근하려고 할때

      - return문이 없거나 호출되지 않는 함수의 실행 결과

  - 직접 undefined를 할당하지 않으면 통제 범위를 벗어나지 않는다.

- null
  - '비어있음'을 명시적으로 나타날 때 null을 사용하면 된다.
  ```
  typeof null은 object로 나오는데 이는 js자체 버그이다.
  null인지 판단하기 위해선 동등 연산자(==) 말고 일치 연산자(===)를 사용해야 한다
  ```

```
배열을 순회하는 함수

forEach: 배열의 모든 요소를 순회하며, 비어있는 요소는 건너뛰고 실행.

map: 배열을 변환한 후 새로운 배열을 반환하며, 비어있는 요소는 건너뛰고 새로운 배열에서도 빈 요소로 유지.

filter: 조건을 만족하는 요소만을 반환하며, 비어있는 요소는 건너뛴다.

reduce: 배열의 모든 요소를 누적하여 하나의 값을 반환하며, 비어있는 요소는 건뛴다.
```
