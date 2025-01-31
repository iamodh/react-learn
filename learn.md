## Hook 사용하기

Hook은 다른 함수보다 더 제한적입니다. 컴포넌트(또는 다른 Hook)의 상단에서만 Hook을 호출할 수 있습니다. 조건이나 반복에서 useState를 사용하고 싶다면 새 컴포넌트를 추출하여 그곳에 넣으세요.

> 조건이나 반복에서 훅을 사용하고 싶다면 새 컴포넌트를 추출하기

## state 끌어올리기

여러 자식 컴포넌트에서 데이터를 수집하거나 두 자식 컴포넌트가 서로 통신하도록 하려면, 부모 컴포넌트에서 공유 state를 선언하세요. 부모 컴포넌트는 props를 통해 해당 state를 자식 컴포넌트에 전달할 수 있습니다. 이렇게 하면 자식 컴포넌트가 서로 동기화되고 부모 컴포넌트와도 동기화되도록 유지할 수 있습니다.

> 컴포넌트 간의 state 동기화가 필요할 때 부모 컴포넌트로 state를 끌어올려라.

## slice와 splice

### 1. `slice()`

- 용도: 배열을 잘라내는 메서드입니다. 배열의 일부를 추출하지만, 원본 배열을 수정하지 않고 새로운 배열을 반환합니다.

- 동작: 주어진 인덱스 범위에서 복사본을 만들어 반환합니다.

- 특징:

  - 원본 배열을 변경하지 않음.

  - 새로운 배열을 반환합니다.

```js
const arr = [1, 2, 3, 4, 5];
const newArr = arr.slice(1, 4); // [2, 3, 4]
console.log(arr); // [1, 2, 3, 4, 5]  // 원본 배열은 변하지 않음
```

### 1. `splice()`

- 용도: 배열의 요소를 추가, 삭제, 교체하는 메서드입니다. 원본 배열을 직접 변경하며, 삭제된 요소들을 반환합니다.

- 동작: 지정된 인덱스에서 요소를 삭제하거나 추가하거나 교체할 수 있습니다.

- 특징:

  - 원본 배열을 변경함.

  - 새로운 배열을 반환하지 않고, 삭제된 요소를 배열로 반환합니다.

```js
const arr = [1, 2, 3, 4, 5];
const removed = arr.splice(1, 2, "a", "b"); // [2, 3]을 삭제하고 'a', 'b'를 추가
console.log(arr); // [1, 'a', 'b', 4, 5]  // 원본 배열이 변함
console.log(removed); // [2, 3]  // 삭제된 요소들
```

## 리렌더에의한 무한루프

```jsx
<Square value={squares[0]} onSquareClick={handleClick(0)} />
```

이유는 다음과 같습니다. handleClick(0) 호출은 보드 컴포넌트 렌더링의 일부가 됩니다. handleClick(0)은 setSquares를 호출하여 보드 컴포넌트의 state를 변경하기 때문에 보드 컴포넌트 전체가 다시 렌더링 됩니다. 하지만 이 과정에서 handleClick(0)은 다시 실행되기 때문에 무한 루프에 빠지게 됩니다.

> props로 함수를 전달할 때 함수를 호출하지 않도록 주의해야한다.

## 이벤트 처리 함수 컨벤션

DOM `<button>` 엘리먼트의 onClick 어트리뷰트는 빌트인 컴포넌트이기 때문에 React에서 특별한 의미를 갖습니다. 사용자 정의 컴포넌트, 예를 들어 사각형의 경우 이름은 사용자가 원하는 대로 지을 수 있습니다. Square의 onSquareClick prop나 Board의 handleClick 함수에 어떠한 이름을 붙여도 코드는 동일하게 작동합니다. React에서는 주로 이벤트를 나타내는 prop에는 onSomething과 같은 이름을 사용하고, 이벤트를 처리하는 함수를 정의 할 때는 handleSomething과 같은 이름을 사용합니다.

> 이벤트를 처리하는 함수를 정의할 때는 handleSomething을 사용한다. handleSomething을 props로 전달할 때 props의 이름에 onSomething을 사용한다.

```jsx
function Square({ value, onSquareClick }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}

export default function Board() {
  //...
  return <Square value={squares[0]} onSquareClick={() => handleClick(0)} />;
  //...
}
```

## 불변성과 불변성이 중요한 이유

불변성을 사용하면 복잡한 기능을 훨씬 쉽게 구현할 수 있습니다. 우리는 이 자습서의 뒷부분에서 게임의 진행 과정을 검토하고 과거 움직임으로 “돌아가기”를 할 수 있는 “시간 여행” 기능을 구현할 예정입니다. 특정 작업을 실행 취소하고 다시 실행하는 기능은 이 게임에만 국한된 것이 아닌 앱의 일반적인 요구사항입니다. 직접적인 데이터 변경을 피하면 이전 버전의 데이터를 그대로 유지하여 나중에 재사용(또는 초기화)할 수 있습니다.

불변성을 사용하는 것의 또 다른 장점이 있습니다. 기본적으로 부모 컴포넌트의 state가 변경되면 모든 자식 컴포넌트가 자동으로 다시 렌더링 됩니다. 여기에는 변경 사항이 없는 자식 컴포넌트도 포함됩니다. 리렌더링 자체가 사용자에게 보이는 것은 아니지만 성능상의 이유로 트리의 영향을 받지 않는 부분의 리렌더링을 피하는 것이 좋습니다. 불변성을 사용하면 컴포넌트가 데이터의 변경 여부를 저렴한 비용으로 판단할 수 있습니다. memo API 참고서에서 React가 컴포넌트를 다시 렌더링할 시점을 선택하는 방법에 대해 살펴볼 수 있습니다.

> 불변성을 사용하면 이전 버전의 데이터를 재사용할 수 있고, 이전 state와 변경사항이 없는지 저렴한 비용으로 판단할 수 있다.

## key를 사용하는 이유와 index를 key로 사용하면 안 되는 이유

<a href="https://ko.legacy.reactjs.org/docs/reconciliation.html#recursing-on-children">자식에 대한 재귀적 처리</a>

<a href="https://velog.io/@phjjj/React-map%EC%9C%BC%EB%A1%9C-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8-%ED%98%B8%EC%B6%9C%EC%8B%9C-key%EB%A5%BC-index%EB%A1%9C-%EB%91%90%EB%A9%B4-%EC%95%88%EB%90%98%EB%8A%94-%EC%9D%B4%EC%9C%A0">index를 key로 사용하는 경우의 문제점</a>

> 컴포넌트가 동적인 리스트 형태로 렌더될 때 key를 이용해 서로를 구분한다면 컴포넌트가 가지고 있는 state를 유지하면서 효율적인 리스트 아이템의 이동, 추가, 제거가 가능하다.
