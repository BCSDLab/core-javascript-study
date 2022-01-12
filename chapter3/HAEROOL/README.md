# 3주차 - this

# 목차

- 상황에 따라 달라지는 this
- 명시적으로 this를 바인딩하는 방법

<aside>
💡 대부분의 객체지향 언어에서 this는 클래스로 생성한 인스턴스 객체를 의미한다. 하지만 JS의 this는 상황에 따라 this가 가리키는 대상이 달라지게 된다. 때문에 정확한 작동 방식을 이해하고 사용해야 한다.

</aside>

# 상황에 따라 달라지는 this

## 전역 공간에서의 this

<aside>
💡 JS에서 this는 기본적으로 실행 컨텍스트가 생성될 때, 함께 결정되는데, 실행 컨텍스트는 함수를 호출할 때 생성되니까 this는 **함수를 호출할 때 결정된다고 할 수 있다.**

</aside>

- 전역 공간에서 this는 전역 객체를 가리키는데 개념상 전역 컨텍스트를 생성하는 주체가 전역 객체이기 때문이다. 브라우저 환경에서는 window이고 Node.js 환경에서는 global이다.

```jsx
var a = 1;
console.log(a);          //1
console.log(window.a);   //1
console.log(this.a);     //1
```

`window.a`와 `this.a` 의 값이 같은 이유는 자바스크립트의 **모든 변수가 특정 객체의 프로퍼티로서 동작하기 때문이다.** 여기서 특정 객체는 실행 컨텍스트의 LexicalEnvironment이다.  실행 컨텍스트는 변수를 수집해서 LexicalEnvironment의 프로퍼티로 저장하는데, 이후 어떤 변수를 호출하면 LexicalEnvironment를 조회해서 일치하는 프로퍼티가 있을 경우에는 그 값을 반환한다. 따라서 **전역변수를 선언하게 되면 JS엔진은 이를 전역객체의 프로퍼티로 할당한다고 할 수 있다.** a를 직접 호출했을 경우에는 스코프 체인에서 a를 검색하다가 가장 마지막에 도달하는 스코프의 LexicalEnvironment에서 해당 프로퍼티를 발견하면 값을 반환하게 된다.

따라서 전역 공간에서 `var` 로 변수를 선언하는 대신 window의 프로퍼티에 할당을 하더라도 결과적으로는 문제가 되지 않는다. 하지만 `delete` 연산자를 사용한다면 문제가 생기게 되는데, 전역 변수를 선언할 때, 처음부터 전역 객체의 프로퍼티로 할당했을 경우, `delete` 연산자가 정삭적으로 작동하여 삭제가 되지만, 전역 변수를 `var` 등을 사용해 선언했을 경우에는 불가능하다. 이는 전역변수를 선언하면 JS엔진이 이를 자동으로 전역객체의 프로퍼티로 할당하면서 추가적으로 해당 프로퍼티의 configurable(변경 및 삭제 가능성)속성을 false로 정의하기 때문이다.

## 메서드로서 호출할 떄 그 메서드 내부에서의 this

### 함수 vs 메서드

- 함수: 그 자체로 독립적인 기능을 수행한다.
- 메서드: 자신을 호출한 대상 객체에 관한 동작을 수행한다.

```jsx
var func = function(x){
    console.log(this, x);
};
func(1);            //Window{...} 1

var obj = {
    method: func
};
obj.method(2);      //{method: f} 2
```

이처럼 4번째 줄에서 `func()` 를 호출했을 때는 this가 전역 객체를 가리키게 되고, 마지막 줄에서 프로퍼티로 `func()` 를 호출했을 때는 this가 `obj` 를 가리키게 된다. 이렇게 함수로서의 호출과 메서드로서의 호출을 구분하는 방법은 간단하다. 함수 앞에 점(.)이 있는지 여부만으로 간단하게 구분 가능하다. **함수를 호출할 떄, 그 함수 이름(프로퍼티 명) 앞에 객체가 명시되어 있는 경우에는 메서드, 그렇지 않은 모든 경우는 함수로서의 호출이다.**

<aside>
💡 this에는 호출한 주체에 대한 정보가 담기는데, 함수를 메서드로서 호출하는 경우의 호출 주체는 바로 함수명(프로퍼티명) 앞의 객체이며, 점 표기법의 경우 **마지막 점 앞에 명시된 객체가 곧 this가 된다.**

