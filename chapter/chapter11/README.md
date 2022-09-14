# CHAPTER 11 일급 함수 II

### 공감가는 내용

- 반복되는 부분과 반복되지 않는 부분을 구분 후, 커링을 통해 HOC로 변환하는 작업

```jsx
const withTryCatch = (callback, onError) => (...args) => {
  try{
     callback.apply(null, args);
  } catch(error){
     onError(error);
  }
}
```

- 과도하게 함수의 로직을 고차함수로 추상화하면 오히려 코드의 가독성을 떨어트릴 수 있다.
- 일급 함수, 고차 함수라는 개념은 강력한 기능이지만 항상 직관적인 방법(일반적인 방법을 의미하는 것 같음)과 비교를 해야 한다. 가독성이 증가했는지? 코드 중복을 얼마나 없앴는지? 코드가 하는 일을 쉽게 알 수 있는지?…

### **공감 안가는 내용**

- 리턴값인 함수를 변수로 사용할 경우 function 키워드로 정의 할때와 헷갈릴 수 있지만 함수명을 동사형 그리고 변수명은 명사를 사용해서 구분 할 수 있다? 그렇지 않은 경우도 있지 않나?

```javascript
isOpen = 불이 켜져 있는지 확인하는 플래그 변수
hasItem = 아이템을 가지고 있는지

// 정적검사(타입)
enum FLAG{
	state1
	state2
}
```

- 함수명과 변수명에 대한 컨벤션을 지정하지 않는다. (enum 값은 파스칼 케이스로 지정하는 정도)
- 현구: 보통 불리언 값은 enum 값이나 dto의 속성 값으로 가지고 있음.
- 정적 타입에 비해 동적 타입에서는 함수 및 변수를 구분하는데 있어서 어려움이 있을 수 있음.

### **토론해 볼 내용**

- 이번 장이랑 연관 없는 내용이지만,, 에러 처리에 대해 나와서 ㅎㅎ;;어떤 요청에 대해 에러가 발생하면 catch로 에러에 대한 처리를 하고 요청을 계속해서 처리해야 할까? 아니면 에러가 발생했으니 거기서 요청을 중단해야 할까?
    - 프론트의 경우, fetch를 취소시킬 수 있음..
        - MDN : https://developer.mozilla.org/ko/docs/Web/API/AbortController
        - blog : https://blog.outsider.ne.kr/1602
    - 호준: 실시간 데이터일 경우 모든 데이터를 보장할 필요 없을때 (일부 데이터가 소실되어도 괜찮은 경우)
- 마지막 부분에서 withLogging() 함수에서 모든 코드를 계속 감싸줘야 했었음.이를 익명함수를 리턴하는 wrapLogging()으로 개선을 했는데, 중복이 없어 진게 맞는건지...

```jsx
const withLogErrr = (f) => {
	try{
		f();
	} catch{
		logTosnaperror();
	}
}

function wrappLoggin(f){
	return function(arg){
		try{
			f(arg);
		}catch(error){
			logTosnappError(error);
		}
	}
}

// 선언하는 시점에 savename에 들어갈 arguments를 미리 정의해야함..
1. withLogErorr(() => { savename('name') });

// 선언하는 시점에 굳이 arguments 뭐가 와도 상관 없다??..
2. wrapLogging(callback)

const saveNameWithLogging = wrapLoggin(function() { savename("name") })
saveNameWithLogging(args)

const wrapLogging = callback => (...args) => {
	try{
		callback.apply(null, args);
	} catch{
		erro()
	}
}
```

- 호출 할때 마다 withLogError()를 호출하는 것이 아니라 wrapLoging으로 예외를 감싸 변수로 재사용할 수 있음.
- 커링으로 선언 시 argument로 지정할 수 있음.
    - 커링 사용 X ⇒ argument 고정
    - 커링 사용 O ⇒ argument 동적
