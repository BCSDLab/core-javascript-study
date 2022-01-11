# this

자바스크립트에서 가장 혼란스러운 개념이다.
다른 대부분의 객체지향 언어에서는 this는 클래스를 생성한 인스턴스의 객체를 의미한다.
클래스에서만 사용할 수 있기에 혼란의 여지가 없거나 많지 않았지만, JS의 경우에는 this가 바라보는 대상이 달라지는데 어떤 이유로 그렇게 되었는지 파악하기도 쉽지 않고, 예상과는 전혀 다른 대상을 바라보는 경우도 있다.
이런 경우에 문제를 해결하려면 원인을 추적해서 수정해야하는데 정확한 작동 방식을 이해하지 못하면 원인을 파악해서 해결할 수 없다.
함수와 객체의 구분이 느슨한 자바스크립트에서 this는 실질적으로 이 둘을 구분하는 거의 유일한 기능이다.
이번 장에서는this가 어떻게 달라지는지 왜 그렇게 되는ㄴ지 예상과 다른 대상을 바라보고 있을 경우 그 원인을 효과적으로 추적하는 방법등을 살펴보겠다.

## 상황에 따라 달라지는 this

JS 기준, this는 기본적으로 실행 컨텍스트가 생성될 때 함께 결정된다. 실행 컨텍스트는 함수를 호출할 때 생성되므로, 바꿔 말해 **this는 함수를 호출할 때 결정**된다고 할 수 있다.
함수를 어떤 방식으로 호출하느냐에 따라 값이 달라지는 것이다.

### 전역 공간에서의 this

전역공간에서 this는 전역 객체를 가리킨다. 개념상 전역컨텍스트를 생성하는 주체가 바로 전역객체이기 때문이다.
전역객체는 JS런타임 환경에 따라 다른 이름과 정보를 가지고 있다.
브라우저 환경에서 전역객체는 window이고 Node.js환경에서는 global이다.

- 전역공간의 특이한 성질
  전역변수를 선언하면 자바스크립트 엔진은 이를 전역객체의 프로퍼티로도 할당한다.
  변수이면서 객체의 프로퍼티이기도 한 것이다.
  자바스크립트의 모든 변수는 특정 객체의 프로퍼티로써 동작하기 때문이다.
  사용자가 변수를 선언하더라도 실제 자바스크립트 엔진은 어떤 특정 객체의 프로퍼티로서 인식한다.
  특정 객체란 바로 실행 컨텍스트의 LexicalEnviroment이다.
  실행 컨텍스트는 변수를 수집해서 LE의 프로퍼티로 저장한다. 이후 어떤 변수를 호출하면 LE를 조회하여 일치하는 프로퍼티가 있는 경우 그 값을 반환한다.
  (전역 컨텍스트의 경우, LE는 전역객체를 그대로 참조한다.)

정확히는 전역변수를 선언하면 자바스크립트 엔진은 이를 전역객체의 프로퍼티로 할당한다.
변수 a라는 것에 접근하고자 하면 스코프 체인에서 a를 검색하다가 가장 마지막에 도달하는 전역 스코프LE에서 a를 발견하여 도달한다. 전역변수 a에는 그냥 단순하게 window.이 생략된 것이라고 여겨도 괜찮다.

전역변수 선언과 전역객체의 프로퍼티 할당 사이에 전혀 다른 경우는 삭제의 경우를 들 수 있다.
delete연사자를 사용하는 것이 이상할 수 는 있지만 전역변수가 곧 전역객체의 프로퍼티이므로 문제가 되지 않는다. 다만 처음부터 전역객체의 프로퍼티로 할당한 경우에는 삭제되지만 반면 전역변수로 선언된 것은 삭제되지 않는다.
이는 이 둘을 나누는 기준이 된다. 과정을 살펴보면 전역변수를 선언하면 JS 엔진이 자동으로 전역객체의 프로퍼티를 할당하면서 추가저긍로 해당 프로퍼티의 configurable속성을 false로 정의한다.
var로 선언한 변수와 전역객체의 프로퍼퍼티는 호이스팅 여부 및 configurable여부에서 차이를 보여준다.

