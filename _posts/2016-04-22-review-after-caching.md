---
published: true
layout: post
cover: assets/images/cover2.jpg
title: "Travis CI에 캐시 구현하기 - 2"
tags: 블로그
subclass: "post tag-test tag-content"
categories: casper
navigation: true
---

캐싱 구현에 성공한 줄 알았건만
===

![스크린샷 2016-04-22 오후 8.17.52.png]({{site.baseurl}}/assets/images/스크린샷 2016-04-22 오후 8.17.52.png)

> 테스팅 결과 1분 40초대에 진입했다. 성공을 했지만 과정은..

사실은 제대로 돌아가지 않고 있었다. 

첫번째 삽질
---

원인파악을 위해서 다음과 같은 과정을 거쳤다
1. heroku에 업로드한 keyfile이 문제인듯 해서 확인해보니 500에러가 났다.
1. keyfile repository의 .env에 있는 database_url 값을 바꾸지 않은 것을 확인해서 바꿨다.
1. 여전히 500 에러가 났다. DB를 직접 까보는 짓을 했지만, 별다른 성과는 없었다.
1. 알고보니 Travis에서 rebund수행을 위해서 keyfile을 heroku에서 얻어야하는데 그걸 위한 계정 encryption이 필요했던거다.. 

	스크립트는 아래와 같다
    `travis encrypt REBUND_CREDENTIALS=[KEYFILE_CREDENTIAL_ID]:[KEYFILE_CREDENTIAL_PWD]`

위 과정을 마치고서 다음 삽질이 이어진다.

두번째 삽질
---

변경된 .travis.yml을 가지고 source 브랜치에 푸시를 했지만 캐싱이 제대로 되질 않았다.

> 무엇이 문제인가..

1. `bundle install --local`에서 local을 삭제했다 일단 캐시를 만들어야되니까
2. 여전히 캐시가 만들어지지 않았다. 캐시를 저장할 경로지정을 하지 않았던 것. `--path vendor/bundle` 
3. 드디어 캐시가 생겼다. 이제 --local을 추가하면 된다. 단 gem의 변경이 없을 때 수행하는것이다.

여전히 다음 과제가 남았다
---

루비 버전 설정을 2.1.1로 해두었는데 매 빌드마다 루비 재설치가 진행된다. 

[Travis의 공식문서](https://docs.travis-ci.com/user/languages/ruby)에 의하면 2.1.x를 지원한다고 나와있는데도 왜 그러는지는 모르겠다.
2.1.0 쯤으로 바꿔볼까 한다.


후기
---

- 삽질하며 성장한다지만, 그만좀 하고 싶다 삽질.
- prose.io에서 그림을 업로드하면 바로 푸시한다 ㅡㅡ..
- 다음 과제는 ***multiple author*** 구현하기!
