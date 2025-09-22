# Tìm hiểu về state

## Tại sao cần dùng state?

Giả sử muốn làm 1 bộ đếm tăng index lên, ta dùng:

```jsx
let index = 0;

const handleIncreseIndex = () => {
  index += 1;
};
```

> Nhưng sẽ không được, vì:
>
> 1. Biến cục bộ không duy trì giữa 2 lần render - nghĩa là khi lần đầu render ra index = 1, sau đó lần render tiếp theo react > vẫn sẽ lấy biến index = 0 như lúc đầu
>
> 2. Thay đổi 1 biến cục bộ thì không trigge re-render

?> Vì vậy nên mới cần State, React sẽ lưu state này trong bộ nhớ của nó, không nằm trong cục bộ và nhờ đó react mới phát hiện được sự thay đổi và duy trì state trong nhiều lần render

!> Render nghĩa là component function chạy, re-render là function đó chạy lại, re-render chỉ xảy ra khi React phát hiện được sự thay đổi (ví dụ như state)

!> State là riêng biệt, cùng 1 component `<Product />` nhưng nếu mình gọi 2 lần thì React sẽ lưu trữ state của 2 compoennt Product này riêng biệt

## Quá trình render và commit

Khi 1 sự kiện nào đó trigger side-effect thì React sẽ nhận thấy sự thay đổi và re-render (nghĩa là chạy lại compoennt function đó), sau đó sẽ commit (Hiển thị UI lên màn hình)

Để trigger 1 render, có 2 trường hợp

1. Initial render: Khi lần đầu khởi động app sẽ trigger render
2. re-render: khi state uodate cũng sẽ trigger

## State như Snapshot

Nghĩa là mỗi 1 lần render, react chỉ lấy state trong lần chạy render đó, nếu có setState nào đó diễn ra thì state mới đó sẽ được thực thi vào ần render kế tiếp

Ví dụ cụ thể:

```bash
export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 1);
        setNumber(number + 1);
        setNumber(number + 1);
      }}>+3</button>
    </>
  )
}
```

Ví dụ trên sẽ chỉ tăng biến number lên 1 đơn vị thay vì 3 như mong đợi

## Queue / batch

?> React batch nghĩa là trong 1 lần render mà sinh ra nhiều trigger thì react sẽ đưa các lần trigger đó vào 1 queue, sau khi kết thúc lần render đó xong, react sẽ tính toán tất cả trong queue và render ra 1 kết quả duy nhất --> để tăng hiệu năng, chỉ render 1 làn chú ko phải nhiều lần

```bash
export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 1); // dòng 1
        setNumber(number + 1); // dòng 2
        setNumber(number + 1); // dòng 3
      }}>+3</button>
    </>
  )
}
```

Ví dụ trên, cả dòng 1 2 3 đều lấy number = 0 vì mỗi lần render React chỉ lấy giá trị state tại lần render đó

- Tại dòng 1: state được cập nhật là: 0 + 1 = 1 - trigger re-render - Đưa lần re-render này vào queue
- Tại dòng 1: state được cập nhật là: 0 + 1 = 1 - trigger re-render - Đưa lần re-render này vào queue
- Tại dòng 1: state được cập nhật là: 0 + 1 = 1 - trigger re-render - Đưa lần re-render này vào queue

Lúc này sẽ có 3 lần re-render trong queue, đáng lý ra nó sẽ render lại 3 lần và kết quả là number = 1, nhưng có react batch - react sẽ gộp 3 lần render trên lại thành 1 và re-render 1 lần thôi

Để có thể cập nhật giá trị state trong cùng 1 lần render thì:

## Update object / array

?> Nên tạo 1 bản sao object / array của state rồi replace nó bằng setState, bởi vì nếu ta cập nhật trực tiếp trong object như

```bash
const [position, setPosition] = useState({
    x: 0,
    y: 0
})

position.x = 5 // Lúc này object mơi và object cũ nó vẫn refer tới cùng 1 ô địa chỉ nên React không nhận ra sự thay đổi ==> không re-render (gọi là mutate)

setState({...position, x: 5}) // Tạo 1 bản sao va ghi đè biến x = 5 ==> gọi là immutate
```

?> Dùng spread chỉ đối với các object shallow (nông), đối với các object lồng sâu thì nên dùng thư viện immer
