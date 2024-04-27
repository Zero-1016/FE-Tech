# **tsconfig.json**

tsconfig 파일은 타입스크립트 파일을 자바스크립트로 컴파일 하는 과정에서의 세부설정이 가능합니다.

## 프로젝트 루트 설정

root옵션은 TypeScript 또는 JavaScript 프로젝트가 설정되는 방식과 관련됩니다.

#### `file` :

해당 파일을 컴파일 합니다. 없을시에 에러가 발생합니다.

> 보통 파일의 수가 적을 경우 `*` ( 와일드 카드 ) 대신해서 사용합니다.

#### `extends` :

모듈화한 다른 설정파일을 포함시키거나 확장할 때 사용을 합니다.

tsconfig.path.json

```json

{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"],
      "@apis/*": ["src/apis/*"],
      "@assets/*": ["src/assets/*"],

```

tsconfig.json

tsconfig.path.json파일들이 실제 작성된 것처럼 사용이 가능합니다.
extends로 받아온 파일보다 실제로 tsconfig.json 파일내의 코드가 우선순위입니다.

```json
{
  "extends": "./tsconfig.path.json",
  "compilerOptions": {
    "target": "esnext",
    "lib": ["esnext", "DOM", "DOM.Iterable"],
    "module": "ESNext",
```

#### `include` :

해당 파일내의 작성된 코드들만 타입을 검사할 대상이 됩니다.

tsconfig.json

src 폴더내의 ts파일들만 검사를 합니다.

```json
{
  "include": ["src/**/*.ts"]
}
```

#### `exclude` :

컴파일에서 제외할 폴더들을 골라줍니다. 만약 include에 포함이 되어있다고 하더라도 exclude에 포함이 되어있으면 제외됩니다. 우선순위가 더 높습니다.

#### `references` :

모노레포 프로젝트에서 프로젝트간의 종속성을 명시적으로 정의하고 컴파일 순서를 정의할 수 있습니다. 또한 각 프로젝트는 독립적으로 컴파일 될 수 있습니다.

```sass
my-monorepo/
  ├── tsconfig.json (루트 프로젝트의 설정)
  ├── admin/
  │     ├── tsconfig.json (admin 프로젝트의 설정)
  │     ├── src/
  │     │    ├── ...
  ├── web/
  │     ├── tsconfig.json (web 프로젝트의 설정)
  │     ├── src/
  │     │    ├── ...
```

```json
{
  "compilerOptions": {
    "target": "ES6"
  },
  "references": [{ "path": "./admin" }, { "path": "./web" }]
}
```

## 컴파일러 설정

컴파일할 때의 세부 설정이 가능합니다.

#### `target` :

타입스크립트 파일을 컴파일할 JS 버전을 선택합니다.

#### `module` :

`module` 의 타입을 결정할 수 있습니다.

```sass
- commonjs : CommonJS 모듈을 사용합니다.

- amd : AMD(Asynchronous Module Definition) 모듈 시스템을 사용합니다.

- es6: ECMAScript 6 (ES6) 모듈 시스템을 사용합니다.

- umd: Universal Module Definition을 사용하며, 다양한 환경에서 사용할 수 있습니다.

- system: SystemJS를 사용합니다.
```

> **module Tip**
>
> 1. IE 호환성을 원한다면 ES5, CommonJS가 좋습니다.
> 2. commonJS는 require 분법 es2015, exnext는 import 문법을 사용합니다.

#### 다양한 module 사용방법

```typescript
// CommonJS

import { valueOfPi } from './constants';

export const twoPi = valueOfPi * 2;
```

```typescript
// CommonJS

import { valueOfPi } from './constants';

export const twoPi = valueOfPi * 2;
```

```typescript
// Umd

(function (factory) {
  if (typeof module === 'object' && typeof module.exports === 'object') {
    var v = factory(require, exports);
    if (v !== undefined) module.exports = v;
  } else if (typeof define === 'function' && define.amd) {
    define(['require', 'exports', './constants'], factory);
  }
})(function (require, exports) {
  'use strict';
  Object.defineProperty(exports, '__esModule', { value: true });
  exports.twoPi = void 0;
  const constants_1 = require('./constants');
  exports.twoPi = constants_1.valueOfPi * 2;
});
```

```typescript
// AMD

define(['require', 'exports', './constants'], function (require, exports, constants_1) {
  'use strict';
  Object.defineProperty(exports, '__esModule', { value: true });
  exports.twoPi = void 0;
  exports.twoPi = constants_1.valueOfPi * 2;
});
```

```typescript
// System

System.register(['./constants'], function (exports_1, context_1) {
  'use strict';
  var constants_1, twoPi;
  var __moduleName = context_1 && context_1.id;
  return {
    setters: [
      function (constants_1_1) {
        constants_1 = constants_1_1;
      },
    ],
    execute: function () {
      exports_1('twoPi', (twoPi = constants_1.valueOfPi * 2));
    },
  };
});
```

```typescript
// ESNext

import { valueOfPi } from './constants';
export const twoPi = valueOfPi * 2;
```

```typescript
// ES2015 / ES6 / ES2020 / ES2022

import { valueOfPi } from './constants';
export const twoPi = valueOfPi * 2;
```

## 엄격한 설정을 위한 옵션

<br>

#### `allowUnreachableCode` :

연결할 수 없는 코드에 대한 설정을 제공합니다.

- `true` : 구문에 도달하지 못하는 코드가 있어도 무시합니다.
- `false` : 구문에 도달하지 못하는 코드가 있을시에 에러를 나타냅니다.
- `undefined` : 구문에 도달하지 못하는 코드가 있을시에 경고를 나타냅니다.

```typescript
function fn(n: number) {
  if (n > 5) {
    return true;
  } else {
    return false;
  }
  // 해당 구문에 도달할 수 없습니다.
  return true;
}
```

#### `allowUnusedLabels` :

선언되지 않은 변수에 대한 참조에 대해 설정을 합니다.

- `true` : 선언되지 않은 변수가 있어도 무시합니다.
- `false` : 선언되지 않은 변수가 있을 시에 에러를 나타냅니다.
- `undefined` : 선언되지 않은 변수가 있을시에 경고를 나타냅니다.

```typescript
function verifyAge(age: number) {
  // Forgot 'return' statement
  if (age > 18) {
    verified: true;
    // Unused label.
  }
}
```

#### `strict` :

엄격한 검사를 지원합니다. "noImplicit" 관련 옵션들을 전부 활성화 합니다.

#### `alwaysStrict` :

파일들을 strict 모드로 검사하고 파일 최상단에 "use strict"를 생성합니다.

