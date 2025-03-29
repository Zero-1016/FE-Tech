## 객체 속성 설명자

객체의 각 값은 다음과 같은 속성 설명자를 가집니다.

- `value`: 해당 키의 값을 나타냅니다. 값을 설정할 수 있습니다.
- `writable`: 값을 할당 연산자로 수정할 수 있는지 여부를 결정합니다.
- `enumerable`: 해당 속성이 객체의 열거 가능한 속성 목록에 포함될지를 결정합니다. (단, 직접 접근은 가능합니다.)
- `configurable`: 속성의 변경 가능 여부 및 객체에서 삭제할 수 있는지를 결정합니다.

---

## 객체 생성자 (Constructor)

### 1. `Object` 생성자

값이 `null` 또는 `undefined`라면 빈 객체를 생성하여 반환합니다.

```tsx
const objNull = new Object(null);
console.log(objNull); // {}

const objUndefined = new Object(undefined);
console.log(objUndefined); // {}

const objNumber = new Object(3);
console.log(objNumber); // [Number: 3]
console.log(objNumber.Number); // undefined
console.log(objNumber[0]); // undefined

const objValue = new Object();
objValue.age = 20;
console.log(objValue); // { age: 20 }
```

---

## 객체 메서드

### 2. `Object.assign`

여러 개의 객체를 병합하며, 동일한 키를 가진 경우 마지막에 전달된 객체의 값으로 덮어씌웁니다.

```tsx
const target = { a: 1, b: 2 };
const source1 = { b: 4, d: 6 };
const source2 = { c: 4, z: 6 };

const assignObj = Object.assign(target, source1, source2);
console.log(assignObj); // { a: 1, b: 4, d: 6, c: 4, z: 6 }
```

### 3. `Object.create`

지정된 프로토타입 객체 및 속성을 갖는 새 객체를 생성합니다.

```tsx
function Shape() {
  this.x = 0;
  this.y = 0;
}

Shape.prototype.move = function (x, y) {
  const prevX = this.x;
  const prevY = this.y;
  this.x += x;
  this.y += y;
  if (x) console.log(`Shape moved X: ${prevX} to ${this.x}`);
  if (y) console.log(`Shape moved Y: ${prevY} to ${this.y}`);
};

function Rectangle() {
  Shape.call(this);
}

Rectangle.prototype = Object.create(Shape.prototype);
Rectangle.prototype.constructor = Rectangle;

const rect = new Rectangle();
rect.move(0, 2); // Shape moved Y: 0 to 2
rect.move(4, 0); // Shape moved X: 0 to 4
```

### 4. `Object.defineProperties`

객체에 여러 개의 속성을 한 번에 정의하거나 수정할 수 있습니다.

```tsx
const definedObject = {};
Object.defineProperties(definedObject, {
  name: {
    value: "John",
    writable: true,
    enumerable: true,
  },
  age: {
    value: 30,
    writable: true,
  },
});

console.log(definedObject); // { name: 'John' }
```

### 5. `Object.defineProperty`

단일 속성을 정의하거나 수정할 때 사용됩니다.

```tsx
const obj = {};

Object.defineProperty(obj, "property1", {
  value: 40,
  writable: true,
  enumerable: true,
});

console.log(obj); // { property1: 40 }
```

### 6. `Object.entries`

객체의 속성을 `[key, value]` 형태의 배열로 반환합니다.

```tsx
const object1 = { a: "something", b: 40 };

for (const [key, value] of Object.entries(object1)) {
  console.log(`${key}: ${value}`);
}
```

### 7. `Object.freeze`

객체의 속성을 변경할 수 없도록 동결합니다.

```tsx
const obj = { prop: 42 };
Object.freeze(obj);
obj.prop = 33;
console.log(obj.prop); // 42
```

### 8. `Object.fromEntries`

키-값 쌍의 목록을 객체로 변환합니다.

```tsx
const entries = new Map([
  ["foo", "bar"],
  ["baz", 42],
]);

const obj = Object.fromEntries(entries);
console.log(obj); // { foo: 'bar', baz: 42 }
```

### 9. `Object.getOwnPropertyDescriptor`

객체 속성의 설명자를 반환합니다.

```tsx
const obj1 = { name: "zero-1016", age: 20 };
console.log(Object.getOwnPropertyDescriptor(obj1, "name"));
```

### 10. `Object.getOwnPropertyNames`

객체의 모든 속성 이름을 배열로 반환합니다.

```tsx
const obj = { a: 1, b: 2, c: 3 };
console.log(Object.getOwnPropertyNames(obj)); // [ 'a', 'b', 'c' ]
```

### 11. `Object.getPrototypeOf`

객체의 프로토타입을 반환합니다.

```tsx
const obj = { animal: "dog" };
console.log(Object.getPrototypeOf(obj));
```

### 12. `Object.hasOwn`

객체가 특정 속성을 직접 소유하고 있는지 확인합니다.

```tsx
const object = { prop: "exists" };
console.log(Object.hasOwn(object, "prop")); // true
```

### 13. `Object.is`

두 값이 같은지를 비교합니다.

```tsx
console.log(Object.is(20, 20)); // true
console.log(Object.is({}, {})); // false
```

### 14. `Object.preventExtensions`

객체의 확장을 방지합니다.

```tsx
const obj = {};
Object.preventExtensions(obj);
console.log(Object.isExtensible(obj)); // false
```

### 15. `Object.seal`

객체의 속성 추가 및 삭제를 막고, 기존 속성의 수정만 가능하도록 합니다.

```tsx
const person = { name: "John", age: 30 };
Object.seal(person);
person.age = 31; // 가능
person.name = "Jane"; // 가능
console.log(person);
```

### 16. `Object.keys`

객체의 키를 배열로 반환합니다.

```tsx
const obj = { a: "somestring", b: 42, c: false };
console.log(Object.keys(obj)); // [ 'a', 'b', 'c' ]
```

### 17. `Object.values`

객체의 값을 배열로 반환합니다.

```tsx
console.log(Object.values(obj)); // [ 'somestring', 42, false ]
```