### 메서드로서 호출할 때 그 메서드 내부에서의 this

- 함수 vs 메서드
  어떤 함수를 실행하는 방법은 여러가지가 있는데 가장 일반적인 방법은 두가지로 함수로서 호출하는 겨웅와 메서드로서 호출하는 경우가 있다. 프로그래밍 언어에서 하수와 메서드는 미리 정의한 동작을 수행하는 코드 뭉치로 이둘을 구분하는 유일한 차이는 독립성이다. 함수는 그 자체로 독립적인 기능을 갖고 있지만 메서드는 자신을 호출한 대상 객체에 관한 동작을 수행한다.
  this 키워드에 다른 값을 부여하게 함으로써 이를 구현한다.

흔히 메서드를 객체의 프로퍼티에 할당된 함수로 이해한다. 나는 그렇다. 암초보
이것은 50의 정답이다. 어떤 함수를 객체의 프로퍼티에 할당한다고해서 그자체로 메서드가 되는 것이 아닌 객체의 메서드로서 호출한 경우에만 메서드로 동작하고 그렇지 않으면 함수로 동작한다.
//메서드란? 객체의 프로퍼티에 내부에 할당된 함수 중 메서드로서 호출된 함수를 메서드라고 한다??
예시를 보자

```
	var func = function(x){
		console.log(this, x)
	}
	func(1)
	//Window {0: Window, window: Window{...} Window 1
	var obj = {
		method: func
	};
	obj.method(2)
	//{method: ƒ}method: ƒ (x)[[Prototype]]: Object 2
```

변수 func에 익명함수를 할당하고 4번째 줄에서 func를 호출 했더니 this로 전역객체 window가 호출되었다.
obj라는 변수에 객체를 할당하고 그 객체에는 method 프로퍼티 앞에서 만든 함수를 할당했다.
obj의 method를 호출했더니 이번에는 this가 obj라고 한다. obj의 method 프로퍼티에 할당한 값과 func변수에 할당한 값은 모두 var func를 참조한다. 즉, 원래의 익명함수는 그대로인데 이를 변수에 담아 호출한 경우와 obj객체의 프로퍼티에 할당해서 호출한 경우에 this가 달라지는 것이다.

그렇다면 함수와 메서드로서의 호출을 구분해보자. 함수 앞에 점이 있는지를 여부만으로 간단하게 구분할 수 있다.
함수앞에 점이 있다면 메서드로 호출, 없으면 함수로 호출
obj.method : 메서드로 호출
func() : 함수로 호출
팩트) 다
메서드로서 호출 - 대괄호 표기법

```
	var obj = {
		method: function(x){
			console.log(this, x)
		}
	};
	obj.method(1);
	obj['method'](2);
```

### 메서드 내부에서 this

this에는 호출한 주체에 대한 정보가 담기게 되고 어떤 함수를 메서드로서 호출하는 경우 호출 주체는 바로 함수명 앞의 객체이다. 점 표기법의 경우 마지막 점 앞에 명시된 객체가 곧 this가 된다.

```
	var obj = {
		methodA: function (){ console.log(this); }
		inner : {
			methodB: function(){ console.log(this}; }
		}
	};
	obj.methodA();
	obj['methodA']();

	obj.inner.methodB();
	obj.inner['methodB']();
	obj['inner'].methodB();
	obj['inner']['methodB']();
```

### 함수로 호출할 때 그 함수 내부에서의 this

- 함수 내부에서의 this
  어떤 함수를 함수로서 호출할 경우에는 this가 지정되지 않는다. this에는 호출한 주체에 대한 정보가 담긴다.
  그런데 함수로서 호출한 것은 호출 주체를 명시하지 않고 개발자가 코드에 직접 관여해서 실행한 것이기 때문에 호출 주체의 정보를 알 수 없는 것이다.실행 컨텍스트를 활성화할 당시에 this가 지정되지 않은 경우 this는 전역객체를 바라본다.따라서 함수에서의 this는 전역 객체를 가리킨다.(설계상의오류라고 지적되는 부분)

