# 클로저

- 클로저의 의미 및 원리 이해
  클로저는 여러 함수형 프로그래밍 언어에서 등장하는 보편적인 특성이다.
  자바스크립트 고유의 개념이 아니라서 ECMAScript명세에서도 클로저의 정의를 다루지 않고
  다양한 문헌에서 제각각의 정의로 클로저를 설명한다
  더구나 클로저를 설명하는 문장 자체도 이해하기 어려운 문장이 많다.

MDN에서는 클로저에 대해 클로저는 **함수와 그 함수가 선언될 당시는 lexical Enviroment의 상호관계에 따른 현상**
선언될 당시의 lexical enviroment는 실행컨텍스트의 구성요소 중 하나인 outerEnviromentReference에 해당한다.
LE의 enviromentRecord와 outerEnviromentReference에 의해 변수의 유효 범위인 스코프가 결정되고 스코프 체인이 가능해진다. 어떤 컨텍스트 A에서 선언한 내부함수 B의 실행 컨텍스트가 활성화된 시점에는 B의 outerEnviromentReference가 참조하는 대상인 A의 LexicalEnviroment에도 접근이 가능하다

A에서 B에서 선언한 변수를 접근할 수는 없지만 B에서 A에서 선언한 변수에 접근이 가능하다
여기서 combination의 의미를 알 수 있다
내부함수는 A의 Lexical Enviroment를 언제나 사용하는 것은 아니다. 내부 함수에서 외부 변수를 참조하지 않는 경우라는 combination이라고 하지 않고 내부함수에서 외부 변수를 참조하는 경우에 한해서만 combination. 즉 선언될 당시의 LexicalEnviroment와의 상호 관계가 의미가 있을 것이다.

**클로저란 어떤 함수 A에서 선언한 변수 a를 참조하는 내부함수 B를 외부로 전달할 경우 A의 실행컨텍스트가 종료된 이후에도 변수 a가 사라지지 않는 현상**

클로저를 함수라고 표현하는 것은 다소 아쉬운 표현이다. 클로저란 하나의 현상을 의미한다.

## 클로저와 메모리 관리

클로저에 대해서 메모리 누수 위험에 대해서 클로저 사용을 조심해야한다거나 지양해야한다는 이론이 있다.
여기서 누수란?
개발자의 의도와 달리 어떤 값의 참조 카운트가 0이 되지 않아 GC의 수집대상이 되지 않음을 의미한다.
하지만 개발자의 의도적으로 참조카운트가 0이 되지 않도록 설계한 경우를 누수라고 표현하는 것은 적절치 않다.
이제는 의도대로 설계한 메모리 소모에 대한 관리법만 잘 파악해서 적용하는 것으로 충분하다
관리 방법은 클로저는 어떤 의도에 의해 의도적으로 함수의 지역변수를 메모리를 소모하도록 함으로써 발생한다.
그렇다면 그 필요성이 사라진 시점에는 더 메모리를 소모하지 않게 해주면 된다.
참조 카운트를 0으로 만들면 언젠가 GC가 수거해 갈 것이고 이때 소모됐던 메모리가 회수된다.
클로저에서 참조카운트를 0으로 만든 방법은 식별자에 참조형이 아닌 기본형 데이터를 할당하면 된다.

## 클로저의 활용사례

