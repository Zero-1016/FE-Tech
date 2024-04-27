## 타입스크립트 실행 명령어
```shell
npm install -g ts-node

ts-node {파일명}
```

## 타입스크립의 기본 타입

### number
number 타입은 정수나 소수를 나타내기 위해서 사용합니다.
```typescript
const user_age: number = 20;
console.log(user_age); // 20
```
### string
string 타입은 문자열을 나타내기 위해서 사용합니다. 문자열 안에는 숫자가 들어갈 수도 있습니다.
```typescript
const user_name: string = "zero";
console.log(user_name); // zero
```
### boolean
boolean 타입은 참 또는 거짓을 나타내기 위해서 사용이 됩니다.
```typescript
const isMale: boolean = true;
console.log(isMale) // true
```
### any
any 타입은 타입스크립트가 오류를 검사하지 않습니다.
```typescript
let isAnyThing: any = "zero";
isAnyThing = false;
isAnyThing = "one";
isAnyThing = undefined;
isAnyThing = null;
isAnyThing = "zero";
console.log(isAnyThing); // zero
```
### object
object 타입은 안에 여러가지 키를 가지고있는 값을 넣을 수 있습니다.
```typescript
const isObject: object = {
    user_name: "zero",
    user_age: 20,
    user_gender: 'male'
}
console.log(isObject) // { user_name: 'zero', user_age: 20, user_gender: 'male' }
```
### array
array 타입은 기존 타입처럼 따로 지정을 할 수가 없습니다. 
```typescript
const isArray: string[] = [];
isArray.push("z")
isArray.push("e")
isArray.push("r")
isArray.push("o")
console.log(isArray) // [ z, e, r, o]
isArray.push(3) // TS2345: Argument of type 'number' is not assignable to parameter of type 'string'
```
### unknown
unknown 타입은 any타입과 유사하지만 typeGuard를 할 수 있습니다.
```typescript
let isUnknown : unknown = 2;
isUnknown = {};

let isString: string = isUnknown; // TS2322: Type 'unknown' is not assignable to type 'object'.
let isNumber: number = isAnyThing;
// isAnyThing 같은 경우 위에서 any type으로 지정을 하였기 때문에 마지막이 문자열임에도 할당이 됩니다.
// 하지만 isString의 경우 isUnkown의 값을 모르므로 isString에 할당이 되지 않습니다.
```
### union
유니온 타입은 해당 값이 아닐 경우 지정하지 못하게 고정 시켜줄 수 있습니다. 또한 자동완성도 가능하게 만들어줘서 생산성이 좋습니다.
```typescript
let union : "top" | "left" | "right" | "bottom";
union = "right";
union = "left";
union = "top";
union = "right";
union = "center"; // TS2322: Type '"center"' is not assignable to type '"top" | "left" | "right" | "bottom"'.
```
### conditional
conditional 타입은 조건부 타입으로서 조건에 따라 타입이 지정이 됩니다.
```typescript
type Man = true
type Girl = false

type Gender<T> = T extends boolean & true ? Man : Girl

type ZeroGender = Gender<true>

const zeroGender: ZeroGender = true;
```
### type alias
type alias는 타입에 이름을 정해줄 수 있습니다. 가독성이 증가하고 문서화 작업에 수월합니다.
```typescript
type UserInfoType = {
    userName: string,
    userAge: number,
    userGender: boolean,
    userAddress: string,
}
```
### interface
Interface는 선언전 병합이 가능합니다. 동일한 이름의 인터페이스가 선언될 경우 병합이 됩니다. 확장성이 좋습니다. 
또 클래스를 사용할 경우 상속이 가능합니다.
```typescript
interface IUserInfo {
    user_name: string
}

interface IUserInfo {
    user_age: number
}

interface IUserInfo {
    user_gender: boolean
}

const userInfo: IUserInfo = {
    user_name: "zero",
    user_age: 20,
    user_gender: true
}
```
### type
type도 병합이 가능합니다. 둘 다 병합에 사용할 수 있습니다.
```typescript
type UserInfoName = {
    user_name: string
}

type UserInfoAge = {
    user_age: number
}

const userInfo: UserInfoName & UserInfoAge = {
    user_name: "zero",
    user_age: 20,
}
```
확장성을 고려해..? API 같은 경우에는 Interface를 나머지는 type을 쓰는 경우도 있다고 합니다. 협업을 할 때 정해서 사용하면 될 것 같습니다.

