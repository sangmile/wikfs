# decorator

## decorator란?

기존 함수가 그대로 실행되면서 추가 문장이 더 실행되도록 하는 설계 패턴이다.

### decorator의 예시

wrapper 함수는 기존 함수가 실행되면서 걸린 시간을 초 단위로 반환하는 문장을 추가한다. decorator는 wrapper 함수를 생성하여 반환하는 작업을 수행한다.

```python
import time

def make_timer(func):
    def wrapper(*args, **kwargs):
        t1 = time.time()
        return_val = func(*args, **kwargs)
        t2 = time.time()
        print('소요 시간: ', t2 - t1)
        return return_val
    return wrapper
```

decorator를 wrapping하고자 하는 함수를 만들자.

```python
def my_origin_func(n):
    for i in range(n):
        for j in range(10000):
            pass

my_origin_func = make_timer(my_origin_func)
```

wrapping된 함수 `my_origin_func`를 실행하면 다음과 같이 실행 시간이 출력된다.

```python
>>> my_origin_func(10000)
소요 시간:  1.3125348091125488
```

### 사용 방법

```python
@decorator
def my_origin_func(n):
    문장
```

위 문법은 아래와 같다.

```python
def my_origin_func(n):
    문장
my_origin_func = decorator(my_origin_func)
```

### 정리

위 예제를 decorator 문법을 사용해서 정리하면 다음과 같다.

```python
@make_timer
def my_origin_func(n):
    for i in range(n):
        for j in range(10000):
            pass
```

