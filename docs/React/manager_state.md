# Cách viết State hiệu quả

Để dùng React trong việc trực quan hóa trạng thái UI ta cần 5 bước

1. Xác định các trạng thái có trong UI đó

Ví dụ: 1 form đăng nhập gồm username và pasword có các trạng thái như: disable button, enable button, error, snipper...

2. Xác định các action nào gây ra các trạng thái đó

- disable button khi chưa có đủ thông tin
- enable button khi đã có đủ tất cả
- error khi có lỗi j đó
- snipper khi đang call api login

3. Tạo các useState lưu các trạng thái UI đó
4. Bỏ các state không cần thiết
5. Kết nối các state với event handler

## Cách tổ chức cấu trúc State hiệu quả

## Chia sẻ State giữa 2 component

Lifting state up: Là bỏ state khỏi 2 component đó, đưa state đó lên cấp cha gần nhất của cả 2 và truyền state xuống lại với props

## Preserve and resetting state

Nếu 1 component biến mất (có thể do conditionally rendering) thì state của nó cũng sẽ bị hủy

Vị trí của 1 component được xác định dựa trên cây UI

Tóm lại: Nếu muốn state duy trì giữa các lần re-render thì nó cần có cấu trúc giống nhau trên cây UI

Thực tế: Dùng trong ứng dụng chat

ứng dụng chat chỉ có 1 khung chat `<Chat />`, nhưng sẽ có nhiều `<Contact />`
Tình huống là muốn khi mình nhập nội dung vào `<Chat />` sau đó chuyển sang `<Contact />` khác thì Chat phải reset state lại
Cách đơn giản và phổ biến là gắn 1 key cho nó `<Chat key={contact.id} />` hoặc cũng có thể đổi vị trí nó tới 1 nơi khác trong cây UI

Nhưng đôi khi mình lại muốn preserve nội dung trong đoạn chat cua người trước, Có nhiều cách:

- render all chat nhưng ẩn các chat khác đi --> fail vì khi số lượng component lớn sẽ gây chậm
- Cho thẻ cha có state lưu chat của từng thẻ con và truyền sang props (lifting up) --> phổ biến hơn
- Lưu vào localstorage và đọc lên --> cũng là 1 cách tốt

## Reducer

Vấn đề: giả sử cần thao tác thêm xóa sửa với 1 mảng products, thông thường sẽ tạo ra 3 event handler, nhưng khi số lượng logic tăng lên, việc viết nhiều event handler như vậy khiến code trở nên phức tạp, vì vậy ý tưởng là tách các event handler này sang 1 nới khác - gọi là Reducer

Thay vì dùng event handler để phản hồi lại tương tác của người dùng như:

- Khi nhấn Add thì call addProduct
- Khi nhấn delete thì call deleteProduct
- ...
  Ta thay đổi cách tiếp cận bằng việc `dispatch` 1 `action`:
- Người dùng vừa nhấn `add` với dữ liệu là `{... data product}`
- Người dùng vừa nhấn `delete` với duex liệu là `id: 5`
- ...
  Và phần logic xử lý sẽ được viết ở 1 nơi khác

?> Trong React đã có hook useReducer, cũng có thể dùng useImmerReducer cho việc viết non-mutation

Luyện tập với bài tập trong React document:

- Luyện thêm dùng immer

## Pasing with props / paswing with context

Vấn đề: Khi tuyền bằng props thì cần truyền tuần tự từ cha tới con tới chắt ... tới level của component cần nhận props đó, khá dài dòng, vì vậy react nghĩ ra 1 cách dịch chuyển data từ component cha tới component con mà không cần phải qua nhiều bước ở giữa ==> context

Không nên lạm dụng context, nếu các component đơn giản, không nhiều cấp thì có thể truyền 1 đống props tới 1 đống components mà không có vấn đề cả, điều này khiến nó rõ ràng hơn

?> Nếu component sâu thì nên dùng context còn nông thì dùng props là được

1 số case nên dùng context: https://react.dev/learn/passing-data-deeply-with-context#use-cases-for-context
