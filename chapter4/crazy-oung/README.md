<details>
  <summary>목차</summary>
  <div markdown="1">

- [콜백 함수](#콜백-함수)
  - [4-1 콜백 함수란?](#4-1-콜백-함수란)
  - [4-2 제어권](#4-2-제어권)
    - [4-2-1 호출 시점](#4-2-1-호출-시점)
      - [호출 시점 예시1](#호출-시점-예시1)
      - [호출 시점 예시2](#호출-시점-예시2)
    - [4-2-2 인자](#4-2-2-인자)
      - [인자 예시1](#인자-예시1)
      - [인자 순서를 임의로 바꾼 경우](#인자-순서를-임의로-바꾼-경우)
    - [4-2-2 this](#4-2-2-this)
      - [별도의 this를 지정하는 방식 및 제어권에 대한 이해를 위한 map 구현](#별도의-this를-지정하는-방식-및-제어권에-대한-이해를-위한-map-구현)
  - [4-3 콜백 함수는 함수다](#4-3-콜백-함수는-함수다)
      - [메서드를 콜백 함수로 전달한 경우](#메서드를-콜백-함수로-전달한-경우)
  - [4-4 콜백 함수 내부의 this에 다른 값 바인딩하기](#4-4-콜백-함수-내부의-this에-다른-값-바인딩하기)
      - [콜백 함수 내부 this에 다른 값 바인딩 하기](#콜백-함수-내부-this에-다른-값-바인딩-하기)
      - [콜백 함수 내부 this를 사용하지 않기 (전통적 방식)](#콜백-함수-내부-this를-사용하지-않기-전통적-방식)
      - [함수 재활용하기](#함수-재활용하기)
  - [4-5 콜백 지옥과 비동기 제어](#4-5-콜백-지옥과-비동기-제어)
    - [비동기 코드와 그 비중](#비동기-코드와-그-비중)
      - [콜백 지옥의 예시1](#콜백-지옥의-예시1)
      - [콜백 지옥의 예시1 해결](#콜백-지옥의-예시1-해결)
  - [부록](#부록)
    - [ES5에 등장한 bind메서드를 활용하기](#es5에-등장한-bind메서드를-활용하기)
    - [Promise 예시](#promise-예시)
    - [Generator 예시](#generator-예시)
    - [async/await 예시](#asyncawait-예시)
    </details>

# 콜백 함수

## 4-1 콜백 함수란?

> 다른 코드의 인자로 넘겨주는 함수

- 콜백함수를 넘겨 받은 코드는 콜백함수를 필용 따라 적절한 시점에 실행
- 콜백함수는 [제어권](#4-2-제어권)을 넘겨줄 수 있음
  - 다른 코드(함수 또는 메서드)에게 인자로 넘겨주면서 제어권도 함께 위임

## 4-2 제어권

> 제어권을 넘겨 받은 코드는
>
> - 콜백함수 호출 시점을 스스도 판ㄷ나해서 실행
> - 콜백 함수 호출 때 인자로 넘겨줄 값들과 순서가 정해져 있음
> - 콜백 함수의 this가 바라볼 대상이 정해져 있는 경우가 있음(정하지 않는 경우 전역객체)

### 4-2-1 호출 시점

#### 호출 시점 예시1

```javascript
var count = 0;
var timer = setInterval(function () {
  console.log(count);
  if (++count > 4) clearInterval(timer);
}, 300);
```

setInterval의 구조

```javascript
var intervalID = scope.setInterval(func, delay,[,param1,param2,...]);
```

- scope에 window객체 또는 Worker의 인스턴스가 들어갈 수 있음
  - 두 객체 모두 setInterval 메서드를 제공하기 떄문
  - 매개변수로 func, delay 값 반드시 필요
  - func에 넘겨주는 함수는 매 delay(ms) 마다 실행
  - 결과 리턴 X
  - 반복적으로 실행되는 내용 자체를 특정할 수 있는 고유한 ID값이 반환
    - 변수에 담는 이유? 반복 실행되는 중간에 종료(clearInterval) 할 수 있게 하기 위함

#### 호출 시점 예시2

```javascript
var count = 0;
var cbFunc = function () {
  console.log(count);
  if (++count > 4) clearInterval(timer);
};

var timer = setInterval(cbFunc, 300);
```

- timer 변수에 setInterval 의 ID값이 담김
- setInterval에 전달한 첫 번째 인자인 콜백함수 cbFunc 함수가 0.3초마다 자동 실행

코드 실행 방식과 제어권
| code | 호출 주체 | 제어권 |
|--------|--------|--------|
| cbFunc(); | 사용자 | 사용자 |
| setInterval(cbFunc,300); | setInterval | setInterval |

setInterval이라고 하는 `다른코드`에 첫번 째 인자로 cbFunc 함수를 넘겨주면 제어권을 넘겨 받은 setInterval이 스스로 판단에 따라 적절한 시점에(0.3초마다) 이 익명 함수를 실행

- 콜백함수의 제어권을 넘겨받은 코드는 콜백 함수 호출 시점에 대한 제어권을 가지게 됨

### 4-2-2 인자

#### 인자 예시1

```javascript
var newArr = [10, 20, 30].map(function (currentValue, index) {
  console.log(currentValue, index);
  return currentValue + 5;
});

console.log(newArr);
```

`Array.prototype.map` 메서드는 어떤 방식으로 동작하는가

```javascript
Array.prototype.map(callback,[,thisArg])
callback : function(currentValue,index,array)
```

첫번째 인자로 callback 함수를 받고 생략 가능한 두 번째 인자로 콜백 함수 내부에서 this로 인식할 대상을 특정가능

- this로 인식할 대상을 특정할 수 있음
- thisArg를 생략할 경우 일반적인 함수와 마찬가지로 전역객체가 바인딩됨
- map메서드는 메서드의 대상이 되는 배열의 모든 요소를 처음부터 끝까지 하나씩 콜백함수를 반복 호출
  - 콜백함수의 실행 결과들을 모아 새로운 배열을 만듬
  - 콜백의 **첫 인자는 배열 요소중 현재값, 둘째 인자는 현재값의 인덱스, 셋째 인자는 map메서드의 대상이 되는 배열 자체**

#### 인자 순서를 임의로 바꾼 경우

```javascript
var newArr = [10, 20, 30].map(function (index, currentValue) {
...
```

위 처럼 인자 순서를 바꿔도 동작은 가능하다

- 인자는 순서에 의해서 그 역할이 부여되고 사용되어짐
  - 인자 명칭을 다르게 부여해서 사용했으므로 결과도 달라짐
  - 원하는 결과를 얻기 위해서는 규칙에 따라 사용해야 할 것

**콜백 함수의 제어권을 넘겨 받은 코드는 콜백 함수를 호출할 떄 인자에 어떤 값들을 어떤 순서로 넘길 것인지에 대한 제어권을 가진다.**

### 4-2-2 this

> 콜백함수도 함수이기에 기본적으로 this가 전역객체를 참조하지만, 제어권을 넘겨받을 코드에서 콜백 함수에 별도로 this가 될 대상을 지정한 경우에는 그 대상을 참조하게 된다

#### 별도의 this를 지정하는 방식 및 제어권에 대한 이해를 위한 map 구현

```javascript
  Array.prototype.map = function(callback,thisArg){
    var mappedArr = [];
    for (var i = 0; i < this.length; i++)}{
      var mappedValue = callback.call(thisArg||window,this[i],i,this);
      mappedArr[i] = mappedValue;
    }
    return mappedArr;
  }
```

구현의 핵심 call/apply

- this에 thisArg값이 있을 경우 그 값을, 없을 경우 전역객체를 지정

제어권을 넘겨받을 코드에서 call/apply 메서드의 첫 인자에 콜백 함수내부에서의 this가 될 대상을 명시적으로 바인딩하기 때문에 this에 다른 값이 담기게 된다.

## 4-3 콜백 함수는 함수다

> 콜백 함수로 어떤 객체의 메서드를 전달하더라도 그 메서드는 메서드가 아닌 함수로서 호출된다.

#### 메서드를 콜백 함수로 전달한 경우

```javascript
var obj = {
  vals: [1, 2, 3],
  logValues: function (v, i) {
    console.log(this, v);
  },
};

obj.logValues(1, 2); // {vals : [1,2,3], logValues:f} 1 2
[4, 5, 6].forEach(obj.logValues);
// Window{...} 4 0
// Window{...} 5 1
// Window{...} 6 2
```

- forEach 함수에 의해 콜백이 함수로서 호출
- 별도로 this를 지정하는 인자를 지정하지 않았으므로 함수 내부에서의 this는 전역객체

어떤 함수의 인자에 객체의 메서드를 전달하더라도 이는 결국 메서드가 아닌 함수이다.

## 4-4 콜백 함수 내부의 this에 다른 값 바인딩하기

> 객체의 메서드를 콜백함수로 전달하면 해당 객체를 this로 바라볼 수 없게 된다.

함수 내부에서 this가 객체를 바라보게 하는 방법?

- 별도의 인자로 this를 받은 함수의 경우: 인자로 원하는 값을 넘겨 주면 된다.
- 별도의 인자로 this ㄴㄴ 인 경우: this의 제어권도 넘겨주게 되므로 사용자가 임의로 값을 변경 불가능

So,
this를 다른 변수에 담아 콜백함수로 활용할 함수에서는 this대신 그 변수를 사용하게 하고, 이를 클로저로 만드는 방식이 쓰였음

#### 콜백 함수 내부 this에 다른 값 바인딩 하기

```javascript
var obj1 = {
  name: "obj1",
  func: function () {
    var self = this;
    return function () {
      console.log(self.name);
    };
  },
};
var callback = obj1.func();
setTimeout(callback, 1000);
```

- self 에 this를 담아 익명함수 선언과 동시에 반환
- callback을 setTimeout함수에 인자로 전달하면 딜레이 뒤에 콜백이 실행되면서 obj1를 출력

**this를 사용하지 않으며 절대 간결과는 거리가 멀며 번거롭다는 단점이 있음**

#### 콜백 함수 내부 this를 사용하지 않기 (전통적 방식)

```javascript
var obj1 = {
  name: "obj1",
  func: function () {
    console.log(obj1.name);
  },
};

setTimeout(obj1.func, 1000);
```

어? 간결한데
this를 재활용 할수 없게 되어버렸음 ㅋㅋ ㅅㄱ

- 처음부터 바라볼 객체를 명시적으로 지정 했음
  - 어떤 방법으로도 다른 객체를 바라보게 할 수 없음.
  - 불편함 + 메모리 낭비
- 다양한 객체에 재활용할 필요성이 없는 경우 이렇게 해도 문제없음

#### 함수 재활용하기

```javascript
var obj2 = {
  name: "obj2",
  func: obj1.func,
};

var callback2 = obj2.func();
setTimeout(callback2, 1500);

var obj3 = { name: "obj3" };
var callback3 = obj1.func.call(obj3);
setTimeout(callback3, 2000);
```

- this를 우회적으로 활용이 가능함
  - 다양한 사오항에서 원하는 객체를 바라보는 콜백함수를 생성 가능

[전통적 방식의 아쉬움을 보완한 방법](#es5에-등장한-bind메서드를-활용하기)

## 4-5 콜백 지옥과 비동기 제어

> 콜백 지옥? 콜백 함수를 익명 함수로 전달하는 과정이 반복, 코드의 들여쓰기 수준이 감당하기 힘들 정도로 깊어지는 현상
>
> - 자바스크립트에서 흔히 발생
> - 주로 이벤트 처리, 서버 통신등과 같은 비동기적 작업을 수행하기 위해 이런 형태가 등장
>   - 가독성 똥 + 수정하기 힘듬

비동기? 동기?

- 비동기<sup>asynchronous</sup>: 현재 실행중인 것의 완료 여부와 무관하게 즉시 다음코드 실행
  - 사용자 요청에 의한 특성 시간이 경과 되기 전 까지 어떤 함수의 실행을 보류 하는 경우(setTimeout)
  - 사용자의 직접적 개입이 있을 때 비로소 어떤 함수를 실행하도록 대기 (addEventListner)
  - 웹브라우저 자체가 아닌 별도의 대사엥 무언가를 요청하고 그에 대한 응답이 왔을 때 비로소 어떤 함수를 실행하도록 대기(XMLHttpRequest)
  - 위와 같은 **별도의 요청, 실행 대기, 보류** 등과 과련된 코드는 비동기 코드
- 동기<sup>synchronous</sup>: 현재 실행중인 코드가 완료된 후에 다음 코드를 실행
  - CPU의 계산에 의해 **즉시** 처리가 가능한 대부분의 코드가 동기적 코드
  - 계산식이 복잡해 CPU가 계산하는데 시간이 많이 필요한 경우도 동기적 코드

### 비동기 코드와 그 비중

웹의 복잡도가 높아지면서 비동기 코드의 비중이 높아짐

- 콜백 지옥에 빠지기 쉬워짐

#### 콜백 지옥의 예시1

```javascript
  setTimeout(function(name){
    var coffeeList = name;
    console.log(coffeeList);

    setTimeout(function(name){
      coffeeList += ', ' + name;
      console.log(coffeeList);

      setTimeout(function(name){
        coffeeList += ', ' + name;
        console.log(coffeeList);

          setTimeout(function(name){
            coffeeList += ', ' + name;
            console.log(coffeeList);
          },500,'카페라떼'};
        },500,'카페모카'};
      },500,'아메리카노'};
    },500,'에스프레소'};
```

뎁스가 엄청난걸 볼 수 있다.
0.5주기마다 커피목록을 수집하고 출력하는 걸 꼭 이렇게 짜야했을까?

- 익명 콜백함수를 모두 기명 함수로 전환하자 .

#### 콜백 지옥의 예시1 해결

```javascript
var coffeeList = "";

var addEspresso = function (name) {
  coffeeList = name;
  console.log(coffeeList);
  setTimeout(addAmericano, 500, "아메리카노");
};

var addAmericano = function (name) {
  coffeeList = name;
  console.log(coffeeList);
  setTimeout(addAmericano, 500, "카페모카");
};

var addMocha = function (name) {
  coffeeList = name;
  console.log(coffeeList);
  setTimeout(addLatte, 500, "카페라떼");
};

var addLatte = function (name) {
  coffeeList = name;
  console.log(coffeeList);
  setTimeout(addEspresso, 500, "에스프레소");
};
```

- 가독성을 높일 수 있음
- 함수 선언과 호출만 구분할줄 알면 위에서 아래로 읽어내려가는데 어려움이 없음
- 변수를 최상단으로 끌어올려서 외부에 노출됐지만 전체를 즉시 실행 함수 등으로 감싸면 간단히 해결 가능

근데 일회성 함수를 전부 변수에 할당함?

- 코드명을 자꾸 따라다녀야되니까 헷갈림

그래서 JS가 했어요

- ES6에서 [Promise](#promise-예시), [Generator](#generator-예시)
- ES2017에서 [async/await](#asyncawait-예시)를 도입
  - 부록에서 살펴보자

## 부록

### ES5에 등장한 bind메서드를 활용하기

```javascript
var obj1 = {
  name: "obj1",
  func: function () {
    console.log(this.name);
  },
};

setTimeout(obj1.func.bind(obj1), 1000);

var obj2 = { name: "obj2" };
setTimeout(obj1.func.bind(obj2), 1500);
```

### Promise 예시

> ES6의 Promise

```javascript
new Promise(function (resolve) {
  setTimeout(function () {
    var name = "에스프레소";
    console.log(name);
    resolve(name);
  }, 500);
})
  .then(function (preName) {
    return new Promise(function (resolve) {
      setTimeout(function () {
        var name = prevName + ", 아메리카노";
        console.log(name);
        resolve(name);
      }, 500);
    });
  })
  .then(function (prevName) {
    return new Promise(function (resolve) {
      setTimeout(function () {
        var name = prevName + ", 카페모카";
        console.log(name);
        resolve(name);
      }, 500);
    });
  })
  .then(function (prevName) {
    return new Promise(function (resolve) {
      setTimeout(function () {
        var name = prevName + ", 카페라떼";
        console.log(name);
        resolve(name);
      }, 500);
    });
  });
```

- new 연산자와 함께 호출한 Promise의 인자로 넘겨주는 콜백함수는 호출할때 바로 실행
- 내부 resolve/reject 함수를 호출하는 구문이 잇을 경우 둘중 하나가 실행되기 전까지 다음(then.) 또는 오류(catch)로 넘어가지 않음

비동기 작업이 완료 될 때 비로소 resolve또는 reject를 호출하는 방법으로 비동기 작업의 동기적 표현이 가능해진다.

```javascript
var addCoffee = function (name) {
  return function (prevName) {
    return new Promise(function (resolve) {
      setTimeout(function () {
        var newName = prevName ? prevName + "," + name : name;
        console.log(newName);
        resolve(newName);
      }, 500);
    });
  };
};

addCoffee("에스프레소")()
  .then(addCoffee("아메리카노"))
  .then(addCoffee("카페모카"))
  .then(addCoffee("카페라떼"));
```

위 예시를 함수화 하여 짧게 표현

### Generator 예시

> ES6의 Generator

```javascript
var addCoffee = function (prevName, name) {
  setTimeout(function () {
    coffeeMaker.next(prevName ? prevName + ", " + name : name);
  }, 500);
};

var coffeeGenerator = function* () {
  var espresso = yield addCoffee("", "에스프레소");
  console.log(espresso);
  var americano = yield addCoffee(espresso, "아메리카노");
  console.log(americano);
  var mocha = yield addCoffee(americano, "카페모카");
  console.log(mocha);
  var latte = yield addCoffee(mocha, "카페라떼");
  console.log(latte);
};

var coffeeMaker = coffeeGenerator();
coffeeMaker.next();
```

- function\*() 이 Generator 함수
- 함수 실행시 Iterator 반환
  - Iterator는 next메서드를 가지고 있음
  - next호출하면 Generatorㅎ마수 내부에서 가장 먼저 등장하는 yeild에서 함수의 실행을 멈춤
  - 다시 next 메서드를 호출하면 멈췄던 부분부터 시작해서 다음 등장하는 yeild에서 함수의 실행을 멈춤
  - 그니까 한마디로 비동기 작업이 완료되는 시점마다 next메서드를 호출해준다면 Generator 함수 내부의 소스가 위에서부터 아래로 순차적으로 진행된다.

### async/await 예시

> Promise + async/await

```javascript
var addCoffee = function (name) {
  return new Promise(function (resolve) {
    setTimeout(function () {
      resolove(name);
    }, 500);
  });
};

var coffeeMaker = async function () {
  var coffeeList = "";
  var _addCoffee = async function (name) {
    coffeeList += (coffeeList ? "," : "") + (await addCoffee(name));
  };

  await _addCoffee("에스프레소");
  console.log(coffeeList);
  await _addCoffee("아메리카노");
  console.log(coffeeList);
  await _addCoffee("카페모카");
  console.log(coffeeList);
  await _addCoffee("카페라떼");
  console.log(coffeeList);
};

coffeeMaker();
```

- 비동기 작업을 수행하고자 하는 함수 앞에 async를 표기
- 함수 내부에서 실질적인 비동기 작업이 필요한 위치마다 await를 표기하는 것만으로 뒤의 내용을 Promise로 자동 전환
- 해당 내용이 resolve된 이후 다음으로 진행
  Promise의 then과 흡사한 효과
