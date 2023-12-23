## SASS

### SASS란?

> Sass is the most mature, stable, and powerful professional grade CSS extension language in the world.

Sass는 세계에서 가장 성숙하고 안정적이며 강력한 전문 등급 CSS 확장 언어입니다.

사스는 기초 언어에 힘과 우아함을 더해주는 CSS의 확장이다. [출처](https://heropy.blog/2018/01/31/sass/)

---
### Next-JS with Sass
Next.js에서 Sass를 사용하려면 다음 방법을 따라야합니다.

1. Sass 설치 

    ```cmd
    $ npm install --save-dev sass
    ```

2. next.config.json 설정하기
    ```typescript
    const path = require('path')

    module.exports = {
        sassOptions: {
            includePaths: [path.join(__dirname, 'styles')],
        },
    }
    ```

**Tip** Next.js는 CSS 모듈에서 내보낸 Sass 변수를 지원합니다.

```sass
$primary-color: #64ff00;

:export {
primaryColor: $primary-color;
}
```

```typescript
// maps to root `/` URL

import variables from './variables.module.scss'

export default function Page() {
return <h1 style={{ color: variables.primaryColor }}>Hello, Next.js!</h1>
}
```

---

### Sass의 특징
Sass의 특징은 다음과 같습니다.
1. 모든 버전의 CSS와 완벽하게 호환이 됩니다.
2. 다른 어떤 CSS의 확정 언어보다 더 많은 기능을 제공합니다.
3. 17년 이상 지원을 받아 성장해 왔습니다.
4. CSS가 동작하기 이전에 사용되는 전처리기입니다.
5. 진입 장벽이 낮습니다.

Sass는 두가지의 버전이 있습니다.
기본적으로 지원하는 Sass와 3.x버전부터 지원하는 Scss가 있습니다.

두 버전의 차이는 단지  `{ }` (중괄호), `;` (세미콜론)의 유무입니다.

scss에서의 코드
```scss
body {
  .react-datepicker__month-container {
    border-radius: 10px !important;
    background-color: #232120 !important;
    height: 300px;
  }

  .selectContainer {
    display: flex;
  }

  .select_OneContainer {
    display: flex;
    position: relative;
  }
}
```

sass에서의 코드
```sass
body
  .react-datepicker__month-container
    border-radius: 10px !important
    background-color: #232120 !important
    height: 300px

  .selectContainer
    display: flex

  .select_OneContainer
    display: flex
    position: relative
```

둘 다 동일하게 동작합니다.

또한 들여쓰기를 통해서 CSS를 적용하게 할 수 있씁니다.

```scss
.child {
    color: white
}

.parent {
  .child {
    color: black
  }
}
```

``child`` 클래스를 가지고 있는 태그가 만약 ``parent``클래스 내에 있다면 color가 검은색상이 됩니다.

---

### **Sass의 장단점**

**장점**

**변수 및 재사용**:

Sass/SCSS에서는 변수를 사용하여 색상, 폰트, 여백 등의 값을 한 곳에서 관리할 수 있습니다. 이는 일관성 있고 유지보수가 용이한 코드를 작성하는 데 도움이 됩니다.

**중첩 규칙**:

Sass/SCSS는 중첩된 규칙을 지원하여 HTML 요소의 계층 구조를 반영할 수 있습니다. 이는 가독성을 향상시키고 작성하기 편한 코드를 만들 수 있도록 돕습니다.

**믹스인 (Mixins) 및 확장 (Extend)**:

믹스인은 재사용 가능한 코드 블록을 정의하고 호출할 때마다 확장할 수 있는 기능을 제공합니다. 또한, ``@extend``를 사용하여 스타일 규칙을 확장하고 코드의 중복을 피할 수 있습니다.

**함수와 연산**:

Sass/SCSS에서는 함수와 연산을 활용하여 스타일을 동적으로 생성할 수 있습니다. 이는 특정 값을 계산하거나 동적으로 스타일을 설정할 때 유용합니다.

**가독성과 유지보수성**:

Sass/SCSS는 코드를 구조화하고 가독성을 높이는데 도움이 됩니다. 모듈화된 코드 작성과 계층 구조는 프로젝트의 규모가 커질수록 유지보수를 용이하게 만들어줍니다.

**자동 접두어 및 미디어 쿼리**:

자동 접두어 생성 및 미디어 쿼리를 쉽게 관리할 수 있는 기능이 내장되어 있어, 브라우저 호환성 및 반응형 디자인을 구현하기 용이합니다.

**단점**

**학습 곡선**:

프리프로세서에 익숙하지 않은 개발자들에게는 Sass/SCSS의 문법과 기능을 이해하는 데 학습 곡선이 존재할 수 있습니다. 특히 일반적인 CSS 작성에 익숙한 경우에 해당합니다.

**디버깅**:

디버깅이 어려울 수 있습니다. 생성된 CSS가 항상 Sass/SCSS 소스와 직접 매핑되지 않을 수 있어, 컴파일된 CSS에서 문제를 식별하고 수정하는 데 어려움이 있을 수 있습니다.

**컴파일 시간**:

컴파일 단계가 추가되므로 큰 프로젝트에서는 일부 컴파일 시간이 소요될 수 있습니다. 이는 개발 중에 변경 사항을 빠르게 확인하고 테스트하는 데 일부 제약을 줄 수 있습니다.

---
### 사용법

기본적으로 Scss를 사용한다면 css와 문법이 똑같습니다. 이제 확장된 기능을 사용하며 Scss를 사용하고 싶다면 추가 문법을 사용할 수 있습니다.

**주석**
두 가지 스타일의 주석을 사용합니다.
```Scss
// 컴파일되지 않는 주석
/* 컴파일되는 주석 */
```

**변수**

Sass에서는 변수를 사용할 수 있습니다.

```Scss
$font-stack: Helvetica, sans-serif;
$primary-color: #333;

body {
  font: 100% $font-stack;
  color: $primary-color;
}
```

global 키워드를 사용하여 전역으로 설정 할 수 있습니다.

```Scss
.box1 {
  $color: #111 !global;
  background: $color;
}
.box2 {
  background: $color;
}
```

default 변수를 설정할 수 있습니다.

```Scss
$color-primary: red;

.box {
  $color-primary: blue !default;
  background: $color-primary;
}
```

변수와 값을 설정하겠지만, 기존 변수가 있을 경우에는 현재 설정하는 변수의 값은 사용하지 않겠다’는 의미로 쓸 수 있습니다.

**중첩 문법**

괄호를 이용하여 중첩되게 사용할 수 있습니다.
```scss
nav {
  ul {
    margin: 0;
    padding: 0;
    list-style: none;
  }

  li { display: inline-block; }

  a {
    display: block;
    padding: 6px 12px;
    text-decoration: none;
  }
}
```
css로 컴파일을 할 경우 다음과 같이 작성됩니다.

```css
nav ul {
  margin: 0;
  padding: 0;
  list-style: none;
}
nav li {
  display: inline-block;
}
nav a {
  display: block;
  padding: 6px 12px;
  text-decoration: none;
}
```

**모듈로 관리하기**

중복되서 사용하는 파일의 경우 분할하여 사용 할 수 있습니다. 때문에 단일 파일이 아닌 언더바(_)와 같이 부분 파일을 이용하여 만들 수도 있습니다. ``(ex: _partial.scss)``

불러와서 사용할 경우 다음과 같이 사용할 수 있습니다.

```scss
// _base.scss
$font-stack: Helvetica, sans-serif;
$primary-color: #333;

body {
  font: 100% $font-stack;
  color: $primary-color;
}
```

```scss
// styles.scss
@use 'base';

.inverse {
  background-color: base.$primary-color;
  color: white;
}
```
> 확장자명을 따로 작성할 필요가 없습니다. Sass는 해당 파일을 찾아낼 수 있습니다.

**반복되는 코드 재활용**

`@mixin`키워드와 `@import`키워드를 사용하여 재활용 할 수 있습니다.
```Scss
@mixin theme($theme: DarkGray) {
  background: $theme;
  box-shadow: 0 0 1px rgba($theme, .25);
  color: #fff;
}

.info {
  @include theme;
}
.alert {
  @include theme($theme: DarkRed);
}
.success {
  @include theme($theme: DarkGreen);
}
```

**상속**

`@extend`키워드를 통하여 다른 스타일의 속성들을 전부 가져와야 할 때 사용합니다

```scss
.btn {
  padding: 10px;
  margin: 10px;
  background: blue;
}
.btn-danger {
  @extend .btn;
  background: red;
}
```

다음과 같이 상속을 위한 스타일을 만들어 둘 수도 있습니다.

```scss
/* This CSS will print because %message-shared is extended. */
%message-shared {
  border: 1px solid #ccc;
  padding: 10px;
  color: #333;
}

// This CSS won't print because %equal-heights is never extended.
%equal-heights {
  display: flex;
  flex-wrap: wrap;
}

.message {
  @extend %message-shared;
}

.success {
  @extend %message-shared;
  border-color: green;
}

.error {
  @extend %message-shared;
  border-color: red;
}

.warning {
  @extend %message-shared;
  border-color: yellow;
}
```

**함수**

위에서 사용했던 `@include`와 다르게 함수는 바로 사용할 수 있습니다.
```scss
$max-width: 980px;

/*인수 부분에서 default 값을 설정할 수 있습니다.*/
@function columns($number: 1, $columns: 12) {
  @return $max-width * ($number / $columns)
}

.box_group {
  width: $max-width;

  .box1 {
    width: columns();  // 1
  }
  .box2 {
    width: columns(8);
  }
  .box3 {
    width: columns(3);
  }
}
```


**가변 인수**

인수의 갯수가 불확실할 경우 `...` 키워드를 붙여줍니다.
```scss
@mixin bg($width, $height, $bg-values...) {
  width: $width;
  height: $height;
  background: $bg-values;
}

div {
  @include bg(
    100px,
    200px,
    url("/images/a.png") no-repeat 10px 20px,
    url("/images/b.png") no-repeat,
    url("/images/c.png")
  );
}
```

**변수 삽입**

`${}`키워드를 사용하여 어디서든지 문자를 넣을 수 있습니다.
```scss
$family: unquote("Droid+Sans");
@import url("http://fonts.googleapis.com/css?family=#{$family}");
```

Sass의 내장 함수 `unquote()`을 사용할 경우 문자열의 `""`을 제거할 수 있습니다.

---

### 참고자료

- [Sass 가이드북](https://sass-lang.com/guide/)
- [Sass(SCSS) 완전 정복!](https://heropy.blog/2018/01/31/sass/)