- 메서드의 내부 함수의 this
  메서드 내부에서 정의하고 실행한 함수에서의 this는 자바스크립트 초심자들이 this에 관해 가장 자주 혼란을 느낀다. this라는 단어 자체가 주는 느낌적으로 느낌 그대로 코드를 바라보면 예상과 다른 결과가 나온다.
  내부함수 역시 이를 함수로서 호출했는지 메서드로 호출했는지만 파악하면 this의 값을 정확히 예측가능하다.
  예제를 통해 각 console의 위치에서 this가 무엇을 가리킬지 예상하고 정답과 비교해보자

```
	var obj1 = {
		outer: function (){
		 console.log(this); // (1)객체내부의 함수니깐 obj1를 가리키고
		 var innerFunc =function (){
				  console.log(this);
				}
		innerFunc();//(2)함수로 호출되었기에 window

		var obj2 = {
			innerMethod: innerFunc()
			};
			obj2.innerMethod();//(3)메서드로 호출되었기에 obj2
		}
	};
	obj1.outer();//메서드로 사용되었기에 obj1를 가리킬 것 같다.

```

정답은 놀랍게도 전부 정답
코드의 흐름을 보자
객체 obj1를 생성, 이때 객체 내부에는 outer라는 프로퍼티가 있으며 여기에는 익명함수가 연결된다.
이렇게 생성한 객체를 변수 obj1에 할당한다.
obj1.outer 호출하고 obj1.outer함수의 실행 컨텍스트가 생서되고 호이스팅, 스코프체인 정보를 수집하고this를 바인딩한다. 이 함수는 메서드로 호출한 것! 따라서 .this에는 마지막 점 앞의 객체인 obj1을 바인딩한다.
obj1 객체정보를 불러온다,. 호이스팅된 변수 innerFunc에 익명함수를 할당
innerFunc호출한다.
innerFunc함수의 실행 컨텍스트가 생성되면서 호이스팅, 스코프 체인 수집 , this 바인딩을 수행.
이 함수는 함수로써 호출되었음 그렇기에 this가 지정되지 않고 전역객체 window를 바인딩한다.
호이스팅된 변수 obj2에는 객체를 할당하고 , 그 객체 안에는 innerMetod라는 프로퍼티가 존재하기 이에 앞서 정된 변수 innerFunc와 연결된 익명함수가 연결된다.
obj2는 메서드로 호출된 것이기에 객체인 obj2가 바인딩 된다.

- 메서드 내부함수에서의 this를 우회하는 방법
  이렇게 하면 this에 대한 구분을 명확히 하는 것이 가능하지만, 결과 this라는 단어가 주는 인상과는 달라진다.
  호출 주체가 없는 경우 자동으로 전역객체를 바인딩 하지 않고 호출 당시 주변 환경의 this를 그대로 상속받는 것이 자연스러운 것이다.
  변수를 검색하면 우선 가장 가까운 스코프의 LE를 찾고 없으면 상위 스코프를 탐색하듯 말이다.
  그러나 사용자 입장에서는 어새갛거나 설득력 없는 기능들이라 하더라도 그 자체를 언어가 가지는 고유한 특성으로 받아들이고 주어진 환경에 적응할 수 밖에 없다.
  우회할 방법이 있긴하다
  예제를 보자

```
	var obj1 = {
		outer: function (){
		 console.log(this);//outer함수
		 var innerFunc1 =function (){
				  console.log(this);//window객체
				}
		innerFunc1();

		 var self = this;
		 var innerFunc2 =function (){
				  console.log(self);//outer함수
				}
		innerFunc2();
	};
	obj1.outer();
```

