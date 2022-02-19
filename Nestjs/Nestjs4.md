# 데코레이터
Nest는 데코레이터를 적극 활용합니다. 데코레이터를 잘 사용하면 횡단관심사를 분리하여 관점 지향 프로그래밍을 적용한 코드를 작성할 수 있습니다. 타입스크립트의 데코레이터는 파이썬의 데코레이터나 **자바의 어노테이션**과 유사한 기능을 합니다. **클래스, 메서드, 접근자, 프로퍼티, 매개변수**에 적용 가능합니다. 각 요소의 선언부 앞에 @로 시작하는 데코레이터를 선언하면 데코레이터로 구현된 코드를 함께 실행합니다. 예를 들어 다음코드는 유저 생성 요청의 본문을 DTO로 표현한 클래스입니다.

```typescript
class CreateUserDto {
  @IsEmail()
  @MaxLength(60)
  readonly email: string;

  @IsString()
  @Matches(/^[A-Za-z\d!@#$%^&*()]{8,30}$/)
  readonly password: string;
}
```

데코레이터는 타입스크립트 스펙에서 아직 실험적인 기능입니다. tsconfig.json파일을 보겠습니다.

```json
{
  "compilerOptions": {
        ...
    "experimentalDecorators": true,
        ...
  }
}
```

`experimentalDecorators`옵션이 true로 되어있어야 데코레이터를 사용할 수 있습니다.