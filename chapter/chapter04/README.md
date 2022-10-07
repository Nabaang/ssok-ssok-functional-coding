# CHAPTER 4 액션에서 계산 빼내기

### 이번 장에서 살펴볼 내용

1. 어떻게 함수로 정보가 들어가고 나오는지 살펴본다.
2. 테스트하기 쉽고 재사용성이 좋은 코드를 만들기 위한 함수형 기술에 대해 알아본다.
3. 액션에서 계산을 빼내는 방법을 배운다.

## 함수에는 입력과 출력이 있다.

모든 함수는 입력과 출력이 있다.

입력은 함수가 계산을 하기 위해 <b>함수 내부로 들어온</b> 외부 정보이고,
출력은 <b>함수 밖으로 나오는</b> 정보나 어떤 동작이다.

아래 함수를 보면서 어떤 입력과 출력이 있는지 살펴보자.
```js
let total = 0;
function addToTotal(amount) { // 함수 인자(입력)
    // 전역변수를 읽는 것(입력), 콘솔에 무언가 찍는 것(출력)
    console.log(`Old total  ${total}`); 
    total += amount; // 전역변수를 바꾸는 것(출력)
    return total; // 리턴 값(출력)
}
```

### 입력과 출력은 명시적이거나 암묵적일 수 있다.

함수 외부의 정보에 접근하고 있다면 암묵적, 함수의 인자와 리턴 값으로 표현되는 것은 명시적이라 한다.
위에서 찾은 입력과 출력에서 명시적인지 암묵적인지 구분해보자.

```js
let total = 0;
function addToTotal(amount) { // 함수 인자(명시적 입력)
    // 전역변수를 읽는 것(암시적 입력), 콘솔에 무언가 찍는 것(암시적 출력)
    console.log(`Old total  ${total}`); 
    total += amount; // 전역변수를 바꾸는 것(암시적 출력)
    return total; // 리턴 값(명시적 출력)
}
```

입력은 함수 인자를 제외하고는 모두 암시적 입력이고, 출력은 리턴 값을 제외하고는 모두 암시적 출력이다.

### 함수에 암묵적 입력과 출력이 있으면 액션이 된다.

그래서 액션인 함수에서 암묵적 입력과 출력을 모두 없애면(혹은 명시적으로 바꾸면) 계산이 된다.

없애는 방법은 암묵적 입력은 함수의 인자로, 암묵적 출력은 함수의 리턴 값으로 바꾸면 된다.

| 함수형 프로그래머는 암묵적 입력과 출력을 <b>부수 효과</b>라고 부른다.

## 테스트와 재사용성은 입출력과 관련 있다.

아래 코드는 장바구니의 금액 합계를 구하는 함수이다.

```js
let shoppingCart = []
let shoppingCartTotal = 0

function calculateCartTotal() {
    shoppingCartTotal = 0 // 전역변수에 접근(압묵적 출력)
    for(let i = 0; i < shoppingCartTotal.length; i++) {
        const item = shoppingCart[i]; // 전역변수에 접근(압묵적 입력)
        shoppingCartTotal += item.price;
    }
    setCartTotalDom(); // dom에 접근
}
// 반환 값 없음
```

위 코드를 테스트하거나 장바구니의 총합을 구하는 로직을 재사용하려면 어떻게 해야할까?

우선 테스트를 하려면 브라우저를 이용해서 테스트를 해야한다. 장바구니 총합을 구하고 DOM에 업데이트가 잘 되는지 확인해야 되기 때문이다.

그리고 장바구니에 새로운 물품을 담고 장바구니 총합을 얻기 위해서는 calculateCartTotal()를 호출하고 전역변수에 접근해야 한다. 만약, 다른 모듈에서 필요로 한다면..? 전역변수를 export 시켜주면 되겠지만 항상 calculateCartTotal()을 호출한 다음에 접근해야 하기 때문에 그다지 도움이 되진 않는다.

지금 상태라면 코드가 바뀔 때마다 테스트하기도 어렵고 재사용하기도 어렵다.

## 액션에서 계산 빼내기

위 코드의 테스트와 재사용성을 개선시키려면, 아래 나열된 규칙을 따라야한다.

- DOM 업데이트와 비즈니스 규칙을 분리
- 전역변수를 없애야 한다. (암시적 입력 -> 명시적 입력)
- 함수가 결괏값을 리턴해야 한다. (암시적 출력 -> 명시적 출력)

위 코드를 규칙을 적용해서 바꿔보자. 

```js
let shoppingCart = []
let shoppingCartTotal = 0

function calculateCartTotal() {
    shoppingCartTotal = calculateTotal(shoppingCart);
    setCartTotalDom();
}

// 기존의 동작은 유지하면서 코드를 고치는 것을 리팩토링이라고 한다.(여기서의 리팩토링은 서브루틴 추출하기라고도 한다.)
function calculateTotal(cart) {
    let total = 0; 
    for(let i = 0; cart.length; i++) {
        const item = cart[i]; // 함수 인자에 접근(명시적 입력)
        total += item.price;
    }
    return total; // 리턴 값을 반환(명시적 출력)
}
```

calculateCartTotal()에서 비즈니스 로직을 calculateTotal()로 분리하였다.
calculateCartTotal()에는 아직 전역변수를 사용하고 있지만, calculateTotal()에서는 전역변수를 사용하지 않고 함수 인자만 사용하고 있다.
또한 함수의 결괏값을 리턴 값으로만 반환하고 있어서, 비즈니스 로직만 테스트할 수도 있게 되었고, 재사용성도 높아졌다.

## 헷갈릴 수 있는 암묵적 입,출력(배열관련)

```js
function addItemToCart(cart, name, price) {
    cart.push({ name, price });
}
```

이렇게 하면 함수로 넘겨진 인자만 사용하고 있어서 명시적 출력을 사용한 것 같지만, 그렇지 않다.

명시적 출력은 리턴 값만 해당되고, 지금 변경하고 있는 cart는 함수 외부에서 봤을 때는 인식할 수 없기 때문에 암시적 출력으로 보는게 맞다.

명시적 입,출력으로 바꾸려면 아래 코드처럼 바꿔야 한다.

```js
function addItemToCart(cart, name, price) {
    let newCart = cart.slice(); 
    newCart.push({ name, price }); // 복사본을 변경, 인자로 주어진 cart가 변경되는게 아님
    return newCart; // 리턴 값(명시적 출력)
}
```

이렇게 되면 이제 addItemToCart()는 액션에서 계산으로 바뀌게 되는 것이다.

## 결론

- 함수는 입력과 출력을 가지는데, 암묵적 입,출력을 가지고 있다면 액션이고, 명시적 입,출력만 가지고 있다면 계산이다.
- 액션을 계산으로 바꾸기 위해서는 암묵적 입,출력을 명시적 입,출력으로 바꿔 주어야 한다.
- 위에서 배운 원칙들을 적용하면 액션을 줄어들고 계산이 늘어난다.
- 계산이 늘어나면 테스트와 재사용성이 개선(증가)된다. 
- 모든 액션을 계산으로 바꾼다고해서 액션이 없어지지는 않는다. 하지만, 액션은 전이되기 때문에 함수안에 하나의 액션만 있어도 그 함수는 액션이 되기 때문에 액션이 전이되는 범위를 최대한 줄이기 위해 계산으로 최대한 바꿔주는게 좋다.