```sass
strict 모드의 주요 제약사항

1. 변수 선언: 모든 변수 선언은 var 키워드 대신 let 또는 const 키워드로 선언되어야 합니다.

2. 선언되지 않은 변수 사용: 선언되지 않은 변수를 사용하려고 하면

3. 삭제 금지: delete 연산자를 사용하여 변수, 함수, 함수 매개변수 등을 삭제할 수 없습니다.

4. 매개변수 이름 중복 금지: 함수 매개변수의 이름은 중복될 수 없습니다.

5. this 사용: this가 undefined 또는 전역 객체(window)가 아닌 컨텍스트 내에서 사용될 때 오류가 발생합니다.

6. 객체 리터럴 중복 속성 금지: 객체 리터럴 내에서 중복된 속성 이름을 사용할 수 없습니다.

7. eval 사용 금지: eval 함수를 사용할 수 없으며 with 문도 사용할 수 없습니다.

8. 예약어 사용: strict 모드에서 추가로 예약된 키워드들이 있으며, 이러한 예약어를 변수나 함수 이름으로 사용할 수 없습니다.

9. arguments 객체 변경 금지: strict 모드에서 arguments 객체를 변경하는 것이 제한됩니다.

10. 함수의 중복 파라미터 금지: 함수 파라미터 목록에서 중복된 파라미터 이름을 사용할 수 없습니다.
```

#### `strictNullChecks` :

null 타입과 undefined 타입에 대해 좀 더 엄격하게 검사를 진행합니다.

```typescript
let name: string = null; // 오류: null은 string에 할당할 수 없음
let age: number = undefined; // 오류: undefined는 number에 할당할 수 없음

const greet = (message: string) => {
  console.log(message);
};

greet(null); // 오류: null은 string 매개변수에 할당할 수 없음
```

#### `strictFunctionTypes` :

함수 파라미터의 타입체크를 엄격하게 검사합니다.

```typescript
// strictFunctionTypes 활성화
type Animal = { name: string };
type Dog = { name: string; breed: string };

type AnimalHandler = (animal: Animal) => void;

const handleDog: AnimalHandler = (dog: Dog) => {
  // 오류: Dog 타입을 AnimalHandler에 할당할 수 없음
};

// 비활성화시에 작동이 정상적으로 이루어집니다.
```

#### `exactOptionalPropertyTypes` :

optional property (`?`) 에 대한 정보를 설정합니다.

```typescript
type ExampleType = {
  property?: string;
};
// "exactOptionalPropertyTypes": true로 설정을 하였을 경우 해당 property의 타입은 string | undefined가 아닌 string
```

#### `strictPropertyInitialization` :

클래스 프로퍼티의 초기화를 강제할지 설정을 합니다.

#### `noFallthroughCasesInSwitch` :

switch 문에서 fall-through를 허용할 지 결정합니다.

```typescript
// fall-through란 case 블록을 벗어나서 다음 케이스까지 진행이 되는 것을 의미합니다.

switch (value) {
  case 1:
    console.log('Value is 1');
  case 2:
    console.log('Value is 2');
  case 3:
    console.log('Value is 3');
}

// 활성화 할 경우 다음과 같은 상황에서 경고를 나타내줍니다.
```

#### `noImplicitThis` :

this의 암시적 any 타입을 사용하거나 방치할지 결정합니다.

#### `noImplicitAny` :

any 타입을 사용하거나 방치할지 결정합니다.

#### `noImplicitOverride` :

클래스를 상속받아 override 메소드를 구현할 때 override 키워드를 강제화할지 결정합니다.

#### `noImplicitReturns` :

함수의 리턴문이 없어도 사용이 가능하게 할 지 결정합니다. 리턴을 강제할 수 있습니다.

#### `noPropertyAccessFromIndexSignature` :

객체에 접근할 때 점 표기법을 사용할 지 또는 꺽쇠 괄호 표기법을 사용하게 할 지 결정할 수 있습니다.

```typescript
// noPropertyAccessFromIndexSignature : true의 경우

const obj: { [key: string]: number } = {
  propertyName: 42,
};

const value = obj.propertyName; // 이 부분에서 경고 또는 오류 발생
```

#### `noUncheckedIndexedAccess` :

배열이나 객체의 접근을 할 때 null 이나 undefined와 같은 상황을 계산하고 진행하였는지 체크합니다.

```typescript
// noUncheckedIndexedAccess : default(true)

function getElementAtIndex(arr: string[], index: number) {
  return arr[index]; // false일 경우 이 부분에서 경고 또는 오류 발생
}
```

#### `noUnusedLocals` :

쓰지 않는 지역변수가 있는지 검사를 합니다.

#### `noUnusedParameters` :

쓰지 않는 파라미터가 있는지 검사를 합니다.

#### `strictBindCallApply` :

strict 모드를 활성화 함으로서 `bind`, `call`, `apply`와 같은 메소드의 사용을 엄격하게 제한하며 인수의 개수와 유형을 보다 엄격하게 검사합니다. (엄격한 검사로 인해 코드의 가독성이 저하될 수 있습니다.)

```typescript
// strictBindCallApply가 활성화가 되어있는 경우

function greet(name: string) {
  console.log(`Hello, ${name}!`);
}

greet.call(null, 'John', 42); // 이 부분에서 경고 또는 오류 발생
```

#### `useUnknownInCatchVariables` :

catch 블록에서 예외(Exception)의 유형을 unknown으로 설정을 합니다. 기존에는 any 타입이었기에 모든 작업을 허용했지만 해당 옵션을 활성화 할 시 타입 안정성을 유지하면서 에러를 처리할 수 있습니다.

```typescript
// useUnknownInCatchVariables : true의 경우

try {
  // 예외를 던지는 코드
} catch (error) {
  console.log(error.message); // 이 부분에서도 오류 발생
}

// message를 사용하고 싶다면 다음과 같이 접근을 해야합니다.

try {
  // 예외를 던지는 코드
} catch (error: unknown) {
  if (error instanceof Error) {
    console.log(error.message); // 예외 객체의 message 프로퍼티를 안전하게 사용
  } else {
    console.log('Unknown error occurred');
  }
}
```

#### `baseUrl` :

모듈 해석 및 모듈 경로 해석에 사용되는 기본 기준 경로(base path)를 설정하는 데 사용됩니다. 해당 옵션을 사용할 경우 컴파일러가 상대경로가 아닌 지정경로를 기준으로 찾을 수 있습니다.

```json
{
  "compilerOptions": {
    "baseUrl": "./src"
  },
  "include": ["./src/**/*.ts"]
}
```

```typescript
import { TodoList } from './todo';
```

#### `moduleResolution` :

모듈 해석 방법을 선택하는 옵션입니다.

```sass
- classic : 타입스크립트 1.6 이전 버전에서 사용한 쓰면 안되는 옵션입니다.

- node10 : 이전에는 `node`라고 하였습니다. CommonJS만 지원을 하고 CommonJS의 require만 지원을 합니다.

- node16 | nodenext : node 12버전 이상의 경우 import와 export, require를 모두 지원하며 상황에 따라 다른 해결방법을 사용합니다.

- bundler : node16 | nodenext와 마찬가지로 import와 export를 지원하지만 상대경로에는 파일 확장자가 필요하지 않습니다.
```

