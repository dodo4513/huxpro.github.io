---
layout:     post
title:      "Python으로 알고리즘 문제 풀기"
subtitle:   "Python3 초보자를 위한 아주 간단한 안내"
date:       2017-7-02
author:     "Xavy"
catalog:    true
tags:
    - Python
    - Algorithm
---

### 시작하기 전에

서버를 운영하다보면 리눅스 쉘의 도움을 얻어야 할 경우가 굉장히 많다. 
나같은 경우엔 서버가 2대 이상인 경우에 무중단 배포를 적용하고자 간단한 쉘 스크립트를 작성한 경험이 있다.
그렇지만 잘 알지도 못하는 언어로 작성하자니 죽을 맛이더라. 
앞으로도 서비스 운영에 종종 사용해야할 일이 생길것 같아 파이썬 공부를 시작했다.

지금까지 주로 C/C++로 알고리즘을 풀었는데, Python으로는 처음 하다보니 input/output부터 낯설어서 진도가 아주 지지부진했다.
아래의 것들만 알아도 어느정도 문제는 풀 수 있을 것이다.

- **이하 모두 Python3 문법 기준입니다!**
- \#은 주석입니다.
- 혹 잘못된 점이나 더 좋은 방법을 알려준다면 굉장히 고마울겁니다!

제가 이 포스트를 잊어버린게 아니라면, 공부하면서 틈틈히 추가할 예정입니다.

### Python으로 알고리즘 시작하기

###### 1. Hello world 출력

```python
print("Hello world")
```

아주 간단하다.
딱히 import 할 것없이 그냥 `print()`를 사용하면 된다.

※ print()는 문자열을 출력하고 줄바꿈이 자동으로 일어나게 되는데, 이를 제거하려면 `print("hello world", end="")` 이렇게 사용하면 된다.

###### 2. 한줄 단위로 입력 받기

```text
# input
1 2 3
``` 

**A.** 기본적으로 `input()`을 사용해 값을 입력 받는다. 한 줄을 모두 읽는다.

```python
a = input()
print(a) # 1 2 3
```

**B.** Python은 하나의 함수가 여러개의 리턴값을 가질 수 있는 것처럼, 아래처럼도 가능하다.

 ```python
a, b, c = input().split()
print(a) # 1
print(b) # 2
print(type(c)) # 3
```

**C.** input()으로 값을 받는 경우, str type을 반환하는데 `map()`을 사용하면 이를 int 형으로 변경해서 변수에 담을 수도 있다.

```python
a, b, c = map(int, input().split())
print(a+b+c) # 6
```

###### 3. 끝을 모르는 입력 받기

**A.** `sys의 read()`를 이용한다. 해당 함수는 input()처럼 한줄을 읽는 것이 아니라 입력의 끝(EOF)까지 읽는다.
```python
import sys
p = sys.stdin.read()
sys.stdout.write(p)
```

**B.**  무식하지만 EOF에러가 날 때까지 입력받고 출력하는 것도 가능하다. 
python은 Java나 c/c++과는 다르게 스크립트 언어라서 답을 모두 출력하고 프로그램이 에러로 종료된다. 
(백준 저지에서는 아래와 같은 방식으로 문제를 풀어도 통과할 수 있었음) 

```python
while True:
    try:
        print(input())
    except EOFError:
        break
```

###### for문 사용하기 

**A.** 기본적으로 `for e in list:` 으로 list를 모두 순회할 수 있다.
```python
list=[1,2,3]
for var in list:
    print(var)
    # print 1 2 3
```

**B.** 범위로 리스트를 순회할 땐 숫자 리스트를 쉽게 만들어주는 `range()`를 사용한다.
```python
for var in range(1,5):
    print(var)
    # print 1 2 3 4
```

**C.** 순회를 하면서 index가 필요한 경우에는 `enumerate()`를 사용한다.
```python
string = {"가위", "풀", "종이"}
for i, v in enumerate(string):
    print(v, end="는 ")
    print(i, end="번\n") #print 풀는 0번 종이는 1번 가위는 2번
```    

### 참고

[알고리즘 트레이닝 사이트 - 백준 저지](https://www.acmicpc.net/)