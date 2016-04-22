---
layout: post
cover: 'assets/images/cover2.jpg'
title: Travis CI에 캐시 구현하기 - 1
tags: 블로그
subclass: 'post tag-test tag-content'
categories: 'casper'
navigation: True
---

문제의 발견
===

- 포스트를 작성하고 source 브랜치를 푸시할 때마다 빌딩과정이 너무 오래걸렸다. 적게는 3분에서 길게는 5분..
- 원인 파악을 위해서 [Travis CI](https://travis-ci.org)의 빌딩 로직을 살펴보았다.

Travi CI의 빌딩 로직
---

1. 연동된 github의 소스브랜치로부터 소스를 복사한다 `(git clone --depth ...)`
1. 사이트 빌딩에 필요한 ruby gem들을 설치한다. `(bundle install ...)`
1. Rakefile을 실행해서 Jekyll 사이트를 빌드하고 배포한다.

여기에서 bundle install의 수행 시간이 가장 오래걸렸다(Travis CI에서 빌딩하면 전체 과정을 살펴보고 수행시간도 알 수 있다!)

그래서 알아보니, [Rebund](https://github.com/mezis/rebund)라는 툴을 통해서 설치한 번들목록을 캐시로 저장해 성능향상을 할 수 있단다!

Rebund로 캐시 구축하기
---

Rebund의 로직은 빌딩 전에 캐시를 확인하고, bundled gem을 설치한 후, 다시 캐시에 업로드하는 과정을 통해서 이루어진다.

여기에 keyfile로 encrypting을 하는 듯 한데, 일단은 시키는대로 했다.

1. [keyfile](https://github.com/mezis/keyfile) 호스팅하기 (heroku에 올리는 과정이 친절하게 설명되어 있다)
1. source 브랜치에 rebund 설치`git submodule add https://github.com/mezis/rebund.git`
1. .travis.yml에 빌딩 시 수행할 rebund 다운로드 및 업로드 스크립트 추가하기

> 그리고 나서 source브랜치를 푸시! 빌딩 과정을 보니 Caching과 관련된 무언가가 추가되기는 했는데 정확히 모르겠다.
테스트해보고 결과를 보면 제대로 됐는지 알 수 있겠지?
이게 그 첫 제물이다. 고고