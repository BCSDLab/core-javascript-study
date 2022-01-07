# 03. this


## 01. 상황에 따라 달라지는 this

- this는 기본적으로 실행 컨텍스트가 생성될 때 함께 결정된다.
- 실행 컨텍스트는 함수를 호출할 때 생성되므로, **this는 함수를 호출할 때 결정된다**

### 전역 공간에서의 this

- 전역 공간에서 this는 **전역 객체**를 가리킨다.(전역 컨텍스트를 생성하는 주체 : 전역 객체)
- 전역 객체의 종류 : 브라우저 환경(window), Node.js 환경(global)


전역 공간에서의 this(브라우저 환경)
```javascript
  console.log(this === window); // true
```

전역 공간에서의 this(Node.js 환경)
```javascript
  console.log(this === global); // true
```

참고 - 전역 공간에서만 발생하는 특이한 성질

- 전역변수를 선언하면, 자바스크립트 엔진은 이를 전역객체의 프로퍼티로 할당한다.

```javascript
  var a = 1;
  console.log(a); //1
  console.log(window.a); //1
  console.log(this.a) //1
```

- 자바스크립트의 모든 변수는 특정 객체의 프로퍼티로 동작한다.
- 특정 객체 : LexicalEnvironment
- 실행 컨텍스트는 변수를 수집해서 L.E의 프로퍼티로 저장한다. / 변수 호출시 LE를 조회해서 일치하는 경우 그 값을 반환한다.
- 전역 컨텍스트의 경우 L.E는 전역객체를 그대로 참조한다.

결론 : **전역변수를 선언하면 자바스크립트 엔진은 이를 전역객체의 프로퍼티로 할당한다**

왜 window.a, this.a가 아닌 그냥 a도 1이 나오는 걸까?

- 변수 a에 접근할때, 스코프 체인에서 a를 검색하다가 가장 마지막인 **전역 스코프의 L.E**, 전역객체에서 해당 프로퍼티 a를 발견해서 반환하기 때문에


전역 공간에 var 변수를 선언하는 대신, window 프로퍼티에 직접 할당

```javascript
  var a = 1;
  window.b = 2;
  console.log(a,window.a,this.a); // 1 1 1
  console.log(b,window.b,this.b); // 2 2 2 
  
  window.a = 3;
  b = 4;
  console.log(a, window.a, this.a); // 3 3 3
  cosnole.log(b, window.b, this.b) // 4 4 4
```

'삭제' 명령

```javascript
  var a = 1;
  delete window.a; // false
  console.log(a,window.a,this.a); // 1 1 1
```

```javascript
  var b = 2;
  delete b; // false
  console.log(b,window.b,this.b); // 2 2 2
```

```javascript
  window.c = 3;
  delete window.c; // true
  console.log(c,window.c,this.c); // Uncaught ReferenceError : c is not defined
```

```javascript
  window.d = 4;
  delete window.d; // true
  console.log(d,window.d,this.d); // Uncaught ReferenceError : d is not defined
```

- 전역변수가 곧 전역객체의 프로퍼티이므로 delete는 문제가 되지않는다
- 전역객체의 프로퍼티로 할당하는 경우 삭제가 된다.
- 전역변수로 선언한 경우 삭제가 되지 않는다. -> 사용자가 의도치 않게 삭제하는 것을 방지하는 차원에서 마련한 방어 전략
- 전역변수 선언시, 자바스크립트 엔진이 이를 자동으로 전역객체의 프로퍼티로 할당하면서 추가적으로 해당 프로퍼티의 configurable 속성(변경 및 삭제 가능성)을 false로 정의한다.

결론 : var로 선언한 전역변수와 전역객체의 프로퍼티는 호이스팅 여부 및 configurable 여부에서 차이를 보인다.

### 메서드로 호출할 때 그 메서드 내부에서의 this


**함수 vs 메서드**

- 함수,메서드 : 미리 정의한 동작을 수행하는 코드 뭉치
- 함수,메서드의 유일한 차이 : **독립성**
- 함수 : 그 자체로 **독립적인 기능**을 수행
- 메서드 : 자신을 호출한 **대상 객체**에 관한 동작을 수행

어떤 함수를 객체의 프로퍼티에 할당한다 -> 무조건 메서드가 아니다.

객체의 메서드로 호출할 경우 메서드로 동작하고, 그렇지 않으면 함수로 동작한다.
```javascript
  var func = function(x){
    console.log(this,x);
  };
  
  func(1); // Window{...} 1
  
  var obj = {
    method:func
  };
  obj.method(2) // {method:f} 2
```

- func에 익명함수 할당 후, func를 호출하면 this -> window 1 
- obj라는 객체의 method 프로퍼티에 func 할당 후, obj.method 호출하면 this -> {method:f} 2
- 익명함수는 그대로이지만, 변수를 담아 호출한 경우, obj 객체의 프로퍼티에 할당해서 호출한 경우에 this가 달라진다.

'함수로서 호출'과 '메서드로서 호출' 구분 방법
- 함수 앞에 점(.)이 있는지 여부만으로 간단하게 구분이 가능하다.


```javascript
  var obj = {
    method : function (x) {console.log(this,x);}
  };
  
  obj.method(1);  // {method:f} 1
  obj['method'](2); // {method:f} 2
```
- 점 표기법, 대괄호 표기법에 상관없이 함수 이름 앞에 객체가 명시돼어있는 경우엔 메서드 호출, 그렇지 않은 모든 경우엔 함수 호출

