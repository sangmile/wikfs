# 고급 리스트 기능

이 페이지에서 정리할 내용입니다.

* 파이썬 리스트 생성 및 활용
* 리스트 복사 vs 리스트 변수 복사
* 인덱스
* 조각으로부터 데이터 가져오기
* 조각 안에 값 대입하기
* 리스트 연산자
* 얕은 복사 vs 깊은 복사
* 리스트 함수
* 리스트 메서드: 리스트 수정하기
* 리스트 메서드: 내용 정보 가져오기
* 리스트 메서드: 재편성하기
* 스택 역할을 하는 리스트: RPN 애플리케이션
* reduce 함수
* 람다 함수
* 리스트 함축
* 딕셔너리와 세트의 함축
* 리스트를 통한 인수 전달하기
* 다차원 리스트

## 파이썬 리스트 생성 및 활용

파이썬은 데이터를 선언할 필요가 없다. 다른 데이터를 만드는 방법과 동일하게 만들 수 있습니.

* x = \[1, 2, 3\]

리스트 컬렉션의 변수 이름을 정할 때 'list'를 접미사로 사용하는 것이 좋습니다.

숫자와 문자열 데이터를 섞어서 리스트를 생성할 수 있지만 피하는 것이 좋습니다. 파이썬 3.0에서는 데이터 타입이 섞여 있으면 sort 메서드를 사용할 수 없기 때문입니다.

컬렉션을 구축하는 또 다른 방법은 다음과 같이 빈 리스트에 항목을 추가\(append\)하는 것입니다.

```python
my_list = [] # 반드시 빈 리스트를 만들어야 합니다.
my_list.append(1)
my_list.remove(1) # 항목을 삭제할 수도 있습니다.
```

리스트에 없는 값을 삭제하면 ValueError를 발생시킵니다.

리스트 항목의 나열 순서는 중요합니다.

```python
the_scores = [1.0, 1.0, 9.5, 1.0]
the_scores.remove(1.0) # 첫번째 항목 삭제됨. [1.0, 9.5, 1.0]
```

## 리스트 복사 vs 리스트 변수 복사

파이썬의 변수는 C++의 '값'보다는 '참조'에 가깝습니다. 한 컬렉션을 제대로 복사하려면 추가 작업이 필요하다는 의미입니다. 다음의 경우 `b_lst`는 `a_lst`의 별칭이 됩니다. 둘 중 한 변수의 값을 변경하면 다른 하나도 함께 변경됩니다.

```python
a_lst = [1, 2, 3]
b_lst = a_lst
```

리스트 전체 항목을 복사하려면 항목 간 복사를 해야 합니다. 다음은 슬라이싱을 사용한 예입니다.

```python
a_lst = [1, 2, 3]
b_lst = a_lst[:]
```

## 익덱스

파이썬은 0을 포함한 양수와 음수 인덱스를 제공합니다.

### 양수 인덱스

항목의 개수가 N개면 인덱스는 0부터 N-1까지 저장됩니다.

{% hint style="info" %}
print 함수를 반복적으로 호출하는 것은 속도를 저하시킵니다. 빠르게 확인하려면 다음과 같이 print를 한 번만 호출합니다.  
print\(a_lst\[0\], b_list\[1\], c\_lst\[2\], sep='\n'\)
{% endhint %}

### 음수 인덱스

리스트 항목은 리스트 끝에서의 거리를 의미하는 음수 인덱스로 조회할 수도 있습니다.

### enumerate 함수로 인덱스 숫자 생성

코드를 작성할 때 range 함수가 반드시 필요하지 않다면 사용하지 않는 것이 '파이썬스러운\(pythonic\)' 방법입니다.

```python
a_lst = ['A', 'B', 'C']
for s in a_lst:
    print(s)
```

위 방법은 비효율적이고 느린 인덱스를 사용하는 다음 코드보다 더 자연스럽고 효율적입니다.

```python
for i in range(len(a_lst):
    print(a_lst[i])
```

각 항목을 숫자와 함께 출력하고 싶다면 인덱스를 사용해도 되지만 enumerate 함수를 사용하는 것이 좋습니다.

* enumerate\(이터러블, 시작=0\)

