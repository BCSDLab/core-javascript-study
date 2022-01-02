# 02. 실행 컨텍스트

## 01. 실행 컨텍스트란?

### Stack, Queue(사전 지식)

**Stack** : 출입구가 하나인 깊은 우물과 같은 데이터 구조

- 저장순서 : a->b->c->d

- 사용순서 : d->c->b->a


**Queue** : 양쪽이 모두 열려있는 파이프

- 저장순서 : a->b->c->d
- 사용순서 : a->b->c->d

### 실행 컨텍스트

- 실행 컨텍스트 : **실행할 코드에 제공할 환경 정보들을 모아놓은 객체**

**환경과 순서 보장 하는 방법**

- 코드 실행시, 필요한 환경 정보들을 모아 컨텍스트를 구성후, **콜 스택**에 쌓아올림
- 가장 위에 쌓여있는 컨텍스트와 관련있는 코드들을 실행

**실행 컨텍스트를 구성하는 방법**
- 전역공간, eval()함수, 함수
- 흔히 실행 컨텍스트를 구성하는 방법 : **함수를 실행**

**실행 컨텍스트와 콜 스택**
```javascript
//(1)전역 컨텍스트

var a = 1;
function outer(){
  function inner(){
    console.log(a);//undefined
    var a = 3;
  }
  inner();//(2)
  console.log(a);//1
}

outer(); //(3)
console.log(a); //1
```

**콜 스택 순서**(왼쪽:위,오른쪽:아래)

빈 객체 -> 전역 컨텍스트 -> outer/전역컨텍스트 -> inner/outer/전역 컨텍스트 -> outer/전역 컨텍스트 -> 전역 컨텍스트 -> 빈 객체

- 콜 스택의 맨 위에 쌓이는 순간 = 현재 실행할 코드에 관여하게 되는 시점

**실행 컨텍스트 객체에 저장하는 정보는 무엇인가?**

- 실행 컨텍스트가 활성화 될 때, javascript 엔진은 **해당 컨텍스트에 관련된 코드들을 실행하는데 필요한 환경 정보**들을 수집해서 실행 컨텍스트 객체에 저장

종류

- **VariableEnvironment** : 현재 컨텍스트 내의 식별자들에 대한 정보 + 외부 환경 정보, 선언 시점의 LexicalEnvironment의 스냅샷(**변경 사항 반영x**)
- **LexicalEnvironment** : 처음에는 VariableEnvironment와 같지만 **변경 사항이 실시간으로 반영**
- **ThisBinding** : this 식별자가  바라봐야 할 대상 객체

## 02. VariableEnvironment

**특징**
- LexicalEnvironment와 내용은 같지만, 최초 실행 시의 **스냅샷을 유지**한다는 점이 다르다.

**실행컨텍스트 생성**
- VariableEnvironment에 정보를 먼저 담은 다음, 복사를 한 후 LexicalEnvironment를 만들고, 이후에는 LexicalEnvironment를 주로 활용

**VariableEnvironment, LexicalEnvironment의 내부 구성 요소**
- environmentRecord, outer-EnvironmentReference

## 03. LexicalEnvironment

번역 : Lexical(사전적인) + Environment(환경)

**사전적인 환경 예시**

- 백과사전 : "바나나"검색 -> "칼로리가 가장 높고 당질이 많고, 칼륨, 카로틴, 비타민C를 함유함"

- LexicalEnviroment : "현재 컨텍스트" -> "a,b,c,와 같은 식별자가 존재하고, 그 외부 정보는 D를 참조하도록 구성되어 있다."

### environmentRecord와 호이스팅

**environmentRecord 저장 정보**

- 현재 컨텍스트와 관련된 코드의 식별자 정보들이 저장
- 컨텍스트 내부 전체를 처음부터 끝까지 쭉 훑어나가며 **순서대로** 수집한다.

**참고**

