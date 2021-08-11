# 고급 문자열 기능

이 페이지에서 다룰 내용입니다.

* 문자열은 불변
* 바이너리를 포함한 숫자 변환
* 문자열 연산자\(+, =, \*, &gt;, 기타\)
* 인덱싱과 슬라이싱
* 단일-문자 함수\(문자 코드\)
* join을 사용하여 만든 문자열
* 주요 문자열 함수
* 이진수와 10진수, 16진수 변환 함수
* 간단한 불리언\('is'\) 메서드
* 대소문자 변환 메서드
* 검색-교체 메서드
* 'split'을 활용한 입력 값 쪼개기
* 앞뒤 문자 제거하기
* 자리 맞춤 메서드

## 문자열은 불변이다

파이썬의 데이터 타입은 가변\(mutable\)이거나 불변\(immutable\)입니다.

변경할 수 있는 타입은 리스트, 딕셔너리, 세트입니다. 불변 타입의 장점은 딕셔너리의 키로 사용할 수 있다는 점입니다. 다음 딕셔너리가 그 예입니다.

```text
movie_dict = { '기생충': 5.0,
               '해리포터': 4.5 }
```

불변 타입의 장점은 이 타입을 사용할 때 이미 내부적으로 성능이 최적화되어 있는 데이터 타입을 사용할 수 있다는 것입니다. 가령 튜플을 사용하면 리스트를 사용하는 것보다 더 효율적입니다.

불변 객체는 변경할 수 없지만 새로운 객체로 생성하는 것은 가능합니다.

```text
my_str = '안녕하세요.'
my_str[0] = '김'    # 타입 에러

my_str = '안녕'
my_str = '안녕하세요' # OK
```

파이썬 변수는 그저 객체를 호출하는 이름일 뿐이며 같은 변수를 계속해서 재사용할 수 있습니다.



## 바이너리를 포함한 숫자 변환

파이썬의 타입 이름은 타입 변환이 가능한 모든 곳에서 묵시적으로 타입 변환을 수행합니다.

```text
s = '45'
n = int(s) # 45
x = float(s) # 45.0
```

int 변환은 문자열을 숫자로 변환할 때 변환 대상 문자열의 진수를 명시합니다.

```text
n = int('10001', 2) # 이진수 변환
n1 = int('775', 8) # 8진수
n2 = int('1E', 16) # 16진수
```



## 문자열 연산자

문자열 타입 str에서 숫자 타입 연산자와 같은 연산자를 사용할 수 있으나 다르게 동작합니다.

```text
text1_str = '헬로'    # 값 대입
text2_str = text1_str # 별칭 생성

text1_str == test2_str # 참
text1_str == '헬로'     # 참
```

이 `text1_str`에 새로운 데이터를 대입하더라도 `text2_str`는 '헬로'를 참조합니다. text1과 text2는 같은 데이터를 참조하므로 참을 반환합니다.

'헬로' 문자열은 text1\_str과 값은 같지만 서로 다른 객체다. 하지만 두 문자열이 같은 내용이라면 참입니다.

파이썬의 문자열 연산자를 사용하여 비교하는 모든 기능은 대소문자를 구분합니다. upper 혹은 lower 메서드를 사용하여 비교할 수도 있지만, 유니코드로 이루어진 문자열을 사용하고 있다면 casefold 메서드를 사용하여 비교하는 것이 안전합니다.

```text
def compare_no_case(str1, str2):
    return str1.casefold() == str2.casefold()

print(compare_no_case('cat', 'CAT')) # 
```

| 연산자 문법 | 설명 |
| :--- | :--- |
| name = 문자열 | 문자열을 name에 대입한다. |
| 문자열1 == 문자열2 | 문자열1과 문자열2가 같은 값을 가지고 있다면 True를 반환한다. |
| 문자열1 != 문자열2 | 문자열1과 문자열2가 서로 다른 값을 가지고 있다면 True를 반환합니다. |
| 문자열1 &lt; 문자열2 | 문자열1이 문자열2보다 알파벳 순서가 앞이면 True를 반환합니다. |
| 문자열1 &gt; 문자열2 | 문자열1이 문자열2보다 알파벳 순서가 뒤면 True를 반환합니다. |
| 문자열1 &lt;= 문자열2 | 문자열1이 문자열2보다 알파벳 순서가 앞이거나 같으면 True를 반환합니다. |
| 문자열2 &gt;= 문자열2 | 문자열1이 문자열2보다 알파벳 순서가 뒤거나 같으 True를 반환합니다. |
| 문자열1 \* n | 문자열1을 정수 n만큼 반복하여 연결합니다. |
| 문자열1 in 문자열2 | 문자열1 전체가 문자열2에 포함되면 True를 반환합니다. |
| 문자열1 not in 문자열2 | 문자열1 전체가 문자열2에 포함되지 않으면 True를 반환합니다. |
| 문자열 is 객체 | 문자열과 객체가 메모리상에 동일 객체를 참조하고 있으면 True를 반환합니다. None이나 알지 못하는 객체 타입과 비교할 때 사용합니다. |
| 문자열 is not 객체 | 문자열과 객체가 메모리상에 동일 객체를 참조하지 않으면 True를 반환합니다. |

