# CHAPTER 13 함수형 도구 체이닝

### 공감 가는 내용

- 프로그래밍 관점에서 항등 함수(자기 자신을 반환)라는 개념이 재미있었다.
- 절차 지향적 프로그래밍보다 함수형 프로그래밍으로 체이닝 하는 게 더 가독성이 좋다. (337p)
  - 파이프라인처럼 이전 단계가 끝나고 다음 단계가 시작한다.(전 단계를 생각하지 않아도 됨)
- 절차 지향 구문을 함수형 구문의 계산으로 분리하는 방법 (ex) 반복문 => filter)

  ```tsx
  // 자바스크립트는 결국은 절차지향으로 포팅 됨
  function range(s = 0, e, step = 1) {
    while (s < e) {
      yield s;
      s += step;
    }
  }
  ```

- 여러 함수형 계산을 하나의 체인으로 묶어 가독성을 높이는 행위
- 체이닝을 통해 과감하게 배열 전체를 다루면서 여러 단계를 파이프라인으로 사용하는 것이 더 명확하고 읽기 쉬움.  
  (병목이 발생하면 stream fusion~!)
- java의 람다나 스트림 API 를 단순히 코드가 간결해진다. stream 는 이유로 많이 사용을 했었는데, 데이터 불변성을 위해 데이터가 변경되지 않는다는 점과, 그로인해 발생하는 오버헤드를 생각해볼 수 있었음.

### 토론 하고 싶은 내용

- 고차 함수 전체를 함수로 vs 고차 함수에서 사용하는 콜백을 함수로 (326p)

  ```tsx
  // 1
  function getBiggestPurchases(customers) {
    return customers.map(getBiggestPurchase);
  }

  // 2
  bestCustomers.map(getBiggestPurchase);
  ```

  - 현구: 가독성은 1번이 더 높아보이는데 고차함수가 익숙하다면 2번으로도 충분히 이해가 빠른 것 같다.
  - 재욱: 1번이 함수로 감싸서 테스트 해야 하는 단위가 아니라면 굳이 감싸야 할 필요가 없다.
  - 호준: 콜백 함수를 재사용할 수 있고, 고차함수 자체로도 의미가 있기 때문에 가독성이 좋지 않을까

- 인라인 함수를 별도의 함수로 뺄지 말지?

  ```tsx
  // 1. 인라인
  const bests = bestCustomers.map(item => item.price > 100)

  // 2. 인라인 함수를 별도로 빼는 지
  const isBestCustomer = item => item.price > 100;
  const bests = bestCustomers.map(isBestCustomer);

  function outer(){
  	const isXXFoo = () => {}
  	const isXXBar = () => {}
  	const isXXBaz = () => {]
  	//..

  	return pipe(
  		foo(isXXFoo),
  		bar(isXXBar),
  		baz(isXXBaz),
  	)
  }

  // 1
  pipe(
  	foo(isXXFoo)
  	bar(isXXBar)
  	baz(isXXBaz)
  )

  // 2
  pipe(
  	foo(x => x.customer.price > 100)
  	bar(x => x.xxx < 100)
  )
  ```

- 추상화 단계를 하나 더 두는게 계층을 나누는게 좋을 것 같음.
- 책을 읽을수록 타입스크립트를 사용하고 싶네요.. (타입을 원해!!)

  ```tsx
  // p.341
  type Products = {
    name: string;
    quantity: number;
  };

  const products: Products[] = [
    {name: 'shirt', quantity: 10},
    {name: 'pen', quantity: 20},
    {name: 'note', quantity: 30},
    {name: 'bag', quantity: 40},
  ];

  const pluck = <T extends object, K extends keyof T>(array: T[], field: K) =>
    array.map((element) => element[field]);
  const productQuantites = pluck(products, 'quantity');
  ```

- `Event Sourcing` 장바구니에 제품 추가 및 제거 기능에 적용한다면??
  - 이벤트가 있을 때 마다 전체 시퀀스를 돌아야하나??
  - p345, 장바구니를 reduce를 통해서 item append하는 예제
  - 사용자의 이벤트(변경 사항)들을 가지고 현재 상태를 결과 값으로 리턴해주는 이벤트소싱 패턴인 것 같음.
  - 해당 로그를 통해 과거 상태로 rollback 할 수 있음.
  - [이벤트 소싱 event-sourcing 패턴 JavaScript로 구현하기](https://edykim.com/ko/post/event-sourcing-implementing-eventsourcing-patterns-with-javascript/)
  - [https://github.com/jaceshim/springcamp2017/blob/master/springcamp2017_implementing_es_cqrs.pdf](https://github.com/jaceshim/springcamp2017/blob/master/springcamp2017_implementing_es_cqrs.pdf)