- 전역실행 컨텍스트는 변수 객체를 생성하는 대신, **전역 객체**를 활용한다.
- 전역 객체의 종류 : window, Node.js의 global 등(호스트 객체)

**호이스팅**

- '자바스크립트 엔진은 식별자들을 최상단으로 끌어올려놓은 다음 실제 코드를 실행한다'
- hoisting : '끌어올리다'


### 호이스팅 규칙

```javascript
  function a(x) {
    console.log(x);
    var x;
    console.log(x);
    var x = 2;
    console.log(x);
  }
  
  a(1);
```

**호이스팅이 되지 않은 경우 출력 값**

```javascript
  1
  undefined
  2
```

**호이스팅이 적용이 된다면(임의 시뮬레이션)**

```javascript
  function a(){
    var x; //(1)
    var x; //(2)
    var x; //(3)
    
    x = 1; //(4)
    console.log(x); //(5)
    console.log(x); //(6)
    x = 2; //(7)
    console.log(x); //(8)
  }
```
**순서 요약**

- (1) 변수 x 선언 : 저장공간 확보, 확보한 주솟값을 변수 x에 연결
- 
- (2),(3) 변수 x 선언 : 이미 앞서 선언되었기 때문에 새로 선언하지 않는다.
- 
- (4) x에 1을 할당 : 숫자 1의 값을 별도의 메모리에 담은 뒤에, x와 연결된 메모리 공간에 숫자 1을 가리키는 주솟값 입력
- 
- (5),(6) x를 출력 : 1이 출력
- 
- (7) x에 2를 할당 : 숫자 2의 값을 별도의 메모리에 담은 뒤에, x와 연결된 메모리 공간에 숫자 2를 가리키는 주솟값으로 대체
- 
- (8) 2가 출력되고, 함수 내부의 모든 코드가 실행되었으므로, 실행 컨텍스트가 콜 스택에서 제거


```javascript
  1
  1
  2
```

```javascript
  function a(){
    console.log(b);
    var b = 'bbb';
    console.log(b);
    function b(){}
    console.log(b);
  }
  a();
```

**호이스팅이 되지 않은 경우 출력 값**

```javascript
  undefined
  'bbb'
  function b
```
**호이스팅이 적용이 된다면(임의 시뮬레이션)**

```javascript
  function a(){
    var b; //(1)
    function b(){} //(2)  //편의를 위해서 변경 -> var b = function b(){}
    
    console.log(b); //(3)
    b='bbb'; //(4)
    console.log(b); //(5)
    console.log(b); //(6)
  }
  ```
  
**순서 요약**

- (1) 변수 b 선언 : 저장할 공간을 미리 확보하고, 확보한 공간의 주솟값을 변수 b에 연결

- (2) b 함수 선언 : 변수 b를 선언하고 함수b를 선언된 변수 b에 할당 -> 이미 b는 선언되었으므로 무시

- (3) 변수 b에 할당된 함수 b출력

- (4) 변수 b에 'bbb'를 할당 : 함수가 저장된 주소값을 'bbb'가 저장된 주솟값으로 덮어씀

- (5),(6) 둘다 'bbb'가 출력됨, 함수 내부의 모든 코드가 실행되었으므로 실행 컨텍스트가 콜 스택에서 제거됨

### 함수 선언문과 함수 표현식

- **함수 선언문** : function 정의부만 존재하고 별도의 할당 명령이 없는 것
- **함수 표현식** : 정의한 function을 별도의 변수에 할당 하는 것
- 함수명을 정의한 함수 표현식 : '기명 함수 표현식'
- 함수명을 정의하지 않은 표현식 : '익명 함수 표현식'

**함수 표현 3가지 방식**
```javascript
  function a(){}
  a(); //실행
  
  var b = function(){}
  b(); //실행
  
  var c = function d(){}
  
  c(); // 실행
  d(); // 에러
```

**참고**

