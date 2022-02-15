# 프로토타입

자바스크립트는 프로토타입 기반 언어이다.
클래스 기반 언어에서 상속을 사용하지만 프로토타입 기반 언어에서는 어떤 객체를 원형으로 삼고 이를 복제함으로써 상속과 비슷한 효과를 얻는다
유명한 프로그래밍언어는 클래스 기반인 것에 비하면 굉장히 독특한 개념이다.

프로토타입은 어려워보이지만 무척 쉬운 개념이다.

프로토타입의 구조

```js
var instance = new Constructor();
```

어떤 생성자 함수를 new 연산자와 함께 호출하면
Constructor에서 정의된 내용을 바탕으로 새로운 인스턴스가 생성된다
이때 instance에는 **proto**라는 프로퍼티가 자동으로 부여되는데
이 프로퍼티는 Constructor의 prototype이라느 프로퍼티를 참조한다

prototype이라는 프로퍼티
** proto ** 프로퍼티가 새로 등장했다
이 둘이 프로토타입 개념의 핵심이다
prototype와 ** proto**는 객체이다
prototype 객체 내부에는 인스턴스가 사용할 메서드를 저장한다
그러면 인스턴스에서도 숨겨진 ** proto**를 통해 이 메서드들에 접근할 수 있게 된다.

하지만 이에 대해서 메서드를 호출하는 과정을 코드로 보면서 이상한 점을 보자

```js
var suzi = new Person("Suzi");
suzi.__proto__.getName(); // undefined
```

이런식으로 undefined가 나오게 된다. 이 문제가 발생하는 이유는 this인데
메서드는 마지막 . 앞에 나오는 객체를 참조하게 되는데
suzi.`__proto__` 객체가this가 되어 name 프로퍼티가 없으므로 undefined가 출력된다.
하지만 여기서 또 하나의 예시를 보면

```js
suzi.getName(); //Suzi
```

위와 같은 출력을 얻는데 메서드의 this 참조에 의해서 instance를 가리키는 것이 맞지만
이게 왜 되는 것이지 라는 의무을 갖게 된다.
이유는 생략가능한 프로퍼티이기 때문이다.

결론적으로 배열 리터럴과 Array의 관계를 예시로
Arr(내장 생성자 함수) = { isArray(), from(), ... , prototype : { forEach(), concat(), ... } }

```js
var arr = [1, 2];
arr.forEach(function () {}); //배열리터럴에서 prototype메서드 자유롭게 사용O
Array.isArray(arr); //내장 생성자 함수 Array에서 isArray사용가능함
arr.isArray(); //X Array가 prototype에 담지 않은 메서드는 생성자에서는 사용불가능
```

### construtctor 프로퍼티

생성자 함수의 프로퍼티인 prototype 객체 내부에는 constructor라는 프로퍼티가 있다.
이에 대한 constructor에 직접 접근하여 변경이 가능하다
number, string, boolean 제외
인스턴스에서 생성자 정보를 알아낼 수 있는 유일한 수단인 constructor이다.

## 프로토타입 체인

만약 인스턴스가 동일한 이름의 프로퍼티나 메서드를 가지고 있다면 무슨일이 발생할까?
이 현상을 메서드 오버라이드라고하고
메서드 위에 메서드를 덮어 씌웠다고 판단할 수 있겠다.

일반적으로 메서드가 오버라이드 된 경우에는 자신으로 부터 가장 가까운 메서드에만 접근할 수 있지만 그 다음으로 가까운 `_proto_`의 메서드도 우회적인 방법을 통해서이지만 접근이 가능하다.

또한 프로토타입또한 객체이기에 객체에 대한 `_proto_`를 지니고있다.
어떤 데이터의 `_proto_` 프로퍼티 내부에서 다시 `_proto_` 프로퍼티가 연쇄적으로 이어진 것을 **프로토타입 체인**이라 하고 이 체인을 따라가며 검색하는 것을 **프로토타입 체이닝**이라고 한다.

## 프로토타입 상속

개발을 할 때 기존의 기능을 확장해야 하는 경우가 생긴다. 자바스크립트에서는 프로토타입 상속을 사용하여 확장을 할 수 있다.

## `[[Prototype]]`

자바스크립트에서는 객체가 `[[Prototype]]`이라는 숨김 프로퍼티를 가진다. 이 값은 `null`이거나 다른 객체에 대한 참조가 되는데, 다른 객체에 대한 참조일 때 참조 대상을 **프로토타입**이라고 부른다.

한 객체에서 프로퍼티를 읽으려는데 해당 프로퍼티가 없다면 자바스크립트는 자동으로 프로토타입에서 프로퍼티를 찾는다.

`[[Prototype]]`은 내부 프로퍼티이고 숨김 프로퍼티이지만 개발자가 값을 설정할 수 있다.

### `__proto__`

`__proto__`는 `[[Prototype]]`용 getter/setter이다. 그래서 `__proto__`를 사용해서 프로토타입 상속을 사용할 수 있다.

