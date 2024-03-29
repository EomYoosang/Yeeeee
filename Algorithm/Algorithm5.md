# 덱
## 목차
- 정의와 성질
- 기능과 구현
- STL deque
- 연습문제

## 정의와 성질
### 정의
**덱**: 양쪽 끝에서 삽입과 삭제가 모두 가능한 자료구조. Double Ended Queue
### 성질
1. 원소의 추가: O(1)
2. 원소의 삭제: O(1)
3. 제일 앞/뒤 원소 확인: O(1)
4. 제일 앞/뒤가 아닌 원소는 확인/변경이 원칙적으로 불가능

## 기능과 구현

```cpp
const int MX = 1000005;
int dat[2*MX+1];
int head = MX, tail = MX;
```

**push_front/push_back**
```cpp
void push_front(int x) {
	dat[--head] = x;
}
void push_back(int x) {
	dat[tail++] = x;
}
```

**pop_front/pop_back**
```cpp
void pop_front(int x) {
	head++;
}
void pop_back(int x) {
	tail--;
}
```

**front/back**
```cpp
int front() {
	return dat[head];
}
int back() {
	return dat[tail-1];
}
```

## STL deque
[reference](http://www.cplusplus.com/reference/deque/deque/)

```cpp
#include <bits/stdc++.h>
using namespace std;

int main(void){
  deque<int> DQ;
  DQ.push_front(10); // 10
  DQ.push_back(50); // 10 50
  DQ.push_front(24); // 24 10 50
  for(auto x : DQ)cout<<x;
  cout << DQ.size() << '\n'; // 3
  if(DQ.empty()) cout << "DQ is empty\n";
  else cout << "DQ is not empty\n"; // DQ is not empty
  DQ.pop_front(); // 10 50
  DQ.pop_back(); // 10
  cout << DQ.back() << '\n'; // 10
  DQ.push_back(72); // 10 72
  cout << DQ.front() << '\n'; // 10
  DQ.push_back(12); // 10 72 12
  DQ[2] = 17; // 10 72 17
  DQ.insert(DQ.begin()+1, 33); // 10 33 72 17
  DQ.insert(DQ.begin()+4, 60); // 10 33 72 17 60
  for(auto x : DQ) cout << x << ' ';
  cout << '\n';
  DQ.erase(DQ.begin()+3); // 10 33 72 60
  cout << DQ[3] << '\n'; // 60
  DQ.clear(); // DQ의 모든 원소 제거
}
```

## 연습문제
**문제**  
정수를 저장하는 덱(Deque)를 구현한 다음, 입력으로 주어지는 명령을 처리하는 프로그램을 작성하시오.

명령은 총 여덟 가지이다.

- push_front X: 정수 X를 덱의 앞에 넣는다.  
- push_back X: 정수 X를 덱의 뒤에 넣는다.  
- pop_front: 덱의 가장 앞에 있는 수를 빼고, 그 수를 출력한다. 만약, 덱에 들어있는 정수가 없는 경우에는 -1을 출력한다.  
- pop_back: 덱의 가장 뒤에 있는 수를 빼고, 그 수를 출력한다. 만약, 덱에 들어있는 정수가 없는 경우에는 -1을 출력한다.  
- size: 덱에 들어있는 정수의 개수를 출력한다.  
- empty: 덱이 비어있으면 1을, 아니면 0을 출력한다.  
- front: 덱의 가장 앞에 있는 정수를 출력한다. 만약 덱에 들어있는 정수가 없는 경우에는 -1을 출력한다.  
- back: 덱의 가장 뒤에 있는 정수를 출력한다. 만약 덱에 들어있는 정수가 없는 경우에는 -1을 출력한다.  

**입력**  
첫째 줄에 주어지는 명령의 수 N (1 ≤ N ≤ 10,000)이 주어진다. 둘째 줄부터 N개의 줄에는 명령이 하나씩 주어진다. 주어지는 정수는 1보다 크거나 같고, 100,000보다 작거나 같다. 문제에 나와있지 않은 명령이 주어지는 경우는 없다.

**출력**  
출력해야하는 명령이 주어질 때마다, 한 줄에 하나씩 출력한다.

1. STL deque를 이용한 경우
```cpp
#include <bits/stdc++.h>
using namespace std;

int main(void) {
  ios::sync_with_stdio(0);
  cin.tie(0);

  deque<int> DQ;
  int n;
  cin >> n;
  while (n--) {
    string q;
    cin >> q;
    if (q == "push_back") {
      int val;
      cin >> val;
      DQ.push_back(val);
    }
    else if (q == "push_front") {
      int val;
      cin >> val;
      DQ.push_front(val);
    }
    else if(q == "pop_front"){
      if(DQ.empty()) cout << -1 << '\n';
      else{
        cout << DQ.front() << '\n';
        DQ.pop_front();
      }
    }
    else if(q == "pop_back"){
      if(DQ.empty()) cout << -1 << '\n';
      else{
        cout << DQ.back() << '\n';
        DQ.pop_back();
      }
    }
    else if (q == "size")
      cout << DQ.size() << '\n';
    else if (q == "empty")
      cout << DQ.empty() << '\n';
    else if (q == "front") {
      if(DQ.empty()) cout << -1 << '\n';
      else cout << DQ.front() << '\n';
    }
    else { // back
      if(DQ.empty()) cout << -1 << '\n';
      else cout << DQ.back() << '\n';
    }
  }
}
```

2. 직접 deque를 구현한 경우
```cpp
#include <bits/stdc++.h>
using namespace std;

const int MX = 1000005;
int dat[2*MX+1];
int head = MX, tail = MX;

void push_front(int x){
  dat[--head] = x;
}
void push_back(int x){
  dat[tail++] = x;
}
void pop_front(){
  head++;
}
void pop_back(){
  tail--;
}
int front(){
  return dat[head];
}
int back(){
  return dat[tail-1];
}

int main(void) {
  ios::sync_with_stdio(0);
  cin.tie(0);
  int n;
  cin >> n;
  while (n--) {
    string q;
    cin >> q;
    if (q == "push_back") {
      int val;
      cin >> val;
      push_back(val);
    }
    else if (q == "push_front") {
      int val;
      cin >> val;
      push_front(val);
    }
    else if(q == "pop_front"){
      if(tail==head) cout << -1 << '\n';
      else{
        cout << front() << '\n';
        pop_front();
      }
    }
    else if(q == "pop_back"){
      if(tail==head) cout << -1 << '\n';
      else{
        cout << back() << '\n';
        pop_back();
      }
    }
    else if (q == "size")
      cout << tail-head << '\n';    
    else if (q == "empty")
      cout << (tail==head) << '\n';
    else if (q == "front") {
      if(tail==head) cout << -1 << '\n';
      else cout << front() << '\n';      
    }
    else { // back
      if(tail==head) cout << -1 << '\n';
      else cout << back() << '\n';      
    }
  }
}
```