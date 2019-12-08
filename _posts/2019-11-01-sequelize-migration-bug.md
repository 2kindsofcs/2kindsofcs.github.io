---
layout: post
title: "sequelize 마이그레이션 버그를 고쳐 보자"
tags: [TIL, opensource]
---

토이 프로젝트를 진행하다가 조금 쉬고 싶어서 sequelize 이슈에서 처리할만한 것이 있는지 뒤적거렸다. 그러다 [이 버그](https://github.com/sequelize/sequelize/issues/10944)를 발견했다. **마이그레이션을 할 때 이름에 온점(.)이 들어가있는 테이블에 인덱스를 추가하려고 하면 에러가 난다**는 것이었다.   

`$ npx sequelize-cli db:migrate` 같은 형태로 마이그레이션을 실행하기 때문에, 비쥬얼 스튜디오 코드의 기본 디버거 기능은 부적합했다. `node --inspect`의 힘을 빌려서 크롬 디버거를 활용하기로 했다. 

### 디버깅 준비하기

먼저 sequelize-cli가 어떻게 실행되는지 확인하기 위해 node_modules/.bin/sequelize-cli.cmd 파일을 확인했다. 

```batch
@IF EXIST "%~dp0\node.exe" (
  "%~dp0\node.exe"  "%~dp0\..\sequelize-cli\lib\sequelize" %*
) ELSE (
  @SETLOCAL
  @SET PATHEXT=%PATHEXT:;.JS;=;%
  node  "%~dp0\..\sequelize-cli\lib\sequelize" %*
)
```
%~dp0은 해당 스크립트 파일이 있는 위치였다. 스크립트를 보면, 이 스크립트가 있는 위치에서 윗 폴더로 이동한 다음 sequelize를 찾아간다. 스크립트가 .bin 폴더 밑에 있으므로 결국 위로 올라가면 node_modules이다. `%~dp0\..\sequelize-cli\lib\sequelize`은 `node_modules\sequelize-cli\lib\sequelize`가 되는 것이다.  

이제 `node --inspect node_modules\sequelize-cli\lib\sequelize db:migrate`를 터미널에 실행시키면 크롬 디버거로 디버깅을 할 수 있다.

...하지만 잘 되지 않았다. 실행시키면 몇 초만에 에러 메세지가 뜨기 때문에 chrome://inspect에 디버거 링크가 잠깐 보였다가 사라져버린다. 답은 **`node --inspect-brk`**이다. -brk를 붙여주면 코드가 시작하기 전에 멈춘 상태로 대기하고 있기 때문에, 편하게 확인할 수 있다. 더 자세한 옵션들은 [공식 홈페이지](https://nodejs.org/en/docs/guides/debugging-getting-started/)서 확인할 수 있다.



### 디버깅하기

수상해 보이는 코드가 나올 때 까지 적절하게 넘긴다. 

```javascript
// sequelize/lib/query-interface.js

    const sql = this.QueryGenerator.addIndexQuery(tableName, options, rawTablename);
```
디버거를 이용해 계속 함수를 넘기거나 타고 들어가다 보니, 마이그레이션에서 하려고 했던 동작(인덱스 추가)과 관련이 있어보이는 sql을 만드는 것을 확인할 수 있었다. addIndexQuery 함수는 세 가지 인자를 받는다. tableName이 있는데 rawTablename이 따로 있는 것을 유심히 봤다. 


```javascript
// sequelize/lib/dialects/abstract/query-generator.js

  addIndexQuery(tableName, attributes, options, rawTablename) {
    options = options || {};

    if (!Array.isArray(attributes)) {
      options = attributes;
      attributes = undefined;
    } else {
      options.fields = attributes;
    }
    // more code...
```
attributes는 생략이 가능하다. 앞서 인자를 tableName, options, rawTablename 3가지를 넣었다. 따라서 attributes 위치에 options가, options위치에 rawTablename이 들어갔을 것이다. 
attributes는 배열이어야 한다. 그런데 지금 attributes에 options가 들어갔으므로, attributes가 배열이 아니라는 것은 attributes가 생략되어 인자가 한 칸씩 땡겨서 입력되었다는 뜻이다. 
rawTablename은 options가 되어야 하고, options는 attributes가 되어야 한다.  
코드를 보면 rawTablename에 대한 처리를 해주지 않아 rawTablename에 대한 정보가 유실된 것을 확인할 수 있다. 첫번째 문제점이다. 


```javascript
// sequelize/lib/dialects/abstract/query-generator.js

    if (typeof tableName === 'string') {
      tableName = this.quoteIdentifiers(tableName);
    } else {
      tableName = this.quoteTable(tableName);
    }
```
addIndexQuery 함수를 계속 살펴보다보면, tableName이 string일 경우 처리를 하는 부분이 있다.
tableName의 type이 string이면 tableName은 this.quoteIdentifiers(tableName)이 된다.
에러가 발생하는 예시에서 string type의 tableName을 사용하고 있으므로, 해당 함수를 조사한다. 

```javascript
// sequelize/lib/dialects/abstract/query-generator.js

  quoteIdentifier(identifier, force) {
    return QuoteHelper.quoteIdentifier(this.dialect, identifier, {
      force,
      quoteIdentifiers: this.options.quoteIdentifiers
    });
  }

  quoteIdentifiers(identifiers) {
    if (identifiers.includes('.')) {
      identifiers = identifiers.split('.');

      const head = identifiers.slice(0, identifiers.length - 1).join('->');
      const tail = identifiers[identifiers.length - 1];

      return `${this.quoteIdentifier(head)}.${this.quoteIdentifier(tail)}`;
    }

    return this.quoteIdentifier(identifiers);
  }
```

quoteIdentifiers함수를 보면, identifiers라는 하나의 인자를 받는다. 
이 인자에 "."이 포함되어 있을 경우 해당 점을 기준점으로 하여 split 메소드를 이용해 여러 개의 문자열로 나눈 배열을 만든다. "dev.user"의 경우 "."이 들어있으므로 split당할 것이다.  
head는 'dev->user'가 되고, tail은 undefined가 된다. 여기서 문제가 발생하고 있음을 알 수 있다.
지금 예시에서 table 이름은 "dev.user"이므로 이것을 그대로 사용하면 된다. 즉 this.quoteIdentifier(identifiers)로 처리하면 되는 것이다. 

따라서 코드는 아래와 같이 수정되어야 한다.

```javascript
// sequelize/lib/dialects/abstract/query-generator.js

  if (!Array.isArray(attributes)) {
      rawTablename = options;
      options = attributes;
      attributes = undefined;
    } else {

// 중략

    if (typeof tableName === 'string') {
      tableName = this.quoteIdentifiers(tableName);
      if (tableName !== rawTablename) {
        tableName = this.quoteIdentifiers(tableName);
      } else {
        tableName = this.quoteIdentifier(rawTablename);
      }
    } else {
      tableName = this.quoteTable(tableName);
    }

```

### 풀 리퀘스트 하기

테스트를 돌려보니 에러가 나서 당황스러웠는데, 정작 마스터 브랜치 코드를 그대로 테스트 실행해도 에러가 떴다. 내 코드 수정으로 인한 에러는 아닌 것으로 보였다. 안심하고 진행했다.
원본 저장소인 sequelize를 fork 한 뒤 branch를 새로 만들어서 코드를 수정했다. sequelize의 커밋 메세지 정책에 맞춰 커밋 메세지를 작성했다. 그리고 sequelize에 [풀 리퀘스트](https://github.com/sequelize/sequelize/pull/11627)를 했다. 역시 master branch 코드 자체에 에러가 제법 있는 모양인지 check들을 통과하지는 못했는데, 리뷰어분이 잘 처리해주실 거라 믿는다(?).
