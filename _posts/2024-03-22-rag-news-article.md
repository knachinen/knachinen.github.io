---
layout: single
title: "RAG :: 기사에서 정보 추출하기"
tag: [rag, news, article, civic-hacking]
categories: [til]
toc: true
sidebar:
  nav: "docs"
---

bs4, selenium 으로 290여개의 기사에서 텍스트를 가져왔다.  
매체가 모두 다르다보니, 본문만 가져오는 게 편하지 않았다.  
그렇다고 매체마다 다르게 하려니 좀 번거롭다.  

그냥 전체를 텍스트로 변환하여 gemma 모델로 rag 를 해봤는데,  
정보를 제대로 추출하지 못한다.  
OpenAI GPT 3.5 로 해보니, 너무... 잘 된다.  

...  

텍스트를 정제해보기로 햇다.  
살펴보니 본문 앞 뒤로 '\n' 이 두 개 이상 반복된다.  

```
\n\n\n\n\n\n\n\n2024.02.05 19:48\n\n\n\n\n문서\n\n\n프린트\n\n\n글자크기\n\n\n\n\n글자크기조절\n\n\n\n가나다라마\n\n\n\n가나다라마\n\n\n\n가나다라마\n\n\n\n가나다라마\n\n\n\n가나다라마\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n \n\n술에 취해 아무런 이유 없이 행인들을 폭행해 1명을 중태에 빠뜨리고...
```

```python
def longest_string_length(strings):
    """
    Find the length of the longest string in a list of strings.

    Parameters:
    - strings (list): List of strings.

    Returns:
    - int: Length of the longest string.
    """
    return max(strings, key=len)
```

```python
    df.loc[index, "content_filtered"] = longest_string_length(df.loc[index, "content"].split("\n\n"))
```

텍스트를 '\n\n' 으로 나누고, 가장 큰 길이를 가진 텍스트를 본문이라고 추정한다.

```python
res_json = rag.invoke("""
기사에서 누가, 언제, 어디서, 무엇에 대한 정보를 json 포맷으로 추출해줘.

{
    "who": "",
    "when": "",
    "where": "",
    "what": "",
}
""")
```

```python
'result': '{\n    "who": "A씨",\n    "when": "10월18일 오후 9시30분쯤",\n    "where": "부산 중구의 한 길거리",\n    "what": "폭행 혐의를 받았으며 60대 남성 B씨를 의식불명 상태에 빠뜨린 것으로 전해졌다."\n}'
```
 
엇, 결과가 꽤 괜찮다!
