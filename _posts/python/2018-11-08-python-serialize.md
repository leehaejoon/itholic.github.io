---
title: "[python] serialize (직렬화)"
layout: post
tag:
- python
category: python
author: itholic
sitemap:
  changefreq: daily
  priority: 1.0
---

# 직렬화

햄버거를 생각해보자.

우선 맨 밑에 빵이 있고, 그 위로 각종 재료가 올려질 것이다.

그리고 맨 위에는 다시 빵을 덮어서 마무리가 되어있을 것이다.

<br/>

이제 이 햄버거를 컨베이어 벨트에 올려서 어딘가로 전달해야 하는데,

컨베이어 벨트가 통과하는 통로가 너무너무 낮아서,

재료를 모두 분해해 컨베이어 벨트에 한줄로 올려야한다고 가정하자.

그럼 받는 쪽에서는 분리되어서 온 재료를 다시 조합해 완성된 햄버거를 만든다.

<br/>

예제가 다소 억지스럽긴 했지만, 

이것이 바로 직렬화 (및 역직렬화)다.

<br/>

햄버거를 객체라고 생각하면,

우선 이 객체를 어디론가 전달하기 위해 그 구성요소들을 한 줄의 바이트로 줄세운다(직렬화, 인코딩). 

그리고 줄 세운 데이터를 스트림(파일이나 네트워크)을 통해 전달하는 것이다.

그리고 스트림을 통해 전달받은 쪽에서는 바이트화된 객체를 다시 원래의 형태로 되돌린다(역직렬화, 디코딩).

<br/>

조금 더 공학적으로 생각해보자.

파일이나 네트워크를 통해 데이터가 왔다갔다하는 길(스트림)은, 

데이터가 한 번에 한 바이트씩밖에 통과할 수 없는 아주 좁은 통로이다.

하지만 객체화되어있는 데이터는 입체적인 구조로 되어있어서 이 좁은 통로를 통과할 수 없다.

그렇기 때문에 데이터가 스트림을 통해 통신이 가능하도록 한 줄로 줄세우는 작업이 필요한 것이다.

<br/>

그리고 이 때, 

어떤 데이터를 맨 앞에 줄세우고, 어떤 데이터를 그 다음에 세울지 일련의 순서를 정하는 규칙이 존재한다.

햄버거로 비유하자면 맨 바닥에 깔릴 재료를 제일 먼저 보내고, 그 다음 쌓여질 순서대로 줄을 세우겠다거나,

반대로 맨 위에 올려질 재료를 제일 먼저 보내고, 맨 바닥에 깔릴 재료를 제일 마지막에 보낸다거나 하는 규칙이 있을것이다.

전송하는 쪽과 받는 쪽이 이렇게 동일한 규칙을 공유하고 있어야 원활한 통신이 가능하다.

<br/>

즉, 직렬화란 "스트림 전송을 위해 일정한 규칙에 의해 데이터를 일련의 바이트로 변형하는 작업" 이다.

<br/>

이제 예를 들어보자.

사실 이전에 다룬 <a href="https://itholic.github.io/python-pickle/" target="_blank">Pickle</a>과  <a href="https://itholic.github.io/python-json/" target="_blank">JSON</a> 에서 이미 직렬화/역직렬화를 사용했었다.

직렬화는 "일정한 규칙"에 의해 데이터를 바이트로 변형하는 것 이라고 설명했었다.

pickle, JSON같은 개념들이 바로 그 "일정한 규칙"들 중 하나이다.

<br/>

즉, 

직렬화를 하는 방식은 그 규칙에 따라 여러 방식이 있으며,  

pickle, JSON은 여러 직렬화 방식 중 하나이다.

<br/>

이미 한 번 다루긴 했지만,

직렬화에 초점을 맞추어 다시 한 번 간단하게 살펴보자.

## Pickle

이전에 사용했던 pickle 예제를 가져와보자.

```python
  1 # -*- coding:utf-8 -*-
  2
  3 import pickle
  4
  5 fruit_store = {'apple':1000, 'banana':2000, 'tomato':'soldout', 'melon':'5000'}
  6
  7 # pickle로 파일 쓰기
  8 with open('./data/fruit.txt','wb') as f:
  9     pickle.dump(fruit_store,f)
 10
 11 # pickle로 쓰인 파일 읽기
 12 with open('./data/fruit.txt','rb') as f:
 13     data = pickle.load(f)
```