#### `moduleSuffixes` :

모듈 이름의 파일 확장자를 지정하는데 사용됩니다. 이 옵션을 사용하면 모듈 이름 뒤에 특정 확장자를 자동으로 추가할 수 있습니다.

CommonJS 모듈 형식을 사용하면서 모듈 이름뒤에 js, ts 확장자를 자동으로 붙입니다.

```json
{
  "compilerOptions": {
    "module": "CommonJS",
    "moduleSuffixes": [".js", ".ts"]
  }
}
```

```typescript
// 다음과 같은 가져오기를 설정할 수 있습니다.
import * as todo from './todo';
```

#### `noResolve` :

모듈 해석을 실행하지 않고 모듈 이름을 해석하지 않도록 해주는 옵션입니다. 설정할 경우, TypeScript 컴파일러는 모듈 이름을 해석하지 않고, 모듈 간의 의존성을 추적하지 않습니다.

#### `paths` :

모든 경로를 원하는 경로로 매핑하고 모듈을 더 간단한 이름으로 가져올 수 있습니다.

```json
"compilerOptions": {
    "baseUrl": "./src",
    "paths": {
      "@type/*": ["type/*"]
    }
  },
```

```typescript
import { SomeModule } from '@type/todo';
```

실제로는 "src/type/todo"을 가져옵니다.

#### `resolveJsonModule` :

JSON파일을 모듈로 가져올 수 있게 해줍니다.

```typescript
// "resolveJsonModule": true

import * as jsonData from './data.json';

console.log(jsonData.someProperty);
```

#### `resolvePackageJsonExports`:

package.json 파일 내의 exports 필드에 정의된 모듈 경로를 사용하여 모듈을 가져옵니다.

package.json

```json
{
  "exports": {
    "./foo": "./src/foo.js",
    "./bar": "./src/bar.js"
  }
}
```

컴파일러가 ./foo 모듈을 실제로 ./src/foo.js 파일로 가져오게 만듭니다.

#### `resolvePackageJsonImports` :

package.json 파일의 imports 필드는 모듈의 다양한 경로를 정의하고, TypeScript 컴파일러가 모듈을 어떻게 가져와야 하는지 제어하는 데 사용됩니다.

```json
{
  "imports": {
    "some-library": "./src/some-library.js",
    "another-library": "./src/another-library.js"
  }
}
```

이 설정은 컴파일러가 import { someFunction } from "some-library"를 실제로 ./src/some-library.js 파일로 해석하게 만듭니다.

#### `rootDir` :

프로젝트 내에서 TypeScript 소스 파일이 위치한 최상위 디렉토리를 지정하는 옵션입니다. TypeScript 컴파일러가 소스 파일이 있는 디렉토리를 명시적으로 설정할 수 있습니다.

```json
{
  "compilerOptions": {
    "rootDir": "./src"
  },
  "include": ["./src/**/*.ts"]
}
```

```sass
kkiri
  ├── tsconfig.json (루트 프로젝트의 설정)
  ├── src
  │     ├── a.ts
  │     ├── b.ts
  │     ├── sub
  │     │    ├── c.ts
  ├── type.d.ts

```

위 같은 경우 baseurl은 src이며 build가 되었을 때 다음과 같이 파일이 만들어집니다.

```sass

kkiri
  ├── dist
  │     ├── a.ts
  │     ├── b.ts
  │     ├── sub
  │     │    ├── c.ts

```

만약 다음과 같은 상황일 시에는 오류가 발생합니다. (helpers.ts가 버려지기 때문에)

```sass
kkiri
├── tsconfig.json
├── core
│   ├── a.ts
│   ├── b.ts
├── helpers.ts
```

#### `rootDirs` :

root 역할을 하는 폴더가 여러개가 있음을 나타냅니다. 이를 컴파일러에게 알릴 수 있습니다. 이를 통해 컴파일러는 마치 하나의 디렉토리에 병합된 것처럼 병합이 됩니다.

```json
{
  "compilerOptions": {
    "rootDirs": ["app", "page"]
  }
}
```

```sass
kkiri
├── tsconfig.json
├── app
│   ├── a.ts
│   └── b.ts
├── page
│   ├── c.ts
│   └── c.ts

...

kkiri
├── dist
│     ├── a.ts
│     ├── b.ts
│     ├── c.ts
│     ├── d.ts

```

위가 빌드되기전 아래가 빌드되고 난 뒤에 모습입니다.

#### `typeRoots` :

TypeScript가 타입 정의 파일을 찾을 수 있는 디렉토리를 지정하는 역할을 합니다. 기본적으로는 node_modules/@types 디렉토리를 기본 파일로 설정을 하지만 명시적으로 작성이 되어있을 경우 node_modules/@types 디렉토리를 기본 파일로 사용하지 않습니다. 계속해서 사용하고 싶다면 이또한 추가를 해주어야합니다.

> 타입 정의 파일은 주로 .d.ts 확장자를 가진 파일로, 외부 라이브러리나 프레임워크의 타입 정보를 제공하는 역할을 합니다.

#### `types` :

프로젝트에서 사용할 타입 정의 파일을 명시적으로 지정하는 역할을 합니다. typeRoots와 같이 기본적으로 node_modules/@types 디렉토리에서 타입 정의 파일을 자동으로 찾아 사용하는 기능을 비활성화시키고, 명시적으로 지정된 타입 정의 파일만 사용하도록 설정합니다. 하지만 다른 점은 typeRoots와 같이 특정 폴더를 지정하는 것이 아니라 직접적으로 파일 이름을 나열하는 방식으로 사용합니다.

## 출력 설정

컴파일된 이후에 대한 설정입니다.

#### `declaration` :

TypeScript 컴파일러가 .ts 파일을 컴파일한 결과물로 .d.ts 확장자를 가진 타입 선언 파일을 생성할지 여부를 설정하는 옵션입니다. 컴파일 된 이후에 주로 build나 dist 디렉토리에 위치하게 됩니다.

#### `declarationDir` :

컴파일된 이후 저장된 폴더를 결정합니다.

```sass
example
├── index.ts
├── package.json
└── tsconfig.json
```

```json
{
  "compilerOptions": {
    "declaration": true,
    "declarationDir": "./types"
  }
}
```

```sass
example
├── index.js
├── index.ts
├── package.json
├── tsconfig.json
└── types
    └── index.d.ts
```

#### `declarationMap` :

컴파일 이후 타입 선언 파일(.d.ts) 파일과 소스 맵 파일(.js.map)을 생성할 지 여부를 지정하는 옵션입니다. JavaScript 코드와 컴파일된 TypeScript 코드 간의 매핑 정보를 담은 파일입니다. 이를 통해 디버깅 시 원본 TypeScript 코드에서 발생한 오류를 추적하거나, 개발자 도구에서 정확한 소스 위치를 제공할 수 있습니다.

#### `downlevelIteration` :

