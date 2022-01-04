# 2. 실행 컨텍스트



### 1. 실행 컨텍스트란?

* 실행 컨텍스트란 무엇인가요?

  * 자바스크립트 엔진에 의해 만들어지고 사용하는 코드 정보를 담은 객체 집합입니다.
  * 실행할 코드에 제공할 환경 정보를 모아놓은 객체입니다.

* 실행 컨텍스트를 생성하는 방법은 어떤 것이 있을까요?

  * 전역 공간, eval() 함수, 함수 호출이 있습니다. 각 방법은 자신만의 실행 컨텍스트를 생성합니다

* 실행 컨텍스트에 수집되는 정보는 어떤 것이 있나요?

  * VariableEnvironment, LexicalEnvironment, thisBinding 이 수집됩니다.

  

***연관***

> ***실행 컨텍스트는 생성, 활성화 (실행) 시점으로 구분되어 진행됩니다.***



* 실행 컨텍스트가 생성되는 시점은 언제일까요?
  * 전역 실행 컨텍스트의 경우, 엔진이 스크립트 파일을 실행하기 전에 생성됩니다.
    함수 실행 컨텍스트의 경우 함수가 호출 될 때 생성됩니다.

* 실행 컨텍스트가 활성화 되는 시점은 언제일까요?
  * 실행 컨텍스트가 콜스택에 생성되는 시점에는 활성화가 되지 않습니다. 이후, 콜스택에 쌓인 실행 컨텍스트들이 차례대로 활성화됩니다. 실행 컨텍스트는 여러 개가 동시에 활성화 될 수 없는데, 이는 자바스크립트가 싱글 쓰레드 언어이기 때문에 그렇습니다.
* 실행 컨텍스트에서 메모리는 언제 할당될까요?
  * var 와 함수 선언문은 즉시 메모리가 할당됩니다.
    그러나, let, const 로 선언한 식별자는 해당 시점에서는 메모리가 할당되지 않고 (TDZ - Temporal Dead Zone) 초기화 시점에서 메모리가 할당되어 집니다.



### 2. VariableEnvironment

* VariableEnvironment 에는 어떤 내용이 담기나요?
  * environmentRecord 와 outer 참조 정보를 가집니다.
  * 엄밀히 말하면, environmentRecord 에는 `var` 로 선언한 변수와 함수 선언문이 저장됩니다.




### 3. LexicalEnvironment

* LexicalEnvironment 에는 어떤 내용이 담기나요?
  * arguments, 식별자 정보들과 outer 참조 정보를 가집니다.
  * 엄밀히 말하면, environmentRecord 에는 `let, const` 로 선언한 변수와 `arguments` 가 저장됩니다.




***연관***

* VariableEnvironment 와 어떤 차이점이 있나요?

  * 동일한 성격을 띄지만, VariableEnvironment 는 `var` 로 선언한 변수와 함수 선언문만 저장하며, 
    `let, const` 나 `arguments` 는 `LexicalEnvironment` 에 저장됩니다.

  

### 3-1. environmentRecord 와 호이스팅

* 호이스팅은 어떤 것인가요?
  * 실행 컨텍스트 생성 시 Lexical Scope 내의 선언이 끌어올려지는 것을 호이스팅이라고 합니다.
* 호이스팅이 발생하는 시점은 언제인가요?
  * 실행 컨텍스트가 활성화 될 때, 호이스팅이 발생합니다.
* environmentRecord 에는 어떤 것들이 담기나요?
  * 식별자 정보들이 담깁니다. 매개변수 식별자, 함수 선언문 등이 있습니다.
* 함수 선언문과 함수 표현식은 어떤 것이 다른가요?
  * 함수 선언문은 별도에 식별자에 할당하지 않은 함수 그 자체이며, 표현식은 function 을 새로운 변수에 할당하는 것을 말합니다.
    

***연관***

* const 의 경우엔 hoisting 이 일어나지 않는데, 이 이유는 무엇일까요?
  * 엄밀히 말하면, hoisting 은 일어나지만 TDZ 에 있기 때문에 사용할수는 없습니다. 
  * 실행 컨텍스트가 생성될 때, let, const 로 생성된 식별자는 선언부가 수집되지만 값이 할당되지 않습니다.
    이에 반해, var 식별자는 undefined 값이 할당되므로 식별자 선언 이전에 값을 사용할 수 있습니다.
* React 에서 forwardRef, HoC 등으로 함수들을 감쌀 때, 익명 함수와 기명 함수의 차이는 어떤 점이 있을까요?
  * 기명 함수와 달리 익명 함수는 displayName 이 없어서 React Debugger Tool Name 에 익명으로 보이게 됩니다. 사소하지만, 디버깅을 어렵게 만들 수 있습니다.



### 3-2. 스코프, 스코프 체인, outerEnvironmentReference

* ES5, ES6 에서의 스코프 생성 규칙을 설명해주세요.

  * ES5 에서는 함수에 의해서만 스코프가 생성됩니다. 이 때문에 if, while() 문 블록 내에서의 식별자를 블록 바깥에서 접근할 수 있습니다.
    그러나 ES6 에서는 블록에 의해서 스코프가 생성됩니다. 즉, `{}` 내부에 선언된 변수를 참조할 수 없습니다. 

    


  ```js
  // ES5
  
  if ( 5 > 4 ) {
    var secret = '12345';
  }
  
  secret; // 12345
  
  
  function a() {
    var secret2 = '12345';
  }
  
  secret2 ;  // secret2 is not defined
  
  
  // ES6
  
  if( 5 > 4 ) {
    let secret = '12345';
    const secret2 = '12345';
  }
  
  secret;  // secret is not defined
  sercret2;  // secret2 is not defined
  ```



***연관***

* outer 참조는 언제 선언될까요?
  * 실행 컨텍스트는 생성, 활성화 단계를 거치는데, outer 참조는 생성 단계에서 포인터가 할당되어 집니다.



### 4. this

* 실행 컨텍스트에서 this 는 어디에 저장되나요?
  * thisBinding 에 저장됩니다.
    
    


### 참고자료

[Must-Know-About-Frontend](https://github.com/baeharam/Must-Know-About-Frontend/blob/main/Notes/javascript/execution-context.md)

[Interview_Question_for_Beginner](https://github.com/JaeYeopHan/Interview_Question_for_Beginner)

[자바스크립트는 왜 프로토타입을 선택했을까](https://medium.com/@limsungmook/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EB%8A%94-%EC%99%9C-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85%EC%9D%84-%EC%84%A0%ED%83%9D%ED%96%88%EC%9D%84%EA%B9%8C-997f985adb42)

[How JavaScript works behind the scene - Execution Context and Call Stack](https://dev.to/swastikyadav/how-javascript-works-behind-the-scene-execution-context-and-call-stack-3dnc)

[[JavaScript] 함수 스코프와 블록 스코프](https://velog.io/@cheal3/JavaScript-%ED%95%A8%EC%88%98-%EC%8A%A4%EC%BD%94%ED%94%84-vs-%EB%B8%94%EB%A1%9D-%EC%8A%A4%EC%BD%94%ED%94%84)

