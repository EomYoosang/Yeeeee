# 스택

## 목차
- 정의와 성질
- 기능과 구현
- STL stack
- 연습문제

## 정의와 성질
<img alt="stack" src="stack.png" />  
 
### 정의
**스택**: 한쪽 끝에서만 원소를 넣거나 뺄 수 있는 자료구조  
구조상 먼저 들어간 원소가 가장 나중에 나오기 때문에 FILO(First In Last Out)이라고도 함

>참고: 큐나 덱도 스택처럼 특정 위치에서만 원소를 넣거나 뺄 수 있는 제한이 걸려있다. 그래서 스택, 큐, 덱을 묶어서 **Restricted Structure**라고 부르기도 한다.

### 성질

1. 원소의 추가: O(1)
2. 원소의 삭제: O(1)
3. 제일 상단 원소 확인: O(1)
4. 제일 상단을 제외한 원소는 원칙적으로 확인/변경 불가

## 기능과 구현
```cpp
const int MX = 10000001;
int dat[MX];
int pos = 0;
```
**push 함수**  
```cpp
void push(int x) {
	dat[pos++] = x;
}
```
**pop 함수**
```cpp
void pop(int x) {
	pos--;
}
```
**top 함수**
```cpp
int top() {
	return dat[pos-1];
}
```
## STL stack
[reference](http://www.cplusplus.com/reference/stack/stack)

```cpp
#include <bits/stdc++.h>
using namespace std;

int main(void) {
  stack<int> S;
  S.push(10); // 10
  S.push(20); // 10 20
  S.push(30); // 10 20 30
  cout << S.size() << '\n'; // 3
  if(S.empty()) cout << "S is empty\n";
  else cout << "S is not empty\n"; // S is not empty
  S.pop(); // 10 20
  cout << S.top() << '\n'; // 20
  S.pop(); // 10
  cout << S.top() << '\n'; // 10
  S.pop(); // empty
  if(S.empty()) cout << "S is empty\n"; // S is empty
  cout << S.top() << '\n'; // runtime error 발생
}
```

## 연습문제
### BOJ 10828번: 스택
**문제**  
정수를 저장하는 스택을 구현한 다음, 입력으로 주어지는 명령을 처리하는 프로그램을 작성하시오.

명령은 총 다섯 가지이다.

- push X: 정수 X를 스택에 넣는 연산이다.
- pop: 스택에서 가장 위에 있는 정수를 빼고, 그 수를 출력한다. 만약 스택에 들어있는 정수가 없는 경우에는 -1을 출력한다.
- size: 스택에 들어있는 정수의 개수를 출력한다.
- empty: 스택이 비어있으면 1, 아니면 0을 출력한다.
- top: 스택의 가장 위에 있는 정수를 출력한다. 만약 스택에 들어있는 정수가 없는 경우에는 -1을 출력한다.

**입력**  
첫째 줄에 주어지는 명령의 수 N (1 ≤ N ≤ 10,000)이 주어진다. 둘째 줄부터 N개의 줄에는 명령이 하나씩 주어진다. 주어지는 정수는 1보다 크거나 같고, 100,000보다 작거나 같다. 문제에 나와있지 않은 명령이 주어지는 경우는 없다.  

**출력**  
출력해야하는 명령이 주어질 때마다, 한 줄에 하나씩 출력한다.

1. STL stack을 이용하는 경우
```cpp
#include <bits/stdc++.h>
using namespace std;

int main(void)
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    int n;
    cin >> n;
    stack<int> S;
    while (n--)
    { // n번 반복
        string c;
        cin >> c;
        if (c == "push")
        {
            int t;
            cin >> t;
            S.push(t);
        }
        else if (c == "pop")
        {
            if (S.empty())
                cout << -1 << '\n';
            else
            {
                cout << S.top() << '\n';
                S.pop();
            }
        }
        else if (c == "size")
            cout << S.size() << '\n';
        else if (c == "empty")
            cout << (int)S.empty() << '\n';
        else
        { // top
            if (S.empty())
                cout << -1 << '\n';
            else
                cout << S.top() << '\n';
        }
    }
}
```
2. 직접 stack을 구현할 경우
```cpp
#include <bits/stdc++.h>
using namespace std;

const int MX = 1000005;
int dat[MX];
int pos;

void push(int val){
  dat[pos++] = val;
}

void pop(){
  pos--;
}

int top(){
  return dat[pos-1];
}

int main(void) {
  ios::sync_with_stdio(0);
  cin.tie(0);
  int n;
  cin >> n;
  while(n--){ // n번 반복
    string c;
    cin >> c;
    if(c=="push"){
      int t;
      cin >> t;
      push(t);
    }
    else if(c=="pop"){
      if(pos == 0) cout << -1 << '\n';
      else{
        cout << top() << '\n';
        pop();
      }
    }
    else if(c=="size")
      cout << pos << '\n';
    else if(c=="empty")
      cout << (int)(pos == 0) << '\n';
    else{ // top
      if(pos == 0) cout << -1 << '\n';
      else cout << top() << '\n';
    }
  }
}
```