## 타입스크립트의 유틸리티 타입

### enum
enum은 상수 값을 만들어줍니다. map과 같이 key와 value로 이루어집니다. key는 중복이 될 수 없습니다. 
만약 value를 지정하지 않을시 자동으로 위에서부터 숫자로 지정이 되게 됩니다. 
만약 맨 위에 값을 4로 지정해 둘시에는 그 뒤에 값부터는 5가 지정이 됩니다. 없을 경우 undefind가 반환 됩니다

대표적인 예시로는 boolean과 같은 타입이 있습니다.

만약 Enum을 사용하게 된다면 true, false만 하더라도 0과 1도 같이 상수 값으로 만들어지기 때문에 메모리 낭비가 있을 수 있습니다.
```typescript
enum booleanValue {
    false,
    true,
}

console.log(booleanValue[0]) // false
console.log(booleanValue['true']) // 1
```
### as const
as const는 해당 값을 상수로 바꿔줍니다. 객체와 같은 경우도 수정이 불가능해집니다.
```typescript
const userInfo = {
    name : "zero",
    age : 20,
    isMale : true
}

userInfo.name = "sugar"
// 일반적으로 선언한 객체의 경우 수정이 가능합니다.
```

```typescript
const userInfo = {
    name : "zero",
    age : 20,
    isMale : true
} as const;

userInfo.name = "sugar" // TS2540: Cannot assign to 'name' because it is a read-only property.
// as const의 경우 객체 자체가 상수가 되었기 때문에 수정이 불가능 합니다.
```

```typescript
const userInfo = {
    name : "zero",
    age : 20,
    isMale : true
}
```

