---
layout: post
current: post
cover: assets/built/images/spring.png
navigation: True
title: Spring - 첫 포스팅!!
date: 2022-11-28 06:00:00 +0900
tags: [spring]
class: post-template
subclass: 'post tag-etc'
author: rissD
---

리스는 왜 스프링을 선택했을까..?  
이 글을 적으며... 드는 생각  
아 공부 처음부터 다시 해야겠다..ㅎㅎ 
앞으로 열심히 정리해볼개혁!!

### Spring Security Config Class
```java
@RequiredArgsConstructor
@Configuration
@EnableWebSecurity(debug = false)
@EnableGlobalMethodSecurity(securedEnabled = true, prePostEnabled = true)
public class SecurityConfig {

    private final UsersRepository usersRepository;
    private final RedisLogoutTokenRepository redisLogoutTokenRepository;
    private final JwtAccessDeniedHandler jwtAccessDeniedHandler;
    private final JwtAuthenticationEntryPoint jwtAuthenticationEntryPoint;

    private final PrincipalOAuth2UserService principalOAuth2UserService;

    @Bean
    public WebSecurityCustomizer configure () throws  Exception {
        return web -> web
                .ignoring()
                .antMatchers("/",
                "/test",
                "/users",
                "/users/verification",
                "/users/oauth/**",
                "/users/id",
                "/users/password/token",
                "/service/**",
                "/favicon.ico"
                )
                .antMatchers(HttpMethod.PUT, "/users/token");
    }

    @Bean
    public SecurityFilterChain filterChain (HttpSecurity http) throws Exception {

        return http
                .csrf().disable()   // disable 끄고 메서드 불러오는 법 R&D 하기
                .cors()
                .and()

                .formLogin().disable()
                .httpBasic().disable()
                .apply(new CustomDsl())
                .and()
                .exceptionHandling()
                .authenticationEntryPoint(jwtAuthenticationEntryPoint)
                .accessDeniedHandler(jwtAccessDeniedHandler)
                .and()

                .authorizeRequests(authorize ->
                        authorize
                                .antMatchers("/users/**")
                                .authenticated()
                                .antMatchers("/login/oauth2/code/**")
                                .permitAll()
                                .anyRequest()
                                .permitAll())

                .oauth2Login()
                .userInfoEndpoint()
                .userService(principalOAuth2UserService)

                .and()
                .and()
                .build();
    }

    @RequiredArgsConstructor
    public class CustomDsl extends AbstractHttpConfigurer<CustomDsl, HttpSecurity> {

        @Override
        public void configure(HttpSecurity http) throws Exception {
            AuthenticationManager authenticationManager=http.getSharedObject(AuthenticationManager.class);

            http
                    .addFilterBefore(new JwtAuthenticationFilter(authenticationManager, principalOAuth2UserService), WebAsyncManagerIntegrationFilter.class)
                    .addFilter(new JwtAuthorizationFilter(authenticationManager, usersRepository, redisLogoutTokenRepository));

        }
    }
}

```