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

