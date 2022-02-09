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
