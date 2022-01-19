<details>
  <summary>목차</summary>
  <div markdown="1">

- [this](#this)
  - [3-1 상황에 따라 달라는 this](#3-1-상황에-따라-달라는-this)
    - [3-1-1 전역 공간에서의 this](#3-1-1-전역-공간에서의-this)
      - [전역 공간에서만 발생하는 특이한 성질](#전역-공간에서만-발생하는-특이한-성질)
        - [전역 공간에서는 var로 변수를 선언하는 대신 window의 프로퍼티에 직접 할당하더라도 결과적으로 var로 선언한 것과 똑같이 동작할 까?](#전역-공간에서는-var로-변수를-선언하는-대신-window의-프로퍼티에-직접-할당하더라도-결과적으로-var로-선언한-것과-똑같이-동작할-까)
    - [3-1-2 메서드로서 호출할 때 그 함수 내부에서의 this](#3-1-2-메서드로서-호출할-때-그-함수-내부에서의-this)
      - [함수 vs 메서드](#함수-vs-메서드)
        - [메서드는 객체의 프로퍼티에 할당된 함수인가 ?](#메서드는-객체의-프로퍼티에-할당된-함수인가-)
        - [`함수로서 호출` 과 `메서드로서 호출`은 어떻게 구분하는가?](#함수로서-호출-과-메서드로서-호출은-어떻게-구분하는가)
      - [메서드 내부에서의 this](#메서드-내부에서의-this)
    - [3-1-3 함수로서 호출할 때 그 함수 내부에서의 this](#3-1-3-함수로서-호출할-때-그-함수-내부에서의-this)
      - [함수 내부에서의 this](#함수-내부에서의-this)
      - [메서드의 내부함수에서의 this](#메서드의-내부함수에서의-this)
      - [메서드 내부 함수에서 this를 우회하는 방법](#메서드-내부-함수에서-this를-우회하는-방법)
      - [this를 바인딩 하지 않는 함수](#this를-바인딩-하지-않는-함수)
    - [3-1-4 콜백 함수 호출 시 그 함수 내부에서의 this](#3-1-4-콜백-함수-호출-시-그-함수-내부에서의-this)
      - [this는 어떤 값을 참조 하는가 ?](#this는-어떤-값을-참조-하는가-)
      - [대표적인 콜백 함수의 예](#대표적인-콜백-함수의-예)
    - [3-1-5 생성자 함수 내부에서의 this](#3-1-5-생성자-함수-내부에서의-this)
  - [3-2 명시적으로 this를 바인딩하는 방법](#3-2-명시적으로-this를-바인딩하는-방법)
    - [3-2-1 call 메서드](#3-2-1-call-메서드)
    - [3-2-2 apply 메서드](#3-2-2-apply-메서드)
    - [3-2-3 call / apply 메서드의 활용](#3-2-3-call--apply-메서드의-활용)
      - [1. 유사 배열 객체(array-like-object)에 배열 메서드를 적용](#1-유사-배열-객체array-like-object에-배열-메서드를-적용)
      - [2. 생성자 내부에서 다른 생성자를 호출](#2-생성자-내부에서-다른-생성자를-호출)
      - [3. 여러 인수를 묶어 하나의 배열로 전달 하고 싶을 때](#3-여러-인수를-묶어-하나의-배열로-전달-하고-싶을-때)
    - [3-2-4 bind 메서드](#3-2-4-bind-메서드)
      - [name 프로퍼티](#name-프로퍼티)
      - [상위 컨텍스트의 this를 내부 함수나 콜백함수에 전달](#상위-컨텍스트의-this를-내부-함수나-콜백함수에-전달)
    - [3-2-5 화살표 함수의 예외사항](#3-2-5-화살표-함수의-예외사항)
    - [3-2-6 별도의 인자로 this를 받는 경우(콜백 함수 내에서의 this)](#3-2-6-별도의-인자로-this를-받는-경우콜백-함수-내에서의-this)
  - [부록](#부록)
    </details>

# this

> 다른 객체지향 언어에서 this는 클래스로 생성한 인스턴스 객체
> 클래스에서만 사용할 수 있기 때문에 혼란의 여지가 많지 않음.

- 그런데 자바스크립트는 this를 어디서나 사용하기 때문에 혼란의 여지가 다분함
  - JS에서 this를 이용해 함수와 객체(메서드)의 구분을 할 수 있음

**자바스크립트에서 상황별로 this가 어떻게 달라지는지, 왜 그렇게 되는지, 예상을 벗어난 경우 그 원인을 효과적으로 추적하는 방법을 알 수 있다.**

---

## 3-1 상황에 따라 달라는 this

> JS에서 this는 기본적으로 실행 컨텍스트가 생성될 때 함께 결정된다.
> **즉, this는 함수를 호출할 때 결정된다.**

함수를 어떤 방식으로 호출하느냐에 따라 값이 달라진다.

### 3-1-1 전역 공간에서의 this

> 전역 공간에서의 this는 전역 객체를 가리킨다.

- 개념상 전역 컨텍스트를 생성하는 주체가 전역객체
  - 전역 객체는 JS 런타임 환경에 따라 다른 이름과 정보를 가지고 있음
- 브라우저 환경에서 전역객체는 window 이고 Node.js환경에서는 global

#### 전역 공간에서만 발생하는 특이한 성질

- <u>전역 변수를 선언하면 JS엔진은 이를 자동으로 전역객체의 프로퍼티로도 할당함</u>
  - 변수이면서 객체의 프로퍼티이기도 함

전역 변수와 전역 객체

```javascript
var a = 1;
console.log(a); //1
console.log(window.a); //1
console.log(this.a); //1
```

- 전역공간에서 선언한 변수 a에 1을 할당하면 window.a와 this.a에 모두 1이 출력
  - 전역공간에서의 this는 전역 객체를 의미하므로 두 값은 같은 값을 출력하는 것
- **JS의 모든 변수는 실은 특정 객체의 프로퍼티로서 동작**
  - 사용자가 var 연산자를 이용해 변수를 선언하더라도 실제 JS엔진은 어떤 특정 객체의 프로퍼티로 인식
  - 특정 객체? 실행 컨텍스트의 LexicalEnvironment(L.E)
  - 실행 컨텍스트가 변수를 수집에서 L.E의 프로퍼티로 저장하고 어떤 변수를 호출하면 L.E를 조회해서 일치하는 프로퍼티가 있을 경우 그 값을 반환
- 전역 컨텍스트의 경우 L.E는 전역객체를 그대로 참조
  - GlobalEnv가 전역객체를 참조하는데 전역 컨텍스트의 L.E가 이 GlobalEnv를 참조한다는 뜻

**전역 변수를 선언하면 JS엔진은 이를 전역객체의 프로터피로 할당한다.**

- 그렇기 때문에 window와 this의 출력 결과가둘다 a가 되는 것
  a를 직접 호출 할 때도 1이나오는 이유?
- 스코프 체인에서 a를 검색하다가 가장 마지막에 도달하는 전역 스코프의 L.E, 즉 전역객ㅊ에서 해당 프로퍼티 a를 발견해서 그 값을 반환하기 떄문
  - 짧게 말해서 (window.)이 생략 된 것

##### 전역 공간에서는 var로 변수를 선언하는 대신 window의 프로퍼티에 직접 할당하더라도 결과적으로 var로 선언한 것과 똑같이 동작할 까?

대부분의 경우 그렇다

- 전역변수 선언과 전역객체의 프로퍼티 할당 사이에 전혀 다른 경우
  - 삭제 명령에서 다름
  - 변수에 `delete` 연산자를 쓰는 것 -> 전역벼눗가 곧 전역객체의 프로퍼티 이므로 문제가 되지 않음
  - 전역 객체의 프로터피로 할당한 경우에는 삭제가 되는 반면 전역 변수로 선언한 경우에는 삭제가 되지 않음
    - 사용자가 의도치 않게 삭제하는 것을 방지
  - 전역변수를 선언하면 자바스크립트 엔진이 이를 자동으로 전역객체의 프로퍼티로 할당하면서 추가적으로 해당 프로타피의 configurable 속성(변경 및 삭제 가능성)을 false로 정의

**var로 선언한 전역변수와 전역 객체의 프로퍼티는 호이스팅 여부 및 configurable 여부에서 차이를 보인다.**

### 3-1-2 메서드로서 호출할 때 그 함수 내부에서의 this

#### 함수 vs 메서드

> 함수와 메서드를 구분하는 유일한 차이는 **독립성**

JS는 상황별로 this 키워드에 다른 값을 부여하게 하여 이를 구현

- 함수
  - 그 자체로 독립적인 기능을 수행
- 메서드
  - 자신을 호출한 대상 객체에 관한 동작을 수행

##### 메서드는 객체의 프로퍼티에 할당된 함수인가 ?

반은 맞고 반은 틀리다.

- 어떤 함수를 객체의 프로퍼퍼티에 할당한다고 해서 그 자체로 무조건 메서드가 되는 것은 아님
  - 객체의 메서드로서 호출할 경우에만 메서드로 동작
  - 위 경우가 아니면 함수로 동작

```javascript
var func = function (x) {
  console.log(this, x);
};
func(1); //  Window{...} 1

var obj = {
  method: func,
};
obj.method(2); // {method: ƒ} 2
```

- func의 변수에 익명 함수를 할당하고 func을 호출
  - this가 전역객체에서 window로 출력
- obj변수에 객체를 할당하고 그객체의 method 프로퍼티 앞에서 만든 func 함수를 할당
  - obj의 method를 호출하면 this가 obj로 출력

obj 의 method 프로터피에 할당한 값과 func변수에 할당한 값은 모두 1번재 줄에서 선언함 함수를 참조

- 원래의 익명함수는 그대로이나 변수에 담아 호출한 경우와 obj객체의 프로퍼티에 할당해서 호출한 경우에 this가 달라짐 열리는

##### `함수로서 호출` 과 `메서드로서 호출`은 어떻게 구분하는가?

- 함수 앞에 점(.)이 있는지 여부로 구분 가능

  - `func(1);` 은 함수
  - `obj.method(2);` 는 메서드
    - var obj = { method: function(x){...}}; 과 같이 정의시 `obj['method'](2)` 도 메서드

- 점 표기법, 대괄포 표기법 둘다 어떤 함수를 호출할 떄 그 함수 이름(프로퍼티명) 앞에 객체가 명시돼 있는 경우 메서드
- 그외 모든 경우 함수

#### 메서드 내부에서의 this

- this에 호출한 주체에 대한 정보가 담기고 어떤 함수를 메서드로서 호출하는 경우 호출 주체는 함수명(프로퍼티명) 앞의 객체
- 점 표기법의 경우 마지막 점 앞에 명시된 객체가 this

### 3-1-3 함수로서 호출할 때 그 함수 내부에서의 this

#### 함수 내부에서의 this

> 어떤 함수를 함수로서 호출할 경우에는 this가 지정되지 않음

- this에는 호출한 주체에 대한 정보가 담김
- 함수로서 호출하는 것은 호출 주체에(객체지향언어의 객체)를 명시하지 않고 개발자가 코드에 직접 관여해서 실행한 것이기 때문에 호출 주체의 정보를 알 수 없음
  - 2장에서 실행 컨텍스트를 활성화하면 this가 지정되지 않은 경우 this는 전역객체를 바라봄
  - 따라서 함수의 this는 전역객체를 가리킴

#### 메서드의 내부함수에서의 this

> 더글라스 크락포드왈 함수에서의 this가 전역객체를 가리키는 것이 명백한 `설계상의 오류`라고 했음

- 메서드 내부에서 정의하고 실행한 함수에서의 this, `설계상의 오류`로 인해 실제 동작과 다르게 예측할 가능성이 있음
- 내부 함수를 **함수로서 호출했는지 메서드로서 호출했는지** 파악하면 this의 값을 정확히 맞출 수 있음

예시 경우

1. 어떤 객체(obj1) 내부의 객체에 outer: function() 함수에서 바로 this를 콘솔에 찍어볼 경우
   - obj1가 콘솔에 찍힘
2. 위 outer 함수 내에서 다시 변수(var innerFunc)를 선언하고 함수를 할당하고 그 함수 내부에서 this를 콘솔에 찍어볼 경우
   - innerFunc(); 를 실행하면 전역객체 window 가 콘솔에 찍힘
3. 내부 함수내에서 var obj라는 변수선언후 innerMethod: innerFunc 인 객체를 할당한 경우

   - 내부함수에서 obj.innerMethod();를 실행한 경우 obj2가 콘솔에 찍힘

위 예시에서

- 메서드 내부에 있는 함수를 함수로서 호출하는 경우
  - outer메서드 내부에 있는 함수 innerFunc를 함수로서 호출
- 같은함수를 메서드로서 호출하는 경우

  - innerFunc를 메서드로서 호출

- 두 경우 모두 같은 함수임에도 innerFunc();에 의해 바인딩되는 this와 obj2.innerMethod();에 의해 바인딩되는 this의 대상이 서로 다름

**즉, this 바인딩에 관해서는 함수를 실행하는 당시의 환경(메서드 내부인지, 함수 내부인지 등)은 중요하지 않고, 오직 해당 함수를 호출하는 구문 앞에 점 또는 대괄호 표기가 있는지 없는지가 관건이다.**

#### 메서드 내부 함수에서 this를 우회하는 방법

> this에 대한 구분은 명확히 할 수 있음

호출 주체가 없을 때는 자동으로 전역객체를 바인딩하지 않고 호출 당시 주변 환경의 this를 그대로 상속받아 사용할 수 있으면?

- 변수를 검색하면 가장 가까운 스코프의 L.E를 찾고 없으면 상위 스코프를 탐색하듯이, this 역시 현재 컨텍스트에 바인딩된 대상이 없으면 직전 컨텍스트의 this를 바라보도록

**상위 스코프의 this를 저장해서 내부 함수에 전달하려는 목적으로 변수를 활용할 수 있음**

- ES5까지는 내부함수에 this를 상속할 방법이 없으나 변수를 활용할 수 있음
- 함수내부에서 this 를 찍지 말고 함수 선언 전에 self 라는 변수에 this를 할당하고 함수 내에서 self를 콘솔에 출력
- 변수 명으로는 self, _this, that, _ 등이 사용 됨 (self가 가장 유력)

#### this를 바인딩 하지 않는 함수

> ES6에서는 함수 내부에서 this가 전역객체를 바라보는 문제를 보완하려고 this를 바인딩 하지 않는 arrow function 을 새로 도입
> var func = () => {...} 의 형태

- arrow function 을 사용하면 우회법이 불필요함
  - **this를 바인딩하는 과정이 빠져 상위 스코프의 this를 그대로 활용할 수 있음**
  - ES5에서는 사용 불가
- [call, apply 등의 메서드를 활용해 함수를 호출할 때 명시적으로 this를 지정하는 방법도 존재](#3-2-1-call-메서드)

### 3-1-4 콜백 함수 호출 시 그 함수 내부에서의 this

> 콜백함수의 정의와 동작 원리는 [다음장에서](/chapter4/crazy-oung)

#### this는 어떤 값을 참조 하는가 ?

- 함수 A의 제어권을 다른 함수(또는 메서드) B에게 넘겨주는 경우 함수 A를 콜백 함수라 함
- 이때 함수 A는 함수 B의 내부 로직에 따라 실행되며, this 역시 함수 B내부 로직에서 정한 규칙에 따라 값 결정

#### 대표적인 콜백 함수의 예

1. setTimeout 함수

   - 시간 지연 뒤 콜백 함수를 실행
   - 일정 시간 후 전역 객체가 출력

2. forEach

   - 배열의 각 요소를 앞에서 차례대로 꺼내 그 값을 콜백 함수의 첫 번째 인자로 삼아 함수를 실행
   - 전역객체와 배열의 요소가 반복되어 출력

3. addEventListener

   - 지정한 HTML 요소에 인자로 받은 이벤트가 발생할 떄마다 그 이벤트의 정보를 하뭇의 첫 번쨰 인자로 삼아 함수를 실행
   - 이벤트가 일어나면 앞서 지정한 요소와 클릭 이벤트에 관한 정보가 담긴 객체가 출력

setTimeout, forEach 는 내부에서 콜백 함수를 호출할 때 대상이 될 this를 지정하지 않음

- 따라서 콜백 함수 내부에서 this는 전역객체를 참조

addEventListener 메서드는 콜백 함수를 호출할 때 자신의 this를 상속하도록 정의 돼 있음

- 메서드 명의 점(.) 앞부분이 곧 this

### 3-1-5 생성자 함수 내부에서의 this

> 생성자 함수는 어떤 공통된 성질을 지니는 객체들을 생성하는 데 사용되는 함수

객체지향에선 생성자를 클래스(7장에서 자세히) , 클래스를 통해 만든 객체를 인스턴스라 함

- 현실세계를 예로
  - 인간의 공통 특성 : 직립 보행, 언어 구사, 도구 사용 등
    - 이런 공통 속성을 모아 인간 집합을 정의 = 클래스
    - 각 사람은 이 클래스에 속하는 인스턴스
- 생성자? **구체적인 인스턴스를 만들기 위한** 일종의 **틀**
  - 틀에는 해당 클래스의 공통 특성들이 미리 준비
  - 구체적인 인스턴스의 개성을 더해 개별 인스턴스를 만듬

JS는 함수에 생성자로서 역할을 함께 부여했음

- new 명령어와 함게 함수를 호출하면 함수가 생성자로 동작
- 어떤 함수가 생성자 함수로서 호출된 경우 내부에서 this는 곧 새로 만들 구체적인 인스턴스 자신이 됨
- 생성자 함수를 호출(new 명령어와 함께 함수를 호출) 시
  - 생성자의 프토토타입 프로퍼티를 참조하는 **proto**라는 프로퍼티가 있는 인스턴스를 만들고
  - 미리 준비된 공통 속성 및 개성을 해당 this에 부여

---

## 3-2 명시적으로 this를 바인딩하는 방법

> 상황별 this에 바인딩되는 규칙에 어긋나는 this에 별도의 대상을 바인딩 하는 방법 존재
> 3-1의 규칙에 부합하지 않으면 3-2의 방법이 사용됨

### 3-2-1 call 메서드

- call 메서드? 메서드의 호출 주체인 함수를 즉시 실행하도록 하는 명령어
  - call 메서드의 첫 인자를 this로 바인딩
  - 이후 인자들을 호출할 함수의 매개변수로 사용

함수를 그냥 실행하면 this는 전역객체를 참조 하지만 **call 메서드를 이용하면 임의의 객체를 this로 지정할 수 있다.**

- 메서드에 대해서 객체의 메서드를 호출하면 this는 객체를 참조하지만 call 메서드를 이용하면 임의의 객체를 this로 지정

### 3-2-2 apply 메서드

- apply 메서드? call 메서드와 기능적으로 완전 동일
  - call vs apply
    - call 메서드는 나머지 모든 인자들을 호출할 함수의 매개 변수로 지정
    - apply 메서드는 **두번재 인자를 배열로 받아 그 배열의 요소들을 호출할 함수의 매개변수로 지정**

### 3-2-3 call / apply 메서드의 활용

#### 1. 유사 배열 객체(array-like-object)에 배열 메서드를 적용

객체에 배열 메서드를 직정 적용할 수 없지만 아래의 경우 call 또는 apply메서드를 이용해 배열 메서드 차용 가능

- **키가 0또는 양의 정수인 프로퍼티가 존재 & length 프로퍼티의 값이 0 또는 양의 정수인 객체**
- 배열의 구조와 유사한 객체의 경우(유사배열객체)
- `Array.prototype.push.call(obj, 'd');`, `Array.prototype.slice.call(obj);`

함수 내부에서 접근가능한 arguments 객체도 유사배열객체이므로 위 방법으로 배열로 전환해서 활용 가능

- querySelectorAll, getElementsByClassName 등의 Node 선택자로 선택한 결과인 NodeList도 마찬가지

```javascript
const nodeList = document.querySelectorAll("div");
const nodeArr = Array.prototype.slice.call(nodeList);
nodeArr.forEach(function node){
    ...
}
```

이 외에 유사배열객체에는 call/apply 메서드를 이용해 모든 배열 메서드를 적용 가능

- 배열 처럼 인덱스와 legnth 프로퍼티를 지니는 문자열에 대해서도 마찬가지
- 문자열의 경우 length 가 읽기 전용이기에 문자열을 변경하는 pusth, pop, shift, unshift, splice 등은 에러
  - concat처럼 대상이 반드시 배열이어야 하는 경우 에러는 안나지만 제대로된 결과를 얻을 수 없음

ES6에서는 유사배열객체 또는 순회 가능한 모든 종류의 데이터 타입을 배열로 전환하는 Array.from 메서드를 도입

- var arr = Array.from(obj)

#### 2. 생성자 내부에서 다른 생성자를 호출

생성자 내부에서 다른 생성자와 공통된 내용이 있을 경우 call 또는 apply메서드를 이용해 다른 생성자를 호출하면 간단하게 반복을 줄일 수 있음

- Student 와 Person 이 있을 때 Student내에서 Person.call()을 이용

#### 3. 여러 인수를 묶어 하나의 배열로 전달 하고 싶을 때

> apply를 활용

여러개의 인수를 받는 메서드에게 하나의 배열로 인수들을 전달하고 싶을 때 apply 메서드를 사용

- 배열에서 최대/최솟값을 구해야할 경우 apply를 사용하지 않는다면
  - if(number>max) 와 if(number < min) 조건문이 forEach 내부에 둘다 존재해야 됨
  - 코드가 불필요 하게 길고 가독성도 떨어짐
- Math.max/min 을 apply와 함께 쓰면
  - Math.max.applu(null, numbers) 또는 Math.min.applu(null, numbers); 와 같이 사용해 변수에 바로 대입 가능
- ES6에서는 spread 연산자를 이용해 apply를 이용하는 것보다 간편하게 작성 가능
  - Math.max(...numbers), Math.min(...numbers)

call/apply 메서드는 명시적으로 별도의 this를 바인딩하면서 함수 or 메서드를 실행하지만 이로 인해 this를 예측하기 어렵게 만드므로 해석을 방해할 수 있으므로 주의

### 3-2-4 bind 메서드

```javascript
Function.prototype.bind(thisArg[,arg1[, arg2[, ... ]]])
```

- bind는 ES5애서 추가됨
- call 과 비슷하지만 즉시 호출하지 않고 넘겨 받은 this 및 인수들을 바탕으로 새로운 함수를 반환
  - 새로운 함수를 호출할 때 인수를 넘기면 그 인수들은 기존 bind메서드를 호출할 때 전달 했던 인수들의 뒤에 이어서 등록
- **bind 메서드는 함수에 this를 미리 적용하는 것과 부분 적용 함수를 구현하는 두가지 목적을 모두 지님**

func(a, b, c, d) 인 함수에 func.bind({x:1}) 나 func.bind({x:1}, 4, 5) 와 같이 바인딩

- 바인딩한 결과를 담은 변수인 bindFunc를 실행하면 bindFunc(5, 6, 7, 8) 이나 bindFunc(7, 8) 처럼 실행하면 this를 {x:1} 로 지정한 새 함수가 담기고 바인딩 결과를 console.log(this, a, b, c, d) 를 실행하면 {x:1} 5 6 7 8 과 {x:1} 4 5 7 8 을 얻음

#### name 프로퍼티

바인드 메서드를 적용해서 새로 만든 함수는 name 프로퍼티에 동사 bind 수동태인 'bound' 접두어가 붙음

- 네임 프로퍼티가 'bound xxx' 가 되므로 call이나 apply보다 코드를 추적하기 쉬워짐

#### 상위 컨텍스트의 this를 내부 함수나 콜백함수에 전달

- 메서드 내부 함수에서 메서드 this를 그대로 바라보게 하기 위한 방법으로 self와 같은 변수를 이용한 우회 방법이 있는데 이 외에 call, apply, bind를 이용하면 깔끔하게 처리 가능
- 콜백함수를 인자로 받는 함수나 메서드 중에서 기본적으로 콜백 함수 내에서 this에 관여 하는 함수 or 메서드에 대해서 bind 이용시 this값을 사용자의 입맛에 맞게 변경이 가능

### 3-2-5 화살표 함수의 예외사항

> ES6에 새로 도입된 화살표 함수는 실행 컨텍스트 생성시 this를 바인딩하는 과정이 제외됨
> this가 아예 없고 접근하고자 하면 스코프체인상 가장 가까운 this에 접근

내부 함수를 arrow function 으로 바꾸면 별도의 변수로 우회하거나 call/ apply/ bind 를 적용할 필요가 없어 간결&편리

### 3-2-6 별도의 인자로 this를 받는 경우(콜백 함수 내에서의 this)

콜백 함수를 인자로 받는 메서드 중 일부는 추가로 this로 지정할 객체(thisArg) 를 인자로 지정할 수 있는 경우가 있음

- 메서드의 thisArg값을 지정하면 콜백함수 내부에서 this 값을 원하는대로 변경 가능
  - 여러 내부 요소에 대해 같은 동작을 반복 수행해야 하는 **배열 메서드에 많이 포진**
- ES6에서 새로 등장한 Set, Map등의 메서드에도 일부 존재

```javascript
...
add: function() {
    var args = Array.prototype.slice.call(arguments);
    args.forEach(function(entry){
        this.sum += entry;
        ++this,count;
    }, this)
}
...
```

thisArg를 인자로 받는 메서드

- forEach
- map
- filter
- some
- every
- find

## 부록