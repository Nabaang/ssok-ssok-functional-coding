# CHAPTER 12 함수형 반복

### 공감가는 내용

- 시퀀스 관점에서 `map()`, `filter()`, `reduce()`을 바라보는 점
- reduce를 이용해서 min or max를 구하는 방법(시퀀스를 하나의 값으로 만듦)
- map 사용 시 배열의 항목을 확인하지 않기 때문에 조심해야 함.(null을 제거하는 filter를 사용하는게 안전)
    - 1차 정적 처리
    - 2차 필터링..

<h4>reduce 시간 복잡도</h4>

```jsx
// O(N^2) ???
reduce((acc, list) => list.prop ? acc.concat(list) : acc}, [])

// n = 10
[] +[1].        O(1)
[1] + [2]       O(2)
[1,2] + [3].    O(3)
[1,2,3] + [4]   O(4)
[1,2,3,4] + [5] O(5)
...
[1,2,3,4,5,6,7,8,9] + [10]

O(1+2+3....10) = O(n(n+1) / 2) = O(n²)
```

- java의 경우
  
    ```jsx
    String lhs (길이 n)
    String rhs (길이 k)
    lhs + rhs , O(n+k) 시간 복잡도가 걸림.
    ```
    
    - StringBuffer, StrinBuilder의 경우 일반적으로 O(1)

### **다른 사람의 의견을 들어보고 싶은 내용** (미 해결)

- reduce를 통한 **우아하게** 실행 취소/실행 복귀를 구현하는 방법

```jsx
// 정방향
{
	prev : [1,2,3,4],
	curr : 5
}

// 역방향
{
	prev : [5,4,3,2],
	curr : 1
}

1,2,3,4,5

undo action => 4=3=2=1 
ctrl + z 4
ctrl + z 3
ctrl + z 2
ctrl + z 1

// 시퀀스에서 중간에 나가지 못한다면 그것을 실행취소라고 할 수 있을까?
reduce 

1,2,3,4,5 
5가 최신 액션인데, 실행 취소할경우에는 
역방향으로 탐색하면서 실행취소 값이 false 값을 찾아서(실행된액션) undo

실행 복귀할경우
정방향으로 탐색하면서 실행취소 값이 true 값 찾아서 do

const transaction = reduce(
	fn1,  
	fn2, return null
	fn3, null api 요청을 안내보겠다...???..
	fn4,
)  === null ? "오류" : "정상"
```

- 미제 사건, 공소시효 3개월 땅땅땅

### **토론해 볼 내용**

- 고차 함수는 항상 반복문보다 옳은가?
    - 목적에 맞는 고차 함수를 잘 사용하자.
    - `for -awiat` ,`Promise.all()`

    ```jsx
    const item = {
      x : 1,
      y : {
        z : 3
      }
    }
    
    // ======
    const copy = Object.assign({}, item);
    copy['y'] = 100;
    
    console.log(item) // item 원본이 안 바뀜
    console.log(copy) // item 원본이 안 바뀜
    //  ======
    
    const arr2 = [item];
    
    // elemet를 접근할 때?
    arr2.forEach((obj => origin[i], i , origin) => {
    	obj.y = 100 // item에 반영
    	obj = 100   // array2에 반영 X
    	
    	const obj = Object.assign({}, obj);
    	
    })
    
    console.log(arr2) 
    console.log(item)
    ```

    <h4>for loop</h4>

    1. 인덱스 변수를 초기 값으로 설정
    2. 루프를 종료할지 여부를 확인합니다.
    3. 루프 본문 실행
    4. 인덱스 변수 증가
    5. 2단계로

    <h4>forEach loop</h4>

    1. Instantiate the callback function

    2. 처리할 다음 element 있는지 확인

    3. **새로운 실행 컨텍스트를 사용해서 다음 요소에 대한 콜백 호출**(context, arguments, inner variables, and references to any outer variables -- if used)

    4. 콜백 실행

    5. Teardown of callback function call

    6. 2단계로

       

    - forEach의 3단계와 5단계에서 function setup & teardown 으로 인해 오버헤드 발생
    - 최신 브라우저의 경우 forEach 호출을 인식하고 최적화 하므로 경우에 따라 forEach 가 더 빠를 수도 있음.

