# Chương 2: Adding interactivity

## [Responding to Event](https://react.dev/learn/responding-to-events#adding-event-handlers)

Là các hàm gọi là event handler - nghĩa là hàm thực hiện logic cho 1 tương tác người dùng nào đó như onClick, onDoubleClick, onMouseEnter (hover), onMouseLeave (thoát hover), onMouseDown,...

- **Quy tắc đặt tên:** `CamelCase` - cú pháp `onNameAction`
- **Event Propagation:** Là việc 1 event (tương tác người dùng) nổi từ phần con lên phần cha, ví dụ:

```bash
function Card({ onClick }: { onClick: () => void }) {
  return <p onClick={onClick}>Card component</p>;
}

function App() {
  const handleOnClick = (text: string) => {
    alert(text);
  };

  return (
    <div onClick={() => handleOnClick('Thẻ cha')}>
      <Card onClick={() => handleOnClick('Thẻ con')} />
    </div>
  );
}
```

!> Để ngăn event propagation ta dùng:

```bash
function Card({ onClick }: { onClick: (e: any) => void }) {
  return <p onClick={onClick}>Card component</p>;
}

function App() {
  const handleOnClick = (text: string) => {
    alert(text);
  };

  return (
    <div onClick={() => handleOnClick('Thẻ cha')}>
      <Card
        onClick={(e: any) => {
          handleOnClick('Thẻ con');
          e.stopPropagation(); // Thêm ở đây
        }}
      />
    </div>
  );
}
```

## Side-Effect (Tác dụng phụ)

Là những việc **nằm ngoài** phạm vi của function, React muốn mỗi component là 1 **pure function**, nhưng nhiều lúc sẽ cần làm những việc ngoài phạm vi render - setState, call API,...đó gọi là side-effect

?> 1 component **phải là pure function**, nhưng 1 event handler **không nhất thiết phải pure**
