---
layout: single
title: "Zed - Configuration"
tag: [ide, zed, editor, config]
categories: env
toc: true
sidebar:
    nav: "docs"
---

## Configuration Options

### Format On Save 

파일을 저장하는데 자꾸 얘써 띄어놓은 공백을 다 삭제시킨다.
환경옵션 중에서 format_on_save 가 디폴트로 설정되어있다.
해당 옵션을 끄면 된다.

```
{
  "format_on_save": "off"
}
```

### Vim

환경옵션들을 살펴보다가 Vim 모드가 있길래 활성화시켰다. 
이 옵션은 "on" / "off" 가 아닌, true / false 로 설정한다.

```
{
    "vim_mode": true
}
```

## References

Configuring Zed - Zed (zed.dev) | [Link](https://docs.zed.dev/configuration/configuring-zed#folder-specific-settings) | `w02.2024-01-03.Wednesday, 21:49:22`
