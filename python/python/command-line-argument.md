# command-line argument

커맨드 라인 인수에 접근하려면 sys 패키지가 필요하다. argv 리스트를 참조하여 함수 이름을 포함하는 모든 커맨드 라인 인수를 참조할 수 있다.

모든 인수를 출력하는 `comm.py`를 만들어보자.

```python
import sys
for i in sys.argv:
    print(i, end=' ')
```

위 파일 `comm.py`를 실행하면 다음과 같이 출력된다.

```python
$ python3 comm.py arg1 arg2 arg3
comm.py arg1 arg2 arg3
```

이차 방정식의 해를 구하는 함수를 만들어보자.

```python
import sys

def quad(a, b, c):
    determin = (b * b - 4 * a * c) ** .5
    x1 = (-b + determin) / (2 * a)
    x2 = (-b - determin) / (2 * a)
    return x1, x2

def main():
    if len(sys.argv) > 3:
        s1, s2, s3 = sys.argv[1], sys.argv[2], sys.argv[3]
    else:
        s1 = input('Enter a: ')
        s2 = input('Enter b: ')
        s3 = input('Enter c: ')
    a, b, c = float(s1), float(s2), float(s3)
    x1, x2 = quad(a, b, c)
    print('x values: {}, {}'.format(x1, x2))

main()
```

실행 값은 다음과 같다.

```python
$ python3 quad.py 1 -9 20
x values: 5.0, 4.0
```

