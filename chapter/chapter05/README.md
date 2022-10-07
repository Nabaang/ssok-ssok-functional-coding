# ch5 - 더 좋은 액션 만들기

## 비즈니스 요구 사항에 맞춰 더 나은 추상화 단계를 선택하기

**[before]**

```tsx
// 비즈니스 로직: 무료 상품이 가능한지 여부를 판단하는 것
// 비즈니스 로직에 맞지 않는 불필요한 인자 (전체 합계, 아이템의 가격)
function getsFreeShipping(total, itemPrice) {
  return itemPrice + total >= 20;
}
```

**[after]**

```tsx
// 1. 중복되는 로직을 calcTotal 함수로 제거
// 2. 함수 인자를 전자상거래에서 많이 쓰이는 엔티티인 cart로 대체함으로써 비즈니스 로직에 가까워졌다고 할 수 있음
function getsFreeShipping(cart) {
  return calcTotal(cart) >= 20;
}
```

## 암묵적인 입력과 출력은 적을수록 좋다

- **계산: 암묵적인 입력과 출력이 없는 함수**

⇒ 어떤 함수에 암묵적인 입력과 출력이 있다는 것은 다른 컴포넌트와 강하게 결부되어 있다는 의미로 “`모듈화된 컴포넌트`”라고 볼 수 없다

- 암묵적인 입력과 출력이 많을수록 side effect가 많이 발생할 수 있기 때문에 테스트에 용이하지 않다

## 암묵적 입력과 출력 줄이기

기존에 전역변수로 쓰던 아이를 함수 인자로 받도록 만듦으로써 코드 상에 내재되어 있던 암묵적인 입출력을 없앨 수 있다

```tsx
// before - 전역변수인 shoppingCart를 참조해서 함수 내에 사용하고 있음
function calcCartTotal() {
  shoppingCartTotal = calcTotal(shoppingCart);
  setCartTotalDom();
  updateShoppingIcons(shoppingCart);
  updateTaxDom();
}

// after - cart를 함수 인자로 받아서 사용함으로써 함수 내에서 전역변수인 shoppingCart를 참조하고 있지 않음
function calcCartTotal(cart) {
  const total = calcTotal(cart);
  setCartTotalDom(total);
  updateShoppingIcons(cart);
  updateTaxDom(total);
}
```

## 의미있는 계층으로 계산 분류하기

<aside>
💡 **C - 장바구니 구조**
**I  - 제품 구조**
**B - 비즈니스 규칙에 대한 함수**

</aside>

```tsx
// **C - 장바구니가 어떻게 구성되어 있는지에 대한 정보 필요
// I - 각 제품은 어떻게 구성되어 있는지에 대한 정보 필요**
function addItem(cart, name, price) {
	const newCart = cart.slice();
	newCart.push({
		name,
		price
	});
	return newCart;
}

**// C, I**
**// B - 합계를 결정하는 비즈니스 로직도 필요**
function calcTotal(cart) {
	let total = 0;
	for (let i = 0; i < cart.length; i++) {
		total = cart[i].price;
	}
	return total;
}

... 중략
```

> **함수를 사용하면 인자로 넘기는 값 & 그 값을 사용하는 방법으로 관심사를 자연스럽게 분리 가능하다**

## addItem 함수를 분리해서 더 좋을 설계로 만들기

addItem 함수를 분리하면 다음과 같이 4단계로 분리해볼 수 있다

1. cart 배열을 복사한다
2. item 객체를 만든다 ⇒ 이 부분을 분리해보면 어떨까?
3. 복사본에 item을 추가한다
4. 복사본을 리턴한다

```tsx
// item 객체를 생성하는 함수
function makeCartItem(name, price) {
  return {
    name,
    price,
  };
}

// 기존에 함수 인자로 name, price를 전달해주었지만 이젠 외부에서 생성한 item을 함수 인자로 전달할 것
// copy-on-write와 관련된 로직들을 한곳에 모아두었다
function addItem(cart, item) {
  const newCart = cart.slice();
  newCart.push(item);
  return newCart;
}

// 함수 호출부
addItem(cart, makeCartItem(name, price));
```

⇒ cart와 item 관심사를 분리함으로써 각각을 독립적으로 확장할 수 있는 구조를 만들 수 있게 되었다…!

또한 addItem 함수는 어떤 배열과 새롭게 추가할 객체를 함수 인자에 넣어도 동작하기 때문에 함수 이름을 좀 더 다음과 같이 generic하게 만들어 볼 수 있다.

```tsx
function addElementLast(array, element) {
  const newArray = array.slice();
  newArray.push(element);
  return newArray;
}
```

## 우리가 코드의 계층을 나눈 이유

- 비즈니스 로직과 비즈니스 로직에 필요한 데이터의 구조에 대한 관심사를 완전히 분리하지 위해서이다!
  - 비즈니스 로직에서 우리가 장바구니가 배열 형태여야 한다는 사실을 알아야만 한다면 코드에서 냄새가 난다고 볼 수 있다
- 이제부터 함수가 어떤 역할을 담당하고 있는지를 쪼개보는 연습을 하면 좋을 것 같다 (p.106)
  - **액션(A), 계산(C), 데이터(D)**
  - 역할에 맞게 함수를 쪼개는 연습을 해본다면 계층적으로 함수를 분리하는데도 도움이 될 것 같다

## 요약

- 일반적으로 암묵적 입력과 출력은 인자와 리턴값으로 바꿔 없애는 것이다!
- 설계는 엉켜있는 것을 푸는 것이다. 풀려있는 것은 언제든 합칠 수 있다!
- 엉켜있는 것을 풀어 각 함수가 하나의 일만 하도록 하면, 개념을 중심으로 쉽게 구성할 수 있다!
