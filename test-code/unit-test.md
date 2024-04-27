## 단위 테스트 (Unit Test)

단위 테스트는 응용 프로그램에서 테스트 가능한 가장 작은 소프트웨어를 실행하여 예상대로 동작하는지 확인하는 테스트입니다.

### Jest (testing library)
Jest는 meta팀에서 만든 테스팅 라이브러리 입니다. 기존에 사용하던 Test 방식은 Mocha나 Jasmin을 통해 test runner를 진행하였고 expect, chai를 이용하여 test matcher를 진행하였습니다. 이와 같이 여러가지 일을 하다보니 사용자에게 불편함을 주었고 이에 출시한 Jest는 하나로 통합되어 있기에 프레임워크라고 불리기도 하였습니다.

### Jest의 기본 패턴
```javascript
description("단위 설명" , () => {

    test("테스트 설명", () => {
        expect("검증 대상").toXxx("기대 결과");
    });

    test("테스트 설명", () => {
        expect("검증 대상").toXxx("기대 결과");
    });

})
```

``toXxx``에 해당하는 부분이 test matcher로서 expect에서 반환된 값과 비교를 하여 해당 test의 성공여부를 나타내줍니다.

### 다양한 matcher들

여기에 정리된 matcher들은 작성자가 사용한 matcher들입니다. 더 많은 matcher들은 [공식 문서](https://jestjs.io/docs/expect)에 정리되어 있습니다.

``not``

**not** 해당 테스트의 반대의 결과를 테스트 할 수 있습니다. (ex: 정해진 값을 바라지 않는다.)
```javascript
test('the best flavor is not coconut', () => {
    expect("kimchi").not.toBe('coconut');
});
```

``Promise``

**Promise** 형태의 값들을 풀어서 테스트를 할 때에는 다음과 같이 **resolves**와 **rejects**를 사용할 수 있습니다.

```javascript
test('resolves to lemon', async () => {
  await expect(Promise.resolve('lemon')).resolves.toBe('lemon');
  await expect(Promise.resolve('lemon')).resolves.not.toBe('octopus');
});

test('rejects to octopus', async () => {
  await expect(Promise.reject(new Error('octopus'))).rejects.toThrow('octopus');
});
```

``.toBe(value)``

**toBe** 메소드는 **객체가 아닌 원시 값**을 비교할 때 사용합니다.
```javascript
const can = {
  name: 'pamplemousse',
  ounces: 12,
};

describe('the can', () => {
  test('has 12 ounces', () => {
    expect(can.ounces).toBe(12); 
    // can.ounces가 12이므로 통과
  });

  test('has a sophisticated name', () => {
    expect(can.name).toBe('pamplemousse'); 
    // can.name이 동일하므로 통과
  });
});
```

``.toHaveLength(number)``

**toHaveLength** 메소드는 해당 값의 **length** 프로퍼티가 있고 특정 숫자 값으로 설정되어 있는지 확인 할 수 있습니다.
```javascript
expect([1, 2, 3]).toHaveLength(3);
expect('abc').toHaveLength(3);
expect('').not.toHaveLength(5);
```
``.toBeNull()``

**toBeNull()** 은 전달 받은 값이 null 값인지 확인 할 수 있습니다.
```javascript
function bloop() {
  return null;
}

test('bloop returns null', () => {
  expect(bloop()).toBeNull();
});
``` 

``.toContain(item)``

해당 배열에 해당 값이 있는지 확인하는데 사용합니다.

```javascript
test('the flavor list contains lime', () => {
  expect(getAllFlavors()).toContain('lime');
});
```

``.toEqual``

해당 객체나 배열의 값이 일치하는지 검사합니다. 순서는 상관없습니다. undefind 속성의 값을 가지고있을 경우 무시하기 떄문에 ``toStrictEqual``을 사용하는 것이 좋습니다.

```javaScript
const expectedObject = { a: 1, b: 2 };
const actualObject = { b: 2, a: 1 };

expect(actualObject).toEqual(expectedObject); // 성공
expect(actualObject).toStrictEqual(expectedObject); // 실패
```


### 단위 테스트 작성 방법

단위 테스트는 테스트 가능한 가장 작은 소프트 웨어를 사용하는 테스트를 말한다고 얘기하였습니다.

다음 코드를 예시로 살펴보겠습니다.

```javascript
class Lotto {
   #numbers
   constructor() {
       let number = getRandomLottoNumber(1, 45, 6)
       // getRandomLottoNumber함수는 랜덤한 6개의 숫자 배열을 반환합니다.
       this.#validation(number)
       this.#numbers = number
   }
   
   #validation(number){
    ...
   }
}
---
class LottoMachine {
   execute() {
       const lotto = new Lotto()
   }
}
```

다음 코드는 ``LottoMachine`` 클래스를 이용하여 ``lotto``를 관리하고 있습니다. 여기서 테스트를 할 수 있는 건 만약. 잘못된 값이 만들어진다면?과 같은 예외 상황을 살펴 볼 수 있습니다.

하지만 이건 사용자가 입력한 것이 아닌 개발자가 직접 처리한 것이기에 예외 상황은 없는데요? 라고 물어 볼 수도 있습니다. 애초에 지정된 범위에서 나오게했으면 side-effect는 생기지 않으니까요 하지만 모르기 때문에 코드 분리를 통해 단위 테스트를 할 수 있습니다.

```javascript
class Lotto {
   #numbers

   constructor(numbers) {
       this#.validation(numbers)
       this.#numbers = numbers
   }

   #validation(number){
    ...
   }
}


class LottoMachine {
   execute() {
       const numbers = Randoms.pickUniqueNumbersInRange(1, 45, 6)
       const lotto = new Lotto(numbers)
   }
}
```
그럼 이제 다음과 같이 단위 테스트를 할 수 있습니다.

```javascript
description("Lotto 생성 테스트", () => {

    test("한글이 입력이 되었을 경우", () => {
        // given
        const number_array = [1, 2, 3, 4, 5, 6, 7]

        // when
        const result = new Lotto(given)

        // then
        expect(result).toThrow("[ERROR] 숫자가 아닌 잘못된 문자를 입력을 받았습니다.");
    });

    test("중복된 숫자를 입력을 받았을 경우", () => {
        // given
        const number_array = [1, 2, 3, 4, 4, 6, 7]

        // when
        const result = new Lotto(given)

        // then
        expect(result).toThrow("[ERROR] 중복된 숫자를 입력을 받았습니다.");
    });
})
```

이처럼 단위 테스트를 할 수 있도록 코드를 분리하고 예외 상황을 생각하다보면 코드가 좀 더 완성도가 깊어집니다.

또 ``given``, ``when``, ``then``과 같이 패턴을 적용해주면 작성자나 다른 개발자가 보고 무슨 의도의 테스트인지 작성자의 의도를 파악하기 쉽습니다.

> TIP 반복해서 사전에 설정을 해야하는 경우가 있을 경우 ``beforEach()``를 사용하면 됩니다.
```javascript
describe('로또 맞은 갯수 테스트', () => {
  let lotto;

  beforeEach(() => {
    lotto = new Lotto([7, 5, 10, 12, 17, 42]);
  });

  test('동일한 갯수가 2개일 경우', () => {
    // given
    const userCheck = [10, 20, 30, 40, 45, 5]

    // when
    const result = lotto.matcher(userCheck)

    // then
    expect(result).toBe(2);
  });

  test('동일한 갯수가 없을 경우', () => {
    // given
    const userCheck = [1, 2, 3, 4, 6, 8]

    // when
    const result = lotto.matcher(userCheck)

    // then
    expect(result).toBe(0);
  });

  test('전부 다 맞았을 경우', () => {
    // given
    const userCheck = [7, 5, 10, 12, 17, 42]

    // when
    const result = lotto.matcher(userCheck)

    // then
    expect(result).toBe(6);
  });
});
```

> TIP 에러처리와 같은 테스트를 여러번 반복하고 싶다면 ``test.each()``를 사용하면 됩니다.

```javascript
  describe("Lotto 생성 테스트", () => {
    // given
    const lottoCases = [
      {
        case: [11, 2, 30, 42, 5, 36, 5], 
        expected: "[ERROR] 중복된 값을 입력 받았습니다."
      },
      {
        case: [1, 23, 34, 43, 35, 6, 47],
        expected: "[ERROR] 범위에 맞지 않는 값을 입력 받았습니다"
      },
      {
        case: [23, 22, 23, 24, 35, 36, 43],
        expected: "[ERROR] 중복된 값을 입력 받았습니다."
      }
      {
        case: [10, 20, 30, 40, 15, 26, -1]
        expected: "[ERROR] 범위에 맞지 않는 값을 입력 받았습니다"
      },
      {
        case: [5, 6, 9, 12, 133, 5, 7]
        expected: "[ERROR] 범위에 맞지 않는 값을 입력 받았습니다"
      },
    ]; 
    
    test.each(lottoCases)(

      ({ input, expected }) => {
		// when
        const result = new Lotto(input);
        
        // then
        expect(result).toBe(expected);
      },
    );
  });

```