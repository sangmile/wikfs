# 패키지 탑재

## 패키지란?

서비스에 필요한 객체와 함수가 있는 라이브러리다. 패키지는 기본적으로 포함된 패키지와 인터넷에서 다운로드할 수 있는 패키지 두 가지 종류가 있다.

#### 기본적으로 제공하는 패키지

* math, random, sys, os, time, datetime, os.path

#### 사용 방법

```python
import math
```

#### 함수 사용

함수를 사용하려면 점 기호\(.\)를 사용해야 한다.

```python
>>> math.sqrt(2)
```

#### 함수 별칭

함수 이름 `matplotlib.pyplot`을 plt로 소스 파일에서 사용할 수 있다.

```python
import matplotlib.pyplot as plt
```

#### 모듈 탑재

아래와 같이 math 패키지에서 pi 모듈만 탑재할 수 있다. 콤마 기호로 분리하여 나열할 수도 있다. 이렇게 탑재된 모듈은 심벌 이름만으로도 바로 호출할 수 있다.

```python
from math import pi, sqrt
```

모든 객체와 함수를 바로 호출할 수 있도록 하려면 다음과 같이 별표 기호\(\*\)를 사용하면 된다.

```python
from math import *
```

위 방법은 단점이 있다. 큰 프로그램일 경우, 패키지 이름 없이 호출했을 때 이름이 겹쳐 충돌이 날 수도 있다. 심벌을 구체적으로 명시하는 것이 좋다.



