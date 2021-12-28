# 1. 데이터 타입



### 1. 데이터 타입의 종류

* 자바스크립트의 기본형 데이터 타입에는 어떤 것이 있을까요?
  * number, string, boolean, null, undefined, Symbol, BigInt 가 있습니다.




* 참조형 데이터 타입에는 어떤 것이 있을까요?
  * object 타입에는 객체, 함수, 배열 등이 있습니다.



* 자바스크립트의 데이터 타입에는 기본형, 참조형 타입이 있습니다. 이 두 가지의 차이점은 무엇인가요?

  * 기본형의 경우 Call Stack 에 값을 할당합니다.
    그러나, 참조형의 경우 Memory Heap 에 값을 할당하며 해당 Heap 주소값을 Call Stack 에 할당합니다.

  

* 기본형 타입은 immutability 한 성질을 가지고 있습니다. 그러나, `let a = 10; a = 20;` 에서 a 는 변합니다. 
  그렇다면 immutability 라는 것은 어떤 의미일까요?

  * 불변성은 해당 식별자가 참조하는 값이 변하지 않는 다는 것입니다.
    10, 20의 경우, 변수값 선언 시점에 메모리를 새로 생성하여 주소값을 받게 됩니다.
    식별자 a 가 가리키고 있는 주소값은 이전에 10에서 20으로 변하게 됩니다.
    즉, 식별자가 가리키는 주소값이 변하는 것이지 기본형 타입이 변하는 것은 아닙니다.



* string은 primitive type 입니다. 그러나, `.length` 등 여러 메소드를 사용할 수 있고 배열 처럼 `특정 index` 를 참조할 수도 있습니다. 이는 string은 object 라는 것 아닐까요? 어떻게 이 현상이 일어나는 걸까요?

  * 특정 string 이 method 를 사용하려 하거나, property 조회를 하게 될 경우 JS 는 string primitive 를 `String object` 로 감싸며 메소드나 property 조회를 할 수 있게 만듭니다. 이는 `Boolean` ,`Number` 도 마찬가지 입니다. 
    
  

### 2. 데이터 타입에 관한 배경지식

* 자바스크립트에서 숫자의 경우 몇 바이트를 할당하나요?
  * 자바스크립트는 변수값 할당 과정에서 해당 값이 숫자임을 알게 될 경우, 8바이트의 메모리를 확보합니다.
    

* 변수와 식별자의 차이에 대해 설명해주세요.
  * 변수란 변할 수 있는 데이터가 담길 수 있는 공간이며, 식별자는 이를 식별하는 데 사용하는 이름입니다.
    

### 3. 변수 선언과 데이터 할당

* 변수는 어떤 과정을 거쳐서 생성되나요?
  * 변수는 선언, 초기화, 할당이라는 세 가지 과정을 거쳐 생성됩니다.
    선언 과정에선 변수 이름을 해당 실행 컨텍스트의 Lexical Environment 에 등록합니다.
    초기화 과정에선 값을 저장하기 위해 메모리 공간을 확보하고, 해당 메모리 주소값을 변수 값에 할당합니다.
    할당 과정에선 초기화 되어 있던 값에 새로운 값을 할당합니다.



* 변수 이름 (식별자) 는 어디에 등록되나요?
  * 실행 컨텍스트에서 식별자를 수집하며 Lexical Environment 에 등록하게됩니다.



* 함수 선언문과 함수 표현식은 어떻게 다른가요?

  * 함수 선언문의 경우, 해당 함수가 한꺼번에 호이스팅됩니다.
    이 때문에 해당 함수 선언 이전에 함수를 참조할 수 있습니다.
    그러나 함수 표현식의 경우, 해당 함수 선언부만 호이스팅 되고 할당은 Runtime 에 됩니다.
    이 때문에, 해당 함수 선언 이전에 함수를 호출할 수 없습니다.

  

* arrow function 과 function 키워드는 어떻게 다른가요?

  * 화살표 함수의 this 는 정적으로 결정됩니다.
    화살표 함수 내부에서 this 에 접근하면 항상 상위 lexical scope의 this 를 가리킵니다. (Lexical this)

    이 때문에 콜백 함수로 사용하기 편리합니다.

  * 화살표 함수는 생성자 함수로서 사용할 수 없습니다.
    생성자 함수는 prototype 프로퍼티를 가지지만 화살표 함수는 prototype 프로퍼티를 가지지 않기 때문입니다.

  * 화살표 함수는 arguments 가 없습니다.



### 4. 기본형 데이터와 참조형 데이터

* 기본형과 참조형 데이터의 차이점은 무엇인가요?
  * 기본형 데이터는 값을 Call Stack 에 저장합니다. 
    참조형 데이터는 해당 object 를 memory heap 에 저장하고, heap 주소값을 Call Stack 에 저장합니다.