이 함수는 리스트와 같은 이터러블을 인수로 받아서 튜플이 나열된 또 다른 이터러블을 생성합니다. 각 튜플은 다음과 같은 모습입니다.

* \(숫자, 항목\)

```python
>>> list(enumerate(a_lst,1))
[(1, 'A'), (2, 'B'), (3, 'C')]
```

for 루프를 사용하여 다음과 같이 나타낼 수 있습니다.

```python
>>> for n, ch in enumerate(a_lst, 1):
...     print(n, '. ', ch, sep='')
...
1. A
2. B
3. C
```

## 조각으로부터 데이터 가져오기

인덱스를 사용하면 한 번에 하나의 항목만 가져올 수 있지만, 슬라이싱 기법을 사용하면 구체적인 범위의 하위 리스트를 만들 수 있습니다.

인수 '스텝'은 양수나 음수일 수 있지만, 0이 될수는 없습니다. '스텝'이 음수면 다른 인수의 기본값은 다음과 같이 변경됩니다.

* 인수 '시작'의 기본값은 리스트의 마지막 항목이 됩니다.
* 인수 '종료'의 기본값은 리스트의 첫 항목이 됩니다.

그러면 \[::-1\]은 기존 리스트를 역순으로 나열한 리스트를 생성합니다.

## 조각 안에 값 대입하기

리스트는 가변 타입이며 항목에 바로 값을 대입할 수 있습니다. 슬라이싱에도 마찬가지입니다.

```python
>>> my_lst = [1, 2, 3, 4, 5, 6]
>>> my_lst[1:4] = [100, 200]
>>> my_lst
[1, 100, 200, 5, 6]
```

\[2, 3, 4\]를 삭제하고 \[100, 200\]을 삽입합니다.

슬라이싱 범위의 길이가 0인 인덱스를 넣을 수도 있습니다. 기존 값을 삭제하지 않고 해당 위치에 새로운 리스트 항목을 삽입합니다.

```python
>>> my_lst[0:0] = [-2, -1]
>>> my_lst
[-2, -1, 1, 100, 200, 5, 6]
```

조각 안에 값을 대입할 때는 다음과 같은 제약 사항이 있습니다.

* 조각 안에 리스트를 대입할 때, 대입하려고 하는 대상은 반드시 다른 리스트나 컬렉션이어야 합니다.
* 조각 안에 리스트를 대입할 때 '스텝'이 명시된다면 조각의 범위와 삽입할 데이터의 길이가 반드시 같아야 합니다.

## 리스트 연산자

다음은 리스트에 적용되는 내장\(build-in\) 연산자를 요약한 것입니다.

| 연산자/문법 | 설명 |
| :--- | :--- |
| 리스트1 + 리스트2 | 이어 붙이기\(concatenation\)가 수행되면서 모든 항목이 포함된 새로운 리스트 생성 |
| 리스트1 \* n 또는 n \* 리스트1 | 리스트1의 항목을 n번 반복한 리스트 생성 |
| 리스트\[n\] | 인덱스 |
| 리스트\[시작:종료:스텝\] | 슬라이싱 |
| 리스트1 = 리스트2 | 리스트1에 리스트2가 참고하고 있는 객체를 대입. 별칭 생성 |
| 리스트1 = 리스트2\[:\] | 리스트1에 리스트2의 항목 간 복사로 생성된 신규 리스트를 대입 |
| 리스트1 == 리스트2 | 각 항목을 비교하여 내용이 같으면 True |
| 리스트1 != 리스트2 | 내용이 같으면 False |
| 항목 in 리스트 | 리스트 내에 '항목'이 존재하면 True |
| 항목 not in 리스트 | 리스트 내에 '항목'이 존재하지 않으면 True |
| 리스트1 &lt; 리스트2 | 항목 간 미만 비교를 수행 |
| 리스트1 &lt;= 리스트2 | 항목 간 이하 비교 수행 |
| 리스트1 &gt; 리스트2 | 항목 간 초과 비교 수행 |
| 리스트2 &gt;= 리스트2 | 항목 간 이상 비교 수행 |
| \*리스트 | 리스트를 '언팩\(unpacked\)'하여 나열한 독립적인 각각의 항목들로 대체 |