for ... of 루프와 같은 ES6 이터레이션 프로토콜을 사용하는 코드를 ES3 혹은 ES5와 같이 이전 버전의 JS로 변환할 때 관련된 코드를 생성할 정할 수 있습니다. (ex : for ... of => for ... in 코드로 대체됩니다.)

#### `emitBOM` :

컴파일러가 생성하는 JS 코드 시작부분에 BOM을 포함할 지 여부입니다.

> BOM은 유니코드 인코딩에서 텍스트 파일의 시작 부분에 추가되는 특수한 바이트 시퀀스입니다. UTF-8과 같은 인코딩에서 사용되며, 파일이 해당 인코딩으로 인코딩 되었음을 나타냅니다.

#### `emitDeclarationOnly` :

TypeScript 컴파일러가 .ts 소스 파일 대신에 타입 선언 파일(.d.ts)만 생성하도록 지시하는 옵션입니다. JS 파일은 생성되지 않고 타입 선언 파일만 생성이 됩니다. 이는 주로 라이브러리나 패키지를 만들 때 사용이 됩니다.

#### `importHelpers` :

TypeScript 컴파일러가 `__importDefault`와 같은 도움 함수를 사용하여 ES 모듈을 임포트하는 코드를 생성할지 여부를 지정하는 옵션입니다.

#### `importsNotUsedAsValues` :

TypeScript 컴파일러가 모듈 임포트 구문을 값으로 사용하지 않는 경우 해당 구문을 제거할지 여부를 지정하는 옵션입니다. 만약 모듈 임포트 구문이 값을 사용하지 않을 경우에 해당 구문을 컴파일시에 제거합니다.

#### `inlineSourceMap` :

TypeScript 컴파일러가 생성하는 소스 맵(Source Map) 파일을 컴파일된 JavaScript 파일에 인라인으로 포함할지 여부를 지정하는 옵션입니다. 해당 옵션을 활성화 할 시 소스 맵 파일을 별도의 파일로 생성하지 않고, 컴파일된 JavaScript 파일 내에 인라인으로 포함시킵니다. 그러나 javascript 코드를 증가시킨다는 단점이 큽니다.

#### `inlineSources` :

TypeScript 컴파일러가 컴파일된 JavaScript 코드에 원본 TypeScript 코드를 인라인으로 포함할지 여부를 지정하는 옵션입니다. 컴파일된 JavaScript 코드 내에 원본인 TypeScript 코드를 인라인으로 포함시킵니다. 이를 통해 디버깅시에 JavaScript 내에서 원본 코드를 확인할 수 있습니다. 그러나 이 또한 javascript 코드를 증가시킨다는 단점이 큽니다.

#### `mapRoot` :

소스 맵(Source Map) 파일의 경로를 지정하는 옵션입니다.

> 소스 맵은 컴파일된 JavaScript 코드와 원본 TypeScript 코드 간의 매핑 정보를 제공하는 파일입니다.

#### `newLine` :

파일을 내보낼 때 줄바꿈을 지정합니다 : ‘CRLF’ (docs) 또는 ‘LF’ (unix).

#### `noEmit` :

TypeScript 컴파일러가 JavaScript 코드를 생성하지 않도록 지정하는 옵션입니다. TypeScript 컴파일러는 소스 코드를 분석하고 유효성을 검사하지만, 실제로는 JavaScript 코드를 생성하지 않습니다.

#### `noEmitHelpers` :

TypeScript 컴파일러가 헬퍼 함수(helper functions)를 생성하지 않도록 지정하는 옵션입니다.

> 헬퍼 함수는 TypeScript에서 특정 기능을 지원하기 위해 필요한 보조 함수들입니다. **extends, **awaiter, \_\_decorate 등의 함수로 구성됩니다.

#### `noEmitOnError` :

TypeScript 컴파일러가 컴파일 오류가 발생한 경우에만 JavaScript 코드를 생성하도록 지정하는 옵션입니다. true로 설정하면 컴파일러는 컴파일 오류가 발생한 경우에만 JavaScript 코드를 생성하고, 오류가 없는 경우에는 코드를 생성하지 않습니다.

#### `outDir` :

TypeScript 컴파일러가 컴파일된 JavaScript 파일을 출력할 디렉토리를 지정하는 옵션입니다.

#### `outFile` :

TypeScript 컴파일러가 모든 모듈을 단일 파일로 번들링할 때 사용하는 옵션입니다. 이를 통해 여러 TypeScript 모듈 파일을 하나의 JavaScript 파일로 결합할 수 있습니다.

> outFile 옵션을 사용할 때는 module 옵션을 "amd"나 "system"으로 설정해야 합니다.

#### `preserveConstEnums` :

TypeScript 컴파일러가 const enum을 보존하도록 지정하는 옵션입니다. preserveConstEnums 옵션을 사용하면 const enum을 별도의 객체로 유지할 수 있으며, 가독성과 디버깅 편의성을 향상시킬 수 있습니다.

즉시실행 함수를 통해 Direction에 Enum 값을 대입하고 있습니다. 이를 통해 객체와 같이 접근이 가능합니다.

```typescript
let Direction;
(function (Direction) {
  Direction['Up'] = 'UP';
  Direction['Down'] = 'DOWN';
  Direction['Left'] = 'LEFT';
  Direction['Right'] = 'RIGHT';
})(Direction || (Direction = {}));

function move(direction) {
  if (direction === Direction.Up) {
    console.log('Moving up');
  } else if (direction === Direction.Down) {
    console.log('Moving down');
  } else if (direction === Direction.Left) {
    console.log('Moving left');
  } else if (direction === Direction.Right) {
    console.log('Moving right');
  } else {
    console.log('Invalid direction');
  }
}

move(Direction.Up);
```

#### `removeComments` :

컴파일시에 모든 주석을 제거합니다.

#### `sourceMap` :

컴파일시에 소스맵 파일 생성을 할성화하는 옵션입니다.

#### `sourceRoot` :

TypeScript 컴파일러가 생성하는 컴파일된 JavaScript 파일의 소스 맵(Source Map)에 포함되는 소스 파일의 기본 경로를 지정하는 옵션

#### `stripInternal` :

TypeScript 컴파일러가 내부(Internal)로 지정된 멤버를 제거하도록 지시하는 옵션입니다.

> 내부 멤버란 일반적으로 API 문서화를 위해 사용되지 않는 비공개(private) 멤버나 내부 구현을 위한 멤버를 말합니다.

## 자바스크립트 설정

자바스크립트에 관련된 세팅입니다.

#### `allowJs` :

`.ts` 파일 대신 `.js` 파일이 프로젝트 내부로 들어올 수 있게 허용합니다. 이를 통해 js와 ts가 공존하는 프로젝트를 만들 수 있습니다.

#### `checkJs` :

TypeScript 컴파일러가 JavaScript 파일을 타입 체크(Type Checking)하는지 여부를 지정하는 옵션입니다.

