---
layout: post
title: "jekyll 지킬 카테고리 추가하기"
slug: "making-categories"
tags: [TIL]
---

우여곡절 끝에 지킬과 깃헙 페이지를 연결하여 블로그를 만드는 데 성공하였다. 
본격적으로 글들을 작성하기에 앞서,
포스트를 작성할 때 카테고리(일종의 태그)를 표시하고자 했다. 그리고 카테고리를 클릭하면 해당 카테고리에 속하는 글들을 볼 수 있는 페이지로 연결되게끔 링크를 걸어주고자 했다.

구글에 검색을 해보니 지킬에 카테고리를 추가하는 방법에 대해 기술해놓은 블로그들이 많이 있었다.
하지만 다들 방법이 달랐고, 이해할 수 없는 부분들도 더러 있어서 그대로 실행하기에는 적절치 못했다.
그래도 혹시 몰라 링크를 적어둔다.

* <https://kylewbanks.com/blog/creating-category-pages-in-jekyll-without-plugins>
* <https://blog.webjeda.com/jekyll-categories/>

참고로 아래와 같은 코드는 제대로 작동하지 않는다

 {% raw %} 
`{{page.categories | capitalize | join: ', '}}`
 {% endraw %} 

리퀴드에서 배열에는 capitalize를 적용할 수 없다. 


일단 개인적으로는 아래 블로그에서 실질적인 도움을 많이 받았다.
* [https://jeesub.github.io/blog/jekyll-카테고리 -만들기](https://jeesub.github.io/blog/jekyll-%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC-%EB%A7%8C%EB%93%A4%EA%B8%B0/)

결과적으로 내가 사용한 방법은 다음과 같다.

1 root 디렉토리에 **category 폴더**를 만든다.

2 만든 category 폴더 안에 원하는카테고리명.html 파일을 만들어준다.
파일 내용은 아래와 같은 식으로 적어줬다. 


 {% raw %} 
```
---
layout: default
---
<ul>
	{% for post in site.categories.원하는카테고리명 %}
	<li><a href="{{ post.url }}">{{ post.title }}</a></li>
	{% endfor %}
</ul>
```
 {% endraw %} 


3 _layouts 폴더의 post.html에 아래와 같은 코드를 추가하였다. 이렇게하면 포스트 내용 마지막에 해당 포스트가 속한 카테고리가 출력되며, 이를 클릭하면 해당 카테고리의 글 목록 페이지로 이동할 수 있다. (이 부분은 학교 선배님 한 분이 감사하게도 도와주셨다.)

 {% raw %} 
```
{% for category in page.categories %}
  <a href="{{site.baseurl}}/category/{{category|slugize}}">{{category|capitalize}}</a>
{% unless forloop.last %}, {% endunless %}
{% endfor %}
```
 {% endraw %} 

4 포스트를 작성할 때 프론트매터에 tags: [TIL] 을 추가해준다. 

Ex)

```
---
layout: post
title: "jekyll 지킬 카테고리 추가하기"
slug: "making-categories"
tags: [TIL]
---
```

