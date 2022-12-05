---
layout: post
current: post
cover: assets/built/images/spring.png
navigation: True
title: Spring - Spring Security (OAuth2) 설정을 위한 Class 생성
date: 2022-12-02 11:00:00 +0900
tags: [spring]
class: post-template
subclass: 'post tag-etc'
author: rissD
---

기존에 사용하던 WebSecurityConfigurerAdapter Class 가 Deprecated 되었다.  

서칭을 해보니 SecurityFilterChain 를 만들어서 Bean 으로 등록하고 사용하라고 한다.  

회사에서 JWT 를 인증 토큰으로 구현하라하여 AbstractHttpConfigurer 를 상속하여 새로운 클래스를 하나 만들었고  
configure 메서드를 오버라이딩하여 JWT 를 위해 구현한 Authentication, AuthorizationFilter 를 연결하였으며  
이 커스텀한 클래스를 SecurityChain 에 등록하였다.  

Spring Security 필터들을 무시하고 싶은 특정 URI 들이 있어 찾아보니 WebSecurityCustomizer 를 Bean 으로 등록해서 그 안에서 설정하면 된다고 한다.  

아래는 위를 이용하여 구현한 SpringSecurityConfig Class 이다!  

{% gist rissD/e53376f24682bc86ce6b7c73d667f2e2 %}