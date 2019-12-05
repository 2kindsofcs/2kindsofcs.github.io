---
layout: post
title: "파이썬 리스트에서 요소들은 연속된 메모리에 위치할까?"
tags: [python]
---

**TL;DR**  
아니오.


약 반 년 전에 들었던 궁금증이 불현듯 아침에 다시 떠올라서 찾아보았다.
전통적인 의미에서의 배열을 보면 각 요소들을 연속된 메모리에 저장하기 때문에 검색이 O(1)로 매우 빠르다고 한다.
나는 프로그래밍을 python3으로 시작했고, 파이썬에는 리스트만 있기 때문에 자연스럽게 질문이 떠올랐다.  

> "그러면 파이썬 리스트에서 요소들은 메모리에 연속적으로 위치할까?"

[파이썬 소스코드](https://github.com/python/cpython/blob/99eb70a9eb9493602ff6ad8bb92df4318cf05a3e/Include/listobject.h#L5)의 주석을 보면, 답이 나와 있다.  

> Another generally useful object type is a list of object pointers.

포인터들은 연속된 메모리에 존재하지만, 요소들이 연속된 메모리에 존재한다고 말할 수는 없다.  
즉 요소의 메모리 주소를 저장하는 공간들이 연속적인 것이다.
