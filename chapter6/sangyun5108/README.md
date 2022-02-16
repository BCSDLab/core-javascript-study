# 06. 프로토타입

- javascript는 프로토타입기반 언어이다.
- 어떠한 객체를 원형으로 삼고 이를 복제(참조)함으로 클래스 기반의 상속과 비슷한 효과를 얻는다.

```javascript
  var instance = new Constructor();
```
- 어떠한 생성자 함수(Constructor)를 new 연산자와 함께 호출
- Constructor에서 정의된 내용을 토대로 새로운 인스턴스 생성
- 생성된 인스턴스에 __proto__라는 프로퍼티가 자동으로 부여
- __proto__라는 프로퍼티는 **Constructor의 prototypeㅇ이라는 프로퍼티를 참조**
- __proto__ : 객체, prototype : 객체(인스턴스가 사용할 메서드 지정)

Person.prototype
```javascript
  var Person = function(name){
    this._name = name;
  };
  
  Person.prototype.getName = function(){
    return this._name;
  } // getName 메소드 생성 -> __proto__프로퍼티를 통해 getName 호출이 가능해졌다.
  
  var suzi = new Person('Suzi');
  
  suzi.__proto__.getName(); // undefined
  Person.prototype === suzi.__proto__ // true
```
- instance의 __proto__가 Constructor의 prototype 프로퍼티를 참조해서 같은 객체를 참조하게 된다.

**undefined 발생한 이유**
- 변수를 실행해 undefined가 나온 부분 -> 이 변수가 '호출할 수 있는 함수'에 해당한다.'를 의미
- undefined 발생한 이유 : this에 바인딩된 대상이 잘못 지정됐다.
- suzi.__proto__.getName();에서 getName 함수 내부에서의 this는 suzi가 아닌, suzi.__proto__각 객체가 된다.
- 따라서, 객체 내부에는 name 프로퍼티가 없으므로, 즉, 찾고자 하는 식별자가 정의돼 있지 않기 때문에 undefined를 반환한다.

**__proto__객체에 name프로퍼티 할당**
```javascript
  var suzi = new Person('Suzi');
  suzi.__proto__.name = 'SUZI__proto__';
  suzi.__proto__.getName(); // SUZI__proto__
```
**this를 instance로 하기 위한 __proto__생략**
```javascript
  var suzi = new Person('Suzi',28);
  suzi.getName();
```
- __proto__는 **생략 가능**한 프로퍼티이다.

```javascript
  suzi.__proto__.getName
  suzi.(__proto__).getName
  suzi.getName
```
- __proto__를 생략하지 않은 경우 : this는 suzi.__proto__를 가리킨다.
- __proto__를 생략하는 경우 : suzi를 가리킨다.

### 정리
- JavaScript는 함수에 자동으로 객체인 prototype 프로퍼티를 생성해 놓고, 해당 함수를 생성자 함수로써 사용하는 경우(new 연산자와 함께 함수를 호출할 경우), 생성된 인스턴스에는 숨겨진 프러퍼티인 __proto__ 가 자동으로 생성되며, 이 프로퍼티는 생성자 함수의 prototype 프로퍼티를 참조한다.
- __proto__ 프로퍼티는 생략 가능하다.

### 옅은 색 vs 짙은 색
- 짙은색 : enumerable, 열거가 가능하다.
- 옅은색 : innumerable, 열거할 수 없는 프로퍼티이다.
- for in 등으로 객체의 프로퍼티 전체에 접근하고자 할 때, 접근 가능 여부를 색상을 통해 구분한다.

## Constructor 프로퍼티
- 생성자 함수의 프로퍼티인 prototype 객체 내부에는 constructor라는 프로퍼티가 존재한다.
- 인스턴스의 __proto__ 객체 내부에도 마찬가지로 존재한다.
- 필요한 이유 : 인스턴스와의 관계에 있어서 필요한 정보이다.(인스턴스로부터 그 원형이 무엇인지를 알 수 있는 수단이다.)


### 메서드 오버라이드
- 메서드 위에 메서드를 덮어씌워버린다.(교체가 아닌 원본이 그대로 있는 상태에서 다른 대상을 그 위에 얹는 이미지)
```js
 var Person = function(name){
  this.name = name;
 };
 
 Person.prototype.getName = function(){
  return this.name;
 }
 
 var iu = new Person("지금");
 iu.getName = function(){
  return '바로' + this.name;
 };
 
 console.log(iu.getName()); // 바로 지금
```
- Person.prototype.getName이 iu.getName으로 메서드 오버라이드 된다.

### 프로토타입 체인
- 어떤 데이터의 __proto__ 프로퍼티 내부에 다시 __proto__ 프로퍼티가 연쇄적으로 이어진 것을 프로토타입 체인이라고 한다.
- 이 체인을 따라가며 검색하는 것을 프로토타입 체이닝이라고 한다.(자바스크립트 엔진이 자신의 프로퍼티들을 검색해서 원하는 메서드가 있는경우, 그 메서드를 실행하고, 없는 경우 __proto__ 를 검색해서 있으면 그 메서드를 실행하고, 없으면 ektl __proto__ 를 검색해서 실행하는 식으로 진행한다.)

## 정리
- 생성자 함수 new 연산자와 호출, Constructor에 정의된 내용을 바탕으로 새로운 인스턴스 생성, 이 인스턴스에는 __proto__ 라는, Constructor의 prototype 프로퍼티를 참조하는 프로퍼티가 자동으로 부여된다.
- Constructor.prototype 에는 construcotr라는 프로퍼티가 있는데, 이것은 다시 생성자 함수 자신을 가리킨다.(인스턴스가 자신의 생성자 함수가 무엇인지를 알고자 할 때 필요한 수단이다.)
- 직각삼각형의 대각선 방향, __proto__ 방향을 계속 찾아가면 최종적으로는 Object.prototype에 당도하게 된다.
- __proto__ 안에 __proto__ 를 찾아가는 과정 : 프로토타입 체이닝
- Object.prototype에는 모든 데이터 타입을 사용할 수 있는 범용적인 메서드만이 존재하고, 객체 전용 메서드는 Object 생성자 함수에 스태틱하게 담겨있다.
- 프로토타입 체인은 2단계로만 이뤄지는 것이 아닌, 무한대의 단계를 생성할 수 있다.
 
