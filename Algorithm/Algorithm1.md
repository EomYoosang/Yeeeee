# 배열
## 목차
- 정의와 성질
- 기능과 구현
- STL Vector
- 팁: 배열 초기화
- 연습문제

## 정의와 성질
**배열**: 메모리 상에 원소를 연속하게 배치한 자료구조
성질
1. O(1)에 k번째 원소를 확인/변경 가능
2. 추가적으로 소모되는 메모리의 양(=overhead)가 거의 없음
3. Cache hit rate가 높음
4. 메모리 상에 연속한 구간을 잡아야해서 할당에 제약이 걸림

> Cache hit rate란?  
캐시 메모리는 CPU의 처리속도와 주기억장치의 접근 속도 차이를 줄이기 위해 사용하는 버퍼 메모리.  원하는 정보가 캐시 메모리에 기억되어있다면 hit한다고 하면 그 적중률을 hit rate라고 함.

## 기능과 구현
1. 임의 위치의 원소 확인/변경: O(1)
2. 끝에 원소 추가: O(1)
3. 마지막 원소 제거: O(1)
4. 임의의 위치에 원소를 추가/제거: O(N)

1~3번은 크게 어려움이 없이 구현 가능  
임의의 위치에 원소를 추가/제거 구현
```cpp
void insert(int idx, int num, int arr[], int& len);

void erase(int idx, int arr[], int& len);

int main(void) {
	int arr[10]= [10, 50, 40, 30, 70, 20);
	int len = 6;
	insert(3, 60, arr, len);
	erase(4, arr, len);
```

```cpp
void insert(int idx, int num, int arr[], int& len) {
	// 끝 원소부터 해당 인덱스의 원소까지 순회
	for(int i=0; i>idx; i--)
		// 원소의 인덱스를 하나씩 증가
		arr[i] = arr[i-1];
	// 특정 인덱스의 배열값을 변경
	arr[idx] = num;
	// 배열 크기 증가
	len++;
}

void erase(int idx, int arr[], int& len) {
	// 배열 크기 감소
	len--;
	// 배열의 특정 인덱스부터 끝까지 순회
	for(int i=idx; i<len; i++)
		// 배열을 한 칸씩 왼쪽으로 당김
		arr[i] = arr[i+1];
}
```


## 팁: 배열 초기화
배열을 초기화하는 방법은 크게 3가지
1. memset
2. for문
3. fill

cstring 헤더의 memset함수는 실수할 여지가 많으므로 비추천  
for문을 돌리는 방식은 투박하지만 실수할 확률이 적음  
algorithm 헤더의 fill함수는 실수할 여지도 별로 없고 코드도 짧아서 익숙해진다면 추천  

## STL vector
vector는 배열과 거의 동일한 기능을 수행하는 자료구조로, 배열과 마찬가지로 원소가 메모리에 연속하게 저장되어 있지만 크기를 자유롭게 조절할 수 있다는 장점.  
vector는 =를 사용하여 deep copy가능

```cpp
vector<int> v1 = {1, 2, 3, 4, 5, 6};
vector<int> v2 = v1;	// {1, 2, 3, 4, 5, 6}
// deep copy이므로 v2를 수정해도 v1이 수정되지 않는다.
```

vector의 원소를 순회하는 방법은 두가지  
```cpp
// 1. range-based for loop (since c++11)
for(int e : v1)
	cout<<e<<' ';
// 2. use size()
for(int i=0; i<v.size(); i++)
	cout<<v[i]<<' ';
```

**주의**  
아래와 같은 방식으로 순회해서는 안됨
```cpp
for(int i=0; i<=v.size()-1; i++)
	cout<<v[i]<<' ';
```

size()는 unsigned int 또는 unsigned long long 타입을 반환하는데 32비트 프로세서 기준 위와 같이 쓰면 v가 빈 벡터일 때 v.size()는 unsigned int 0 에서 int 1을 빼는 식이 되고, unsigned int와 int를 연산하면 자동으로 형변환이 발생하여 unsigned int 0 - unsigned int 1의 형식이 됨. 결과는 overflow되어 4294967295이 나옴

## 연습문제
### BOJ 10808번: 알파벳 개수
[백준 10808번](https://www.acmicpc.net/problem/10808)
문제  
알파벳 소문자로만 이루어진 단어 S가 주어진다. 각 알파벳이 단어에 몇 개가 포함되어 있는지 구하는 프로그램을 작성하시오.   
입력  
첫째 줄에 단어 S가 주어진다. 단어의 길이는 100을 넘지 않으며, 알파벳 소문자로만 이루어져 있다.  
출력  
단어에는 포함되어 있는 a의 개수, b의 개수, ... , z의 개수를 공백으로 구분해서 출력한다.

```cpp
#include<bits/stdc++.h>
using namespace std;
int main() {
	ios::sync_with_stdio(0);
	cin.tie(0);
    string s;
    cin>>s;
    int arr[26]={ 0 };
    for(int e : s)
        arr[e-'a']++;
    for(int i=0; i<26; i++)
        cout<<arr[i]<<' ';
}
```

### 문제2
문제  
주어진 길이 N의 int배열 arr에서 합이 100인 서로 다른 위치의 두 원소가 존재하면 1을, 존재하지 않으면 0을 반환하는 함수 func2(int arr[], int N)을 작성하라. arr의 각 수는 0 이상 100 이하이고 N은 1000이하다.

func2({1, 52, 48}, 3) = 1, 
func2({50, 42}, 2) = 0,  
func2({4, 13, 63, 87}, 4) = 1  

1번 문제와 비슷한 맥락으로 O(N)의 시간복잡도로 해결 가능

```cpp
int func2(int arr[], int N) {
    // 이전에 숫자가 등장했는지 알기 위한 배열
    int check[101]={0};
    // check의 100-arr[i]번 째 인덱스가 1이면 합쳐서 100이 되는 숫자 존재
    // 해당 숫자가 존재한다고 check에 표시
    for(int i=0;i<N;i++) {
        if(check[100-arr[i]]==1)
            return 1;
        check[arr[i]]=1;
    }
    return 0;
}
```
