---
layout:     post
title:      "C의 atoi, itoa, memcpy를 구현해보자!"
subtitle:   "C의 간단한 라이브러리 함수들을 직접 구현해보자!"
date:       2017-3-25
author:     "Xavy"
catalog:    true
tags:
    - Interview
    - C/C++
---

### 시작하기 전에

보통 그냥 가져다 쓰는 간단한 함수들을 직접 구현해보자. 사실 실제 각 라이브러리의 소스는 다양한 관점에서 아주 잘 작성된 소스겠지만, 우리는 간단한 예외처리와 기능 동작에 초점을 맞춘다.

### C 라이브러리 함수들

###### atoi 함수

> header file : stdlib.h

> retun value : int형으로 변환된 숫자 값

> **표준 함수**

```code
int atoi(char const *c){

	int value = 0;
	int positive = 1;

	if(*c == '\0')
		return 0;

	if(*c == '-')
		positive = -1;

	while(*c) {
		if(*c > '0' && *c < '9')
			value = value * 10 + *c - '0';
		c++;
	}

	return value*positive;
}
```
###### itoa 함수

> header file : stdlib.h

> return value : 변환된 문자열의 주소

> **비표준 함수**

```code
char* itoa(int val, char * buf, int radix) {

	char* p = buf;

	while(val) {

		if(radix <= 10)
			*p++ = (val % radix) + '0';

		else {
			int t = val % radix;
			if (t <= 9)
				*p++ = t + '0';
			else
				*p++ = t - 10 + 'a';
		}

		val /= radix;
	}

	*p = '\0';
	//reverse(buf);

	return buf;
```

- 해당 함수는 비표준함수다. ms의 Visual stdio 시리즈에서만 사용할 수 있다. 보통의 알고리즘 대회에서는 gcc컴파일러를 주로 사용하기 때문에 위 함수는 어떤 환경의 컴파일러를 사용하는지 잘 확인하고 사용해야한다. 위와 유사한 C 표준 함수는 `int sprintf (char *buffer, const char *format, ...)`이다.

###### strcpy 함수

> header file : string.h

> return value : 복사한 목적지 문자열의 주소

> **표준 함수**

```code
char* strcpy(const char * destination, const char * source){

	while(*src != NULL)
		*dest++ = *src++;

	*dest = '\0';

	return dest;
}
```


### 참고

[atoi 레퍼런스](http://www.cplusplus.com/reference/cstdlib/atoi/)

[strcpy 레퍼런스](http://www.cplusplus.com/reference/cstring/strcpy/)
