---
layout: post
title: "primary key, natural key, surrogate key"
tags: [DB]
---

### primary key

primary key는 어떤 고유한 한 행(row)을 가리키는 key다. primary key는 반드시 고유한 값을 가져야 하며, null 값을 가질 수 없다. primary key가 있으면 query 실행 속도가 상대적으로 더 빠르다.  또한 복수의 table 사이의 연결고리 역할도 할 수 있다.  

그런데 왜 빠를까? 다들 빠르다고만 하지 왜 빠른지는 설명해주는 글이 잘 없어서, 구글의 힘을 빌렸다. 개인적으로는 아래 글이 쉽게 설명해줘서 좋았다.  
https://dba.stackexchange.com/questions/100749/how-does-unique-key-help-to-improve-sql-query-performance

위 글에서 중요한 부분을 간추리면 아래와 같다.  
예를 들어 myTable이라는 table에 id, anotherCol 이라는 두 column이 있다고 하자. id는 정수이며 unique한 값을 가진다. 하지만 primary key로 지정하지는 않았다.  
  
아래 query를 실행하면 DBMS 내부에서는 무슨 일이 일어나는가?  
> select * from myTable where id = 15


위 쿼리는 myTable에서 id가 15인 모든 row를 달라는 뜻이다. DBMS는 테이블 전체를 훑으면서 id가 15인 row를 찾을 것이다. id가 15인 row를 발견하다고 하더라도, 검색을 멈출 수가 없다. 왜냐면 id가 15인 row가 딱 1개만 존재한다는 보장이 없기 때문이다. 

그렇다면 id column을 primary key로 지정한다면 어떻게 되는가? 앞에서 말했듯이 primary key는 unique하며, null 값을 가질 수 없다. 따라서 상황이 단 2개로 나뉜다. id가 15인 row가 존재하거나, 존재하지 않거나. DBMS는 id가 15인 row가 있는지 없는지만 보면 된다. 있으면 해당 row를 return 하고, 없으면 없다고 유저에게 알려줄 것이다. 

이러한 장점이 있기 때문에, 테이블을 만들 때는 대부분의 경우에 primary key로 사용하기 위한 column을 따로 추가하거나(위의 id처럼), 혹은 primary key로써 기능할 수 있는 column을 primary key로 사용한다. 

### natural key
natural key는 하나의 record를 식별할 수 있는 하나의 컬럼이나 복수 개의 컬럼 모음이다. 경우에 따라 natural key를 primary key로 사용할 수도 있다. 이때 컬럼은 실제 데이터로 구성되어 있다. 여기서 실제 데이터라 함은 자연스럽게 생기는 데이터라는 것이다. natural key는 나머지 컬럼의 값들과 연관이 있다.  

Ex) 주민등록번호, ISBN  


### surrogate key
surrogate key는 primary key로 사용할 수 있는 하나의 record를 식별할 수 있는 하나의 컬럼이나 복수 개의 컬럼 모음이며, natural key와 달리 다른 컬럼들의 값과 전혀 자연적인 연관이 없다. surrogate key는 만들어진 값이며 나머지 컬럼의 값들과 함께 record에 저장될 뿐이다. surrogate key 값은 주로 런타임으로 레코드가 테이블에 삽입되기 전에 만들어진다. 값으로는 보통 숫자를 사용한다. 