- 외부에서는 함수명으로 함수를 호출할 수 없다.
- 함수명은 오직 함수 내부에서만 접근할 수 있다.

**원본 코드**
```javascript
  console.log(sum(1,2));
  console.log(multiply(3,4));
  
  // 함수 선언문
  function sum (a,b) { 
    return a+b;
  }
  
  // 함수 표현식
  var multiply = function(a,b){
    return a*b;
  }
```


**호이스팅을 마친 상태**

```javascript
  var sum = function sum(a,b){
    return a+b;
  }; //(1)
  var multiply; //(2)
  
  console.log(sum(1,2)); //(3)
  console.log(multiply(3,4)); //(4)
  
  multiply = function(a,b){
    return a+b;
  } //(5)
  
```

- (1) : 메모리 공간을 확보하고 확보된 공간의 주솟값을 변수 sum에 연결

- (2) : 메모리 공간을 확보하고 확보된 공간의 주솟값을 변수 multiply에 연결

- (1) : sum 함수를 또 다른 메모리 공간에 저장을 하고, 주솟값을 변수 sum의 공간에 할당

- (3) : sum을 실행하여 3을 출력

- (4) : 'multiply is not a function' 에러 메시지 출력

- (5) : 에러로 인해 실행되지 않은 채로 런타임 종료

**함수 선언문 위험성**

```javascript
  console.log(sum(3,4));
  
  function sum(x,y){
    return x+y;
  }
  
  var a = sum(1,2);
  
  function sum(x,y){
    reutrn x+'+'+y+'='+(x+y);
  }
  
  var c = sum(1,2);
  console.log(c);
```

- A라는 사람이 먼저 sum 함수가 Number값을 return하고자 정의

- B라는 사람이 나중에 sum 함수가 문자열 형식으로 return하고자 정의

- A라는 사람이 sum(3,4);를 console.log로찍을 경우 return값이 Number가 아닌 문자열로 나와서 당황

**함수 표현식 사용**

```javascript
  console.log(sum(3,4)); //sum is not a function
  
  var sum = function(x,y){
    return x+y;
  }
  
  var a = sum(1,2);
  
  var sum = function(x,y){
    reutrn x+'+'+y+'='+(x+y);
  }
  
  var c = sum(1,2);
  console.log(c);
```

- 이렇게 선언해주면, A라는 사람과 B라는 사람이 각각 원하는 결과를 얻을 수 있다.

결론 : 상대적으로 함수 표현식이 더 안전하다.


### 스코프,스코프 체인, outerEnvironmentReference

- 스코프 : 식별자에 대한 유효범위

- 스코프 체인 : 식별자의 유효범위를 안에서부터 바깥으로 차례로 검색해나가는 것

- outerEnvironmentReference : LexicalEnvironment의 두번째 수집자료, 스코프 체인을 가능하게 해준다.


### 스코프 체인

- outerEnvironmentReference는 현재 호출된 함수가 **선언될 당시**의 LexicalEnvironment를 참조한다.

- '선언하다' : 콜 스택 상에서 어떤 실행 컨텍스트가 활성화된 상태

ex) A함수 내부에 B함수 선언, B함수 내부에 C함수 선언한 경우

- 함수 C의 outerEnvironmentReference는 함수 B의 LexicalEnvironment를 참조한다.
- 함수 B의 LexicalEnvironment에 있는 outerEnvironmentReference는 함수 B가 선언되던 때 A의 LexicalEnvironment를 참조한다.
- 선언 시점의 LexicalEnvironment를 계속 찾아 올라가면 마지막엔 전역 컨텍스트의 LexicalEnvironment가 있다.
- 각 outerEnvironmentReference는 오직 자신이 선언된 시점의 LexicalEnvironment만 참조하고 있어서, 가장 가까운 요소부터 차례대로 접근 가능하다.
- 구조 특정상 동일한 식별자를 선언한 경우, 스코프 체인 상태에서 **가장 먼저 발견된 식별자에게만 접근이 가능**하다.


