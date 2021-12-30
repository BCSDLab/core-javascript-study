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