```jsx
let animal = {
  eats: true,
};
let rabbit = {
  jumps: true,
};

rabbit.__proto__ = animal; // rabbit의 프로토타입이 animal이 됨

alert(rabbit.eats); // animal를 상속받아서 eats라는 상속 프로퍼티를 쓸 수 있다.
alert(rabbit.jumps);
```

<aside> 💡 근래에는 ES5에서 추가된 `Object.getPrototypeOf`와 `Object.setPrototypeOf`를 더 사용한다.

</aside>

## 쓸 때(write 작업또는 delete 작업)에는 프로토타입을 사용하지 않는다.

풀어서 설명하자면 상속된 데이터 프로퍼티를 변경하는게 되지 않는다는 뜻이다.

```jsx
let animal = {
  eats: true,
  walk() {
    /* rabbit은 이제 이 메서드를 사용하지 않습니다. */
  },
};

let rabbit = {
  __proto__: animal,
};

rabbit.walk = function () {
  alert("토끼가 깡충깡충 뜁니다.");
};

rabbit.walk(); // 토끼가 깡충깡충 뜁니다.
```

`rabbit.walk`을 호출하면 프로토타입에 있는 함수가 호출되지 않고 새로 추가한 메서드가 실행된다.

![](https://pedogunu.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F41df73d4-8b5a-4f3c-9ef9-ee94e86e7c38%2FUntitled.png?table=block&id=edc9e5fa-618c-4ec6-a2c0-08d31b0fde0e&spaceId=3d094cd3-aa49-4ffc-83cc-b5daab6cf8f4&width=2000&userId=&cache=v2)

앞에서 말했듯이 데이터 프로퍼티에만 적용되고, 접근자 프로퍼티를 수정하는 것은 setter 함수를 호출하는 것과 같기 때문에 잘 작동한다.

<aside> 💡 `this`는 프로토타입에 영향을 받지 않는다. 메서드를 객체에서 호출했든 프로토타입에서 호출했든 상관없이 `this`는 언제나 `.` 앞에 있는 객체가 된다. 메서드는 공유되지만 객체의 상태는 공유되지 않는다고 볼 수 있다.

</aside>

### 순회와 상속프로퍼티

`for...in`순회를 할 경우 상속 프로퍼티도 순회 대상에 포함된다. 상속 프로퍼티와 직접 구현된 프로퍼티를 구분하려면 `obj.hasOwnProperty(key)`를 사용하면 된다.

---

# 생성자 함수의 `prototype`프로퍼티

생성자 함수의 `prototype`이 객체라면 `new`연산자는 해당 객체를 사용해 새롭게 생성된 객체의 `[[Prototype]]`을 설정한다(`prototype`값은 객체나 null만 가능하다).

```jsx
let animal = {
  eats: true,
};

function Rabbit(name) {
  this.name = name;
}

Rabbit.prototype = animal;

let rabbit = new Rabbit("White Rabbit"); //  rabbit.__proto__ == animal
alert(rabbit.eats); // true
```

위의 예를 그림으로 그리면 다음과 같다.

![](https://pedogunu.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F1df2ca93-5048-4bb7-ad2f-3cea9f36238a%2FUntitled.png?table=block&id=e7dcb078-522f-49ca-9a7b-61574c5dd2ac&spaceId=3d094cd3-aa49-4ffc-83cc-b5daab6cf8f4&width=2000&userId=&cache=v2)

이때 `prototype`프로퍼티는 `new`연산자와 함수를 같이 호출할 때만 사용된다. 객체를 생성하고 `prototype`을 바꾸고 새로운 객체를 생성하면 처음 생성한 객체는 기존의 `prototype`을 `[[Prototype]]`으로 두고, 새로 생성한 객체는 바꾼 `prototype`을 `[[Prototype]]`으로 둔다.

## prototype 프로퍼티와 constructor 프로퍼티

개발자가 특별히 할당하지 않더라도 모든 함수는 `prototype`프로퍼티를 갖는다. 기본 프로퍼티인 `prototype`의 객체에는 `constructor`프로퍼티 하나만 있는데, 이는 함수 자신을 가리킨다.

```jsx
function Rabbit() {}
// 기본 prototype:
// Rabbit.prototype = { constructor: Rabbit }

let rabbit = new Rabbit(); // {constructor: Rabbit}을 상속받음

alert(rabbit.constructor == Rabbit); // true (프로토타입을 거쳐 접근함)
```

![](https://pedogunu.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F1df2ca93-5048-4bb7-ad2f-3cea9f36238a%2FUntitled.png?table=block&id=e7dcb078-522f-49ca-9a7b-61574c5dd2ac&spaceId=3d094cd3-aa49-4ffc-83cc-b5daab6cf8f4&width=2000&userId=&cache=v2)

자바스크립트는 알맞은 `constructor` 값을 보장하지 않는다. 모든 함수에 기본으로 `prototype` 값이 설정하는 것이 전부이고 `constructor`에 벌어지는 모든 일은 전적으로 개발자에게 달려있다. 즉, `constructor`값을 수정하거나 `prototype`을 `constructor`가 없는 객체로 바꿔도 오류가 발생하지 않는다는 뜻이다.

---

# 네이티브 프로토타입

빈 객체를 생성하고 출력해보면 `"[object Object]"`가 출력된다. 이 문자열을 생성하는 코드는 `Object.prototype`에 구현되어 있다. 새로운 객체를 생성하면 `[[Prototype]]`으로 `Object.prototype`을 참조하기 때문에 빈 객체를 출력하면 `Object.prototype`에 구현되어있는 `toString()`함수를 사용하는 것이다.

![](https://pedogunu.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F163d2bc2-157e-4d54-9780-1b6d6e92f3bb%2FUntitled.png?table=block&id=c475cffc-0330-4d02-b247-31c464b50466&spaceId=3d094cd3-aa49-4ffc-83cc-b5daab6cf8f4&width=2000&userId=&cache=v2)

```jsx
let obj = {};

alert(obj.__proto__ === Object.prototype); // true

alert(obj.toString === obj.__proto__.toString); //true
alert(obj.toString === Object.prototype.toString); //true
```

<aside> 💡 `Object.prototype`의 `[[Prototype]]`은 없다(`null`).

</aside>

## 다른 내장 프로토타입

`Array`, `Date`, `Function`을 비롯한 내장 객체들 역시 프로토타입에 메서드를 저장해 놓는다.

명세서에는 모든 내장 프로토타입 꼭대기에 `Object.prototype`이 있어야 한다고 규정한다.

![](https://pedogunu.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F8181cf17-ee28-43fa-8992-b0560574ab59%2FUntitled.png?table=block&id=51d4acc8-0b57-4283-9cf5-cdfa8b7c845d&spaceId=3d094cd3-aa49-4ffc-83cc-b5daab6cf8f4&width=2000&userId=&cache=v2)

<aside> 💡 중복 메서드가 있을 때는 체인 상에서 가까운 곳에 있는 메서드가 사용된다(ex. 배열에서의 `toString`은 `Array.prototype.toString`이 호출되지 `Object.prototype.toString`이 호출되지 않는다).

</aside>

원시값의 메서드를 실행하면 임시 래퍼 객체의 프로토타입에 메서드를 구현해두고 [보이지 않는 곳에서 래퍼 객체가 만들어지고 메서드를 실행하고 사라진다](https://www.notion.so/JavaScript-Info-fe6ca796fe6543e1841cee1ca105b35b).

### 내장 프로토타입 변경

내장 프로토타입을 수정할 수 있다. 이는 일반적인 경우에는 사용되지 않으며, 보통 폴리필을 만들 때에만 사용된다. 내장 프로토타입은 새로 명세서에 등록된 기능을 사용하고 싶은데 자바스크립트 엔진엔 이 기능이 구현되어있지 않을 때만 변경하는 게 좋다.

---

# 프로토타입 메서드

`__proto__`는 브라우저를 대상으로 개발하고 있다면 구식이기 때문에 더는 사용하지 않는 것이 좋다. 표준에도 관련 내용이 명시되어있다. 대신 다음 메서드를 사용하는 것이 좋다.

- Object.create(proto, [descriptors][)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/create) – `[[Prototype]]`이 `proto`를 참조하는 빈 객체를 만든다. 이때 프로퍼티의 설명자를 추가로 넘길 수 있다(`Object.defineProperties(obj, descriptors)`에서의 `descriptors`와 같다).
- Object.getPrototypeOf(obj) – `obj`의 `[[Prototype]]`을 반환한다.
- Object.setPrototypeOf(obj, proto) – `obj`의 `[[Prototype]]`이 `proto`가 되도록 설정한다.

<aside> 💡 속도가 중요하다면 기존 객체의 `[[Prototype]]`을 바꾸면 안된다. 자바스크립트 엔진이 객체를 생성할 때만 `[[Prototype]]`을 설정하고 그 이후 수정하지 않는 것을 전제를 토대로 최적화되어있기 때문에 `Object.setPrototypeOf`나 `obj.__proto__=`를 써서 프로토타입을 바꾸는 행위는 자제해야 한다.

</aside>

### 아주 단순한 객체

객체는 `__proto__`를 키로 사용할 수 없다. 만약 사용자가 `__proto__`에 객체를 할당한다면 치명적인 버그가 발생할 수 있다. 개발자들은 대개 프로토타입이 중간에 바뀌는 시나리오는 배제한 채 개발을 진행한다. 그래서 버그의 원인을 찾기 힘들어질 수도 있다.

이것에 대한 해결책은 객체대신 `Map`을 사용하는 것이다.

이외에도 `Object.create(null)`을 통해 `[[Prototype]]`이 `null`인 객체를 만들어서 사용하는 방법도 있지만 `Object.prototype`의 메서드를 사용할 수 없기 때문에 불편함을 겪을 수도 있다.