그저 상위 스코프의 this를 저장해서 내부함수에서 활용하는 수단이지만 의미에서는 훌륭한 방법이다.

- this를 바인딩하지 않는 함수
  ES6 기준, 함수 내부에서 this가 전역객체를 바라보는 문제를 보완하고자 this를 바인딩 하지 않는 ()=>{} 화살표 함수가 새로 도입되었다. 화살표 함수는 실행 컨텍스트를 생성할 떄 this 바인딩 과정자체가 빠지게 되어 사우이 스코프의 this를 그대로 사용이 가능하다.

```
	var obj = {
		outer: function (){
		 console.log(this);//outer
		 var innerFunc = function=() => {
				  console.log(this);//outer
				}
		innerFunc();
		}
```

그 밖에도 call, apply등의 메서드를 활요해 함수르 호출 할 때 this를 명시적으로 지정하는 방법도 있다.

### 콜백 함수 호출 시 그 함수 내부에서의 this

this가 어떤값을 참조하는지를 보자 콜백함수 자체는 다음에 알아보자
함수 A의 제어권을 다른 함수(또는 메서드) B에게 넘겨주는 경우 함수 A를 콜백 함수라고 한다.
이때 함수A는 함수B의 내부로직에 따라 실행되면 this역시 함수 B내부 로직에서 정한 규칙에 따라 결정된다.
콜백함수도 함수이기에 기본적으로 this가 전역객체를 참조하지만 , 제어권을 받은 함수에서 콜백함수에 별도로 this가 될 대상을 지정한 경우에는 그 대상을 참조한다
대표적인 콜백함수의 예제를 보고 값을 추측해보자

```
	 setTimeout( function (){console.log(this);},300);	(1)  window가 출력될 것 같다.
	[1,2,3,4,5] forEach(function (x){					(2) 1,2,3,4,5 다 window나올것 같다
		console.log(this, x);
	});
	document.body.innerHTML == '<button id="a">클릭</button>'
	document.body.querySelector('#a')
		.addEventListener('click',function(e){ (3) a가 출력될 것같다
		console.log(a);
		}
```

코드흐름
(1) setTiemout함수에서 300ms만큼 시간 지연을 한뒤에 콜백함수를 실행하며 전역객체가 출력된다.
(2)forEach 메서드는 배여르이 각요소를 하나씩 출력하는데 전역객체와 배열의 각 요소를 출력한다.
(3)이벤트 정보를 콜백함수의 첫번째로 인자로 삼아 함수를 실행하라는 명령이기에 버튼을 클릭하면 앞서 지정한 엘리먼트와 클릭 이벤트에 관한 정보가 담긴 객체를 출력한다.

(1), (2)는 그 내부에서 콜백 함수를 호출할 때 대상이 될 this를 지정하지 않았기에 콜백함수 내부에서의 this는 전역객체를 참조한다. 그러나(3)의 경우에는 콜백함수가 호출될때 앞에 자신의 this를 상속하도록 정의되있다. 그렇기에 . 앞부분이 곧 this가 된다.

이처럼 콜백함수에서 this는 무조건 이것이다라고 할 수 있는 것은 아니고, 콜백함수의 제어권을 가지는 함수나 메서드가 콜백함수에서 this를 무엇으로 할지를 결정하며 특별히 정의 하지 않은 경우에는 기본적인 함수와 마찬가지로 전역객체를 바라보게 된다.

### 생성자 함수 내부에서의 this

생성자 함수에는 어떤 공통된 성질을 지니는 객체들을 생성하는데 사용하는 함수이다.
객체지향 언어에서 생성자를 클래스, 클래스르 통해 만든 객체를 인스턴스라고 한다.