### 메서드 내부에서의 this

```javascript
  var obj = {
    methodA : function(){console.log(this);}
    inner:{
      methodB : function(){console.log(this);}
    }
  };
  
  obj.methodA(); // {methodA : f, inner:{...}} (===obj)
  obj['methodA'](); // {methodA : f, inner:{...}} (===obj)
  
  obj.inner.methodB(); // {methodB : f} (===obj.inner)
  obj.inner['methodB'](); // {methodB : f} (===obj.inner)
  obj['inner'].methodB(); // {methodB : f} (===obj.inner)
  obj['inner']['methodB'](); // {methodB : f} (===obj.inner)
```

### 함수로서 호출할 때 그 함수 내부에서의 this

**함수 내부에서의 this**

- this에는 호출한 주체에 대한 정보가 담긴다.
- 함수를 함수로써 호출한경우, 호출 주체를 명시하지 않고, 개발자가 코드에 직접 관여해서 실행한 것이기 때문에 호출 정보를 알 수 없다.
- this가 지정되지 않은 경우에는 this는 전역 객체를 바라본다.
- 함수에서의 this는 전역 객체를 가리킨다.
- 더글라스 크락포드는 이를 명백한 설계상의 오류라고 지적한다.

**메서드 내부함수에서의 this**

- 함수로써 호출했는지, 메서드로서 호출했는지 파악하면 this의 값을 정확히 맞출 수 있다.

```javascript
  var obj1 = {
    outer : function(){
      console.log(this); //obj1
      var innerFunc = function(){
        console.log(this);
      }
      innerFunc(); //전역객체(Window)
      
      var obj2 = {
        innerMethod : innerFunc
      };
      
      obj2.innerMethod(); //obj2
      
    }
  };
  
  obj1.outer();
```

- 객체를 생성, 객체 내부에는 outer라는 프로퍼티가 있고, 익명함수가 연결된다. 생성한 객체를 obj1에 할당
- obj1.outer 호출
- obj1.outer의 실행 컨텍스트가 생성되면서 호이스팅 발생, 스코프 체인 정보를 수집하고, this를 바인딩(함수명인 outer앞에 점(.)이 존재하므로, 점 앞의 객체인 obj1이 바인딩)
- obj1 객체 정보 출력
- 호이스팅된 변수 innerFunc는 outer 스코프 내에서만 접근이 가능한 지역변수이다. 지역변수에 익명함수를 할당한다.
- innerFunc 호출
- innerFunc 실행 컨텍스트가 생성되면서 호이스팅, 스코프 체인 수집, this 바인딩 수행(함수명 앞에 점(.)이 없어 함수로써 호출한 것이기 때문에, this가 전역객체(Window)로 바인딩)
- Window 객체 정보 호출
- obj2도 outer 스코프 내에서만 접근할 수 있는 지역변수이고, innerFunc로 정의된 innerMethod라는 프로퍼티를 가진 객체를 할당한다.
- obj2.innerMethod 호출
- obj2.innerMethod 함수의 실행 컨텍스트가 생성되고, 함수 호출시 함수명인 innerMethod 앞에 점(.)이 존재하므로 메서드로써 호출한것이다. 따라서 this에는 obj2가 바인딩된다.
- obj2 객체 정보가 출력된다.

정리 : this 바인딩에 관해서는 함수를 실행하는 당시의 주변 환경(메서드 내부, 함수 내부)은 중요하지 않고, **해당 함수를 호출하는 구문 앞에 점 또는 대괄호 표기가 있는지 없는지**가 관건이다.

### 메서드의 내부 함수에서의 this를 우회하는 방법

- 호출 주체가 없는 경우, 자동으로 전역객체를 바인딩하는 것이 아닌, 호출 당시 주변 환경의 this를 그대로 상속받고 싶다!
- 변수를 검색하면 우선 가장 가까운 스코프의 L.E를 찾고, 없으면 상위 스코프를 탐색하듯이
- 현재 컨텍스트에 바인딩된 대상이 없으면 직전 컨텍스트의 this를 바라보도록!


**변수 활용**

```javascript
  var obj = {
    outer : function(){
      console.log(this); // {outer : f}
      var innerFunc1 = function(){
        console.log(this); // Window {}
      };
      innerFunc1();
      
      **var self = this;**
      var innerFunc2 = function(){
        console.log(self); // {outer : f}
      }
      
      innerFunc2();
    }
  };
  
  obj1.outer();
```

- innerFunc1 내부에서 this : 전역객체
- outer 스코프에서 self라는 변수에 this를 정한 상태해서 호출한 innerFunc2의 경우 self에는 객체 obj2가 출력

### this를 바인딩하지 않는 함수

- ES6에서는 this가 전역객체를 바라보는 문제를 보완 -> this를 바인딩하지 않는 **화살표 함수**를 도입
- 화살표 함수는 실행 컨텍스트를 생성시, this 바인딩 과정 자체가 빠지게되어, **상위 스코프의 this**를 그대로 활용한다.

```javascript
  var obj = {
    outer : function(){
      console.log(this); // {outer : f}
      var innerFunc = () => {
        console.log(this); // {outer : f}
      };
      innerFunc();
    }
  };
  
  obj.outerFunc();
```

- call, apply 메서드를 활용해서 함수를 호출할 때 명시적으로 this를 지정하는 방법이 존재한다.
