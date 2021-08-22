## Redux

### Redux란?

 **리덕스**는 Javascript App을 위한 예측 가능한 state container이다. 리액트 뿐만 아니라 Angular, jQuery, vanilla Javascript 등 다양한 framework와 작동하게 설계되어있다.

### 리액트에 리덕스가 필요한 이유?

 리액트 프로젝트에서 각 컴포넌트는 local state를 가지고 어플리케이션은 global state를 가지게 된다. 리액트 라이브러리만으로는 local state를 컴포넌트간 전달하기 어렵고 global state를 모든 컴포넌트에 전달하기 무리가 있다.

 하지만 리덕스를 사용하면 하나의 store를 통해 모든 state를 저장할 수 있고 원하는 컴포넌트로만 데이터를 전달할 수 있다.

![img](https://miro.medium.com/max/1382/1*NMF-GM_ULkLlUtkIWgeGsA.jpeg)

### Flux란?

 **Flux**는 페이스북에서 만든 Application Architecture, Design Pattern이다. **MVC** 구조의 단점을 보완할 목적으로 개발되었다.

![img](https://miro.medium.com/max/1276/1*oRBseHE_yxtlbcRTrbkRnQ.png)

리덕스는 Flux의 구현체라고 볼 수 있다.

### 리덕스 기본개념

#### .Actions

어플리케이션의 store(스토어), 즉 저장소로 data를 보내는 방법이다. view에서 정의되어있는 액션을 호출하면 action creators(액션 생성자)는 어플리케이션의 state(상태)를 변경하여 준다.

#### .Reducer

Action의 결과로 어플리케이션이 어떻게 바뀌는지 특정하는 함수이다. 리듀서 함수는 action type에 따라 변화된 state를 반환한다.

#### .Store

Action과 Reducer를 저장하는 어플리케이션에 단 하나 존재하는 객체이다.

### Reference

https://medium.com/@jsh901220/react%EC%97%90-redux-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0-a8e6efd745c9