# variable-length argument list

## 가변 길이 인수 리스트란?

개수 제약 없이 원하는대로 인수를 넣을 수 있는 리스트다.

### \*args 리스트

\*args 문법은 모든 길이의 인수 리스트에 접근하는데 사용된다.

#### 사용 방법

```python
def 함수_이름([일반_인수,], *args):
    문장
```

파이썬에서는 관습적으로 args를 인수 리스트를 표현하는 데 사용한다.

인수 리스트의 길이와 항목을 검색하는 예시이다.

```python
def my_args_func(*args):
    print('리스트 길이:', len(args))
    for item in args:
        print(item)
```

실행 예

```python
>>> my_args_func(1, 2, 3, 4)
리스트 길이: 4
1
2
3
4
```

리스트의 평균값을 구하는 함수를 만들어보자.

```python
def my_avg_func(*args):
    print(sum(args)/len(args))
```

실행 예

```python
>>> def my_avg_func(*args):
...     print(sum(args)/len(args))
... 
>>> my_avg_func(1, 2, 3)
2.0
```

#### 일반 인수 사용 방법

_args에 추가되지 않는 추가 인수는_ args 앞에 위치하거나 키워드 인수이어야 한다.

```python
def my_comm_func(units, *args):
    print(sum(args)/len(args), units)
```

실행 예

```python
>>> def my_comm_func(units, *args):
...     print(sum(args)/len(args), units)
... 
>>> my_comm_func('inches', 10, 20, 30)
20.0 inches
```

별표 기호는 기본적으로 unpacked list로 사용한다. 즉, 리스트를 분리하여 항목으로 교체한다.

> > > lst = \[1, 2, 3\] print\(\*lst\) 1 2 3 print\(lst\) \[1, 2, 3\]

### \*\*kwargs 리스트

키워드 인수 예이다. 함수 호출 시 `end`와 `sep` 인수에 이름\(. 및 ,\)을 지정한다.

```python
>>> print(10, 20, 30, end='.', sep=',')
10,20,30.
```

#### 사용 방법

```python
def my_kwargs_func(*args, **kwargs):
    for i in args:
        print(i)
    for k in kwargs:
        print(k, ':', kwargs[k])
```

사용 예

```python
>>> my_kwargs_func(10,20,30, a=10, b=20)
10
20
30
a : 10
b : 20
```