```javascript
// parseFloat only takes a string
module.exports.pi = parseFloat(3.142);

// parseFloat는 string 값만 인자로 받을 수 있기 때문에 에러가 발생합니다.
```

#### `maxNodeModuleJsDepth` :

TypeScript 컴파일러가 Node.js 모듈의 .js 파일을 재귀적으로 탐색하는 깊이(depth)의 최대값을 지정하는 옵션입니다. 이 옵션은 주로 프로젝트의 모듈 구조가 복잡하고 깊게 중첩된 경우에 사용됩니다.

## 편집기 지원 설정

#### `disableSizeLimit` :

TypeScript 컴파일러에 대한 사이즈 제한을 비활성화하는 옵션입니다. 기본적으로 타입스크립트는 컴파일된 JavaScript 크기에 대한 제한을 두고 있습니다. 하지만 이 옵션을 설정함으로서 컴파일러가 제한 없이 컴파일 할 수 있습니다.

#### `plugins` :

에디터 내에서 실행할 언어 서비스 플러그인 목록을 나타냅니다. 사용자 정의 플러그인을 구성하기 위해 사용되는 옵션입니다. 플러그인은 컴파일러의 동작을 확장하거나 사용자 정의 기능을 추가하기 위해 사용됩니다.

```sass
예를 들면 다음과 같은 플러그인들을 추가할 수 있습니다.

ts-sql-plugin — 템플릿 문자열 SQL 생성기로 SQL 린팅(linting)을 추가합니다.

typescript-styled-plugin — 템플릿 문자열 내부에 CSS 린팅(linting)을 제공합니다.

typescript-eslint-language-service — 컴파일러 출력 내부에 eslint 오류 메시징 및 수정 기능을 제공합니다.

ts-graphql-plugin — GraphQL 쿼리 템플릿 문자열 내에서 검증 및 자동 완성을 제공합니다.
```

## 상호운용성 제약 설정

#### `allowSyntheticDefaultImportstrue` :

TypeScript 컴파일러가 import 문에서 default import를 합성할 수 있는지 여부를 지정하는 옵션입니다.

```typescript
// allowSyntheticDefaultImportstrue : true

import * as React from "react";

// 만약 모듈이 allowSyntheticDefaultImportstrue를 설정하지 않았고
// default export를 명시하지 않은 경우 다음과 같이 에러가 발생합니다.

// @filename: utilFunctions.js
Module '"/home/runner/work/TypeScript-Website/TypeScript-Website/utilFunctions"' has no default export.
const getStringLength = (str) => str.length;

module.exports = {
  getStringLength,
};

// @filename: index.ts
import utils from "./utilFunctions";

const count = utils.getStringLength("Check JS");
```

```typescript
// CommonJS 모듈 에서 export default로 내보내는 방법

// @filename: utilFunctions.js
const getStringLength = (str) => str.length;

module.exports = {
  default: {
    getStringLength,
  },
};
```

#### `esModuleInterop` :

TypeScript에서 CommonJS 모듈 시스템과 ES 모듈 시스템 간의 상호 운용성을 개선하는 옵션입니다. TypeScript는 기본적으로 CommonJS 모듈 시스템과 ES 모듈 시스템 간의 모듈 가져오기 및 내보내기에 제한이 있습니다. 이러한 차이점을 해결하고 원활한 상호 운용성을 제공하기 위해 도움을 줍니다.

```sass
다음과 같은 이점을 얻을 수 있습니다.
1. import 문에서 기본 가져오기(default import)를 사용할 수 있습니다.

2. ES 모듈에서 CommonJS 모듈을 import 문을 사용하여 가져올 수 있습니다.

3. CommonJS 모듈에서 ES 모듈을 require 문을 사용하여 가져올 수 있습니다.
```

```typescript
import myModule from './myModule'; // 기본 가져오기(default import)

const result = myModule.someFunction(); // 사용자 정의 모듈에서 함수 호출
```

위의 예시에서는 기본 가져오기(default import)를 사용하여 ./myModule 모듈을 가져오고 있습니다. 이렇게 하면 해당 모듈에서 someFunction 함수를 호출할 수 있습니다.

#### `forceConsistentCasingInFileNames` :

파일 이름의 일관된 대소문자 표기를 강제합니다. 기본적으로는 대소문자가 일치하지 않아도 되지만 이를 일치시키게 강제할 수 있습니다.

#### `isolatedModules` :

TypeScript의 옵션 중 하나로, 모듈을 독립적으로 컴파일하는 것을 강제하는 역할을 합니다. 이는 파일 간의 상호 의존성을 제한하고, 전역 네임스페이스 충돌을 방지하는 데 도움이 됩니다. 각 파일은 자체적인 모듈 스코프를 갖고, 모듈 간의 의존성은 명시적으로 import 문으로 선언해야 합니다.

#### `verbatimModuleSyntax` :

TypeScript 컴파일러에게 모듈 구문을 완전히 유지하도록 지시하는 역할을 합니다. 동시에 import type이라는 수정자(modifier)가 있는 import 구문은 TypeScript에서 해당 구문을 삭제하고 나머지 구문만 남기는 역할도 합니다.

#### `noStrictGenericChecks` :

TypeScript 컴파일러가 제네릭 타입의 사용을 더 유연하게 처리하도록 지시할 수 있습니다. 해당 옵션을 사용하는 컴파일러는 제네릭 타입의 인수에 대해 더 유연한 할당 및 호환성 검사를 수행하며, 일부 타입 에러를 무시하거나 경고로 처리할 수 있습니다.

```typescript
// noStrictGenericChecks : false
type A = <T, U>(x: T, y: U) => [T, U];
type B = <S>(x: S, y: S) => [S, S];

function f(a: A, b: B) {
  b = a; // Ok
  a = b; // Error

Type 'B' is not assignable to type 'A'.
  Types of parameters 'y' and 'y' are incompatible.
    Type 'U' is not assignable to type 'T'.
      'T' could be instantiated with an arbitrary type which could be unrelated to 'U'.
}

---
// noStrictGenericChecks : true
type A = <T, U>(x: T, y: U) => [T, U];
type B = <S>(x: S, y: S) => [S, S];

function f(a: A, b: B) {
  b = a;
  a = b;

```

#### `suppressExcessPropertyErrors` :

프로퍼티의 속성의 갯수에 오류에 대해서 관리합니다. (안 사용하는 것이 좋습니다.)

```typescript
// 기본적으로 다음과 같은 사항에서 에러표시가 납니다. 하지만 설정을 할 시 에러가 나지 않습니다.

type Point = { x: number; y: number };
const p: Point = { x: 1, y: 3, m: 10 };
Type '{ x: number; y: number; m: number; }' is not assignable to type 'Point'.
  Object literal may only specify known properties, and 'm' does not exist in type 'Point'.
```

#### `suppressImplicitAnyIndexErrors` :