반복 연산자\(\*\)는 문자로 이루어진 그래픽을 그리거나 단작 구분자와 같이 긴 줄을 초기화할 때 사용합니다.

```text
divider_str = '_' * 30
print(divider_str)
```

is와 is not 연산자는 어떤 값이 특수 값 None과 같은지를 테스트하는 경우에만 사용합니다.

## 인덱싱과 슬라이싱

문자열의 값을 추출하는 두 가지 방법입니다.

* 인덱싱은 문자열 안의 특정 위치에 있는 숫자\(인덱스, 색인\)로 각 문자를 참조합니다.
* 슬라이싱은 파이썬의 고유 기능입니다.

리스트 기능과 비슷할 수 있지만 문자열은 불변 타입이기 때문에 인덱싱, 슬라이싱 혹은 값을 변경하는 어떤 연산자도 실제 값을 변경하지 않습니다.

슬라이싱에서 인덱스를 사용할 때 모든 조합의 양수 인덱스와 음수 인덱스를 사용할 수 있습니다.

```text
txt = 'TEST'
  0   1   2   3
  T   E   S   T
 -4  -3  -2  -1
```

불변성 외에도 문자열과 리스트는 또 다른 점이 있습니다. 리스트 인덱싱은 다양한 타입으로 구성된 항목을 반환할 수 있는 반면 문자열 인덱싱은 항상 1개의 문자를 반환한다는 점입니다. 이 1개의 문자 역시 str 타입입니다.  다른 프로그래밍 언어처럼 별도의 '문자' 타입을 제공하지 않습니다.

슬라이싱은 파이썬 문자열, 리스트, 튜플에서 사용할 수 있는 특별한 기능입니다.

| 문법 | 부분 문자열 결과 |
| :--- | :--- |
| string\[beg: end\] | beg부터 end 전까지 모든 문자를 반환합니다. |
| string\[:end\] | 처음부터 end 전까지 모든 문자를 반환합니다. |
| string\[beg:\] | beg부터 끝까지 모든 문자를 반환합니다. |
| string\[:\] | 문자열 전체를 반환합니다. 이 연산은 전체 문자열을 복사합니다. |
| string\[beg: end: step\] | beg부터 end 전까지 각 항목 간 step 간격의 문자를 반환합니다. |

문자열의 첫 문자와 마지막 문자를 제거하는 방법입니다.

```text
king_name = '"헨리 8세"'
new_str = king_name[1:-1]
print(king_name) # "헨리 8세"
print(new_str)   # 헨리 8세
```

슬라이싱 규칙입니다.

* beg와 end가 모두 양수 인덱스면 beg-end 수 만큼 문자를 반환합니다.
* 처음부터 N번째 문자까지 반환하려면 string\[:N\]을 사용합니다.
* 끝에서 마지막 N번째 문자까지 반환하려면 string\[-N-:\]을 사용합니다.
* 문자열 전체를 복사하려면 string\[:\]을 사용합니다.

슬라이싱은 선택 사항으로 세 번째 인수인 step 인수를 허용합니다. step 값을 음수로 넣는 것도 가능합니다. 문자열을 역순으로 순회하게 만드는 것입니다.

```text
def reverse_str(s):
    return s[::-1]

print(reverse_str('Hello'))  # olleH
print(reverse_str('Wolrd!')) # !dlroW
```

파이썬은 슬라이싱을 수행할 때 인덱스 범위를 벗어나더라도 예외를 발생시키지 않습니다.

## 단일-문자 함수\(문자 코드\)