자바스크립트는 함수에 생성자로서의 역할을 함께 부여했다. new 명령어와 함께 함수를 호출하면 해당 ㅎ마수가 생성자로서 동작하게 된다.
그리고 어떤 함수가 생성자 함수로서 호출된 경우 내부에서의 this는 곧 새로 만들 구체적인 인스턴스 자신이 된다.
생성자를 함수를 호출하면 우선 생성자의 프로퍼티를 참조하는 _proto_ 라는 프로퍼티가 있는 객체를 만들고 미리 준비된 공통 속성 및 개성을 해당 객체 this에 부여한다. 이렇게 해서 구체적인 인스턴스가 만들어지게 된다.
예제를 통해 정확히 알아보자

```
	var Cat = function (name, age){
		this.bark = '야옹';
		this.name= name;
		this.age= age;
		}
	var choco = new Cat('초코', 7);
	var nabi = new Cat('나비', 5);
	console.log(choco , nabi);
```

Cat이라는 변수에 익명함수를 할당, 이 함수 내부에서는 this에 접근해서 프로퍼티에 각각 값을 대입한다.
new 명령어와 함께 Cat함수를 호출하여 변수를 각각 할당한다.
그리고 출력값을 보니 Cat 클래스의 인스턴스 객체가 출력된다.

## 명시적으로 this를 바인딩하는 방법

상황별 this에 어떤 값이 바인딩 되는지를 살펴보았다. 이러한 규칙을 깨고 this에 별도의 대상을 바인딩 하는 방법도 있다.

### call 메서드

call 메서드는 메서드의 호출 주체인 함수를 즉시 실행하도록 하는 명령이다. 이때 call 메서드의 첫 번째 인자를 this로 바인딩하고, 이후의 인자들을 호출할 함수의 매개변수로 한다. 함수를 그냥 실행할 경우 this는 전역객체를 참조하지만 call 메서드를 이용하면 임의의 객체를 this로 지정할 수 있다.

```
	var func = function(a,b,c){
		console.log(this, a,b,c);
	}
	func(1,2,3);
	func.call({x:1},4,5,6);
```

메서드에 대해서도 마찬가지로 객체의 메서드를 그냥 호출하면 this는 객체를 참조하지만 call메서드를 이용해서 임의의 객체를 this로 지정가능하다.

```
	var obj= {
	a:1,
	method: function(x,y){
		console.log(this, a,x,y);
		}
	}
	obj.method(2,3);
	obj.method.call({a:4},5,6);
```

### apply 메서드

apply 메서드는 call 메서드와 기능적으로 완전히 동일하다
apply 메서드의 차별점은 두번째 인자를 배열로 받아 그배열의 요소들을 호출할 함수의 매개변수로 지정하다는 점이 있다.

```
	var func = function(a,b,c){
		console.log(this, a,b,c);
	}
	func.apply({x:1},4,5,6);

	var obj= {
		a:1,
		method: function(x,y){
			console.log(this, a,x,y);
		}
	}
	obj.method.apply({a:4},5,6);
```

### call과 apply 메서드의 활용

위 둘의 메서드를 활용하여 자스를 더욱 다채롭게 활용가능하다.

```
 var obj = {
	0:'a',
	1:'b',
	2:'c',
	length:3
};
Array.prototype.push.call(obj.'b')'
console.log(obj);

var arr = Array.prototype.slice.call(obj);
console.log(arr);
```

객체에는 배열 메서드를 직접 적용할 수 없다. 그러나 키가 0 또는 양의 정수인 프로퍼티가 존재하고 length프로퍼티의 값이 0또는 양의 정수인 객체, 즉 배열의 구조와 유사한 객체의 경우에는 call, apply메서드를 이용해 배열 메서드를 차용할 수 있다.

함수 내부에서 접근할 수 있는 argument 객체도 유사배열객체이므로 위의 방법으로 배열로 전환해서 활용할 수 있다. querySelectorAll, getElementsByClassName 등의 Node선택자로 선택한 결과인 NodeList도 마찬가지이다.

