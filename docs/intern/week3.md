# Tuần 3

## Thứ 6

- Làm task: hiện tài liệu liên quan / tài liệu mới nhất khi click vào 1 tài liệu

## Thứ 5

- Tanstack table:

  - Phải có Colume define và Rows (dữ liệu)
  - Data là 1 mảgr object - mỗi object tương ứng 1 hàng
  - Phải định nghĩa type cho data
  - Tránh `stable reference` - table sẽ re-render nhiều lần khi data và cols thay đổi, dùng useMemo, hoặc đặt data và colums ra ngoài, hoặc dùng useState - không khai bao mảng bình thường
  - dataTable là imutate, là nó tạo ra bản sao của cols và data để hiển thị UI
  - Tạo ColDefine với ColumnDef hoặc ColumeHelper
  - TántackTable không bê nguyên object[] data bỏ vô để render mà nó sẽ chuyển đổi data gốc thành các rows để có thể filter, sort, group, expand,...
  - Để chuyển đổi vậy thì cần các hàm chuyển đổi - gọi là Rows Model, có nhiều hàm rows model, hàm cơ bản và bắt buộc là getCoreRowModel
  - [Link coi các rows model hiện có trong tanstack table](https://tanstack.com/table/latest/docs/guide/row-models#available-row-models-on-table-instance)
  - Để dùng được các Rows model thì cần khởi tạo Table trước
  - Để tương tác với row - lấy theo id, lấy data gốc, ... coi link [đây](https://tanstack.com/table/latest/docs/guide/rows)
  - Tương tác với cell - 1 ô, [tại đây](https://tanstack.com/table/latest/docs/guide/cells#api)
  -

- Task: Quản lý tài liệu, khi mở 1 tài liệu công cộng:
  - Thêm tooltip cho icon mở link xem chi tiết tài liệu
  - Khi mở link sẽ có 2 cột:
    - cột tài liệu liên quan theo tiêu chí: loại tài liệu, năm ban hành, folder, cơ quan ban hành
    - cột tài liệu mới nhất: có option số lượng tài liệu hiển thị, thời gian hiển thị (ví dụ 1 tháng thì hiển thị tài liệu mới trong 1 tháng thôi)

## Thứ 4

- Làm chức năng xuất file csv cho UserList (ưu tiên trước) và DataTable (nếu làm được)

- tanstack query

> Khi staleTime là 15m mà trong 15m này mình có thêm xóa sửa thông tin thì lúc này cần refetch lại data mới
> có method invalidationQueries - dùng để đánh dấu invalidation (vô hiệu) 1 eury theo qeuryKey
> Nhưng nếu chỉ thay đổi 1 object (thêm, xóa, sửa j đó) mà phải refetch lại all thì tốn mạng
> Nên có method setQueryData - gắn thêm data cho cái data sẵn có thay vì refetch lại

- tanstack router:

> params
>
> - const {petID} = Route.params() --> lấy params của route hiện tại
> - Bấm vào 1 Pet sẽ sang chi tiết Pet, thay vì truyền id sang rồi qua /pet/$petId search thông tin theo id đó
> - Thì mình có thể truyền cả object pet sang luôn, bằng cách
>
> ```bash
> PetOne.tsx
> `<Link to='/pet/$petId' params={{petId}} state={{pet}}>`
> routes/pet/$petId.tsx
> const pet = (location.state as any | undefined)?.pet;
> ```

> route tree
>
> - pet/index.tsx --> khi match route /pet sẽ render index.tsx
> - pet/route.tsx --> khi match route /pet sẽ render index.tsx
> - pet/addPet.tsx --> khi match route /pet/addPet render addPet.tsx

> Not found component
> CÓ 2 mode:
>
> - fuzzy mode: khi throw error sẽ tìm NotFoundComponent của route gần nhất
> - root mode: NotFoundComponent của \_\_root
>   Các method:
> - Không tìm thấy route phù hợp tự động sẽ ném NotFoundComponent
> - throw notFound(): muốn tự ném notFoundComponent - có thể chỉ định cụ thể lấy NotFoundComponent ở route nào

## Thứ 3

http://localhost:3002/dot-kham/tuyen-sinh-quan-su
tạo đợt khám mới - hoàn thành đợt khám đó phải reload lại mới hiện hoàn thành

tanstack route:

- `<Outlet />` như children, dùng để hiển thị các compoennt route con
- \_foldername: gọi là pathless route, nó sẽ được bỏ qua khi tạo path nhưng vẫn tạo layout cho các route con được

## thứ 2

Hệ thống lại kiến thức Tanstack query:

Code splitting

- Tách bundle ra nhiều size để giảm thời gian load web ban đầu
- Thông thường dùng autoCodeSplitting: true là đủ, nhưng có thể cấu hình nâng cao hơn, đọc thêm [Phần này](https://tanstack.com/router/latest/docs/framework/react/guide/automatic-code-splitting)

- Route dùng là filebase router, toàn bộ route sẽ được định nghĩa trong folder tên `routes`, phải đặt tên
- đúng như vậy thì vite mới đọc được và gen ra 1 file routeTree.gen.ts
- Để sử dụng route thì cần config trong vite, cần tạo 1 createRoute(routeTree) và mọi component con phải được gói trong RoutePrivider
- Thì Trong folder routes sẽ có file \_\_root.ts - là file trên cùng nhất, những gì được viết trong này sẽ luôn có với mọi path
- Route sẽ được sinh dựa trên cấu trúc file ví dụ: pet / addPet.ts --> /pet/addPet
- pet / manager / addPet.ts --> /pet/manager/addPet
- nếu đặt có \_pet thì cái này đc bỏ qua, \_pet / manager / addPet.ts --> /manager/addPet thôi, phần \_pet bị quả qua
- Bình thường sẽ có file index.tsx - là nơi đặt trang home, nhưng cũng có thể đặt như sau
  - \_pet/index.tsx --> vì \_pet bị bỏ qua nên idnex.tsx vẫn đc truy cập với path /
  - Nhưng nếu có thêm 1 folder \_pet2/index.tsx nữa thì sẽ lỗi tại nó gen ra 2 route: / giống nhau
  - nhưng nếu đổi \_pet2/abc.tsx thì sẽ ko lỗi nữa
  - File index.tsx mặc định được render khi match với route /, còn nếu đặt tên khác index thì phải chỉ rõ route ra /abc
- pet/ và \_pet/ thì routeTree.gen nó đều tạo ra cùng 1 route với name là PetIndex, nên là đặt 2 cái như vậy sẽ dễ lỗi lặp route
- trong cấu hình:

```bash
const router = createRouter({
  routeTree,
  defaultNotFoundComponent: () => <div>Đây là NotFound component tự viết</div>,
  defaultPreload: "intent",
});
```

có field defaultPreload - ví dụ để intent là khi hover vào sẽ load component lên trước khi click luôn, để lượt hơn
Nhưng chỉ áp dụng khi codespliitng thôi, nếu trong vite.config ko để code splitiing thì ko có tác dugnj j cả vì nó đã laod all lên rồi

```bash

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    tanstackRouter({
      target: "react",
      autoCodeSplitting: true, --> cái này có thì cái trên mới có tác dụng
    }),
    react(),
    tailwindcss(),
  ],
});
```

- dùng tsr đã có cấu hình codesplitting, nghĩa là nó chỉ load component kho match route thôi
  Vậy còn createFileLazyROute để làm gì nữa?

```bash
export default defineConfig({
  plugins: [
    TanStackRouterVite({
      autoCodeSplitting: true, --> tsr đã hỗ trợ sẵn
    }),
    react(),


export const Route = createLazyFileRoute('/_authenticated/access-denied')({
  component: AccessDeniedPage,
})

function AccessDeniedPage() {
  return <AccessDenied />
}
==> Cái này để làm gì nữa?
```