1. 콜백함수 내부에서 외부데이터를 사용하고자 할 때

   - 콜백 함수를 내부에서 외부변수를 직접 참조하는 방법

   ```js
   var fruits = ["apple", "banana", "peach"];
   var $ul = document.createElement("ul");

   fruits.forEach(function (fruit) {
     var $li = document.createElement("li");
     $li.innerText = fruit;
     $li.addEventListener("click", function () {
       alert("your choice is" + fruit);
     });
     $ul.appendChild($li);
   });
   document.body.appendChild($ul);
   ```

   forEach메서드에 넘겨준 익명의 콜백함수 내부에서 외부변수를 사용하지 않으므로 클로저가 없다.
   addEventListner에 넘겨준 콜백함수 B에서는 fruit이라는 외부변수를 참조하여 클로저가 있다.
   forEach메서드의 익명의 콜백함수는 fruits의 갯수만큼 실행되며 실행 횟수만큼 새로운 실행 컨텍스트가 활성화 될것이다. 그리고 이에 대한 실행 종료와 무관하게 클릭이벤트에 의해 각 컨텍스트의 addEventListener가 실행될 때마다 outerEnviromentReference가 forEach의 Lexical Enviroment를 참조하게 된다.
   최소한 addEventListener 콜백함수가 참조할 예정인 변수 fruit에 대해서는 forEach가 종료된 이후에도 GC대상에서 제외되어 계속 참조가 가능할 것이다.

   - bind 메서드로 값을 직접 넘겨 클로저는 발생하지 않는 방법

   ```js
   var fruits = ["apple", "banana", "peach"];
   var $ul = document.createElement("ul");

   var alertFruit = function (fruit) {
     alert("당신이 선택한 것은" + fruit);
   };
   fruits.forEach(function (fruit) {
     var $li = document.createElement("li");
     $li.innerText = fruit;
     $li.addEventListener("click", alertFruit.bind(null, fruit));
     $ul.appendChild($li);
   });
   document.body.appendChild($ul);
   ```

   이렇게 한다면 이벤트 객체가 인자로 넘어오는 순서가 바뀌는 점 및 함수 내부의 this가 원래의 것과 달라지는 점을 감안해야한다.

   - 콜백함수를 고차함수로 바꿔 클로저를 사용하는 방법

   ```js
   var fruits = ["apple", "banana", "peach"];
   var $ul = document.createElement("ul");

   var alertFruitBuilder = function (fruit) {
     return function () {
       alert("당신이 선택한 것은" + fruit);
     };
   };
   fruits.forEach(function (fruit) {
     var $li = document.createElement("li");
     $li.innerText = fruit;
     $li.addEventListener("click", alertFruitBuilder(fruit));
     $ul.appendChild($li);
   });
   document.body.appendChild($ul);
   ```

   여기서 고차함수란? =>함수를 인자로 받거나 함수를 리턴하는 함수
   함수를 alertFruitBuiler라는 새로운 함수를 작성했다. 이 함수 내부에서는 다시 기존에 alertFruit함수를 반환하게 된다.
   이렇게 반환된 함수를 리스너에 콜백함수로써 전달할 것이다.
   이후 언젠가 클릭이벤트가 발생하면 비로서 이함수의 실행컨텍스트가 열리면서 alertFruitBuilder의 인자로 넘어온 fruit를 outerEnviromentReference에 의해 참조할 수 있을 것이다.
   즉 , alertFruitBuilder의 실행결과로 반환된 함수에느 클로저가 존재한다.

2. 접근 권한 제어
   - 정보은닉: 어떤 모듈의 내부로직에 대해 외부의 노출을 최소화해서 모듈간의 결합도를 낮추고 유연성을 높이고자 하는 프로그래밍 언어의 중요 개념
     클로저를 이용하면 public한 값과 private한 값을 구분하는 것이 가능하다.
   ```js
   var createCar = function () {
     var fuel = Math.ceil(Math.random() * 10 + 10);
     var power = Math.ceil(Math.random() * 3 + 2);
     var moved = 0;
     return {
       get moved() {
         return moved;
       },
       run: function () {
         var km = Math.ceil(Math.random() * 6);
         var wasteFuel = km / power;
         if (fuel < wasteFul) {
           console.log("이동불가");
           return;
         }
         fuel -= wasteFuel;
         moved += km;
         console.log(km + "km 이동 (총 " + moved + "km). 남은 연료: " + fuel);
       },
     };
   };
   var car = createCar();
   ```

createCar라는 함수ㄹ를 실행함으로 써 객체를 생성하게 한다면 fuel, power 변수는 비공개 멤버로 지정해 외부에서의 접근을 제한했고 moved변수는 getter만을 부여함으로서 읽기 전용속성을 부여하였다.
이제 외부에서는 오직 run 메서드를 실행하는 것과 현재의 moved값을 확인하는 두가지 동작만 할 수 있게 된다.

### 클로저를 활용한 접근권한 제어하는 방법

- 함수에서 지역변수 및 내부함수등을 생성한다.
- 외부에 접근권한을 주고자 하는 대상들로 구성된 참조형 데이터(대상이 여럿일 때는 객체 또는 배열, 하나일 때는 함수)를 return한다.
- retrun한 변수들은 공개 멤버가 되고 그렇지 않은 변수들은 비공개 멤버가 된다.