```
function a(){
		var argv = Array.prototype.slice.call(arguments);
		argv.forEach(function(arg){
			console.log(arg);
		});
	}
	a(1,2,3);

	document.body.innerHTML = '<div>a</div><div>b</div><div>c</div>'
	var nodeList = document.querySelectorAll('div');
	var nodeArr = Array.prototype.slice.call(nodeList);
	nodeArr.forEach(function(node)){
		console.log(node)
	}
```

그 빡에도 유사배열객체에는 call/apply 메서드를 이용해 모든 배열 메서드를 적용할 수 있다.
배열처럼 인덱스와 length프로퍼티를 지니는 문자열에 대해서도 마찬가지이다.
단 문자열의 경우 length프로퍼티가 읽기 전용이기 때문에 원본 문자열에 변경을 가능하는 메서드는 에러를 던지며 concat처럼 대상이 반드시 배열이어야하는 경우에는 에러는 안나지만 원하는 결과를 얻기는 어렵다.

사실 call/apply 메서드를 이용해 형변환하는 것은 this를 원하는 값으로 지정해서 호출한다라는 본래의 메서드의 의도와는 다소 동떨어진 활용법이라 할 수 있다.
그에 ES6에서는 유사배열객체 또는 순회 가능한 모든 종류의 데이터 타입을 배열로 전환하는 Array, from 메서드를 새로 도입했다.

```
	var obj = {
		0:'a',
		1:'b',
		2:'c',
		length:3
	};
	var arr = Array.from(obj);
	console.log(arr);
```

- 생성자 내부에서 다른 생성자를 호출
  생성자 내부에 다른 생성자와 공통된 내용이 있을 경우 call또는 apply를 이용해 다른 생성자를 호출하면 간단하게 반복을 줄일 수있다.
  그에 대한 예제를 확인해보자

```
	function Person(name,gender){
		this.name=name;
		this.gender=gender;
	}

	function Student(name,gender,school){
		Person.call(this,name,gender); //다른 생성자 호출
		this.school=school;
	}

	function  Employee(name,gender,company){
		Person.apply(this,[name,gender]); // 다른 생성자 호출
		this.company=company;
	}
	var by = new Student('보영','female','단국대');
	var jn = new Employee('재난','male','구골');
```

- 여러 인수를 묶어 하나의 배열로 전달 - apply 활용
  여러 개의 인수를 받는 메서드에게 하나의 배열로 인수들을 전달하고 싶을 때 apply 메서드를 사용하면 좋다.
  예시를 보자

```
var  numbers  = [10,20,3,16,45];
var  max  =  Math.max.apply(null,numbers);
var  min  =  Math.min.apply(null,numbers);
console.log(max,min); //45 3
```

ES6에서든 spread 연산자를 활용하여 더욱 간편하게 작성이 가능하다.

```
	const  numbers  = [10,20,3,16,45];
	const  max  =  Math.max(...numbers);
	const  min  =  Math.min(...numbers);
	console.log(max,min);
```

call과 apply메서드는 명시적으로로 별도의 this를 바인딩하면서 함수 또는 메서드를 실행하는 훌륭한 방법이긴 하지만 this의 예측하기 어렵게 만들어 코드 해석을 방해한다라는 단점이있다. ??
하지만 ES5이하의 환경에서는 실무에 매우 광범위하게 활용되고 있다.

### bind 메서드

bind 메서드는 ES5에서 추가된 기능으로 call과 비슷하지만 즉시 호출하지는 안혹 넘겨받은 this 및 인수들을 바탕으로 새로운 함수를 반환하기만 하는 메서드이다.
다시 새로운 함수를 호출할때 인수를 넘기면 기존 bind메서드를 호출할 때 전달햿던 인수들의 뒤에 이어서 등록된다. 즉, bind 메서드는 함수에 this를 미리 적용하는 것과 부분적용함수를 구현하는 두가지 목적을 지닌다.
예제를 통해 확인해보자