길이가 1인 문자열과 함께 사용하는 함수입니다.

* ord\(단일문자\): 숫자 코드 반환
* chr\(숫자\): 아스키코드나 유니코드를 한 글자 문자로 변환

ord 함수는 한 문자를 인수로 받으며, 더 긴 문자를 입력하면 TypeError 예외를 발생시킵니다. chr 함수는 ord 함수와 정반대로 동작합니다.

```text
print(ord('A')) # 65
print(chr(65))  # 'A'
```

in과 not in 연산자는 주로 단일 문자와 함께 사용됩니다. 첫 문자가 모음인지 확인합니다.

```text
s = 'Elephant'
if s[0] in 'aeiouAEIOU':
    print('첫 문자가 모음입니다.)
```

in 연산자는 모든 문자열이 빈 문자열\(''\)을 포함하고 있다고 생각하는 반면, 리스트는 가지고 있지 않다고 간주합니다.

```text
print('' in 'cat') # True
print([] in [1, 2, 3]) # False
```

## join을 사용하여 만든 문자열

모든 알파벳 문자를 포함한 문자열을 만듭니다.

```text
n = ord('A')
s = ''
for i in range(n, n+26):
    s += chr(i)
print(s)                    # ABCDEFGHIJKLMNOPQRSTUVWXYZ
```

파이썬은 내부적으로 메모리에 완전히 새로운 문자열을 계속 반복하여 생성합니다.

이런 비효율을 개선하기 위해 join 메서드를 사용합니다.

```text
n = ord('A')
s_list = []
for i in range(n, n+26):
    s_list.append(chr(i))

s = ''.join(s_list)
print(s)
```

join을 사용한 비교 예입니다.

```text
def print_name(a_lst):
    s = ''
    for item in a_lst:
        s += item + ', '
    if len(s) > 0:
        s = s[:-2]
    print(s)

def print_join(a_lst):
    s = ', '.join(a_lst)
    print(s)
```

## 주요 문자열 함수

이 페이지에서 설명하는 함수는 사실 메서드이며, 클래스의 멤버 함수는 점 기호\(.\) 문법으로 호출할 수 있습니다.

메서드와 더불어 파이썬 언어에서는 기초 타입과 함께 사용할 수 있는 여러 내장 함수가 있습니다.

* input\(문자열\): 프롬프트에 출력할 문자열
* len\(문자열\): 문자 개수를 숫자로 반환
* max\(문자열\): 가장 높은 코드 값을 가진 문자 반환
* min\(문자열\): 가장 낮은 코드 값을 가진 문자 반환
* reversed\(문자열\): 역순 문자열을 지닌 이터레이터 반환
* sorted\(문자열\): 정렬된 문자열을 지닌 리스트 반환

```text
a_str = ''.join(reversed('Hello'))
print(a_str) # olleH
b_str = ''.join(sorted('hello'))
print(b_str) # ehllo
```

## 이진수와 10진수, 16진수 변환 함수

str 변환 함수와 더불어 파이썬은 숫자를 입력받아서 문자열 결과를 반환하는 함수 3개를 제공합니다. 각 함수는 선택한 진수를 표현한 숫자 형태의 문자열을 반환합니다.

* bin\(숫자\): 숫자의 이진수 반환
* hex\(숫자\): 숫자의 16진수 반환
* oct\(숫자\): 숫자의 8진수 반환

```text
print(bin(10), oct(10), hex(10))
0b1010 0o12 0xa
```

## Boolean\('is'\) 메서드