특정 값만 상수로 바꿀수도 있습니다.
```typescript
const userInfo = {
    name: "zero" as const,
    age: 20,
    isMale: true
}
userInfo.name = "sugar" // TS2540: Cannot assign to 'name' because it is a read-only property.
userInfo.age = 30
```
### partial
partial은 일정한 부분만 추출해서 사용할 수 있습니다.
```typescript
type UserType = {
    name: string
    age: number
    isMale: boolean
}
// 타입들이 무조건 필요하지않고 있어도되고 없도도 됩니다. 옵셔널 타입과 같습니다.
const userPartial: Partial<UserType>[] = [
    {name: "sugar"},
    {age: 20},
    {isMale: false},
    {}
]
```
### omit
omit은 해당 타입을 제외한 나머지 부분만 사용을 합니다. 여러개를 제외하고 싶다면 ```|``` 연산자를 이용합니다. pick의 반대입니다.
```typescript
const userOmit1: Omit<UserType, "name"> = {
    isMale: false,
    age: 20
}

const userOmit2: Omit<UserType, "age" | "name"> = {
    isMale: false,
}
```
### pick
pick은 일정한 타입 부분만을 추출해서 사용하기 위해서 사용합니다. 여러개를 사용하고 싶다면 ```|``` 연산자를 이용합니다. omit의 반대입니다.
```typescript
const userName: Pick<UserType, "name"> = {
    name: "sugar"
}

const userNameWithAge: Pick<UserType, "name" | "age"> = {
    name: "sugar",
    age: 30,
}
```
### Extract
```Extract<T, U>``` Extract에는 T에 포함되는 U중에 일치하는 않는 값만 들어갈 수 있습니다.
Type에는 Union타입들이 들어가고 해당 Union 들어갈 수 있는 값들이 들어갑니다. Union에 값을 ```|```을 이용하여 추가할 수 있습니다.
```typescript
type arrayType = "top" | "left" | "right" | "bottom";
type topWithType = Extract<arrayType, "top"> // "top"

let isTop: topWithType = "top";
// arrayType에 "top"로 들어갈 수 있는건 "top" 뿐입니다.
```
### ExClude
```Exclude<T, U>``` ExClude에는 T에 포함되는 U중에 일치하지 않는 값만 들어갈 수 있습니다.
```typescript
type topWithOutType = Exclude<arrayType, "top">

let isNotType: topWithOutType = "bottom";
isNotType = "right"
// "top"을 제외한 모든 값들이 들어갈 수 있습니다.
```
### returntype
함수의 설계서와 같이 함수의 인수부터 반환까지의 타입을 지정하여 추출해줍니다. 반환타입을 추론하거나 다른 곳에서 사용할 때 유용합니다.
```typescript
type MakeUserType = ReturnType<(userInfo: UserType) => void>

function userMarker(newUser: UserType): MakeUserType {
    console.log(newUser)
    return;
}

userMarker({age: 20, isMale: true, name: "zero"});

// MakeUserType이라는 리턴타입의 지정된 인수와 다르거나 리턴타입이 다르면 오류가 발생합니다.
function coffeeMaker(coffee):MakeUserType{
    return "맛있는 커피" // TS2322: Type 'string' is not assignable to type 'void'.
}
```
### optional
optional 타입은 해당 타입을 전달을 받아도되고 안받아도 상관이 없습니다.
```typescript
type StudentType = {
    number?: number,
    name: string,
}

const st_zero: StudentType = {
    number: 0, name: "zero"
}

const st_one: StudentType = {
    number: 1, name: "sugar"
}

const teacher: StudentType = {
    name: "강선생"
}
```
### satisfies
satisfies는 객체 타입의 검사용으로 사용이 됩니다. 이는 타입스크립트 4.9 버전에서 출시한 것으로 기존에는 Record 타입으로 검사가 가능하였습니다.
```typescript
const Human: Record<'name' | 'age' | 'wishMovieList', string | string[]> = {
    name: "zero",
    age: "30",
    wishMovieList: ["aboutTime", "noteBook"]
}

// 위 코드에서는 문제점을 발견하지 못합니다. 하지만 아래에서 문제가 발생합니다.
Human.name.toUpperCase()

// name의 타입이 string | number | string[] 타입으로 인식이 되는 바람에 toUpperCase()를 사용하지 못하는 것입니다.
// satisfies를 사용한다면 타입을 알려줄 수 있습니다.

const isHuman = {
    name: "zero",
    age: "30",
    wishMovieList: ["aboutTime", "noteBook"]
} satisfies Record<'name' | 'age' | 'wishMovieList', string | string[]>

isHuman.name.toUpperCase()
// isHuman.name을 string으로 인식을 하기 때문에 toUpperCase()를 사용할 수 있습니다.
```

### generic
generic은 전달받은 타입을 기준으로 정해진 위치에 해당 타입에 대한 정보를 넣어줍니다.
```typescript
type UserGenericType<T, U, C> = {
    name: T
    age: U,
    isMan: C
}

const newUser1: UserGenericType<string, number, boolean> = {
    name: "zero",
    age: 20,
    isMan: true
}

// 지금 같은 경우에는 T에 number가 들어갔기 때문에 name에서 number 타입의 값을 원하고 있습니다.
const newUser2: UserGenericType<number, string, boolean> = {
    name: "zero", // TS2322: Type 'string' is not assignable to type 'number'.
    age: "20",
    isMan: true
}

// 리액트의 useState의 타입을 보면 제네릭이 잘 적용되어있는 것을 볼 수 있습니다. 
// 전달 받은 타입으로 값의 타입이 만들어지고 해당 값이 변환되는 함수가 있고 반환도 해당 타입으로 나와집니다.
type useStateType = ReturnType<<T>(initialState:T)=>[item:T, setItem:DisPatch<setStateAction<T>>]>
```