3. 부분적용함수
   부분적용함수란 n개의 인자를 받는 함수에 미리 mr개의 인자만 넘겨 기억시켰다가 나중에 n-m개의 인자로 넘기면 원래 함수의 실행 결과를 얻을 수 있게끔 하는 함수이다. this를 바인딩해야 하는 점을 제외하면 bind 메서드의 실행 결과가 바로 부분적용함수이다.
   실무에서 부분함수를 사용하기에 적합한 예로 디바운스가 있다.
   디바운스는 짧은 시간 동안 동일한 이벤트에 대해 한번만 처리하는 것으로 프런트엔드 성능 최적화에 큰 도움을 주는 기능이다. ex) scroll, wheel, mouseMove, resize 등에 적용하기 좋다.
   최소한의 기능에 대한 구현은 생각보다 간단하다.

```js
var debounce = function (eventName, func, wait) {
  var timeoutId = null;
  return function (event) {
    var self = this;
    console.log(eventName, "event발생");
    clearTimeout(timeoutId);
    timeoutId = setTimeout(func.bind(self, event), wait);
  };
};
var moveHandler = function (e) {
  console.log("move이벤트");
};
var wheelHandler = function (e) {
  console.log("whell이벤트");
};
document.body.addEventListener("mousemove", debounce("move", moveHandler, 500));
document.body.addEventListener(
  "mousewhell",
  debounce("wheel", wheelHandler, 700)
);
```

각 이벤트가 바로 이전 이벤트로부터 wait시간 이내에 발생하는 한 마지막에 발생한 이벤트만이 초기화되지 않고무사히 실행될 것이다. 4. 커링함수  
커링함수란 여러개의 인자를 받는 함수를 하나의 인자만 받는 함수로 나눠서 순차적으로 호출될 수 있게 체인형태를 구성한 것을 말한다.

```js
//ES5
	var curry5 = function(func){
		return function(a){
			return function(b){
				return ~~
			}
		}
	}
//ES6
var curry =func=>a=>b=>c=>d=>e=>func(a,b,c,d,e);
```

이 커링함수가 유용한 경우가 있는데 당장 필요한 정보만 받아서 전달하고 또 필요한 정보가 들어오면 전달하는 식으로 하면 결국 마지막 인자가 넘어갈 때 까지 함수 실행을 미루는 셈이다.
이를 함수형 프로그래밍에서는 지연 실행이라고 칭한다.
보통 REST API를 이용할 경우에는baseUrl은 몇개로 고정되지만 path나 id값은 매우 많을 수 있다.
이런 상황에서 서버에 정보를 요청할 필요가 있을 떄마다 baseUrl부터 전부 기입하기 보다는 특정한 값만으로 서버 요청을 수행하는 함수를 만드는 편이 개발 효율성이나 가독성이 더 좋을 것이다.

리덕스 예시

```js
	const logger = store =>next=>action=>{
		console.log('dispatching',action);
		console.log('next state', store,getState());
		return next(action);
	}
	const thunk = store => next =>action{
		return typeof action === 'function'
			?action(dispatch, store.getState)
			:next(action);
	}
```

위 두 미들우에어는 공통적으로 store, next, action순서로 인자를 받는다 이 중 store는 프로젝트 내에서 한번 생성된 이후로는 바뀌지 않는 속성이고 dispatch의 의미를 가지는 next 역시 마찬가지지만 action의 경우는 매번 달라진다.
그러기에 store와 next값이 결정되면 redux 내부에서 logger또는 thunk에 store, next를 미리 넘겨서 반환된 함수를 저장시켜 놓고 이후에 action만 받아서 처리할 수 있게끔 해둔 것이다.

### 정리

클로저란 어떤 함수에서 선언하 변수를 참조하는 내부함수를 외부로 전달할 경우 함수의 실행 컨텍스트가 종료된 후에도 해당 변수가 사라지지 않는 하나의 현상이다

- 외부 변수를 기억하고 이 외부 변수에 접근할 수 있는 기능
- 자바스크립트에서는 모든 함수가 클로저를 사용할 수 있다!
- 클로저는 그 본질이 메모리를 계속 차지하는 개념 이므로 더는 사용하지 않게 된 클로저에 대해서는 메모리를 차지하지 않도록 관리해줄 필요가 있다.
