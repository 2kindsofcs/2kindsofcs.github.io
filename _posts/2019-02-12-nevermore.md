---
layout: post
title: "2019-02-12-TIL minimal mistake-diqus의 난"
slug: "190212-TIL"
tags: [TIL]
---

## Jekyll

* 블로그에 댓글달 수 있게 disqus 한번 붙이겠다고 끙끙대다 몇 시간을 날렸다. 앞으로 이런 작은 일에 쓸데없이 시간을 소모하지 않도록 주의해야겠다.  

* 분명 minimal mistake 테마 제작자의 가이드대로 _config.yml을 설정했지만 계속 disqus가 보이지 않았다. 알고보니 작성하는 포스트의 layout을 front matter로 지정해주고 있는데, single이 아니라 post라고 해둔 탓이었다. _config.yml에서 포스트의 layout 디폴트 값을 single로 해두고서 말이다.  

* 포스트의 front matter가 _config.yml의 디폴트값보다 우선시 되는 것이었다. _config.yml에서 한 줄만 수정하면 될 것을 몇 시간을 끌었다. 

### 앞으로 어떤 문제를 해결하는데 금방 해결되지 않을 경우 아래와 같이 대처할 것이다.

1. 나와 같은 문제를 겪고 있는 사람을 찾는다. (github issue, stackoverflow...)

2. 해결 방법을 완전히 똑같이 따라해본다. 

3. 그래도 해결되지 않을 경우, 주변 전문가에게 도움을 요청한다.  

오늘 disqus 사태의 경우, 1번에는 문제가 없었다. 나와 동일한 문제를 겪고 있는 사람을 쉽게 발견할 수 있었기 때문이다. 거기다 minimal mistake 테마 제작자의 답변까지 달려 있었다. 하지만 2번을 충실히 이행하지 않았기 때문에 시간이 많이 소요되었다. 이제서야 드는 생각이지만, 계속 _config.yml과 죄없는 single.html을 만질 것이 아니라 그냥 문제를 해결했다는 사람의 블로그 글 하나를 그대로 긁어서 똑같이 post로 만들어보고 빌드되는 걸 확인했다면 무엇이 문제인지 훨씬 빨리 해결할 수 있었을 것이다. 해결 방법을 말 그대로 **완전히 똑같이** 따라해볼 필요가 있었다.     

결국 동아리 선배님께 도움을 요청했고, 선배분이 도와주셔서 한참 나중에야 문제를 해결할 수 있었다.  


## javascript/자료구조

* stack: 가장 윗 부분에서만 자료의 추가와 삭제가 일어나므로 추가/삭제 속도가 빠르다. 소위 후입선출(LIFO, *last-in, first-out*) 자료구조이다.  

* class로 stack을 만들고 진법 변환, 회문 검사, 팩토리얼 계산을 하는 함수들을 구현해보았다. 


## HTML  

* iframe: 웹페이지 안에 웹페이지를 보여주기 위해 사용하는 태그이다. 
iframe에 name attribute를 주고 a에 동일한 문자열로 target attribute를 줄 경우 iframe 안에서 해당 링크가 열린다.

{% raw %}
```html
<iframe src="demo_iframe.htm" name="iframe_a"></iframe>
<p><a href="https://www.w3schools.com" target="iframe_a">W3Schools.com</a></p>
```
{% endraw %}











