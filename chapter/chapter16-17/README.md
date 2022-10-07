# CHAPTER 16/17

- **CHAPTER 16** 타임라인 사이에 자원 공유하기
- **CHAPTER 17** 타임라인 조율하기

### 공감가는 내용

- 재욱

  - 액션 간 공유 자원 처리 및 액션의 순서를 보장하기 위해, 스케줄링에 큐를 활용. (동시성 기본형(concurrency primitive))
  - `Cut()` 동시성 기본형을 통해, **읽기 액션**들을 병렬적으로 처리하는 방법. (JavaScript의 `Promise.all()`와 유사..)
    - 단, 공유 자원을 쓰는 액션은 병렬적으로 처리하면 안 됨(위험).
    - 액션 중 하나라도 실패하는 케이스를 어떻게 처리할지, 추가로 처리할 필요가 있음.
  - 동시성 기본형 개념이 재밌네요 !!!..

    ```tsx
    const Cut = (num, callback) => {
      let callbackFinished = 0;

      return () => {
        if (callbackFinished > num) return;
        callbackFinished += 1;

        if (num === callbackFinished) {
          callback();
        }
      };
    };

    const JustOnce = (callback) => {
      let isCalled = false;

      return (...args) => {
        if (isCalled) return;
        return callback.apply(null, args);
      };
    };
    ```

- 현구

  - queue에서 기본적으로 사용하는 순서를 이용해서 호출되는 함수들이 동시에 실행되지 않도록 보장하도록 하였다. 이러한 자원 공유를 위한 도구를 **동시성 기본형**이라고 한다.
  - 문득 든 생각이지만, 메인 쓰레드가 단일 쓰레드인 JS를 사용하고 있어서 동시성 문제에 대해 덜 생각하면서 코딩을 할 수 있는 것 같다.
  - 지역변수라도 공유자원이라면 액션이 될 수 있다.

    ```tsx
    function calc_cart_total(cart, callback) {
      var total = 0;

      cost_ajax(cart, function (cost) {
        total += cost;
      });

      shipping_ajax(cart, function (shipping) {
        total += shipping;
        callback(total);
      });
    }
    ```

  - 책에서 만든 Cut 함수가 유용한가??

    ```tsx
    function calc_cart_total(cart, callback) {
      var toatl = 0;
      var done = Cut(2, function () {
        callback(total);
      });
      cost_ajax(cart, function (cost) {
        total += cost;
        done();
      });
      shipping_ajax(cart, function (shipping) {
        total += shipping;
        done();
      });
    }
    ```

  - 위 예제는 async, await, Promise.all()로도 충분해 보인다. 오래된 책인가? 아니면 일반적인 상황을 위해 예시를 든 것인가??근데 Cut 함수 신기방기 하긴 하다.아, 뒤에서 언어와 상관없이 이런 원칙을 사용할 수 있도록 예를 든거라고 하고 있다 ㅎㅎ..

- 호준
  - queue를 만들어서 비동기 처리에 순서를 제어하는 방식. (생산자-소비자 디자인 패턴과 유사)
  - 두 콜백을 끝나기 까지 기다리는 점선을 컷이라고 하고, 기다릴 타임라인 수 를 기반으로 마지막 타임라인 이 끝났을 때, 콜백을 호출
  - 컷의 앞부분과 뒷부분에 있는 액션은 서로 섞이지 않음. (애플리케이션의 복잡성을 줄일 수 있음, Promise 처럼 언어에서 제공되는 형태도 있음)
  - 언어가 사용하는 암묵적 시간 모델 대신 실행 방식에 가깝게? 새로운 시간 모델(명시적 시간 모델)을 만든다.