+와 \*는 리스트 항목 간 복사본을 만듭니다. 지금까지는 얕은 복사\(shallow copy\)를 사용해도 큰 문제가 없습니다. 하지만 다차원 배열을 다룰 때는 문제가 발생합니다.

## 얕은 복사 vs 깊은 복사

얕은 복사와 깊은 복사의 차이점은 파이썬에서 무척 중요한 주제입니다. 일단 얕은 복사부터 살펴봅시다.

```python
>>> a_lst = [1, 2, [3, 4]]
>>> b_lst = a_lst[:]
>>> b_lst[0] = 0
>>> b_lst[1] = 0
>>> b_lst[2][0] = 0
>>> b_lst[2][1] = 0
>>> a_lst
[1, 2, [0, 0]]
```

b\_lst 값을 변경한 것이 a\_lst에 반영되지 않을 것 같지만 출력이 기대한 것과 다르다.

얕은 복사는 리스트가 품고 있는 리스트는 참조였기 때문에 참조를 복사한 것이다.

깊은 복사는 내부 리스트도 제대로 복사된다.

```python
>>> import copy
>>> a_lst = [1, 2, [3, 4]]
>>> b_lst = copy.deepcopy(a_lst)
>>> b_lst[2][0] = 0
>>> b_lst[2][1] = 0
>>> a_lst
[1, 2, [3, 4]]
```

