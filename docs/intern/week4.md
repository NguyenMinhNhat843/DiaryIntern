## Thứ 5

- Task vẽ chart:

  - Thêm chức năng tải file excel
  - Thêm sắp xếp theo tăng dần / giảm dần lượt truy cập, theo alphabet
  - Sửa lỗi khi sort biểu đồ mất animation

- Tiếp tục làm example table:
  - Chức năng filter: làm thanh search ngay dưới các header

> - Tìm hiểu cách deploy code lên server nội bộ
> - Thử với việc dùng máy tính cá nhân làm sever, điện thoại connect cùng wifi với máy tính xem có load đc không
> - Dùng docker, tunel

> - Đọc hiểu code phần quản lý kho, xuất/nhập thuốc của quản lý sức khỏe

## Thứ 4

- Luyện tập tanstack table với các example

  - Khi định nghĩa các column có thể dùng với ColumnHelper hoặc ColumnDef, trong ColumnDef có fied accessFn, vấn đề là khi mình tạo 1 column accessKey: 'firstName' chẳng hạn thì bình thường nhưng nếu muốn tạo 1 cột là fullName thì sao, lúc này phải tính toán từ firstName + lastName --> dùng accessFn
  - Code chức năng phân trang: tới trang kế tiếp, lùi về trang trước, nhảy tới trang đã nhập, chỉnh số lượng dòng hiển thị trong 1 page

- Task vẽ chart:
  - sửa lại logic lọc các đơn vị con của user hiện tại theo cơ sở y tế
  - Sửa lỗi nháy biểu đồ khi đổi dateRange

## Thứ 3

- làm task vẽ chart thống kê truy cập:
  - Logic lọc theo ngày tháng năm quý
  - Chỉ hiển thị thống kê các đơn vị con của đơn vị user đang đăng nhập

## thứ 2

- nhận task: Tạo trang thống kê lượt truy cập cho phần mềm quản lý sức khỏe
- fetchData và Vẽ UI chart