```javascript
  var a = 1; // 1번
  var outer = function(){ // 2번
    var inner = function(){ //3번
      console.log(a); //4번
      var a = 3; //5번
    }; //6번
    inner(); //7번
    console.log(a); //8번
  }; //9번
  
  outer(); //10번
  console.log(a); //11번
```

**실행 순서**

- 전역 컨텍스트 활성화, 전역 컨텍스트의 environmentRecord에 {a,outer} 식별자 저장, 전역 컨텍스트는 선언 시점이 존재하지 않기 때문에, 전역 컨텍스트의 outerEnvironment-Reference에는 아무것도 담기지 않는다(this:전역객체)

-  1번,2번 줄 : 전역 스코프에 존재하는 변수 a에 1, outer에 함수 할당

-  10번 줄 : outer함수 호출(전역 컨텍스트 코드 중단 + outer 실행 컨텍스트 활성화)

-  2번 줄 : outer 실행 컨텍스트의 environmentRecord : {inner} 식별자 저장, outerEnvironmentRecord : 전역 컨텍스트의 LexicalEnvironment

-  3번 줄 : outer 스코프에 있는 변수 inner에 함수 할당

-  7번 줄 : inner 함수를 호출한다. outer 실행 컨텍스트는 잠시 중단되고, inner 실행 컨텍스트가 활성화 되어 3번줄로 이동

-  3번 줄 : inner 실행 컨텍스트의 environmentRecord : {a} 식별자 저장, outerEnvironmentRecord : outer 컨텍스트의 LexicalEnvironment 

-  4번 줄 : 식별자 a 에 접근 -> inner 컨텍스트의 environmentRecord에 a 검색 -> 아직 할당된 값 없어서 undefined 출력

-  5번 줄 : inner 스코프 변수 a에 3 할당

-  6번 줄 : inner 함수 실행이 종료된다. inner 실행 컨텍스트가 콜 스택에서 제거되고, 바로 아래의 outer 실행 컨텍스트가 다시 활성화 되어, 아까 중단했던 7번째 줄 다음으로 이동

-  8번 줄 : 식별자 a에 접근 -> 전역 LexicalEnvironment의 a에 저장된 값 1을 반환한다.(찾는 방법 : 활성화된 실행 컨텍스트의 LexicalEnvironment에 접근 -> environmentRecord에 a가 있는지 찾음 -> 없는 경우 outerEnvironmentReference에 있는 environmentRecord로 넘어감)

- 9번 줄 : outer 함수 실행이 종료된다. outer 실행 컨텍스트가 콜 스택에서 제거되고, 바로 아래의 전역 컨텍스트가 활성화 되면서, 아까 중단했던 10번줄의 다음으로 이동

- 11번 줄 : 식별자 a에 접근 -> 현재 활성화 상태인 전역 컨텍스트의 envrionmentRecord에서 a를 검색후 1출력, 모든 코드의 실행 완료되어, 전역 컨텍스트가 콜 스택에서 제거되고 종료


**정리**

- 전역 공간 : 전역 스코프에서만 생성도니 변수에만 접근 가능
- outer 함수 내부 : outer 및 전역 스코프에서 생성된 변수에 접근 가능
- inner 함수 내부 : inner,outer,전역 스코프 모두에 접근 가능

**변수 은닉화**

- 식별자 a는 전역 공간에서도 선언하고, inner 함수 내부에서도 선언함
- inner 함수 내부에서 a에 접근하려면 무조건 스코프 체인 상의 첫 번째 인자인 inner 스코프의 LexicalEnvironment부터 검색
- LexicalEnvironment에 a 식별자가 존재하므로, 즉시 a 반환

결론 : inner 함수 내부에서 a 변수를 선언했으므로, 전역 공간에 선언한 동일한 이름의 a 변수에는 접근할 수 없다.
