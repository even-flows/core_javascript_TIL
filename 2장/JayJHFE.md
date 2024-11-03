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