5번째 줄에 선언한 fruit_store라는 딕셔너리 객체를 직렬화하는 예제이다.

7번쨰 줄에 'pickle로 파일 쓰기'라고 되어있는데,

이를 다시 말하면 'pickle로 데이터 직렬화 해서 파일에 쓰기'와 같다.

그리고 11번째 줄의 'pickle로 쓰인 파일 읽기'는,

'pickle로 직렬화된 데이터를 읽어서 역직렬화 하기'와 같다.

9번째 줄에서 pickle.dump를 하면 fruit.txt 파일에 직렬화된 fruit_store 딕셔너리가 쓰여지는데,

해당 파일을 열어보면 다음과 같이 바이트로 직렬화된 것을 직접 확인 가능하다.

```
(dp0
S'tomato'
p1
S'soldout'
p2
sS'melon'
p3
S'5000'
p4
sS'apple'
p5
I1000
sS'banana'
p6
I2000
s.
```

뭔진 모르겠지만 어쨌든 pickle이 정해놓은 규칙에 의해 직렬화가 된 것을 확인할 수 있다.

## JSON

동일한 상황을 JSON으로 처리해보자.

```python
  1 # -*- coding:utf-8 -*-
  2
  3 import json
  4
  5 fruit_store = {'apple':1000, 'banana':2000, 'tomato':'soldout', 'melon':'5000'}
  6
  7 # json으로 파일 쓰기
  8 with open('./data/fruit.txt','wb') as f:
  9     json.dump(fruit_store,f)
 10
 11 # json으로 쓰인 파일 읽기
 12 with open('./data/fruit.txt','rb') as f:
 13     data = json.load(f)
```

실수로 똑같은 코드를 갖다 붙인 것 처럼 완전히 똑같은 형태이다.

자세히보면 완전 동일한 코드에서 pickle만 json으로 바뀌었다.

마찬가지로 직렬화된 파일을 열어보면 다음과 같은 형태이다.

```
{"tomato": "soldout", "melon": "5000", "apple": 1000, "banana": 2000}
```

pickle로 직렬화 했을 때보다 훨씬 직관적이지 않은가?

직렬화된 데이터가 거의 원본과 동일한 형태이다.

json.dump 명령어 뒤에 다음과 같이 indent 옵션을 주면 다음과 같이 더 깔끔한 형태로 직렬화도 가능하다.

```python
  9     json.dump(fruit_store,f,indent=4)
```

직렬화된 데이터를 보자.

```
{
    "tomato": "soldout",
    "melon": "5000",
    "apple": 1000,
    "banana": 2000
}
```

즉, JSON으로 객체를 직렬화하면 Pickle보다 사람이 볼 때 훨씬 가독성이 좋다는 장점이 있다.

하지만 속도면에서만 보자면 Pickle이 훨씬 빠르므로, 상황에 따라 취사선택을 해야한다.

## 결론

앞선 예제에서 봤듯이 직렬화 방식에는 여러가지가 있다.

해당 예제에서는 Pickle과 JSON만 다루었지만,

YAML이나 CSV같은 직렬화 방식도 많이 사용한다.

각 직렬화 방식은 Pickle과 JSON처럼 장단점이 있으므로 상황에따라 필요한 직렬화를 적절히 사용해주어야한다.

<br/>

예를들어 Pickle은 가독성이 떨어지는 대신 직렬화/역직렬화 속도가 JSON에 비해 빠르기때문에, 

어떤 객체가 단순히 프로그램 동작 과정에서만 사용된다면 JSON보다는 Pickle이 좋다.

하지만 log나 설정파일 처럼 사람이 직접 확인하기 용이해야하는 부분에서는 JSON을 사용하면 된다.

참고로 복잡한 설정파일의경우 가독성이 아주 중요하므로, 

JSON보다 더욱 가독성이 좋은 YAML같은 직렬화 방식을 고려해보는 것도 좋다.

또한 REST API응답용으로는 YAML보다는 구조 파악이 용이한 JSON을 고려해보는 것이 좋다.