```
	var func = function(a,b,c,d){
		console.log(this,a,b,c,d);
	};
	func(1,2,3,4);
	var bindFunc1 = func.bind({x:1});
	bindFunc1(5,6,7,8); // {x:1} 5 6 7 8
	var bindFunc2 = func.bind({x:1},4,5);
	bindFunc2(6,7); //{x:1} 4 5 6 7
	bindFunc2(8,9); //{x:1} 4 5 8 9
```

bindFunc변수에는func에 this를 {x:1}로 지정한 새로운함수가 담기게 된다.
그리고 bindFunc1을 호출하면 원하는 결과를 출력할 수 있다.

- name 프로퍼티
  bind 메서드를 적용해서 새로만든 함수는 한가지 독특한 설정이 있다. 바로 name 프로퍼티에 동사 bind의 수동태인 bound라는 접두어가 붙는다는 점이 있다.
  어떤 함수의 name 프로퍼티 bound xxx라면 이는 곧 함수명이 xxx인 원본 함수에 bind메서드를 적용한 새로운 함수라는 의미가 됨으로 call과 method보다 콛르르 추적하기 수월해진 면이 있다.

```
	var func = function(a,b,c,d){
		console.log(this,a,b,c,d);
	};
	var bindFunc = func.bind({x:1},4,5);
	console.log(func.name); // fun c
	console.log(bindFunc.name); // bound func
```

상위 컨텍스트의 this를 내부함수나 콜백함수에 전달하기
메서드의 내부함수에서 메서드의this를 그대로 바라보게 하기 위한 방법으로 self등의 변수를 통해 우회하는 방법을 사용했는데 이 또한 call, apply 또느 bind 메서드를 이용하면 더 깔끔하게 처리할 수 있다.

```
	//call
	var obj = {
		outer: function (){
		 console.log(this);
		 var innerFunc = function() {
				  console.log(this);
			}
		 innerFunc,call(this);
		};
		obj.outer();
		}
	//bind
	var obj = {
		outer: function (){
		 console.log(this);
		 var innerFunc = function() {
				  console.log(this);
				}.bind(this)
			innerFunc();
		};
		obj.outer();
		}
```

또한 콜백 함수를 인자로 받는 함수나 메서드 중에서 기본적으로 콜백함수 내에서의 this에 관여하는 함수 또는 메서드에 대해서도 bind메서드를 이용하면 this값을 사용자에 맞추어 사용할 수 있다.

### 화살표 함수의 예외 사항

ES6에 새롭게 도입된 화살표 함수는 실행 컨텍스트 생성시 this를 바인딩하는 과정이 제외됐다.
즉, 이 함수 내부에는 this가 아예 없으며, 접근하고자 하면 스코프체인상 가장 가까운 this에 접근하게 된다.

```
var obj = {
		outer: function (){
		 console.log(this);
		 var innerFunc = function() =>{
				  console.log(this);//outer
				};
		innerFunc();
		}
	obj.outer();
```

이렇게 하면 별도의 변수로 this를 우회하거나 다른 메서드들을 적용할 필요가 없어 더욱 간결하고 편리하다.

별도의 인자로 this를 받는 경우(콜백 함수 내에서의 this)
콜백함수를 인자로 받는 메서드 중 일부는 추가로 this로 지정할 객체(thisArg) 를 인자로 지정할 수 있는 경우가 있다.
이러한 메서드의 thisArg값을 지정하면 콜백 함수 내부에서 this 값을 원하는대로 변경할 수 있다.
이런 형태는 여러 내부 요소에 대해 같은 동작을 반복 수행해야하는 배열 메서드에 많이 있어 같은 이유로 ES6에서 등장한 Set, Map등의 메서드에도 일부 존재한다. 배열 메서드 중 하나인 forEach를 살펴보자

```
var report ={
	sum : 0,
	count: 0,
	add: function(){
		var args = Array.prototype.slice.call(arguments);
		argv.forEach(function(entry){
			this.sum += entry;
			++this.count;
		}, this);
	},
		average: function(){
			return this.sum /this.count;
		}
}
report.add(60,80,95)
console.log(report.sum, report.count. report.average());
```