함수가 is로 시작하는 모든 메서드는 True나 False를 반환합니다. 긴 문자열과 함께 사용하는 경우 문자열 안의 모든 문자가 통과해야 True를 반환합니다.

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#xBA54;&#xC11C;&#xB4DC; &#xC774;&#xB984;/&#xBB38;&#xBC95;</th>
      <th style="text-align:left">&#xD14C;&#xC2A4;&#xD2B8; &#xD1B5;&#xACFC; &#xC2DC; True &#xBC18;&#xD658;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">str.isalnum()</td>
      <td style="text-align:left">&#xBAA8;&#xB4E0; &#xBB38;&#xC790;&#xAC00; &#xAE00;&#xC790;&#xC640; &#xC22B;&#xC790;&#xB85C;
        &#xC774;&#xB8E8;&#xC5B4;&#xC84C;&#xC73C;&#xBA70;, &#xCD5C;&#xC18C;&#xD55C;
        &#xBB38;&#xC790;&#xAC00; &#xD558;&#xB098; &#xC774;&#xC0C1; &#xC788;&#xB294;
        &#xACBD;&#xC6B0;</td>
    </tr>
    <tr>
      <td style="text-align:left">str.isalpha()</td>
      <td style="text-align:left">&#xBAA8;&#xB4E0; &#xBB38;&#xC790;&#xAC00; &#xC54C;&#xD30C;&#xBCB3; &#xAE00;&#xC790;&#xB85C;
        &#xC774;&#xB8E8;&#xC5B4;&#xC84C;&#xC73C;&#xBA70;, &#xCD5C;&#xC18C;&#xD55C;
        &#xBB38;&#xC790;&#xAC00; &#xD558;&#xB098; &#xC774;&#xC0C1; &#xC788;&#xB294;
        &#xACBD;&#xC6B0;</td>
    </tr>
    <tr>
      <td style="text-align:left">str.isdecimal()</td>
      <td style="text-align:left">
        <p>&#xBAA8;&#xB4E0; &#xBB38;&#xC790;&#xAC00; 10&#xC9C4;&#xC218; &#xC22B;&#xC790;&#xB85C;
          &#xC774;&#xB8E8;&#xC5B4;&#xC84C;&#xC73C;&#xBA70;, &#xCD5C;&#xC18C;&#xD55C;
          &#xBB38;&#xC790;&#xAC00; &#xD558;&#xB098; &#xC774;&#xC0C1; &#xC788;&#xB294;
          &#xACBD;&#xC6B0;</p>
        <p>isdigit&#xACFC; &#xBE44;&#xC2B7;&#xD558;&#xC9C0;&#xB9CC; &#xC720;&#xB2C8;&#xCF54;&#xB4DC;
          &#xBB38;&#xC790;&#xC640; &#xC0AC;&#xC6A9;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">str.isdigit()</td>
      <td style="text-align:left">&#xBAA8;&#xB4E0; &#xBB38;&#xC790;&#xAC00; 10&#xC9C4;&#xC218; &#xC22B;&#xC790;&#xB85C;
        &#xC774;&#xB8E8;&#xC5B4;&#xC84C;&#xC73C;&#xBA70;, &#xCD5C;&#xC18C;&#xD55C;
        &#xBB38;&#xC790;&#xAC00; &#xD558;&#xB098; &#xC774;&#xC0C1; &#xC788;&#xB294;
        &#xACBD;&#xC6B0;</td>
    </tr>
    <tr>
      <td style="text-align:left">str.isidentifier()</td>
      <td style="text-align:left">
        <ul>
          <li>&#xBB38;&#xC790;&#xC5F4;&#xC774; &#xC720;&#xD6A8;&#xD55C; &#xD30C;&#xC774;&#xC36C;
            &#xC2DD;&#xBCC4;&#xC790; &#xC774;&#xB984; &#xADDC;&#xCE59;&#xC744; &#xC9C0;&#xD0A4;&#xACE0;
            &#xC788;&#xB294; &#xACBD;&#xC6B0;</li>
          <li>&#xCCAB; &#xBB38;&#xC790;&#xB294; &#xBC18;&#xB4DC;&#xC2DC; &#xBB38;&#xC790;&#xB098;
            &#xC5B8;&#xB354;&#xC2A4;&#xCF54;&#xC5B4;&#xC774;&#xC5B4;&#xC57C; &#xD558;&#xBA70;,
            &#xAC01; &#xBB38;&#xC790;&#xB294; &#xAE00;&#xC790;, &#xC22B;&#xC790; &#xD639;&#xC740;
            &#xC5B8;&#xB354;&#xC2A4;&#xCF54;&#xC5B4;&#xC774;&#xC5B4;&#xC57C; &#xD55C;&#xB2E4;.</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">str.islower()</td>
      <td style="text-align:left">&#xBAA8;&#xB4E0; &#xBB38;&#xC790;&#xAC00; &#xC18C;&#xBB38;&#xC790;&#xB85C;
        &#xC774;&#xB8E8;&#xC5B4;&#xC84C;&#xC73C;&#xBA70;, &#xCD5C;&#xC18C;&#xD55C;
        &#xBB38;&#xC790;&#xAC00; &#xD558;&#xB098; &#xC774;&#xC0C1; &#xC788;&#xB294;
        &#xACBD;&#xC6B0;</td>
    </tr>
    <tr>
      <td style="text-align:left">str.isprintable()</td>
      <td style="text-align:left">
        <ul>
          <li>&#xBAA8;&#xB4E0; &#xBB38;&#xC790;&#xAC00; &#xCD9C;&#xB825; &#xAC00;&#xB2A5;&#xD55C;
            &#xBB38;&#xC790;&#xC778; &#xACBD;&#xC6B0;</li>
          <li>\n&#xACFC; \t&#xB294; &#xC81C;&#xC678;</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">str.isspace()</td>
      <td style="text-align:left">&#xBAA8;&#xB4E0; &#xBB38;&#xC790;&#xAC00; &apos;&#xACF5;&#xBC31; &#xBB38;&#xC790;(whitespace)&apos;&#xC774;&#xBA70;,
        &#xCD5C;&#xC18C;&#xD55C; &#xBB38;&#xC790;&#xAC00; &#xD558;&#xB098; &#xC774;&#xC0C1;
        &#xC788;&#xB294; &#xACBD;&#xC6B0;</td>
    </tr>
    <tr>
      <td style="text-align:left">str.istitle()</td>
      <td style="text-align:left">
        <ul>
          <li>&#xBAA8;&#xB4E0; &#xBB38;&#xC790;&#xAC00; &#xC720;&#xD6A8;&#xD55C; &#xC81C;&#xBAA9;&#xC774;&#xBA70;,
            &#xCD5C;&#xC18C;&#xD55C; &#xBB38;&#xC790;&#xAC00; &#xD558;&#xB098; &#xC774;&#xC0C1;
            &#xC788;&#xB294; &#xACBD;&#xC6B0;</li>
          <li>&#xCCAB; &#xBB38;&#xC790;&#xB9CC; &#xB300;&#xBB38;&#xC790;&#xACE0; &#xB098;&#xBA38;&#xC9C0;&#xB294;
            &#xBAA8;&#xB450; &#xC18C;&#xBB38;&#xC790;&#xBA74; &#xC870;&#xAC74;&#xC5D0;
            &#xB9CC;&#xC871;&#xD55C;&#xB2E4;. &#xBB38;&#xC790; &#xC0AC;&#xC774;&#xC5D0;
            &#xACF5;&#xBC31; &#xBB38;&#xC790;&#xB098; &#xAD6C;&#xBD84; &#xBB38;&#xC790;&#xAC00;
            &#xC788;&#xC744; &#xC218; &#xC788;&#xB2E4;.</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">str.isupper()</td>
      <td style="text-align:left">&#xBAA8;&#xB4E0; &#xBB38;&#xC790;&#xAC00; &#xB300;&#xBB38;&#xC790;&#xB85C;
        &#xC774;&#xB8E8;&#xC5B4;&#xC84C;&#xC73C;&#xBA70; &#xCD5C;&#xC18C;&#xD55C;
        &#xBB38;&#xC790;&#xAC00; &#xD558;&#xB098; &#xC774;&#xC0C1; &#xC788;&#xB294;
        &#xACBD;</td>
    </tr>
  </tbody>