객체에 대한 인덱스 접근에 암묵적으로 any 타입으로 접근하였을 때 에러를 무시할 수 있습니다. 위험한 방법이므로 `@ts-ignore`를 사용하는 것이 좋습니다.

## 언어와 환경 설정

#### `jsx` :

JavaScript 파일에서 JSX 구문을 내보내는 방법을 관리합니다.

```sass
1. react: React 프레임워크와 함께 JSX를 사용합니다.

2. react-jsx: React 프레임워크와 함께 JSX를 사용합니다. 런타임에서 React.createElement를 호출하는 방식으로 변환됩니다.

3. react-jsxdev: 개발 환경에서의 React 프레임워크와 함께 JSX를 사용합니다. 디버그 정보를 포함한 변환을 수행합니다.

4. preserve: JSX를 변환하지 않고 그대로 유지합니다. 일반적으로 Babel과 같은 도구를 사용하여 JSX를 변환하는 경우에 사용됩니다.

5. react-native:  React Native 애플리케이션에서 JSX를 사용합니다.
```

`react-jsx`

```typescript
import { jsx as _jsx } from 'react/jsx-runtime';
import React from 'react';
export const HelloWorld = () => _jsx('h1', { children: 'Hello world' });
```

`react-jsxdev`

```typescript
import { jsxDEV as _jsxDEV } from 'react/jsx-dev-runtime';
const _jsxFileName = '/home/runner/work/TypeScript-Website/TypeScript-Website/index.tsx';
import React from 'react';
export const HelloWorld = () =>
  _jsxDEV(
    'h1',
    { children: 'Hello world' },
    void 0,
    false,
    { fileName: _jsxFileName, lineNumber: 9, columnNumber: 32 },
    this
  );
```

#### `jsxFactory` :

타입스크립트 컴파일을 할 때 사용할 JSX 팩토리 함수를 지정할 수 있습니다. 리액트에서는 기본적으로 `React.createElement` 함수가 JSX 팩토리 함수로 사용됩니다. 하지만 다른 라이브러리나 프레임워크에서는 JSX 팩토리 함수가 다를 수 있습니다. 이에 함수를 지정함으로서 다른 라이브러리나 프레임워크를 사용할 수 있습니다.

#### `jsxFragmentFactory` :

JSX 프래그먼트를 생성하는 데 사용하는 팩토리 함수를 지정하는데 사용합니다. JSX 코드에서 여러 요소를 하나의 그룹으로 묶을 수 있는 기능입니다. `jsxFactory`와 같이 React에서는 기본적으로 `<React.Fragment>` 구문을 사용하여 JSX 프래그먼트를 생성할 수 있습니다. 하지만 다른 라이브러리나 프레임워크에서는 JSX 프래그먼트를 다른 방식으로 생성하는 경우가 있을 수 있습니다. 이에 팩토리 함수를 지정함으로서 다른 라이브러리나 프레임워크를 사용할 수 있습니다.

#### `jsxImportSource` :

JSX 변환 시 사용할 JSX 팩토리 함수의 모듈 경로를 지정하는데 사용됩니다. JSX 팩토리 함수는 JSX 코드를 변환하는 데 사용되는 함수입니다. 일반적으로 React에서는 `React.createElement` 함수가 JSX 팩토리 함수로 사용됩니다. 하지만 다른 라이브러리나 프레임워크에서는 JSX 팩토리 함수가 다를 수 있습니다. 이에 JSX 팩토리 함수를 지정함으로서 다른 라이브러리나 프레임워크를 사용할 수 있습니다.

#### `lib` :

프로젝트에서 사용할 라이브러리의 타입 정의 파일을 지정하는데 사용됩니다. lib 옵션을 사용하여 타입 정의 파일을 지정하면 TypeScript 컴파일러는 해당 라이브러리의 타입 정보를 이용하여 코드 검사와 타입 체크를 수행합니다.

```json
{
  "compilerOptions": {
    "lib": ["es6", "dom"]
  }
}
```

위의 예시에서는 `"lib": ["es6", "dom"]`로 설정하여 TypeScript 컴파일러에게 ES6와 DOM에 대한 타입 정의 파일을 사용하도록 지시하고 있습니다. 이렇게 설정하면 컴파일러는 ES6와 DOM 관련 기능에 대한 타입 체크를 수행할 때 해당 타입 정의 파일을 참조합니다. 필요한 라이브러리에 따라 해당 라이브러리의 이름을 배열로 나열하여 지정할 수 있습니다.

**상위레벨 라이브러리**

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Content</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>ES5</td>
      <td>모든 ES3 및 ES5 기능에 대한 핵심적인 정의</td>
    </tr>
    <tr>
      <td>ES2015</td>
      <td>ES2015(또는 ES6로 알려진)에서 추가로 사용 가능한 API - array.find, Promise, Proxy, Symbol, Map, Set, Reflect, 기타 등등</td>
    </tr>
    <tr>
      <td>ES6</td>
      <td>“ES2015” 의 별칭</td>
    </tr>
    <tr>
      <td>ES2016</td>
      <td>ES2016에서 추가로 사용 가능한 API - array.include, 기타 등등</td>
    </tr>
    <tr>
      <td>ES7</td>
      <td>“ES2016” 의 별칭</td>
    </tr>
    <tr>
      <td>ES2017</td>
      <td>ES2017 에서 추가로 사용 가능한 API - Object.entries, Object.values, Atomics, SharedArrayBuffer, date.formatToParts, typed arrays, 기타 등등</td>
    </tr>
    <tr>
      <td>ES2018</td>
      <td>ES2018 에서 추가로 사용 가능한 API - async iterables, promise.finally, Intl.PluralRules, rexexp.groups, 기타 등등</td>
    </tr>
    <tr>
      <td>ES2019</td>
      <td>ES2019 에서 추가로 사용 가능한 API - array.flat, array.flatMap, Object.fromEntries, string.trimStart, string.trimEnd, 기타 등등</td>
    </tr>
    <tr>
      <td>ES2020</td>
      <td>ES2020 에서 추가로 사용 가능한 API - string.matchAll, etc.</td>
    </tr>
    <tr>
      <td>ESNext</td>
      <td>ESNext 에서 추가로 사용 가능한 API - 이는 JavaScript의 사양이 향상될 때 마다 변함.</td>
    </tr>
    <tr>
      <td>DOM</td>
      <td><a href="https://developer.mozilla.org/ko/docs/Glossary/DOM">DOM 정의</a> - window, document, etc.</td>
    </tr>
    <tr>
      <td>WebWorker</td>
      <td><a href="https://developer.mozilla.org/ko/docs/Web/API/Web_Workers_API/Using_web_workers">웹 워커(WebWorker)</a> 환경에서 추가로 사용 가능한 API</td>
    </tr>
    <tr>
      <td>ScriptHost</td>
      <td><a href="https://en.wikipedia.org/wiki/Windows_Script_Host">윈도우 스크립트 호스트 시스템(Windows Script Hosting System)</a> 을 위한 API</td>
    </tr>
  </tbody>
