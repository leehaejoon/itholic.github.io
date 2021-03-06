---
title: "[python] closure (클로저)"
layout: post
tag:
- python
category: python
author: itholic
sitemap:
  changefreq: daily
  priority: 1.0
---

# closure

파이썬은 함수 내부에 함수를 선언할 수 있다.

그리고 선언한 함수를 리턴값으로 그대로 던져줄 수도 있다.

```python
def outer_func(name, age):
    name = name
    age = age

    def inner_func():
        print("my name is {}, and i am {} years old".format(name, age))

    return inner_func  # 함수 자체를 반환한다
```

이 때, inner_func를 내부함수라고 하고, outer_func를 외부함수라고 한다.

이런게 가능한 이유는 파이썬에서 함수를 <a href="https://itholic.github.io/etc-first-class-citizen/" target="_blank">First-Class citizen(일급 객체)</a>으로 취급하기 때문이다.

이처럼 함수의 반환값으로 내부 함수를 리턴하는 함수를 클로저라고 한다.

사실 사전적으로 클로저란 **"first-class function을 지원하는 언어에서 유효 범위 이름을 바인딩하는 기술"**인데, 

복잡하니 그냥 반환값으로 내부함수를 리턴하는 함수라고 알고있어도 무관하다.

다만 한가지 중요한 점은, 

**외부함수가 내부함수를 리턴하고 종료하더라도 리턴된 내부함수는 여전히 외부함수의 변수를 사용 가능하다는 것이다.**

말로만하니 점점 머리가 아파온다.

코드로 알아보자.

```python
# -*- coding:utf-8 -*-

def outer_func(name, age):
    name = name
    age = age

    def inner_func():
        print("my name is {}, and i am {} years old".format(name, age))

    return inner_func  # 함수 자체를 반환한다

inner = outer_func("sowut", "29")  # outer_func는 inner_func를 반환하고 종료됨
inner()  # my name is sowut, amd i am 29 years old

# outer_func가 종료되었음에도 불구하고, 여전히 outer_func의 변수인 name과 age를 참조한다
# 이는 클로저가 inner_func의 프리변수인 name과 age를 어딘가에 저장해놓기 때문에 가능하다
# 그 '어딘가'는 바로 이곳이다.
print(of.__closure__[0].cell_contents)  # 29
print(of.__closure__[1].cell_contents)  # sowut
```
프리변수라는 말을 썼는데, 내부함수의 입장에서 본인이 선언하지 않은 외부함수의 변수를 의미한다.

예제에서는 name, age가 inner_func 입장에서 프리변수이다.

어쨌든 코드를 보면 outer_func에서 리턴으로 inner_func를 던지고있다.

그리고 리턴된 inner_func를 inner라는 변수에 담아서 호출했다.

이 때 주목할점은 outer_func가 inner_func를 반환한 후에 종료되었음에도 불구하고,

**리턴된 inner_func는 여전히 outer_func의 내부 변수에 접근이 가능하다는 점이다.**

참고로, inner_func는 outer_func의 리턴 값으로 사용되고 있으므로 <a href="https://itholic.github.io/etc-higer-order-function/" target="_blank">Higher-Order Function</a>이다.

다음과 같이 응용 가능하다
```python
def tag_maker(tag):
    tag = tag

    def contents_inserter(contents):
        contents = contents
        print("<{0}>{1}</{0}>".format(tag, contents))

    return contents_inserter

head_contents_inserter = tag_maker("head")  # contents_inserter 리턴
body_contents_inserter = tag_maker("body")  # contents_inserter 리턴

head_contents_inserter("i am head")  # <head>i am head</head>
body_contents_inserter("i am body")  # <body>i am body</body>
```