</table>

## 대소문자 변환 메서드

다음 메서드들은 대소문자를 변환하여 새로운 문자열을 만듭니다.

* 문자열.lower\(\): 모두 소문자인 문자열을 생성합니다.
* 문자열.upper\(\): 모두 대문자인 문자열을 생성합니다.
* 문자열.title\(\): 'foo foo'.title\(\) =&gt; 'Foo Foo'
* 문자열.swapcase\(\): 대소문자를 서로 변경합니다.

## 검색-교체 메서드

검색-교체 메서드는 str 클래스 메서드 중 가장 유용합니다. startswith와 endswith를 먼저 살펴보겠습니다.

* 문자열.startswith\(부분문자열\): 접두사를 찾으면 True 반환
* 문자열.endswith\(부분문자열\): 접미사를 찾으면 True 반환

M으로 시작하는 문자열에서 첫 번째 문자를 제거합니다.

```text
if test.startswith('M'):
    test = test[1:]
```

그 외 검색-교체 메서드입니다.

* 문자열.count\(부분문자열 \[, 시작 \[, 종료\]\]\)
* 문자열.find\(부분문자열 \[, 시작 \[, 종료\]\]\)
* 문자열.index\(\): find와 같지만 예외 발생
* 문자열.rfind\(\): find와 같지만 끝에서부터 검색
* 문자열.replace\(기존, 신규 \[, 제한횟수\]\): count는 선택적으로 추가, 교체 횟수를 제한

