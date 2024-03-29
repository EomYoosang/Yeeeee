# 타입스크립트

NestJS는 기본적으로 타입스크립트를 지원합니다. 이전까지 자바스크립트만을 이용해 개발해왔으므로 타입스크립트를 정리해보려합니다.

타입스크립트는 마이크로소프트에서 개발한 언어입니다. 자바스크립트 코드에 타입 시스템을 도입하여 런타임에 발생할 가능성이 있는 코드를 정적 분석으로 찾아줍니다.

## 변수 선언
타입스크립트에서 변수를 선언하는 방식은 다음과 같습니다.
```typescript
[선언 키워드] [변수명]: [타입]
```

- 선언키워드: `const`, `let`, `var`로 선언합니다. const는 선언 후 재할당이 불가능하며 let과 var은 재할당이 가능합니다. let과 var은 호이스팅의 여부에 차이가 있는데, var은 변수를 사용한 후에 선언이 가능하지만 let은 그렇지 못합니다.

## 타입
타입스크립트는 자바스크립트가 가지고 있는 자료형을 모두 포함합니다. 자바스크립트의 타입은 기본타입(Primitive value)와 객체형(Object), 함수형(Function)이 있습니다. 

### 기본타입

|typeof|설명|할당 가능한 값|
|--|--|--|
|boolean|참, 거짓|true, false|
|null|"유효하지 않음"을 나타냄|null|
|undefined|값이 존재하지 않음. 즉, 변수 선언 후 "값이 할당되지 않음"을 나타냄|undefined|
|number|배정밀도 64비트 형식 IEEE 754의 값| 4바이트 값의 정수와 실수, 음의 무한대, 양의 무한대, NaN(Not a Number)
|bigint|Number의 범위를 넘어서는 정수를 안전하게 저장하고 연산할 수 있게 해줌| ex) const x = 2n**53n; 정수 끝에 n을 추가
|string|문자열. 변경 불가능(immutable)|ex 'hello'|
|symbol|유일하고 변경 불가능한 기본값. 객체 속성의 키로 활용 가능||

### 객체 타입
객체 타입은 속성(Property)을 가지고 있는 데이터 컬렉션입니다. 속성은 키와 값으로 표현되고 값은 자바스크립트의 타입을 가집니다.

ex)
```typescript
const dexter = {
  name: 'Dexter Han',
  age: 21,
  hobby: ['Movie', 'Billiards'],
}
```
자바스크립트는 유용한 내장 객체들을 제공합니다.
- Date
- Array
- JSON
- etc

### 함수(function) 타입
자바 스크립트는 함수를 변수에 할당하거나 다른 함수의 인자로 전달하거나 함수의 결과로 반환할 수 있습니다. 이런 특징을 **일급 함수** 라고 합니다. 함수타입의 타입을 검사하면 "function"이 됩니다.

### any/unknown/never
타입 스크립트에는 특수한 타입이 있습니다.
- any: 자바스크립트와 같이 어떤 타입의 값도 받을 수 있는 타입으로 any타입의 객체 역시 어떤 타입의 변수에도 할당이 가능합니다. 이 특성때문에 런타임 에러의 가능성이 있습니다.
- unknown: any타입과 마찬가지로 어떤 타입도 할당이 가능하지만 다른 변수에 할당 또는 사용할 때 타입을 강제하도록 하여 any가 일으키는 에러를 줄여줍니다.
- never: 어떤 값도 할당할 수 없습니다. 함수의 리턴 타입으로 지정하면 함수가 어떤 값도 반환하지 않는다는 것을 뜻하고, 다음과 같이 특정 값을 할당받지 못하도록 하는데 사용할 수도 있습니다.
```typescript
type NonString<T> = T extends string ? never : T;
```

타입 스크립트는 타입을 정의해서 사용할 수 있습니다. 기본타입과 같은 타입을 정의한다는 뜻은 아니고, 위에서 설명한 타입들을 조합하여 타입에 이름을 붙여 사용합니다.

변수에 객체를 바로 할당하지 않고 `interface`로 정의할 수 있습니다.

```typescript
interface User {
    name: string;
    age: number;
}

const user: User = {
    name: 'Dexter',
    age: 21,
}
```

interface대신 class 선언도 가능합니다.

```typescript
class User {
  constructor(name: string, age: number) { }
}

const user: User = new User('Dexter', 21);
```

타입은 `type`키워드로 새로운 타입을 만들 수 있습니다.

```typescript
type MyUser = User;
```
`MyUser` 타입은 기존 User타입을 그대로 사용하지만 사용하는 도메인에 맞는 이름으로 변경한 것입니다.

## 타입 구성하기
자바스크립트는 변수에 어떠한 타입의 값도 할당할 수 있습니다. 덕 타이핑이라 부릅니다. 타입스크립트도 여러 타입의 값을 할당할 수 있습니다.
### 유니언(Union) 타입
유니언 타입이 위에서 설명한 여러 타입을 조합한 타입입니다. 다음 코드에서 getLength 함수의 인자인 obj는 string 또는 string 배열 타입을 가질 수 있습니다.
```typescript
function getLength(obj: string | string[]) {
	return obj.length;
```

유니언 타입으로 변수가 가질 수 있는 값을 제한할 수도 있습니다.
```typescript
type Status = "Ready" | "Waiting";
```
>위 코드는 열거형(enum)으로 표기하는 게 바람직합니다.

### 제네릭(Generic) 타입
Java나 C#에서의 제네릭과 유사한 기능을 합니다. 어떤 타입이든 정의될 수 있지만 호출되는 시점에 타입이 결정됩니다. 만약 다음과 같이 인자를 그대로 리턴하는 함수가 있다고 한다면 string 타입을 전달할 때 리턴 타입이 any가 되어버립니다.
```typescript
function identity(arg: any): any {
  return arg;
}
```
다음과 같이 제네릭 타입을 사용하게 되면 리턴되는 값의 타입은 함수를 호출하는 시점의 인자로 넣은 타입으로 결정되도록 할 수 있습니다.
```typescript
function identity<T>(arg: T): T {
  return arg;
}
```