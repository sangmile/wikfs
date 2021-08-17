# doc string 작성

doc string을 작성하는 예시다.

```python
def test(a, b, c):
    '''test function.

    This function is a Test-Purpose Function.
    '''
    
    print(a, b, c)
    return a, b, c
```

다음과 같이 도움말을 볼 수 있다.

```python
help(test)

Help on function test in module __main__:

test(a, b, c)
    test function.
    
    This function is a Test-Purpose Function.
```

커맨드 라인에서 파이썬을 실행할 때 온라인 도움말을 pydoc 유틸리티로 사용할 수 있다. pydoc 유틸리티는 모든 함수의 도움말 요약을 출력해 준다.

```python
> python -m pydoc <모듈명>
```



