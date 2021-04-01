# 알아두면 유용한 문법

## 비구조화 할당

객체 안에 있는 값을 추출해 변수 혹은 상수로 바로 선언

```javascript
const object = { a: 1, b: 2 };

const { a, b } = object;

console.log(a); // 1
console.log(b); // 2
```

함수의 파라미터에도 비구조화 할당

```javascript
const object = { a: 1, b: 2 };

function print({ a, b }) {
  console.log(a);
  console.log(b);
}

print(object);
```

### 비구조화 할당시 기본값 설정

```javascript
const object = { a: 1 };

function print({ a, b = 2 }) {
  console.log(a);
  console.log(b);
}

print(object);
// 1
// 2
```

b = 2가 없을 경우 `undefined` 출력

### 비구조화 할당시 이름 변경

``` javascript
const animal = {
  name: '멍멍이',
  type: '개'
};

// const nickname = animal.name;
const { name: nickname } = animal
console.log(nickname); // 멍멍이
```

### 배열 비구조화 할당

```javascript
const array = [1];
const [one, two = 2] = array;

console.log(one);
console.log(two);
```

### 깊은 값 비구조화 할당

다음의 예시

```javascript
const deepObject = {
  state: {
    information: {
      name: 'velopert',
      languages: ['korean', 'english', 'chinese']
    }
  },
  value: 5
};
```

name, languages, value 값들을 밖으로 꺼내고 싶다면?

1. 비구조화 할당 문법을 두 번 사용

``` javascript
const deepObject = {
  state: {
    information: {
      name: 'velopert',
      languages: ['korean', 'english', 'chinese']
    }
  },
  value: 5
};

const { name, languages } = deepObject.state.information;
const { value } = deepObject;

const extracted = {
  name,
  languages,
  value
};

console.log(extracted); // {name: "velopert", languages: Array[3], value: 5}
```

이 때 extracted 객체 선언은 다음과 같음

```javascript
const extracted = {
  name: name,
  languages: languages,
  value: value
}
```

2. 한번에 모두 추출

```javascript
const deepObject = {
  state: {
    information: {
      name: 'velopert',
      languages: ['korean', 'english', 'chinese']
    }
  },
  value: 5
};

const {
  state: {
    information: { name, languages }
  },
  value
} = deepObject;

const extracted = {
  name,
  languages,
  value
};

console.log(extracted);
```

## spread 와 rest

ES6에서 도입

### spread

객체 혹은 배열을 펼칠 수 있음

```javascript
const slime = {
  name: '슬라임'
};

const cuteSlime = {
  ...slime,
  attribute: 'cute'
};

const purpleCuteSlime = {
  ...cuteSlime,
  color: 'purple'
};

console.log(slime);
console.log(cuteSlime);
console.log(purpleCuteSlime);
```

여기서 `...`가 spread 연산자

아래는 배열에서 사용

```
const animals = ['개', '고양이', '참새'];
const anotherAnimals = [...animals, '비둘기'];
console.log(animals);
console.log(anotherAnimals);
```

### rest

```javascript
const purpleCuteSlime = {
  name: '슬라임',
  attribute: 'cute',
  color: 'purple'
};

const { color, ...rest } = purpleCuteSlime;
console.log(color);
console.log(rest);
```

`const { color, ...rest } = purpleCuteSlime`에서 color 값을 제외한 값이 들어감

__추출한 값의 이름이 꼭 rest일 필요는 없음__

여러 값을 없애고 싶다면 여러번 사용

```javascript
const purpleCuteSlime = {
  name: '슬라임',
  attribute: 'cute',
  color: 'purple'
};

const { color, ...cuteSlime } = purpleCuteSlime;
console.log(color);
console.log(cuteSlime);

const { attribute, ...slime } = cuteSlime;
console.log(attribute);
console.log(slime);
```

### 배열에서의 rest

```javascript
const numbers = [0, 1, 2, 3, 4, 5, 6];

const [one, ...rest] = numbers;

console.log(one);
console.log(rest);
```

이 때 one은 