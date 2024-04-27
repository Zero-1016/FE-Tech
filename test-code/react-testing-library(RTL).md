## RTL (React Testing Library)

앞에 Unit-Test-Jest에서 했던 방식은 간단한 유틸 함수 테스트였습니다. 여기에 추가적으로 리액트에서는 컴포넌트도 모듈의 단위이기 때문에 테스팅을 하는 경우가 있습니다.

이를 위해서는 React Component를 그려주는(render) 라이브러리가 필요한데 대표적으로 RTL이 있습니다.

&nbsp;

아래 글에서는 [공식 문서](https://testing-library.com/docs/react-testing-library/intro/)를 통해 사용할 수 있는 메소드들을 정리합니다.

RTL라이브러리에서는 Dom 요소를 가져오기 위한 다양한 방법(Query)들을 제공하고 있습니다.

&nbsp;

### 단일 요소를 가져오는 방법

`getBy...` : 쿼리에 대한 일치 노드를 반환하고 일치하는 요소가 없거나 둘 이상의 일치 항목이 발견되면 오류가 발생됩니다.

`queryBy...` : 쿼리에 대한 일치 노드를 반환하고 일치하는 요소가 없으면 null을 반환합니다. 이는 존재하지 않는 요소를 할당하는 데 유용합니다. 개 이상의 요소가 발견되면 오류가 발생됩니다. 여러 요소를 발견하고 싶다면 `queryAllBy`를 사용하면 됩니다.

`findBy...` : 지정된 쿼리와 일치하는 요소가 발견되었을 때 promise resolve를 반환합니다. 요소가 발견되지 않거나 기본 시간 초과가 1000ms인 후에 두 개 이상의 요소가 발견되면 오류가 발생됩니다. 여러 요소를 발견하고 싶다면 `findAllBy`를 사용하면 됩니다.

&nbsp;

### 여러 요소를 가져오는 방법

`getAllBy...` : 쿼리에 대해 일치하는 모든 노드의 배열을 반환하고 일치하는 요소가 없으면 오류가 .

`queryAllBy...` : 쿼리에 대해 일치하는 모든 노드의 배열을 반환하고 일치하는 요소가 없으면 빈 배열([])을 반환합니다.

`findAllBy...` : 지정된 쿼리와 일치하는 요소가 발견되면 요소 배열로 해결되는 약속을 반환합니다. 기본 시간이 1000ms인 후에 요소가 발견되지 않으면 promise 형태가 reject로 반환됩니다.

&nbsp;

| Type of Query      | 받아온 값이 없을 경우   | 일치하는 값이 하나일 경우 | 여러개가 일치할 경우     | 재시도의 여부 |
| ------------------ | ----------------------- | ------------------------- | ------------------------ | ------------- |
| **단일 요소** 선택 |                         |                           |                          |               |
| `getBy...`         | 에러를 발생시킵니다.    | 요소를 반환합니다.<T>     | 에러를 발생시킵니다.     | x             |
| `queryBy...`       | Null을 반환합니다.      | 요소를 반환합니다.<T>     | 에러를 발생시킵니다.     | x             |
| `findBy...`        | 에러를 발생시킵니다.    | 요소를 반환합니다.<T>     | 에러를 발생시킵니다.     | o             |
| **다중 요소** 선택 |                         |                           |                          |               |
| `getAllBy...`      | 에러를 발생시킵니다.    | 요소를 반환합니다.<T[ ]>  | 요소를 반환합니다.<T[ ]> | x             |
| `queryAllBy...`    | 빈배열 []를 반환합니다. | 요소를 반환합니다.<T[ ]>  | 요소를 반환합니다.<T[ ]> | x             |
| `findAllBy...`     | 에러를 발생시킵니다.    | 요소를 반환합니다.<T[ ]>  | 요소를 반환합니다.<T[ ]> | o             |

위에서 배운 요소들을 가져오는 법을 적용하여 리액트 컴포넌트를 테스트 해봅니다.

### 컴포넌트 테스트

RTL을 사용하여 해당 컴포넌트가 올바르게 호출되어 렌더링이 되었는지 확인합니다.

사용법은 다음과 같습니다.

```typescript
import { render, screen } from '@testing-library/react';

render(
    <div>
        <h4>Zero의 테스트 React 테스트 코드</h4>
        <div>
            <button>클릭!</button>
        </div>
    </div>
);

test('해당 컴포넌트가 렌더링이 되었는지 테스트 합니다.', async () => {
    expect(await screen.findByRole('button')).toBeInTheDocument();
    expect(await screen.queryByText(/Zero의 테스트 React 테스트 코드/)).toBeInTheDocument();
});
```

render를 통해서 실제와 같이 컴포넌트를 구현을 해줍니다. 이후 실제로 render가 되었는지 찾아옵니다. 그리고 `toBeInTheDocument()`를 통해 검사를 하는 것을 확인할 수 있습니다. 만약 render에 똑같이 들어가지 않았다면 에러가 발생합니다.

### fireEvent를 통한 상태변화 테스트

RTL을 사용하여 해당 컴포넌트내에 상태가 올바르게 변화하는지 확인합니다.

사용자가 특정 이벤트를 하면 변화가 되는 로직을 작성 할 수 있습니다. `fireEvent`를 사용하면 됩니다.

```typescript
export default function Count() {
    const [count, setCount] = useState(0)

    return (
        <>
            <button onClick={()=> setCount(prev => prev + 1)}>click me!</button>
            <h4>{count + "번 눌러졌습니다."}</h4>
        </>
    )
}

------

import {fireEvent, render, screen} from "@testing-library/react";

render(<Count/>)

test("버튼을 클릭하면 상태 값이 변경된다.", async () => {
    const clickButton = await screen.findByRole("button");
    expect(clickButton.innerHTML).toBe("click me!")
    expect(await screen.queryByText("0번 눌러졌습니다.")).toBeInTheDocument() // 변경 전의 값

    fireEvent.click(clickButton); // clickButton을 클릭해줘. 이벤트 전달
    fireEvent.click(clickButton);
    fireEvent.click(clickButton);

    expect(await screen.queryByText("3번 눌러졌습니다.")).toBeInTheDocument() // 변경이 된 값
});
```

위의 로직을 보면 `fireEvent`를 통해 클릭이벤트를 전달해주고 있습니다. 이를 통해 상태변화가 이루어졌고 이를 확인할 수 있습니다. 이외에도 `click()`, `mouseover()`, `focus()`, `change()`등 이벤트를 트리거할 수 있는 다양한 메소드들을 제공합니다.

하지만 RTL에서는 `fireEvent`를 통해 사용자의 관점에서 흐름을 파악하는 것을 추천하지 않습니다. RTL에서는 `userEvent`의 사용을 권고하고있습니다.

### userEvent 
> User Event 라이브러리는 fireEvent와 달리 **사람의 행동에 가까운** 좀 더 추상화된 함수명을 제공합니다. userEvent.type() 함수에는 target.value의 중첩 구조의 이벤트 객체를 넘길 필요가 없이, 실제 입력 텍스트만 넘기면 됩니다. 또한, change 이벤트 뿐만 아니라 focus, keydown, keyup과 같은 실제로 동반되야하는 모든 이벤트가 함께 발생하게 됩니다.

fireEvent의 경우 `click()` 메서드를 버튼에서 실행하는 경우 실제 유저 행동에서 나오는 아래의 5단계 동작을 실행하지 않습니다.
1. fireEvent.mouseOver
2. fireEvent.mouseMove
3. fireEvent.mouseDown
4. element.focus() (버튼을 focus 할 수 있는 경우)
5. fireEvent.mouseUp


즉 `userEvent`는 `fireEvent`보다 추상화되어있는 함수들로 실제 사용자에 더 가깝게 구현을 할 수 있습니다. 또 `fireEvent`보다 편리하게 값을 관리할 수 있습니다.

```typescript
  it("fireEvent input", () => {
    render(<input type="text" onFocus={() => console.log("focus")} />);
    fireEvent.change(screen.getByRole("textbox"), {
      target: { value: "helo" },
    });
    expect(screen.getByRole("textbox")).toHaveValue("helo");
    expect(console.log).not.toHaveBeenCalled();
  });

  it("userEvent input", async () => {
    const user = userEvent.setup();
    render(<input type="text" onFocus={() => console.log("focus")} />);
    await user.type(screen.getByRole("textbox"), "helo");
    expect(screen.getByRole("textbox")).toHaveValue("helo");
    expect(console.log).toHaveBeenCalledWith("focus");
  });
```

`fireEvent`의 경우 `onChange` `DomEvent`를 발생시켜 `target`을 수정을 하고있습니다 반면 `userEvent`는 `type`으로 값을 수정 할 수 있습니다.

여기서 처럼 `fireEvent`를 통해서 사용자가 값은 변경을 하였을 때 ~가 된다.라는 것을 확인할 수 있지만 반면 `userEvent`의 경우 focus부터 mouseEvent까지 세부적인거까지 설정이 가능하기에 RTL에서도 `userEvent`를 권장합니다.

### `userEvent`, `fireEvent` 어느 순간에 사용해야 할까요?
`fireEvent` 같은 경우 프로그래밍 방식으로 이벤트를 발생시키기 때문에 사용자가 직접 상호작용을 하지않고 코드 수준에서 이벤트 작업을 테스트 할 때 사용하면 좋습니다.

ex) 자동화 테스트, 초기 데이터 로딩

`userEvent`의 경우 개발자가 사용자의 시점에서 상호작용을 시뮬레이션 하기위해 디자인된 이벤트입니다. 때문에 `focus`, `mouesEvent`와 같은 사용자의 행동을 모방할 수 있습니다. 

ex) 특정 사용자의 경험, 특정 상황을 테스팅(form 태그내 input 값이 없는데 submit이 눌리는 경우)


- **참고자료**
- [RTL 공식문서 - About Queries](https://testing-library.com/docs/queries/about/)
- [Considerations for fireEvent[Testing Library]](https://velog.io/@nhs075241/Considerations-for-fireEventTesting-Library)
- [유저 이벤트 테스트 (@testing-library/user-event)](https://www.daleseo.com/testing-library-user-agent)