<details>
  <summary>목차</summary>
  <div markdown="1">

- [프로토타입](#프로토타입)
  - [프로토타입의 개념 이해](#프로토타입의-개념-이해)
    - [constructor, property, instance](#constructor-property-instance)
      - [Person 이라는 생성자 함수의 prototype에 getName이라는 메서드를 지정했을 때](#person-이라는-생성자-함수의-prototype에-getname이라는-메서드를-지정했을-때)
        - [undefined 결과를 받은 이유](#undefined-결과를-받은-이유)
        - [`__proto__`객체에 name프로퍼티 할당한다면?](#__proto__객체에-name프로퍼티-할당한다면)
        - [이게 왜 되는가?](#이게-왜-되는가)
      - [프로토타입의 개념 보충 설명](#프로토타입의-개념-보충-설명)
    - [프로토타입 체인](#프로토타입-체인)
    - [메서드 오버라이드](#메서드-오버라이드)
    - [프로토타입 체인이란](#프로토타입-체인이란)
    - [객체전용의 메서드의 예외사항](#객체전용의-메서드의-예외사항)
    - [다중 프로토타입 체인](#다중-프로토타입-체인)
    </details>

# 프로토타입

> - 자바스크립트는 스포토타입 기반 언어
> - 클래스 기반 언어는 상속을 사용 but 프로토타입 기반 언어에서는 어떤 객체를 원형으로 삼고 이를 복제(침조)하여 상속과 비슷한 효과를 가져감

## 프로토타입의 개념 이해

### constructor, property, instance

![proto1](./image/proto1.png)
`var instanc = new Constructor();` 를 위 표를 가지고 도식화 한다면

![proto2](./image/proto2.png)

- 생성자 함수(Constructor)를 new 연산자와 함께 호출
- Constructor에서 정의된 내용을 토대로 새로운 인스턴스 생성
  - 이때 생성된 instance에 `__proto__`라는 프로퍼티가 자동으로 부여
- 이 프로퍼티는 프로퍼티는 Constructor의 prototype이라는 프로퍼티를 참조

> `prototype`은 객체고 이를 참조하는 `__proto__`역시 객체
>
> - `__proto__` : 객체
> - prototype : 객체 `인스턴스가 사용할 메서드 저장`
>   - 인스턴스가 사용할 메서드를 저장하면서 인스턴스에서도 숨셔진 프로터피인 `__proto__` 를 통해 이 메서드들에 접근할 수 있게 됨

#### Person 이라는 생성자 함수의 prototype에 getName이라는 메서드를 지정했을 때

```javascript
var Person = function (name) {
  this._name = name;
};

Person.prototype.getName = function () {
  return this._name;
}; // getName 메소드 생성 -> __proto__프로퍼티를 통해 getName 호출이 가능해졌다.

var suzi = new Person("Suzi");

suzi.__proto__.getName(); // undefined
Person.prototype === suzi.__proto__; // true
```

**Person의 인스턴스가 `__proto__` 라는 프로퍼티를 통해 getName을 호출할 수 있는 이유**

- instance 의 **proto** 가 Constructor의 prototype 프토퍼티를 참조하므로 결국 둘은 같은 객체를 바라보게됨

##### undefined 결과를 받은 이유

- undefined가 나온다 == 에러발생 ㄴㄴ

  - 어떤 변수를 실행 해 undefined 나왔다 = 변수가 `호출할 수 있는 함수`에 해당한다
  - 만일 함수가 아닌 데이터 타입이면 타입에러 발생

- 그렇다면 문제상황은?
  - this에 바인딩된 대상이 잘못 지정됨 => undefined 발생
  - `suzi.proto.getName();` 코드에서 `getName` 함수 내부에서의 `this`는 suzi가 아님
  - `suzi.__proto__` 라는 객체가 됨
  - 객체 내부에는 name 프로퍼티가 없음
  - 즉, 찾고자 하는 식별자가 정의돼 있지 않기 때문에 undefined를 반환한다.

##### `__proto__`객체에 name프로퍼티 할당한다면?

```javascript
var suzi = new Person("Suzi");
suzi.__proto__.name = "SUZI__proto__";
suzi.__proto__.getName(); // SUZI__proto__
```

- SUZI`__proto__` 잘 출력됨
- this?
  - this를 인스턴스로 하는 방법
    - `**proto**없이 인스턴스에서 곧바로 메서드를 쓴다

```javascript
var suzi = new Person("Suzi", 28);
suzi.getName(); // Suzi

var iu = new Person("Jieun", 28);
iu.getName; // Jieun
```

##### 이게 왜 되는가?

- `__proto__` 는 생략 가능하기 때문
- `__proto__`를 생략하지 않은 경우
  - this는 `suzi.__proto__`를 가리킴
- `__proto__`를 생략한 경우
  - suzi를 가리킴
  - `suzi.__proto__` 에 있는 메서드인 getName을 실행하지만 this는 suzi를 바라고게 할 수 있음

![proto3](./image/proto3.png)
`결론`
**new 연산자로 Constructor를 호출하면 instace가 만들어지는데, 이 instance의 생략 가능한 프로퍼티인 `__proto__`는 Constructor의 prototype을 참조한다!**

#### 프로토타입의 개념 보충 설명

- JS는 함수에 자동으로 객체인 prototype 프로퍼티를 생성해 놓음
- 해당 함수를 생성자 함수로서 사용할 경우 (new 연산자와 함께 함수를 호출할 경우)

### 프로토타입 체인

### 메서드 오버라이드

### 프로토타입 체인이란

### 객체전용의 메서드의 예외사항

### 다중 프로토타입 체인

```

```
