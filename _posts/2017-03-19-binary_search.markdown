---
layout:     post
title:      "Binarty search 예제"
subtitle:   "다양한 방식으로 Binarty Search를 구현해보자."
date:       2017-3-19
author:     "Xavy"
catalog:    true
tags:
    - Algorithm
---

### Binary Search

어떤 **정렬된 배열**에서 무엇을 찾을 때, **O(n)번 보다 더 나은 성능**을 원할 때, 생각해볼만한 주제다.

다양한 방식으로 같은 동작을 하는 Binary Search를 구현해 볼 것인데, 딱히 각 방식마다 큰 차이가 있거나, 무엇이 더 좋다는 것은 아니고 Binary Search의 원리을 어떻게 소스로 나타낼 것인가를 고민해 봄으로써 얻는 것이 있지 않을까 해서 정리한다.

###### 반복문을 이용하는 Binary Search

```c++
int binarySearch(int* array, int arraySize, int number) {

	int low = 0;
	int high = arraySize;

	while (low < high) {

		int mid = (low + (long long)high) / 2;

		if (*(array + mid) == number) 
			return mid;

		else if (*(array + mid) > number)
			high = mid - 1;

		else if (*(array + mid) < number)
			low = mid + 1;
	}

	//no search
	return -1;
}
```

- 배열에 원하는 값을 찾지 못했을 때 어떻게 할 것인지 까먹지 말아야한다.(위 예제에서는 -1을 리턴)
- mid를 구할 때 low + high가 순간 int 범위를 넘어갈 수 있기 때문에 long long으로 캐스팅 한다.

###### 재귀적으로 작성한 Binary Search

```C++
int recursiveBinarySearch(int* array, int startIndex, int endIndex, int number) {

	if (startIndex > endIndex)
		return -1;

	int mid = (startIndex + (long long)endIndex) / 2;

	if (*(array + mid) == number)
		return mid;

	else if (*(array + mid) > number)
		recursiveBinarySearch(array, startIndex, mid - 1, number);

	else if (*(array + mid) < number)
		recursiveBinarySearch(array, mid + 1, endIndex, number);

}
```

- 재귀는 매 상태를 함수가 알아야하기 때문에 반복문과는 다르게 `startIndex`, `endIndex`를 파라미터로 받는다.

###### 조금 다르게 재귀적으로 작성한 Binary Search

```C++
int newRecursiveBinarySearch(int* array, int startIndex, int endIndex, int number) {

	int result = -1;

	if (startIndex <= endIndex) {

		int mid = (startIndex + (long long)endIndex) / 2;

		if (*(array + mid) == number)
			return mid;

		else if (*(array + mid) > number)
			result = newRecursiveBinarySearch(array, startIndex, mid - 1, number);

		else if (*(array + mid) < number)
			result = newRecursiveBinarySearch(array, mid + 1, endIndex, number);
	}

	return result;
}
```

- result 변수에 값을 담는 점에서 위 재귀와 돌아가는 방식이 조금 다르다.