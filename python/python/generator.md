# generator

## generator란?

iterator를 만드는 가장 쉬운 방법이다. yield 문장을 사용하면 어디에서나 제너레이터를 만들 수 있다.

#### iterator란?

iterator는 한 번에 하나씩 값을 생성하여 나열된 값의 스트림을 제공하는 객체다.

모든 리스트는 iterator지만, 모든 iterator가 리스트는 아니다. reversed와 같은 많은 함수가 리스트가 아닌 iterator를 생성한다. 예를 살펴보자.

```python
>>> a = [1,2,3]
>>> a_iter = reversed(a)
>>> print(a_iter)
<list_reverseiterator object at 0x7f0529e50790>
>>> type(a_iter)
<class 'list_reverseiterator'>
```

iterator는 리스트로 변환하여 출력하고 인덱스로 값에 접근하거나 슬라이스할 수 있다. iterator는 상태와 함께 동작하기 때문에 다시 접근하면 어떤 값도 반환하지 않는다.

```python
>>> print(list(a_iter))
[3, 2, 1]
>>> print(a_iter)
<list_reverseiterator object at 0x7f0529e50790>
>>> print(list(a_iter))
[]
```

### generator 프로세스

generator 함수는 iterator의 행위를 정의한다. yield는 해당 함수가 값을 반환하지 않지 않고 next에 값을 보내도록 만든다. 상태 정보가 저장되기 때문에 next가 다시 호출되면 iterator는 sequence의 다음 값을 보낸다.

#### generator 함수

```python
def make_gen(m):
    n = 1
    while n <= m:
        yield n
        n += 1
```

#### generator 객체

iterator 객체는 n을 산출\(yield\)한다.

m=2일 때

* n=1 : 생성된 generator 객체인 iterator 객체는 1을 산출한다. next를 호출하면 1을 산출한다.
* n=2 : 생성된 generator 객체인 iterator 객체는 2을 산출한다. next를 호출하면 2을 산출한다.

#### generator 예

짝수를 반환하는 함수를 만들어보자.

```python
def make_even_gen(n):
    for i in range(2, n, 2):
        yield i
```

이 함수는 어떤 값도 반환하지 않는다. 대신 값 i를 산출하고, 실행을 보류하고, 내부 상태를 저장한다. 어떤 값을 반환하는 것은 아니지만 generator 객체라고 불리는 iterator 객체를 반환한다. 다음과 같이 확인해보자.

```python
>>> make_even_gen(11)
<generator object make_even_gen at 0x7f05284fb5f0>
```

iterator 객체는 next에 넘겨서 확인할 수 있다. 시퀀스의 끝에 도달하면 `StopIteration` 예외가 발생한다.

```python
>>> my_gen = make_even_gen(11)
>>> next(my_gen)
2
>>> next(my_gen)
4
>>> next(my_gen)
6
>>> next(my_gen)
8
>>> next(my_gen)
10
>>> next(my_gen)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

generator를 호출하여 generator 객체를 만든 뒤, 해당 객체 안에서 for 루프 안에서 값을 제공할 수 있다.

```python
>>> my_gen = make_even_gen(11)
>>> for i in my_gen:
...     print(i, end=' ')
... 
2 4 6 8 10
```

상태를 저장하기 때문에 generator 객체는 매번 재설정이 필요하다.

```python
>>> my_gen = make_even_gen(11)
>>> a_list = list(my_gen)
>>> a_list
[2, 4, 6, 8, 10]
>>> a_list = list(my_gen)
>>> a_list
[]
```

#### iterator가 가장 많이 사용되는 예

iterator의 가장 실용적인 사용 방법은 `in`과 `not in` 키워드와 함께 사용하는 것이다. 피보나치 수열을 생성하는 함수를 생성해보자.

```python
def make_fibo_gen(n):
    a, b = 1, 1
    while a <= n:
        yield a
        a, b = a + b, a
```

위 generator 함수는 피보나치 수열의 값을 가지는 generator 객체\(iterator\)를 반환한다. 다음과 같이 입력받은 수가 피보나치 값인지 아닌지 테스트할 수 있다.

```python
def test_fibo():
    a = int(input('Enter number: '))
    my_fibo_gen = make_fibo_gen(a)
    if a in my_fibo_gen:
        print('Fibo')
    else:
        print('Not Fibo')
```

테스트하면 다음과 같다.

```python
>>> test_fibo()
Enter number: 3
Fibo
>>> test_fibo()
Enter number: 4
Not Fibo
```