</table>

#### `moduleDetection` :

파일이 스크립트인지 모듈인지를 결정하는 방법을 제어합니다.

```sass
1. "auto" (기본값): 이 모드에서는 TypeScript가 import 및 export 문을 확인하는 것뿐만 아니라 추가적인 조건도 고려합니다. module 옵션을 "nodenext" 또는 "node16"로 설정한 경우, TypeScript는 package.json 파일의 "type" 필드가 "module"로 설정되어 있는지 확인합니다. 또한 jsx 옵션을 "react-jsx"로 설정한 경우, 현재 파일이 JSX 파일인지 확인하여 모듈로 처리할지 여부를 결정합니다.

2. "legacy": 이 모드는 TypeScript 4.6 버전 및 이전 버전과 동일한 동작을 제공합니다. 파일이 모듈인지 여부를 판단할 때 import 및 export 문에만 의존합니다.

3. "force": "force"로 설정한 경우, 모든 비선언 파일이 모듈로 처리됩니다. 이 옵션을 사용하면 내용이나 import/export 문의 여부와 관계없이 모든 파일이 모듈로 처리됩니다.
```

#### `noLib` :

기본 라이브러리의 내장 타입 정의 파일을 사용하지 않도록 설정하는 옵션입니다. 이 경우, 컴파일러가 제공하는 기본 타입인 any를 포함한 모든 타입이 사용될 수 있습니다.

#### `reactNamespace` :

TypeScript 컴파일러가 JSX 구문과 관련된 타입을 검사할 때, 지정된 React 네임스페이스를 사용하여 타입 체크를 수행시킬 수 있습니다.

#### `target` :

TypeScript 컴파일러가 JavaScript로 변환할 때의 대상 환경을 지정하는 옵션입니다. 이 옵션을 사용하여 컴파일러가 생성하는 JavaScript 코드를 해당 환경에 맞게 최적화할 수 있습니다.

```sass
1. 설정한 값이 ES5이하의 경우, 화살표 함수가 function 표현식으로 바뀝니다.

2. target을 바꾸면 lib의 기본값이 바뀔 수도 있습니다.

3. ESNext의 경우 TypeScript에서 지원하는 가장 높은 버전을 나타냅니다.
```

#### `useDefineForClassFields` :

TypeScript에서 클래스 필드를 정의하는 방식을 지정하는 옵션입니다. 이 옵션을 사용하여 클래스 필드를 정의할 때 var, let, const 대신 defineProperty를 사용할지 여부를 결정할 수 있습니다.

```typescript
class Example {
  // useDefineForClassFields : true
  field1 = 'Value 1';
  field2 = 'Value 2';

  // useDefineForClassFields : false
  field3: string;
  constructor() {
    this.field3 = 'Value 3';
  }
}
```

위의 코드에서 useDefineForClassFields 옵션이 true로 설정되어 있다면, 클래스 필드인 field1과 field2는 defineProperty를 사용하여 정의됩니다. 이 경우 클래스 인스턴스가 생성될 때마다 해당 필드가 초기화됩니다.

반면에 useDefineForClassFields 옵션이 false로 설정되어 있다면, 클래스 필드인 field3는 생성자 내에서 일반적인 변수 선언 방식으로 초기화됩니다. 이 경우 클래스 인스턴스가 생성될 때마다 생성자 내에서 필드를 초기화해야 합니다.

## 컴파일 설정

#### `diagnostics` :

TypeScript 컴파일러가 생성하는 진단(Diagnostic) 메시지를 제어하는 옵션입니다. Diagnostic 메시지는 코드에서 발생하는 오류, 경고, 정보 등을 나타내며, 이 옵션을 사용하여 컴파일러가 생성하는 Diagnostic 메시지를 원하는 대로 구성할 수 있습니다.

```json
{
  "compilerOptions": {
    "diagnostics": {
      "pretty": true,
      "exclude": ["unusedLocals", "unusedParameters"]
    }
  }
}
```

위의 예시에서는 diagnostics 옵션을 설정하여 Diagnostic 메시지를 구성하고 있습니다. 예시에서는 pretty 옵션을 true로 설정하여 보다 가독성 있는 형식으로 Diagnostic 메시지를 출력하도록 지정하였습니다. 또한 exclude 옵션을 사용하여 "unusedLocals"와 "unusedParameters"를 제외한 나머지 Diagnostic 메시지만 출력하도록 지정하였습니다. 이렇게 설정하면 사용되지 않는 지역 변수와 매개 변수에 대한 Diagnostic 메시지는 출력되지 않습니다.

#### `explainFiles` :

TypeScript 컴파일러가 컴파일하는 파일에 대한 상세한 설명을 제공하는 옵션입니다. 이 옵션을 활성화하면 컴파일러가 각 파일을 처리하는 과정과 해당 파일에 대한 정보를 자세히 설명해줍니다.

```sass
example
├── index.ts
├── package.json
└── tsconfig.json
```

해당 파일을 컴파일을 시켜보면 다음과 같은 로그가 나옵니다.

```powershell
❯ tsc
node_modules/typescript/lib/lib.d.ts
  Default library for target 'es5'
node_modules/typescript/lib/lib.es5.d.ts
  Library referenced via 'es5' from file 'node_modules/typescript/lib/lib.d.ts'
node_modules/typescript/lib/lib.dom.d.ts
  Library referenced via 'dom' from file 'node_modules/typescript/lib/lib.d.ts'
node_modules/typescript/lib/lib.webworker.importscripts.d.ts
  Library referenced via 'webworker.importscripts' from file 'node_modules/typescript/lib/lib.d.ts'
node_modules/typescript/lib/lib.scripthost.d.ts
  Library referenced via 'scripthost' from file 'node_modules/typescript/lib/lib.d.ts'
index.ts
  Matched by include pattern '**/*' in 'tsconfig.json'
```

#### `extendedDiagnostics` :

TypeScript 컴파일러가 생성하는 진단(Diagnostic) 메시지를 확장된 형식으로 제공하는 옵션입니다. 이 옵션을 활성화하면 컴파일러가 보다 상세한 정보를 포함한 진단 메시지를 출력합니다.

#### `generateCpuProfile` :

TypeScript 컴파일러가 실행 중인 프로세스의 CPU 사용량 및 성능 프로파일링 정보를 기록하여 분석할 수 있는 프로파일 파일을 생성합니다.

