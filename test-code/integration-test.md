## 통합 테스트 (Integration Test)

단위 테스트가 하나의 모듈이었다면 통합 테스트는 여러 형태의 모듈들을 함께 테스트 하는 것을 의미합니다.

node 환경에서는 다음과 같이 사용할 수 있습니다.

```javascript
describe('통합 테스트', () => {
  test('손님의 방문 정보를 통해 알맞은 이벤트가 적용이 되는지 확인한다.', async () => {
    // given
    const orderList = new OrderList([
      { name: '티본스테이크', count: 2 },
      { name: '티본스테이크', count: 4 },
      { name: '아이스크림', count: 4 },
    ]);
    const visitDate = 24;

    const eventManager = new EventManager(orderList, visitDate);

    // when
    const serviceList = eventManager.getServiceList();

    // then
    expect(serviceList).toEqual([
      { benefit: 3400, name: '크리스마스 디데이 할인' },
      { benefit: 25000, name: '증정 이벤트' },
    ]);
  });
});
```

단위 테스트에서 올바르게 입력이 되는지 확인하는 정도였다면, 이제 여러 형태의 모듈들이 의사소통을 잘 하는지를 확인 할 수 있습니다.

react 환경에서는 다음과 같이 테스트를 할 수 있습니다.

> 1.  전역 상태 관리가 정상적으로 이루어졌는가?
> 2.  특정 상태가 변경된 후 랜더링 되어야하는 컴포넌트가 랜더링 되었는가?
> 3.  두개 이상의 컴포넌트가 서로 상호 작용하고 있는가?

이를 확인하기 위해서 `<Modal/>` 컴포넌트를 만들어봅니다. `<Modal/>`의 경우 혼자서 존재하지 못하는 컴포넌트입니다. 특정 버튼을 누르거나 상호작용을 했을 경우 생겨나야합니다.

Modal을 컴포넌트로 만들고 `useContext`로 만든 전역변수를 통해서 상태를 공유하고 있습니다.

```typescript
export default function DetailModal() {
  const { product, setProduct } = useProductStore();
  const { isOpen, setIsOpen } = useDetailStore();

  const modalClose = () => {
    setIsOpen(false);
    setProduct(undefined);
  };
  return (
    <>
      {isOpen && product ? (
        <>
          <div
            role={'back-layout'}
            onClick={modalClose}
            className={'back-layout'}
            style={{
                ...
            }}
          ></div>
          <section
            role={'product-info'}
            className={'product-info'}
            style={{
                ...
            }}
          >
            <div
              className={'product-box'}
              style={{
                ...
              }}
            >
              <div
                className={'product-thumbnail'}
                style={{ overflow: 'hidden', borderRadius: '8px' }}
              >
                <img
                  src={product.thumbnail}
                  alt={product.title + '의 이미지'}
                />
              </div>
              <div
                style={{ ... }}
                className={'product-info-box'}
              >
                <h3 data-testid="product-title">title: {product.title}</h3>
                ...
                <div>Rating: {product.rating}</div>
              </div>
            </div>
          </section>
        </>
      ) : null}
    </>
  );
}
```

```typescript
export default function PhoneList() {
  const { products } = productsMock;
  const { setIsOpen } = useDetailStore();
  const { setProduct } = useProductStore();

  const onClick = (product: IProduct) => {
    setProduct(product);
    setIsOpen(true);
  };

  return (
    <div
      style={{
        ...
      }}
    >
      {products.map((product) => (
        <div
          style={{
            ...
          }}
          key={product.id}
        >
          <div role={'img-box'} onClick={() => onClick(product)} className={'img-box'}>
            <img
              src={product.thumbnail}
              alt={product.title + '소개사진'}
              width={200}
              height={150}
            />
          </div>
          <div style={{ textAlign: 'left' }}>
            {product.title}
            <div>price: {product.price.toLocaleString()}$</div>
            <div>{product.brand}</div>
          </div>
        </div>
      ))}
    </div>
  );
}
```

```typescript
const util = render(
  <>
    <UseDetailStore>
      <UseProductStore>
        <DetailModal />
        <PhoneList />
      </UseProductStore>
    </UseDetailStore>
  </>
);
```

모달과 전역변수를 이용하여 다음과 같이 전역변수로 인해 UI가 바뀌는 것을 테스트 할 수 있습니다.

```typescript
test('이미지를 클릭하면 모달창이 열리고 여백을 클릭하면 다시 닫힌다.', async () => {
  const imgBox = await util.findByAltText('iPhone 9소개사진');
  expect(imgBox).toBeInTheDocument();

  // 이미지 박스를 클릭한다.
  fireEvent.click(imgBox);

  // 상세 모달이 열린다.
  const detailBox = await util.findByRole('product-info');
  expect(detailBox).toBeInTheDocument();

  // 상세 모달의 정보가 누른 이미지와 동일 한지 확인한다.
  expect(util.getByTestId('product-title').innerHTML).toBe('title: iPhone 9');

  // 여백을 다시 누른다.
  fireEvent.click(await util.findByRole('back-layout'));

  // 여백을 누름으로서 상세모달은 보이지 않는다.
  expect(detailBox).not.toBeInTheDocument();
});
```

기존 단위 테스트에서는 이미지를 클릭했을 때 열리는 것만 확인하는 짧은 단위의 테스트였다면 지금은 전역변수를 통해 다른 컴포넌트끼리 상호작용을 하고 테스트의 범위가 커진 것을 확인 할 수 있습니다.
