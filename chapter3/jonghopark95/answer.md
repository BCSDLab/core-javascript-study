# 2. 실행 컨텍스트

### 1. 상황에 따라 달라지는 this

- this 는 언제 결정되나요?

  - 함수를 호출할 때 결정됩니다.

- 콜백 함수에서 this 는 어떻게 결정되나요?
  - 제어권을 위임한 객체에 바인딩됩니다.
- arrow function 의 this 가 결정되는 방식을 설명해보세요.
  - function keyword 는 this 가 동적으로 결정되지만, arrow function 의 경우 `this` 는 정적으로 결정됩니다.
    호출되는 순간 자신을 감싼 scope 를 가리키게 되는데, method 로 호출되거나 `call, apply, bind` 등의 명시적인 바인딩 함수도 무시되며 오로지 scope 에 의해 결정됩니다.

**_연관_**

* 실행 컨텍스트에서 this 는 어디에 저장되나요?

  - thisBinding 에 저장됩니다.

* `strict mode` 에서 this 는 어떻게 정의되나요?

  * undefined 로 정의됩니다.

  

### 2. 명시적으로 this를 바인딩하는 방법

- 명시적으로 this 를 바인딩 할 수 있는 메서드는 어떤것이 있나요?

  - call, apply, bind 가 있습니다.

- NodeList 타입을 Array 의 `reduce` 메서드를 사용하는 방법에 대해 설명해보세요.

  * Array 생성자를 사용하여. `call, apply` 메서드를 호출하면 사용할 수 있습니다.
    NodeList 가 유사 배열 객체이기 때문에 가능합니다.

  * Array.from 이나 `spread` 연산자도 가능합니다.

    ```js
    const divs = document.querySelectorAll('div');
    const divArray = Array.prototype.slice.call(divs);
    
    // OR
    const divArray = Array.from(divs);
    
    // OR
    const divArray = [...divs];
    ```

    

**_연관_**

- this 바인딩의 우선순위에 대해 설명해보세요.
  * 1. new 를 사용하여 함수 생성자로서 호출할 때, 해당 객체로 바인딩 됩니다.
  * 2. call, apply, bind 등의 명시적 바인딩을 사용했을 때 인자로 전달된 객체에 바인딩됩니다.
  * 3. 객체의 method 로 함수를 호출할 때, 해당 객체에 바인딩 됩니다.
  * 4. 그 외의 경우, `strict mode` 일 경우 `undefined` 로 초기화되며 아니라면 브라우저는 `window` 객체에 바인딩됩니다

### 


### 참고자료

[Must-Know-About-Frontend](https://github.com/baeharam/Must-Know-About-Frontend/blob/main/Notes/javascript/this.md)

[Interview_Question_for_Beginner - this 에 대해서](https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/JavaScript#this-%EC%97%90-%EB%8C%80%ED%95%B4%EC%84%9C)

[자바스크립트는 왜 프로토타입을 선택했을까](https://medium.com/@limsungmook/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EB%8A%94-%EC%99%9C-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85%EC%9D%84-%EC%84%A0%ED%83%9D%ED%96%88%EC%9D%84%EA%B9%8C-997f985adb42)

[2ssue's dev note](https://2ssue.github.io/common_questions_for_Web_Developer/docs/Javascript/)