문법에서 등장하는 대괄호는 선택 사항으로 추가할 수 있는 항목입니다.

```text
frank_str = 'doo be doo be doo...'
print(frank_str.count('doo')) # 3
print(frank_str.count('doo',1)) #2
print(frank_str.count('doo',1,10)) # 1

print(frank_str.find('doo')) # 0 출력, 첫 번째 위치를 양수 인덱스로 반환
print(frank_str.find('doob')) # -1 출력, 찾지 못함
```

부분문자열이 나타나는 모든 위치를 알고자 합니다.

```text
frank_str = 'doo be doo be doo...'
n = -1

while True:
    n = frank_str.find('doo', n+1)
    if n == -1:
        break
    print(n, end=' ')
    
# 0 7 14 출
```

index와 rfind 메서드는 find 메서드와 거의 똑같지만 index 메서드는 부분문자열을 찾지 못하면 -1을 반환하지 않고 ValuerError 예외를 발생시킵니다.

replace 메서드는 문자열에 포함된 기존 값을 신규 값으로 모두 변경합니다.

```text
title = 'Greyer Into Grey'
new_title = title.replace('Grey', 'Gray')
print(new_title) # Grayer Into Gray
```

## split을 활용한 입력 값 쪼개기

입력 받은 문자를 다루는 가장 흔한 프로그래밍 작업은 토큰화입니다. 입력받은 문장을 각 단어, 구, 숫자로 분리합니다. 파이썬 split 메서드는 이 토큰화 작업을 쉽고 편리하게 해줍니다.

* 입력문자열.split\(구분문자열=None\)

```text
print('Moe Larry Curly Shemp'.split()) # ['Moe', 'Larry', 'Curly', 'Shemp']
print('Moe, Larry, Curly, Shemp'.split(', ')) # ['Moe', 'Larry', 'Curly', 'Shemp']
print('Moe, Larry, Curly, Shemp'.split(',')) # ['Moe', ' Larry', ' Curly', ' Shemp']
```

구분 문자열이 명시되면 정확하게 구분되어야 한다. 여분의 스페이스를 원하지 않는다면 문자 제거하기 기능을 사용해야 합니다.

## 앞뒤 문자 제거하기

사용자나 텍스트 파일로부터 문자열을 입력받으면 앞뒤에 붙은 빈칸을 제거하여 원하는 형태로 변경해야 할 경우가 있습니다.

* 문자열.strip\(제거문자열=' '\): 앞뒤 문자 지우기
* 문자열.lstrip\(제거문자열= ' '\): 앞 문자 지우기
* 문자열.rstrip\(제거문자열=' '\): 뒤 문자 지우기

예로 '\*+0'이 주어지면 메서드는 앞뒤의 별포 기호를 제거하고, 숫자 0을 제거한 후 더하기 기호도 제거합니다.

## 자리 맞춤 메서드

정교한 텍스트 포맷팅이 필요하다면 format 메서드 또는 기호를 사용해야 합니다. 하지만 str 클래스는 텍스트 자리 맞춤\(justification\)을 위한 가장 기초적인 기법을 제공합니다.

* 문자열.ljust\(길이 \[, 채우기\_문자\]\): 왼쪽 자리 맞춤
* 문자열.rjust\(길이 \[, 채우기\_문자\]\): 오른쪽 자리 맞춤
* 문자열.center\(길이 \[, 채우기\_문자\]\): 텍스트를 가운데에 위치
* 숫자\_문자열zfill\(길이\): 숫자 0 채우기

str의 텍스트는 인수 길이 크기에 명시한 더 큰 출력 필드에 위치합니다.

```text
new_str = 'Help!'.center(10, '#')
print(new_str) # ##Help!### 출력

new_str = '750'.rjust(6, '0')
print(new_str) # 000750 출력

s = '12'
print(s.zfill(5)) # 00012 출력
```

zfill 메서드는 채워진 '0'은 숫자의 일부가 되며, '0'은 숫자와 기호 사이에 위치합니다.

```text
>>> '-3'.zfill(5)
>>> '-0003'
>>> '-3'.rjust(5, '0')
>>> '000-3'
```

## 