* const 는 흔히들 상수라고 얘기합니다. 근데, const 로 할당된 배열 객체에 push 를 통해 값을 추가할 수 있습니다.
  const 는 상수인데 어떻게 값을 변화시킬 수 있는 걸까요?
  * const 에 object 를 할당할 경우, memory heap 에 object 를 위한 메모리를 만들고 해당 주소값을 const 식별자에 할당합니다. 즉, 해당 주소값은 변하진 않지만 참조하고 있는 object 는 값을 변화시킬 수 있습니다.



### 5. 불변 객체

* 얕은 복사와 깊은 복사는 어떤 점이 다른가요?
  * 얕은 복사는 복사할 대상 객체의 주소값만 복사합니다.
    그러나, 깊은 복사는 대상 객체 주소값이 참조하고 있는 Memory heap 에 있는 대상의 값들을 복사합니다.
    때문에, 얕은 복사가 참조하고 있는 값을 변화시키면 해당 주소값을 참조하고 있는 모든 값이 변화합니다.
    그러나, 깊은 복사는 아예 다른 주소값을 참조하고 있으므로 이전 참조값이 변화하지 않습니다.



### 6. undefined와 null

* JS 엔진이 undefined 를 반환하는 경우는 어떤 것이 있을까요?
  * 값을 할당하지 않은 식별자를 참조하는 경우, 초기값이 undefined 이므로 이를 반환합니다.
    또, object 내부에 존재하지 않는 property 에 접근하는 경우 undefined 를 반환합니다.
    마지막으로, 반환값을 지정하지 않은 함수의 실행 결과로 undefined를 반환합니다.



* var 와 let, const 는 어떻게 다른가요?

  * 변수는 선언, 초기화, 할당의 세 단계에 걸쳐서 생성됩니다.
    var의 경우 선언과 초기화가 동시에 진행되어 변수 선언 이전에 해당 식별자를 참조할 수 있습니다.
    그러나 let / const의 경우 선언과 초기화가 분리되어 진행됩니다.
    따라서 선언문 이전에 변수에 접근하면 (TDZ) Reference Error 가 발생합니다. 

  * 또, var 는 let / const 와 달리 scope 가 다릅니다. 
    var 는 function, let / const 는 block level scope 입니다.

  * var 는 스코프 내에서 재선언이 가능합니다. 그러나, let / const 는 재선언이 불가능합니다.
    const 는 재할당도 불가능합니다.

  * 전역으로 선언되는 경우, var 와 let / const는 저장되는 위치도 다릅니다.
    var 의 경우, object environement record 에 등록되지만, 
    let / const의 경우 declarative environment record 에 등록됩니다.
    이 때문에 var 로 선언한 변수는 전역 객체 (window.a, global.a, ...) 로 참조할 수 있습니다.

  > ***변수 선언의 경우, 해당 실행 컨텍스트 내의 Lexical Environment 에 식별자를 등록합니다.***
  >
  > ***초기화의 경우, Call Stack 내에 해당 변수를 위한 메모리를 할당하고, undefined 값을 할당합니다.***
  > ***이후 해당 식별자에 Call Stack 메모리 주소값을 할당합니다.***
  >
  > ***할당의 경우, 원시 타입일 경우 Call Stack 에 데이터 값을 저장하지만 참조 타입일 경우 Memory heap 에 값을 할당하고, 해당 주소값을 Call Stack 값에 반영합니다.***



* == 와 === 는 어떤 점이 다른가요?
  * == 는 비교하기 전에 값을 같은 타입으로 변화시키고 값을 비교합니다. 이 때문에 예측하지 못한 결과를 얻게 될 수 있습니다. 그러나, === 는 타입 변환을 일으키지 않고, 값이 같은지, 타입이 같은 지를 비교합니다.
* falsy 한 값은 어떤 것이 있을까요?
  * false, 0, -0, 0n, `"" , '', `` ` , null, undefined, NaN, document.all 이 있습니다.
    **[] 은 falsy 가 아닙니다.**



### 참고

[String primitives and String objects](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String#string_primitives_and_string_objects)

[[자바스크립트] 메모리 구조, 원시타입 변수 생성 원리, 가비지컬렉터](https://curryyou.tistory.com/275)

[[자바스크립트] 콜스택/메모리힙 구조, 데이터 저장/참조 원리](https://curryyou.tistory.com/276)

[[자바스크립트] 코드 실행 2단계와 변수/함수 생성 과정](https://curryyou.tistory.com/277?category=898979)

[이벤트 핸들러 내부의 this](https://velog.io/@ursr0706/%EC%9D%B4%EB%B2%A4%ED%8A%B8-%ED%95%B8%EB%93%A4%EB%9F%AC-%EB%82%B4%EB%B6%80%EC%9D%98-this-2gyujqm2)

[JavaScript’s Memory Model](https://medium.com/@ethannam/javascripts-memory-model-7c972cd2c239)

[Inequality Operators: `!=` and `!==`](https://codeahoy.com/javascript/2019/10/12/==-vs-===-in-javascript/)

[MDN - Falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy)