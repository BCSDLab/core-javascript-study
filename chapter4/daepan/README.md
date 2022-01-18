# 콜백 함수

<h1>Callback 함수</h1>

콜백함수의 정의 : 함수에 파라미터로 들어가는 함수
콜백함수의 용도 : 순차적으로 실행하고 싶을 떄 사용한다.

하나의 예시로 이벤트리스너를 보겠다.

```
<script>

document.querySelector('.button').addEventListener('click',function(){
console.log("Start")
setTimeout(function(){},1000)})

</script>
```

이벤트 리스너를 사용할때 addEventListener를 사용하고 그안에 파라미터로 function()이라는 함수가
들어와 있다. 이것이 콜백함수이다.
중간에 그리고 setTimeout도 콜백함수이다. 1초뒤에 function을 실행하는 의미를 가진
콜백함수이다.
콜백함수를 만든다고 아무 함수에다가 함수를 넣어서 콜백함수를 만드는 것은 아니므로 사용에 주의하자.

콜백함수의 원리

그렇다면 함수 안에 함수를 넣으려면 코드를 어떻게 만들어야할까?

```

<script>
	function first(parmas){
		console.log(1)
		params()
	}

	function second(){
		console.log(2)
	}
	first(second)
</script>
```

일반 스크립트로 작성한다면 함수한에 함수를 파라미터를 넣음으로써 실행되는 두가지 함수를 생성하고,
first함수를 호출하면 first의 두줄이 먼저 실행, 그리고 second가 실행되게 된다.

first의 파라미터 함수를 first안에서 호출해서 console을 확인하면 1과 2가 순차적으로 나올 것이다.

이렇게만 본다면 굳이 이를 콜백함수를 굳이 사용해야 하는가에 대해 의구심이 생기게 된다.

자바스크립트의 경우에는 위에 함수부터 먼저 실행되는 특징을 갖고 있기 때문에 단순히

```

function first(){

console.log(1)

}

function second(){
console.log(2)

}

```

위와 같이 작성해도 문제가 없다.

그럼 어디서 콜백함수를 사용할까?

예를 들어 다른 B라는 사람이 first함수를 사용하고 싶어한다면 first함수를 호출해서 사용할 것이다.

그리고 B는 first가 출력된 이후에 console.log(3)을 출력하고 싶어한다.

이럴 경우에 만약 first의 호출 시간 때문에 3이 먼저 출력되고 first가 뒤늦게 실행되는 경우가 있을

수 있다. 이를 막기 위해 콜백함수로 코드를 작성하는 것이다.

```

first(function(){console.log(3)})

```

이런식으로 말이다
이렇게 하면 코드를 순차적으로 사용할 때 매우 유용하게 사용할 수 있다.

하지만 이러한 콜백에도 단점이 있는데,
예를 들어 DB에서 순차적으로 A,B,C 데이터를 뽑고 싶어한다면 코드를 아마 이렇게 작성할 것이다.

```

db.collection('post').indOne(A,function(){
	db.collection('post').indOne(B,function(){
		db.collection('post').indOne(C,function(){
	})}))

```

이것이 콜백의 단점, 콜백지옥입니다.~니가 선택한 자바스크립트다.~

또한 이의 해결잭으로 Promise와 async/await을 통해 해결할 수 있다.

책에서는 콜백함수를 **다른 코드의 인자로 넘겨주는 함수**라고 한다.
**함수에 파라미터로 들어가는 함수**그 이상 그 이하의 표현은 없는 것 같다.
한마디로 콜백함수는 다른 함수에게 자신의 제어권을 넘겨주는 것과 같다.
콜백함수는 다른 코드에게 인자로 넘겨줌으로써 그 제어권도 함께 위임한 함수이다
콜백함수를 위임받은 코드는 자체적인 내부로직에 의해 이 콜백함수를 적절한 시점에 실행할 것이다.

```
	function c (params){
		parmas();
		console.log(3)
	}
	function a() { console.log(1)}

	c(a);
```

이런식으로 제어권을 넘겨주게되면 함수 c안에서 a는 마음대로 조작가능하므로 이렇게 제어권을 넘겨준다고 볼 수 있겠다.

### 콜백함수의 인자

map메서드는 두개의 인자를 갖는다. 첫번째는 배열에 대한 값, 두번째 인자는 index 이에 대한 구조의 네이밍을 밑에 예시처럼 변경하고 출력한다면

```
var newArr1 = [10,20,30].map(function(currentValue, index){
 console.log(currentValue, index);
 return currentValue + 5;
}
console.log(newArr1);
출력값
// 0 10
// 1 20
// 2 30
// 15 25 35
```

