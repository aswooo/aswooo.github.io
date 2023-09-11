---
layout: post
title:  "PUT method에서 CORS 에러"
date:   2022-12-20 14:34:25
categories: Error
tags: CORS Error
---
 POST, GET 메서드 등을 통한 통신은 정상적으로 되는데 PUT 메서드에서만 자꾸 CORS 에러가 발생하는 문제가 생겼다.

처음에는 헤더에 문제가 있는 줄 알고 열심히 삽질하며 바꿔보았는데 이 문제는 아니었다.

CORS에러를 해결하기 위해 맵핑해주는 부분에 모든 메서드를 등록하고 나서 정상적으로 통신이 가능하다. 

```
@Override
public void addCorsMappings(CorsRegistry registry) {
  registry.addMapping("/**")
  	.allowedOrigins("http://localhost:8080")
    .allowedMethods(
    	HttpMethod.GET.name(),
    	HttpMethod.HEAD.name(),
    	HttpMethod.POST.name(),
    	HttpMethod.PUT.name(),
    	HttpMethod.DELETE.name());
}
```

[참고링크](https://velog.io/@hellozin/Spring-API-%EC%84%9C%EB%B2%84%EC%97%90%EC%84%9C-PUT-DELETE-%EC%9A%94%EC%B2%AD-%EC%8B%9C-CORS-%EC%84%A4%EC%A0%95%EC%9D%B4-%EC%A0%81%EC%9A%A9-%EC%95%88%EB%90%98%EB%8A%94-%EA%B2%BD%EC%9A%B0)