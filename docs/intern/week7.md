# Tuần 7 (20/10 - 26/10)

## Thứ 7

- Fix lỗi hư css khi thêm scrollbar vào Tablist:
  - Nhưng lỗi nhiều hơn, nên reset lại

## Thứ 6

- Thêm size, scroll cho TabList:
  - Đang lỗi css khi thêm Scroll vào
  - Sửa lại UI scroll ngang cho khám tuyển sinh như khám thường xuyên

## Thứ 5

- Làm tiếp task Khám thường xuyên
  - Sửa lại logic lọc items cho cây: tìm đơn vị con theo parentId --> chuyển sang tìm theo code
  - Thêm logic tự động scroll khi reload page
  - Fix lỗi khi nhập url tào lào sẽ bị lỗi, cho nó reset về default
  - Thêm disable node root trong cây đơn vị

## Thứ 4

- Tiếp tục fix bug dataTable, thử tách hẳn logic drawer ra khỏi UserlIst, vậy thì userList sẽ không re-render

  - Thì sẽ ko bị reset page
  - Dùng forwardRef --> được, nhưng khi setRole trong drawer vẫn bị reset do nó gọi refetAll(), không tách đc
  - Nên chuyển về tìm cách truyền pagination từ bên ngoài vào, bị vòng lặp re-render
  - Nên bí

- Nhận task mới: Làm thanh đơn vị scrollbar ngang cho khám thường xuyên
  - Sửa UI cho có thanh scroll ngang
  - Thêm logic để bấm vô cái nào là hiên rtihj cây đơn vị của đơn vị đang chọn

## Thứ 3

- Làm ở nhà
- Chỉnh sửa vài lỗi và clean code ở hồ sơ y tế, fix bug dataaTable bị reset về page 1 khi có re-render
  - Nhưng bí

## Thứ 2

- Làm task Hồ sơ y tế:
  - thêm nút chỉnh sửa --> bấm vô sang trang edit
  - Thêm cột hồ sơ y tế, có option Đã có hay chưa
  - Không biết cách đồng bộ cột hồ sơ y tế lên url nên chuyển sang làm tabList
  - Thêm thông tin hồ sơ y tế trong drawer