![&#xC595;&#xC740; &#xBCF5;&#xC0AC;&#xC640; &#xAE4A;&#xC740; &#xBCF5;&#xC0AC;](../../.gitbook/assets/image%20%2819%29.png)

## 리스트 함수

파이썬 기본 함수 중 리스트와 함께 사용할 수 있는 len, max, min, sorted, versed, sum과 같은 유용한 함수가 많다.

이 함수들은 내부 메서드가 아니다. 메서드는 점 기호\(.\)를 사용하여 호출할 수 있다는 것이 중요한 차이점이다.

```python
len(list)         # 컬렉션 길이 반환
max(list)         # 최댓값을 가진 항목 반환
min(list)         # 최솟값을 가진 항목 반환
reversed(list)    # 역순으로 정렬된 이터레이터 반환
sorted(list)      # 정렬된 리스트 생성
sum(list)         # 모든 항목을 더한 값 반환, 항목들은 무조건 숫자이어야 함
```

컬렉션은 리스트, 문자열, 그리고 기타 파이썬 컬렉션 타입을 포함한다. 딕셔너리인 경우에는 키 개수를 반환한다.

len 함수는 리스트와 자주 사용한다.

```python
for i in range(len(a_lst)):
    a_lst[i] *= 2
```

max와 min 함수는 각각 항목 중 가장 큰 항목과 가장 작은 항목을 생성한다. 이 함수들은 리스트에만 사용 가능하며 모든 항목이 서로 비교 가능해야 한다.

sorted와 reversed 함수는 sort와 reverse 메서드와 유사하다. 하지만 메서드는 리스트 값을 바로 변경하는 반면 함수는 새로운 리스트를 생성한다.

이 두 함수는 리스트뿐만 아니라 튜플, 문자열에서도 동작하며, sorted 함수는 항상 신규 리스트를 생성하여 반환한다.

reversed 함수는 컬렉션 대신 이터러블\(iterable\)을 반환한다. 반환된 이터러블을 for 루프를 사용하여 리스트나 튜플로 변환해야 사용할 수 있다.

```python
>>> a_tup=(1,2,3,4)
>>> for i in reversed(a_tup):
...   print(i, end=' ')
... 
4 3 2 1
```

다음 코드와 동일하다.

```python
>>> print(tuple(reversed(a_tup)))
(4, 3, 2, 1)
```

sum 함수는 int나 float와 같은 숫자 타입에만 사용할 수 있다.

## 리스트 메서드: 리스트 수정하기

리스트는 신규 리스트를 생성하는 대신 데이터를 직접 수정할 수 있는 여러 메서드가 있다.

```python
list.append(value)        # 항목 추가
list.clear()              # 모든 항목 제거
list.extend(iterable)     # 하위 리스트 추가
list.insert(index, value) # index 위치에 value 삽입
list.remove(value)        # value의 첫 인스턴스 제거
```

append와 extend의 차이는 append 메서드는 한 항목을 추가하지만 extend 메서드는 컬렉션이나 이터러블의 여러 항목을 추가한다.

```python
>>> a_lst = [1, 2, 3]
>>> a_lst.append(4)
>>> a_lst.extend([5])
>>> a_lst
[1, 2, 3, 4, 5]
>>> a_lst.extend([6,7,8])
>>> a_lst
[1, 2, 3, 4, 5, 6, 7, 8
```

insert 메서드는 해당 인덱스에 위치한 값 바로 앞에 값을 집어넣는다.

```python
>>> a_lst=[1,2,3]
>>> a_lst.insert(2,5)
>>> a_lst
[1, 2, 5, 3]
>>> a_lst.insert(100,100)
>>> a_lst
[1, 2, 5, 3, 100]
>>> a_lst.insert(-100,-100)
>>> a_lst
[-100, 1, 2, 5, 3, 100]
```

remove 메서드는 인수로 주어진 값과 동일한 항목을 발견하는 즉시 해당 항목을 제거한다. 제거 대상 값을 발견하지 못하면 ValueError 예외를 발생시킨다.

제거하지 전에 해당 값이 있는지 확인하려면 in, not in 혹은 count 메서드를 활용하자.

다음 예는 최고 점수와 최저 점수를 버리고 나머지 점수들의 평균값을 내는 체조 경기 채점 방식의 예이다.

```python
def eval_scores(a_lst):
    a_lst.remove(max(a_lst))
    a_lst.remove(min(a_lst))
    return sum(a_lst) / len(a_lst)
```

## 리스트 메서드: 내용 정보 가져오기

다음 리스트 메서드는 리스트의 정보를 반환한다. count와 index는 기존 리스트의 값을 변경하지 않으며, 튜플에서도 사용할 수 있다.

```python
list.count(value)                # 인스턴스 개수 반환
list.index(value[, beg [, end]]) # value의 인덱스 반환
list.pop([index])                # 인덱스의 값 반환 및 제거
```

## 리스트 메서드: 재편성하기

아래 두 메서드는 리스트의 항목 순서를 변경하여 리스트 자체를 변경한다.

```python
list.sort([key=None] [, reverse=False])
list.reverse()                            # 현재 순서를 뒤집는다.
```

파이썬 3.0에서 두 메서드를 사용하려면 리스트 모든 항목을 서로 비교할 수 있어야 한다. 전부 문자열이거나 숫자이어야 한다.

{% code title="abc.py" %}
```python
def main():
    my_lst = []
    while True:
        s = input('Enter next name: ')
        if len(s) == 0:
            break
        my_lst.append(s)
    my_lst.sort()
    print('Here is the sorted list:')
    for a_word in my_lst:
        print(a_word, end=' ')
```
{% endcode %}

코드를 실행하면 다음과 같다.

```python
>>> import abc
>>> abc.main()
Enter next name: John
Enter next name: Paul
Enter next name: George
Enter next name: Ringo
Enter next name: Brian
Enter next name: 
Here is the sorted list:
Brian George John Paul Ringo
```

sort 메서드는 선택적으로 입력할 수 있는 인수가 있다. 첫 번째 인수는 key 인수로, 기본값은 None이다. 이 인수에는 함수를 넣을 수 있는데, 이 함수는 각 값을 인수로 하여 연산 처리를 한 후 값을 반환한다.

문자열 리스트를 정렬한다고 할 때 대소문자를 구분하지 않는다고 해보자.

```python
>>> def ignore_case(s):
...     return s.casefold()
... 
>>> a_lst=['john', 'Paul', 'George', 'brian', 'Ringo']
>>> b_lst=a_lst[:]
>>> a_lst.sort()
>>> b_lst.sort(key=ignore_case)
>>> a_lst
['George', 'Paul', 'Ringo', 'brian', 'john']
>>> b_lst
['brian', 'George', 'john', 'Paul', 'Ringo']
```

reverse 메서드는 리스트의 순서를 뒤집지만, 갑을 정렬하지는 않는다.

```python
>>> my_lst=['Brian', 'John', 'Paul', 'George', 'Ringo']
>>> my_lst.reverse()
>>> for a_word in my_lst:
...     print(a_word, end=' ')
... 
Ringo George Paul John Brian
```

## 스택 역할을 하는 리스트: RPN 애플리케이션

append와 pop 메서드는 스택 메커니즘인 후입선출\(LIFO\) 장치로 활용한다.

스택 장치의 가장 유용한 데모는 후위 표기법\(Reverse Polish Notation, RPN\) 언어를 번역하는 프로그램이다.

후위 표기법 언어는 연산자를 피연산자 뒤에 위치시키는 언어다. 괄호 기호 없이도 깔끔하고 명확한 방법의 연산식을 표현하는 것이 장점이다.

```python
10 5 * 7 3 + /
(10 * 5) / (7 + 3) 과 같다.
```

가장 큰 장점은 다음 규칙만 지키면 된다는 것이다.

* 다음 항목이 숫자면 스택에 넣는다.
* 다음 항목이 연산자면 스택 위에서 2개의 항목을 추출한 후 연산자를 반영하여 계산한 값을 다시 스택에 넣는다.

```python
the_stack = []

def main():
    s = input('Enter RPN string: ')
    a_lst = s.split()
    
    for item in a_lst:
        if item in '+-*/' and len(the_stack) > 1:
            op2 = the_stack.pop()
            op1 = the_stack.pop()
            the_stack.append(eval(str(op1) + item + str(op2)))
        else:
            the_stack.append(float(item))
    print(the_stack.pop())
main()
```

## reduce 함수

파이썬 리스트의 더욱 흥미로운 기능은 리스트의 모든 항목을 한 번에 처리할 수 있는 함수를 직접 작성하여 사용할 수 있다는 것이다. map이나 filter와 같은 리스트 메서드가 있다.

functools 패키지의 여러 함수를 사용하면 리스트를 다양한 방식으로 처리할 수 있다.

```python
import functools
functools.reduct(func, list)
```

다음과 같이 동작한다.

1. 첫 2개의 항목을 'func'의 인수로 넣고, 결괏값을 기억한다.
2. 위 결과와 세 번째 항목을 'func'의 인수로 넣어서 처리된 결과를 기억한다.
3. 위 결과와 네 번째 항목을 'func'의 인수로 넣어서 처리된 결과를 반환한다.

```python
import functools

def add_func(a, b):
    return a + b

def multi_func(a, b):
    return a * b

n = 5
a_lst = list(range(1, n+1)

triangle_num = functools.reduce(add_func, a_lst) # 1 + 2 + 3 + 4 + 5
fact_num = functools.reduce(multi_func, a_lst)   # 1 * 2 * 3 * 4 * 5
```

## 람다 함수

리스트에 연산 처리를 할 때 1회용 함수를 만들 때 사용하는 함수이다.

```python
lambda args: ret
```

reduce 함수와 함께 사용하는 경우가 많다.

```python
t5 = functools.reduce(lambda x, y: x+y, [1,2,3,4,5])
f5 = functools.reduce(lambda x, y: x*y, [1,2,3,4,5])
```

## 리스트 함축

리스트 함축은 리스트에서 열거식의 값을 생성하는 코드를 좀 더 간결하게 만든다. 딕셔너리, 세트와 다른 컬렉션에서도 사용할 수 있다.

항목 간 복사하는 방법들이다.

```python
1.
b_lst = a_lst[:]

2.
b_lst = []
for i in a_lst:
    b_lst.append(i)

3.
b_lst = [i for i in a_lst]
```

`for_문_표현식`

```python
b_lst = [ i * i for i in a_lst ]
```

 `for_문_표현식 if_조건문` 

```python
my_lst = [10, -10, -1, 12, -500, 13, 15, -3]

new_lst = []
for i in my_lst:
    if i > 0 :
        new_lst.append(i)

new_lst = [ i for i in my_lst if i > 0 ]
```

대입 연산자 우측은 크게 세 가지로 나뉜다.

* 값 표현식 i는 리스트 값을 바로 가져온다.
* for 문 표현식 for i in my\_lst는 연산할 열거형 값을 제공한다.
* 마지막으로 if 조건문 if i &gt; 0은 선택할 항목을 선별한다.

## 딕셔너리와 세트의 함축

리스트 함축 원칙은 세트와 딕셔너리까지 확장된다.