이런 네이밍을 밑과 같이 변경하는 출력값의 변화를 보자

```
var newArr2 = [10,20,30].map(function(index, currentValue){
 console.log(index, currentValue);
 return currentValue + 5;
}
console.log(newArr2);
출력값
//10 0
//20 1
//30 2
//5 6 7
```

콜백함수에 대한 네이밍에 대해서는 큰 상관이 없다. 중요한 것은 순서일뿐.

콜백함수의 제어권을 넘겨받은 코드는 콜백 함수를 호출할 때 인자에 어떤 값을들을 순서대로 넘길 것인지에 대한 제어권을 가진다.

### this

콜백함수도 함수이기에 기본적으로는 this가 전역객체를 참조하게 된다.
하지만 제어권을 받은 함수에서 this를 별도로 지정가능하다고 한다.
별도의 this를 지정하는 방식 및 제어권에 대한 이해를 높이기 위해 map메서드를 직접 구현해보자

```
 Array.prototype.map = function(callback, thisArg){
	 var mappedArr=[];
	 for(var i=0;i<this.length;i++){
		var mappedValue callback.call(thisArg||window, this[i], i, this);
		mappedArr[i] = mappedValue;
	}
	return mappedArr;
 }
```

메서드 구현해서의 핵심은 call/apply 메서드이다. this에는 thisArg값이 있을 경우에는 그 값을, 없는 경우에는 전역객체를 지정하고, 첫 번째 인자에는 메서드의 this가 배열을 가리킬 것이다.
제어권을 넘겨받을 코드에서 call, apply메서드의 첫 번째 인자에 콜백 함수 내부에서의 this가 될 대상을 명시적으로 바인딩하기 때문이다.

## 콜백함수는 함수이다

콜백 함수는 함수입니다. 당연한 소리이지만 의미를 곰곰히 생각해보면
콜백함수는 어떤 객체의 메서드를 전달하더라도 그 메서드를 전달하더라도 그 메서드는 메서드가 아닌 함수로서 호출된다.

아무리 obj1.method로 호출되더다도 메서드를 그대로 전달하는 것이 아닌 obj1.method가 가리키는 함수만 전달하게 된다.

콜백이 함수로써 호출되는 경우에는 별도로 this를 지정하는 인자를 정의하지 않았다면 함수내부에서의 this는 전역객체를 바라본다.

## 콜백 함수 내부의 this에 다른 값 바인딩 하기

별도의 인자로 this를 받는 함수의 경우에는 함수내에 원하는 값을 넣으면 되지만, 그렇지 않은 경우에는 this의 주도권을 넘겨주게 되어 사용자가 임의로 값을 바꿀수 없다. 그래서
전통적으로는 this를 다른 변수에 담아 콜백함수로 활용할 함수에서는 this 대신 그 변수를 사용하게 하고, 이를 클로저로 만드는 방식을 많이 사용한다.

1. var self =this
2. ` var obj1 = {name:'obj1', func: function(){ console.log(obj1.name) }}`
3. 함수의 재활용? 이거는 너무 이상하다
4. bind 메서드를 이용한다.

```
 var obj1 = {
	 name:'obj1',
	 func: function(){ console.log(this.name) }
	 }
	 setTimeout(obj1.func.bind(obj1),1000);
var obj2 = { name;'obj2'};
setTimeout(obj1.func.bind(obj2),1500);
```

## 콜백지옥과 비동기제어

콜백지옥은 콜백함수를 익명 함수로 전달하는 과정이 반복되어 코드의 들여쓰기의 수준이 감당하기 힘들 정도로 깊어지는 현상으로, 자바스크립트에서 흔히 발생하는 문제 이다
주로 이벤트 처리나 서버 통신과 같이 비동기 작업을 수행하기 위해 이런 형태가 자주 등장했지만 코드를 수정하기도 어렵다
비동기는 동기 반대이다
동기적인 코드는 현재 실행중인 코드가 완료된 후에야 다음 코드를 실행하는 방식이다.
반대로 비동기적인 코드는 현재 실행 중인 코드의 완료 여부와 무관하게 즉시 다음 코드로 넘어간다.
CPI의 계산에 의해 즉시 처리가 가능한 대부분의 코드는 동기적으로 이루어진다
계산식이 복잡해서 CPU가 계산하는데 시간이 많이 필요한 경우라 하더라도 이는 동기적인 코드이다.
반면 사용자의 요청에 의해 특정시간이 경과되기전까지 어떤 함수의 실행을 보류한다거나,
사용자의 직접적인 개입이 있을 때 비로소 어떤 함수를 실행하도록 대기하거나
웹브라우저 자체가 아닌 별도의 대상에 무언가를 요청하고 그에 대한 응답이 왔을 때 비로소 어떤 함수를 실행하도록 대기하게하는 듯이 별도의 요청, 실행 대기, 보류 등과 관련된 코드는 비동기적인 코드이다
그렇지만 현대의 자바스크립트의 웹 복잡도가 높아진 만큼 비동기적인 코드의 비중이 예전보다 높아졌다
그와 동시에 콜백지옥에 빠지기도 쉬워졌다