</aside>

## 함수로서 호출할 때 그 함수 내부에서의 this

### 함수 내부에서의 this

- 함수를 함수르서 호출할 경우에는 this가 지정되지 않는다. 이는 호출 주체를 명시하지 않고 개발자가 코드에 직접 관여해서 실행한 것이기 때문이다.
- 이 경우 함수에서의 this는 전역 객체를 가리키게 되는데, 이 이유는 실행 컨텍스트를 활성화할 당시에 this가 지정되지 않은 경우 this가 전역 객체를 가리키게 되기 때문이다.

### 메서드의 내부함수에서의 this

<aside>
💡 this 바인딩에 관해서는 함수를 실행하는 당시의 주변 환경(메서드 내부, 함수 내부 등)은 중요하지 않고 해당 함수를 호출하는 구문 앞에 점 또는 대괄호 표기가 있는지 없는지가 관건이다.

</aside>

### 메서드의 내부 함수에서의 this를 우회하는 방법

대표적인 방법은 변수를 활용하는 것이다.

```jsx
var obj = {
    outer: function(){
        console.log(this);
        var innerFunc1 = function(){
            console.log(this);
        };
        innerFunc1();
    
            
        var self = this;
        var innerFunc2 = function(){
            console.log(self);
        };
        innerFunc2 = function(){
            console.log(self);
        };
        innerFunc2();
    }
};
obj.outer();
```

위의 예제는 outer스코프에서 self라는 변수에 this를 저장한 상태에서 innerFunc2를 호출하는데, 이 경우 self는 객체 obj가 된다.

### this를 바인딩하지 않는 함수

**화살표 함수(arrow function)은 this를 바인딩하지 않는다.** 화살표 함수는 실행 컨텍스트를 생성할 때 this바인딩 과정이 없기 때문에 상위 스코프의 this를 그대로 활용할 수 있다.

## 콜백 함수 호출 시 그 함수 내부에서의 this

함수 A의 제어권을 다른 함수 B에게 넘겨주는 경우 함수 A를 콜백 함수라고 한다. 콜백 함수 또한 this가 기본적으로 전역 객체를 잠조하지만, 제어권을 받은 함수에서 콜백 함수에 별도로 this가 될 대상을 지정한 경우에는 그 대상을 참조하게 된다.

즉, 콜백 함수의 제어권을 가지는 함수(메서드)가 콜백 함수에서의 this를 무엇으로 할지를 결정하며, 기본적으로는 함수와 마찬가지로 전역 객체를 바라보게 된다.

## 생성자 함수 내부에서의 this

생성자 함수를 호출하면 우선 생성자의 prototype 프로퍼티를 참조하는 **proto**라는 프로퍼티가 있는 객체를 만들고 미리 준비된 공톨ㅇ 속성 및 개성을 해당 객체(this)에 부여한다.

```jsx
var Cat = function(name, age){
    this.bark = '야옹';
    this.name = name;
    this.age = age;
};

var choco = new Cat('choco', 7);
var nabi = new Cat('nabi', 5);
```

위와 같이 생성자 함수의 예시를 살펴보면, Cat함수의 내부에서는 this에 접근하여 bark, name, age 프로퍼티에 값을 대입한다. 여기서 각각 choco를 생성하는 Cat 생성자 함수 내부의 this는 choco인스턴스를, nabi를 생성하는 Cat 생성자 함수 내부의 this는 nabi 인스턴스를 가리킨다.

# 명시적으로 this를 바인딩하는 방법

## call method

```jsx
Function.prototype.call(,arg1[.arg2[, ...]]])
```

call 메서드는 메서드의 호출 주체인 함수를 즉시 실행한다. 이때,call 메서드의 첫 번째 인자를 this로 바인딩하고, 이후의 인자들을 호출할 함수의 매개변수로 한다.

```jsx
var func = function(a,b,c){
    console.log(this, a, b, c);
};

func(1,2,3);            //Window{...} 1 2 3
func.call({x: 1},4,5,6) //{x: 1} 4 5 6
```

## apply method

```jsx
Function.prototype.apply(thisArg[, argsArray])
```

