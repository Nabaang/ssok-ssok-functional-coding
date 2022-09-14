<h1>Chapter 10 일급 함수 I</h1>

### **공감가는 내용**

- 함수에서 반복되는 행위를 콜백 함수로 추상화하는 리팩터링

  ```jsx
  const _tryCatch = ...args => (onTrue, onFalse) => {
  	try{
  		onTrue.apply(null, args);
  	} error(error){
  		onFalse(error);
  	}
  }
  ```

- 반복되는 함수를 하나의 함수로 추상화하는 리팩터링.

  - 함수가 역할은 동일한데 인자에 의존적인 경우 ex) `setPriceByName` => `setFieldByName` (setFieldByName을 한번 더 추상화 하고 싶긴 하다.)

- (데이터 지향) 데이터를 그대로 사용하는 것이 여러가지 방법으로 해석이 되고 제한이 없음.

  - 반면에 제한된 API로 정의하면 데이터를 제대로 활용할 수 없고, 미래에 어떤 방법으로 해석될지 모르기 때문에 호출 그래프에서 하위 계층에서는 데이터를 그대로 사용하는 것이 유지보수 측면에서 더 유리할 것 같다는 생각이 듬.
  - 제한된 데이터? : 타입
    - 재욱 : 동의!!

  ```jsx
  class PersonDto{
  		private string id
  		private string name
  		private string address
  		private void addFriend(){
  		}
  }
  ```

- 필드명을 문자열로 사용할 경우에 문자열의 오타로 인해 생기는 버그가 우려됨. 이러한 점에서 타입이 일관성을 보장해주는 정적 타입 언어가 유리함. BUT 통신 과정에 있는 것은 모두 문자열이고, 데이터 형식이 있다해도 바이트일 뿐, API 역시 받은 데이터를 런타임에 체크를 해야 하고, 많은 비지니스 시스템을 동적 타입으로 운영이 잘되고 있음.

### **토론해보고 싶은 내용**

- 왜 일급이 아닌 값을 일급 함수로 표현하려고 할까요?

  - 추상화 수준을 **값이 아닌 로직**으로 한단계 높인다.
  - 고차함수

- 책에서도 이거 고민할 시간에 숙면하는 게 낫다고 했지만, 동적 언어 vs 정적 언어 둘을 비교하기보다는 어떤 장, 단점이 있는지?

- 인라인 함수 (개발 편의성 VS 성능 저하)

  - 인라인 함수는 지역 변수로 선언한 함수이다.
  - 일반 함수: 컴파일 타임에 생성
  - 인라인 함수: 런타임에 생성된다.

  ```jsx
  // stack + code memory
  function inlineFunction1(){ }
  
  function makeArray(size){
  	// stack + heap
  	const fn = inlineFunction2(){
    }
  }
  
  // call stack
  makeArray()
  ```

  - 성능 최적화는 언제든지 할 수 있다. (미리 최적화 금지)

### **신기했던 내용**

- 함수 인자로 넘겨주는 값이 문자열이었는데, 값으로 사용되는 문자열은 큰따옴표를 쓰고 키로 사용되는 문자열은 작은따옴표를 사용
  - JS에서는 ‘와 ‘’을 모두 사용할 수 있음.
  - 책에서는 ‘와 ‘’을 역할을 분리해서 사용하는 방법을 제시.