> 프로파일 파일은 V8 엔진의 프로파일링 도구를 사용하여 분석할 수 있습니다. 자세한 프로파일링 방법에 대해서는 [Node.js](https://nodejs.org/en/learn/getting-started/the-v8-javascript-engine)와 [V8 엔진의 공식 문서](https://v8.dev/docs)를 참조하시기 바랍니다.

#### `listEmittedFiles` :

TypeScript 컴파일러가 생성한 출력 파일의 목록을 표시하는 옵션입니다. 이 옵션을 활성화하면 컴파일러가 컴파일한 소스 파일로부터 어떤 출력 파일이 생성되었는지 확인할 수 있습니다. 해당 출력 파일의 경로와 이름이 터미널에 표시됩니다. 이를 통해 컴파일러가 어떤 JavaScript 파일, 소스 맵 파일 등을 생성했는지 확인할 수 있습니다.

#### `listFiles` :

TypeScript가 컴파일을 시작할 때 컴파일 하는 대상의 이름들을 출력합니다. 예상 외에 파일이 있는지 검사할 수 있습니다.

#### `traceResolution` :

TypeScript 컴파일러가 모듈 해석 과정을 추적(trace)하여 자세한 정보를 제공하는 옵션입니다. 이 옵션을 활성화하면 컴파일러가 모듈 해석 단계에서 어떤 모듈을 찾고, 어떤 경로를 따라 해석되었는지에 대한 정보를 자세히 제공합니다.

> [핸드북](https://www.typescriptlang.org/docs/handbook/modules/theory.html#module-resolution)을 통해 더 자세한 내용을 확인할 수 있습니다.

## 프로젝트 관련 설정

#### `composite` :

프로젝트를 복합 컴파일(composite compilation) 설정으로 지정하는 데 사용됩니다.

> 복합 컴파일은 대규모 TypeScript 프로젝트에서 빌드 성능을 향상시키기 위한 기능입니다. 복합 컴파일을 사용하면 여러 개의 프로젝트를 동시에 빌드하고, 각 프로젝트 간에 의존성을 파악하여 효율적으로 컴파일할 수 있습니다.

해당 옵션을 설정할 경우 TypeScript 컴파일러는 프로젝트의 의존성 그래프를 구성하고, 변경된 파일만 다시 컴파일하여 빌드 시간을 최적화 할 수 있습니다. 또한 references 섹션을 사용하여 프로젝트 간의 의존성을 설정해야 합니다. (대규모 프로젝트에서 용이합니다.)

```sass
해당 옵션이 설정되어 있을 경우

1. rootDir이 설정이 되지 않았을 경우 tsconfig.json 파일을을 포함하는 디렉토리가 기본 값이 됩니다.

2. 모든 실행 파일은 include 패턴과 일치하거나 파일 배열에 나열되어야 합니다. 만약 이 제약 조건을 위반하면, ‘tsc’는 지정되지 않은 파일을 알려줍니다.

3. declaration의 기본 값은 true입니다.
```

#### `disableReferencedProjectLoad` :

참조된 프로젝트의 로드를 비활성화하는 데 사용됩니다. TypeScript 컴파일러는 references 섹션에 지정된 참조 프로젝트를 자동으로 로드하고 컴파일합니다. 하지만 disableReferencedProjectLoad 옵션을 설정하여 이 동작을 비활성화할 수 있습니다.

#### `disableSolutionSearching` :

TypeScript 컴파일러는 기본적으로 프로젝트 간에 공유된 솔루션 파일(\*.sln)을 검색하여 참조된 프로젝트를 로드하고 컴파일합니다. 하지만 `disableSolutionSearching` 옵션을 설정하여 이 동작을 비활성화할 수 있습니다.

#### `disableSourceOfProjectReferenceRedirect` :

TypeScript 컴파일러는 기본적으로 프로젝트 참조의 리디렉션 소스를 검색하여 해당 소스의 파일을 컴파일합니다. 하지만 `disableSourceOfProjectReferenceRedirect` 옵션을 설정하여 이 동작을 비활성화할 수 있습니다. `disableSourceOfProjectReferenceRedirect` 옵션은 주로 프로젝트 간의 의존성 관리를 통해 빌드 성능을 향상시키고자 할 때 사용됩니다. 리디렉션 소스의 컴파일은 일부 프로젝트에서 불필요한 오버헤드를 초래할 수 있으며, `disableSourceOfProjectReferenceRedirect` 옵션을 사용하여 컴파일을 비활성화하면 빌드 시간을 최적화할 수 있습니다. 단, 이 경우에는 리디렉션 소스를 수동으로 컴파일해야 하므로 주의해야 합니다.

#### `incremental` :

TypeScript 컴파일러는 전체 프로젝트를 매번 다시 컴파일합니다. 하지만 incremental 옵션을 설정하여 증분 컴파일을 활성화하면, 변경 사항이 있는 파일만 다시 컴파일하여 빌드 시간을 줄일 수 있습니다.

> 증분 컴파일은 중간 결과를 캐시하여 다시 컴파일해야 하는 파일 수를 줄이는 장점이 있습니다. 이는 대규모 프로젝트에서 특히 유용하며, 개발자들이 소스 코드를 변경할 때 전체 프로젝트를 다시 컴파일할 필요 없이 변경된 부분만 빠르게 컴파일할 수 있습니다.

#### `tsBuildInfoFile` :

증분 컴파일 정보를 저장하는 빌드 정보 파일(build info file)의 경로를 지정하는 데 사용됩니다.

## 출력 관련 설정

#### `noErrorTruncation` :

TypeScript 컴파일러는 에러 메시지를 표시할 때 일부를 생략하여 표시할 수 있습니다. 이는 에러 메시지가 너무 길어지는 것을 방지하고 가독성을 향상시키기 위한 것입니다. 하지만 `noErrorTruncation` 옵션을 설정하여 생략 없이 전체 에러 메시지를 표시할 수 있습니다.

#### `preserveWatchOutput` :

TypeScript 컴파일러는 --watch 모드에서 변경된 파일을 실시간으로 감지하여 다시 컴파일하고, 컴파일된 출력을 콘솔에 표시합니다. 그러나 `preserveWatchOutput` 옵션을 설정하여 컴파일된 출력을 유지하고 콘솔에 표시하지 않을 수 있습니다.

#### `pretty` :

색상과 컨텍스트를 사용하여 오류와 메시지의 스타일을 지정합니다. 이 기능은 기본적으로 켜져 있습니다. 컴파일러에서 덜 간결하고 단일 색상의 메시지를 얻을 수 있는 기회를 제공합니다. `default : true`

#### `skipLibCheck` :

기본 라이브러리 선언 파일의 유형 검사를 건너뜁니다.

#### `assumeChangesOnlyAffectDirectDependencies` :

TypeScript 컴파일러는 변경 사항이 발생하면 해당 변경 사항과 관련된 모든 모듈을 다시 컴파일합니다. 하지만 `assumeChangesOnlyAffectDirectDependencies` 옵션을 설정하여 컴파일러에게 변경 사항이 직접적인 의존성에만 영향을 미친다고 가정하도록 할 수 있습니다.

> 변경 사항이 전체 프로젝트에 영향을 미치지 않는 경우, 변경된 모듈만 다시 컴파일하여 빌드 시간을 단축시킬 수 있습니다. 하지만 이 옵션은 변경 사항이 직접적인 의존성에만 영향을 미친다고 가정하기 때문에, 의존성 관리에 유의해야 합니다.