그렇지만 일회성 함수를 전부 변수에 할당하는 것이 좋은 방법은 아니다.
코드명을 일일이 따라다녀야 하므로 오히려 헷갈리 소지가 있다
지난 십수년간 자바스크립트 진영은 비동기적인 일련의 작업을 동기적으로, 혹은 동기적인 것처럼 보이게 끔 처리해주는 장치를 마련하고자 노력했고 ES6에서는 promise와 generator가 도입되었고 ES2017에서는 async/await이 도입되었다.

```js
	//callback

function getData(callback) {
  // new Promise() 추가
  return new Promise(function(resolve, reject) {
    $.get('url 주소/products/1', function(response) {
      // 데이터를 받으면 resolve() 호출
      resolve(response);
    });
  });
}

// getData()의 실행이 끝나면 호출되는 then()
getData().then(function(tableData) {
  // resolve()의 결과 값이 여기로 전달됨
  console.log(tableData); // $.get()의 reponse 값이 tableData에 전달됨
});



	//Promise
	function getData(callback) {
	  // new Promise() 추가
	  return new Promise(function(resolve, reject) {
	    $.get('url 주소/products/1', function(response) {
	      // 데이터를 받으면 resolve() 호출
	      resolve(response);
	    });
	  });
	}

	// getData()의 실행이 끝나면 호출되는 then()
	getData().then(function(tableData) {
	  // resolve()의 결과 값이 여기로 전달됨
	  console.log(tableData); // $.get()의 reponse 값이 tableData에 전달됨
	});


	//Generator
	export  const  createRequestSagaActionTypes  = (type) => {
	const SUCCESS = `${type}_SUCCESS`;
	const FAILURE  = `${type}_FAILURE`;
	return [type, SUCCESS, FAILURE]
};

  //Generator
export default function createRequestSaga(type, request) {
	const SUCCESS = `${type}_SUCCESS`;
	const FAILURE = `${type}_FAILURE`;
	return  function* (action) {
		yield  put(startLoading(type));
		try {
			const  response  =  yield  call(request, action.payload);
			yield  put({
				type:  SUCCESS,
				payload:  response.data,
			});
		} catch (error) {
			yield  put({
				type:  FAILURE,
				payload:  error.response.data,
		});
	}
	yield  put(finishLoading(type));
};
	//Promise + async/await
	async function fetchAuthorName(postId) {
	  const postResponse = await fetch(
	    `https://jsonplaceholder.typicode.com/posts/${postId}`
	  );
	  const post = await postResponse.json();
	  const userId = post.userId;
	  const userResponse = await fetch(
	    `https://jsonplaceholder.typicode.com/users/${userId}`
	  );
	  const user = await userResponse.json();
	  return user.name;
	}

	fetchAuthorName(1).then((name) => console.log("name:", name));


```

정리
콜백함수는 다른 코드에 인자로 넘겨줌으로써 그 제어권도 함께 위임한 함수이다.
제어권을 넘겨 받은 코드는 다음과 같은 제어권을 가진다

- 콜백 함수를 호출할 때는 인자로 넘겨줄 값들 및 그 순서가 정해져있다.
- 콜백 함수를 호출할 때는 인자로 넘겨줄 값들 및 그순서가 정해져 있다.
- 콜백 함수의 this가 무엇을 바라보도록 할지가 정해져 있는 경우도 있다. 정하지 않은 경우에는 전역객체를 바라본다 사용자 임의로 this를 바꾸고 싶을 경우 bind메서드를 활용하면 된다.
- 어떤 함수에 인자로 메서드를 전달하더라도 결국은 함수로써 실행한다
- 비동기 제어를 위해 콜백 함수를 사용하다 보면 콜백지옥에 빠지기 쉽다.
- 최근의 ECMAScript에는 promise, generator, async/await등 콜백 지옥에서 벗어날 수 있는 방법이 있다.
