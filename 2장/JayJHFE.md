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
> 
