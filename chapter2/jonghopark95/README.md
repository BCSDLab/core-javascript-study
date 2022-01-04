# 2. 실행 컨텍스트



### 개요

1. 실행 컨텍스트란?
2. VariableEnvironment
3. LexicalEnvironment
   1. environmentRecord 와 호이스팅
   2. 스코프, 스코프 체인, outerEnvironmentReference

4. this

[정답](./answer.md)



### 1. 실행 컨텍스트란?

* 실행 컨텍스트란 무엇인가요?

* 실행 컨텍스트를 생성하는 방법은 어떤 것이 있을까요?

* 실행 컨텍스트에 수집되는 정보는 어떤 것이 있나요?

  

***연관***

* 실행 컨텍스트가 생성되는 시점은 언제일까요?

* 실행 컨텍스트가 활성화 되는 시점은 언제일까요?

* 실행 컨텍스트에서 메모리는 언제 할당될까요?



### 2. VariableEnvironment

* VariableEnvironment 에는 어떤 내용이 담기나요?



### 3. LexicalEnvironment

* LexicalEnvironment 에는 어떤 내용이 담기나요?
  

***연관***

* VariableEnvironment 와 어떤 차이점이 있나요?
  

### 3-1. environmentRecord 와 호이스팅

* 호이스팅은 어떤 것인가요?
* 호이스팅이 발생하는 시점은 언제인가요?
* environmentRecord 에는 어떤 것들이 담기나요?
* 함수 선언문과 함수 표현식은 어떤 것이 다른가요?
  

***연관***

* const 의 경우엔 hoisting 이 일어나지 않는데, 이 이유는 무엇일까요?
* React 에서 forwardRef, HoC 등으로 함수들을 감쌀 때, 익명 함수와 기명 함수의 차이는 어떤 점이 있을까요?



### 3-2. 스코프, 스코프 체인, outerEnvironmentReference

* ES5, ES6 에서의 스코프 생성 규칙을 설명해주세요.



***연관***

* outer 참조는 언제 선언될까요?



### 4. this

* 실행 컨텍스트에서 this 는 어디에 저장되나요?



***연관***

* ES6 에서 this 는 어떤 시점에 바인딩되나요?
* 전역 실행 컨텍스트와 함수 실행 컨텍스으에서 this 는 어떤 값이 바인딩되나요?
  

[정답](./answer.md)