기능적으로는 call 메소드와 동일하다. 차이점은 apply는 두 번째 인자를 배열로 받아서 그 배열의 요소들을 호출할 함수의 매개변수로 지정한다는 것이다.

```jsx
var func = function(a,b,c){
    console.log(this, a, b, c);
};

func.apply({x: 1},[4,5,6]); //{x: 1} 4 5 6
```

## call / apply의 활용

### 유사배열객체에 배열 메서드를 적용

```jsx
var obj = {
    0: 'a',
    1: 'b',
    2: 'c',
    length: 3
};
Array.prototype.push.call(obj, 'd');
console.log(obj);       //{ 0: 'a', 1: 'b', 2:'c', 3: 'd',  length: 4}

var arr = Array.prototype.slice.call(obj);
console.log(arr)        //['a', 'b', 'c', 'd']
```

키가 0이거나 양의 정수인 프로퍼티가 존재하고 length 프로퍼티의 값이 0 또는 양의 정수인 객체(유사배열 객체)의 경우 call이나 apply 메서드를 이용해서 배열 메서드를 사용할 수 있다(원래 객체에서는 배열 메서드를 직접 적용할 수 없다).

<aside>
💡 문자열의 경우에는 length 프로퍼티가 읽기 전용이기 때문에 원본 문자열에 변경을 가하는 메서드는 에러를 던지며, concat 처럼 대상이 반드시 배열이여야 하는 경우에는 에러는 나지 않지만, 결과는 제대로 안나온다.

</aside>

<aside>
💡 ES6에서는 순회 가능한 모든 종류의 데이터 타입을 배열로 전환하는 Array.from 메서드를 새로 도입함.

</aside>

### 생성자 내부에서 다른 생성자를 호출

```jsx
function Person(name, gender){
    this.name = name;
    this.gender = gender;
}

function Student(name, gender, school){
    Person.call(this, name, gender);
    this.school = school;
}
```

생성자 내부에 다른 생성자와 공통된 내용이 있을 경우 call이나 apply를 이용해서 다른 생성자를 호출하면 간단하게 반복을 줄일 수 있다.

## bind method

```jsx
Function.prototype.bind(thisArg[, arg1[, arg2[, ... ]]])
```

bind는 넘겨받은 this 및 인수들을 바탕으로 새로운 함수를 반환한다. 다시 새로운 함수를 호출할 때 인수를 넘기면 그 인수들은 기존 bind 메서드를 호출할 때 전달했던 인수들의 뒤에 이어서 등록된다.

```jsx
var func = function(a,b,c,d){
    console.log(this, a,b,c,d);
};
func(1,2,3,4);              //Window{ ... } 1 2 3 4

var bindFunc1 = func.bind({ x: 1});
bindFunc1(5,6,7,8);         // {x: 1} 5 6 7 8

var bindFunc2 = func.bind({x: 1}, 4, 5);
bindFunc2(6, 7);            // {x: 1} 4 5 6 7
```

<aside>
💡 bind를 적용해서 만든 함수는 name 프로퍼티에 bound라는 접두어가 붇는다.

</aside>

<aside>
💡 화살표 함수의 경우 실행 컨텍스트 생성 시에 this를 바인딩하는 과정이 제외되었기 때문에 함수 내부에는 this가 존재하지 않으며 접근하고자 하면 스코프체인상 가장 가까운 this에 접근한다.

</aside>

### this의 참조규칙

- 전역공간에서의 this는 전역객체를 참조한다
- 어떤 함수를 메서드로서 호출한 경우 this는 메서드 호출 주체를 참조한다
- 어떤 함수를 함수로서 호출한 경우 this는 전역객체를 참조한다. 메서드 내부함수도 동일하다
- 콜백 함수 내부에서의 this는 해당 콜백 함수의 제어권을 넘겨받은 함수가 정의한 바에 따르며, 정의하지 않은 경우에는 전역객체를 참조한다.
- 생성자 함수에서의 this는 생성될 인스턴스를 참조한다

### 명시적 this 바인딩

- call, aplly 메서드는 this를 명시적으로 지정하면서 함수 또는 메소드를 호출한다
- bind 메서드는 this 및 함숭 넘길 인수를 일부 지정해서 새로운 함수를 만든다.
- 요소를 순회하면서 콜백 함수를 반복 호출하는 내용의 일부 메서드는 별도의 인자로 this를 받기 도 한다.
