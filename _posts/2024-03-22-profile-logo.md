---
layout: single
title: "jekyll profile logo"
tag: [github-blog, jekyll, profile, logo]
categories: [til]
toc: true
sidebar:
  nav: "docs"
---

프로필 로고 이미지를 넣고 싶다.  
환경설정 파일에서 아바타 이미지를 추가했는데, 별 반응이 없다.  
jekyll 시스템까지 파악하려니 좀 번거로워서, 그냥 하드코딩으로 넣었다. 

_includes/author-profile.html 에서, 다음과 같이 html 태그로 추가했다.

```html
<div itemscope itemtype="https://schema.org/Person" class="h-card">
    
    <img src="imgs/mulcher.svg" alt="mülcher" style="width:35%;height:35%;">

```
