# Mutation

- Dùng để thực hiện các api write: put/patch/delete
- Có các trạng thái tương tự như useQuery nhưng khác 1 chút: isIdle, isPending, isError, isSuccess
- useMutation chỉ thực hiện khi gọi mutationFn, không phải tự chạy như useQuery
- Gọi useMutation bằng: mutation.mutate(data - có thể là body, pấm, query)
- Có thể tự định nghĩa logic cho onSuccess - 1 callback function thực hiện logic sau khi mutate thành công
- Kết hợp với queryClient.invalidateQueries() - để đánh dấu stae cho query đã cũ - vì khi thay đổi dữ liệu j đó thì đồng nghĩa là dữ liệu hiện tại đang cách đã cũ rồi
- Kết hợp với queryClient.setQuery() - trực tiếp cập nhật dữ liệu cache mà ko cần fetch lại server - vì khi mutate xong thì có dữ liệu mới rồi, thêm thẳng vào cache sẽ giảm tải việc call server
- Dùng mutation.reset() để reset lại lần query này khi lỗi
- 1 ví dụ là khi submit form có lỗi thì hiên thị mutation.error lên UI, cho phép user click vào lỗi để reset lại form - Ui trở lại ban đầu
- Tiện hơn cho user, thay vì relaod lại trang với f5 thì có thể retry lại dễ dang hơn
- 1 Mutation khi chạy trải qua các giai đoạn khác nhau:
  - onMutate: Ngay trước khi mutation chạy
  - onError: khi mutation thất bại
  - onSuccess: mutation thành công
  - onSettled: mutation kết thúc baatskeer thành công hay ko
- Dựa trên những giai đoạn có thể truyền callback function vào tương ứng để thực hiện các logic của mình
- Nếu callback là async function hoặc promise, tanstack query sẽ await trước khi qua giai đoạn kế tiếp
- onMutate chạy trc khi mutatation bắt đầu, sau đó các giai đoạn onSuccess, onError, onSettled sẽ chạy cho useMuatation trước và chạy cho mutate sau
- là khi tạo 1 useMutation có thể định nghĩa các callback bên trong - các hàm này se xchayj trước
- và khi gọi matation đó: mutate(data, {onSuccess: function, ...}) thì các callback này sẽ chạy sau
- Khi gọi nhiều mutate liên tiếp nhau thì, callback trong useMutation sẽ thực thi theo tưng mutate được gọi và ko quan trọng thứ tự vì nó async, nhưng mutate thì sẽ chạy tuần tự theo thứ tự và chỉ gọi callback cho lần cuối cung
- Ví dụ mutate.(data) 3 lần theo vòng lặp thì onSettled trong useMuatation sẽ chạy 3 lần nhưng onSettled trong mutate chỉ chạy 1 lần khi cái cuối cùng trong 3 đã chạy xong, nó vẫn là async nhưng chỉ chạy cái cuối cùng thôi
- Nếu muốn dùng mutate như async/await để chạy tuần tự thì dùng:

```bash
const mutation = useMutation({ mutationFn: addTodo })

try {
  const todo = await mutation.mutateAsync({ title: 'Do Laundry' })
  console.log('Mutation success:', todo)
} catch (error) {
  console.error('Mutation failed:', error)
} finally {
  console.log('done')
}
```

- useQuery mặc định tự động retry khi gặp lỗi, nhưng useMutation thì mặc định ko, nếu muốn dùng phải thêm key retry: 3 - số lần retry

!> Chưa hiểu: persist mutation: https://tanstack.com/query/latest/docs/framework/react/guides/mutations#persist-mutations

- Mutation Scope: mặc định mutation là async, nếu muốn cho nó theo thứ tự thì đưa nó vào cùng 1 scope với id

# Queries invalidation

- Mục đích: DÙng khi mình biết chắc chắn 1 dữ liệu đã lỗi thời và muốn:

  - Stale data ngay lập tức
  - Kích hoạt refetch tự động cho các query active và inactive theo cấu hình

- Thường dùng khi mình thêm/xóa/sửa 1 dữ liệu - chắc chắn đã có thay đổi nên càn stale data ngay lập tức

Cú pháp

```bash
queryClient.invalidateQueries({ queryKey: ['todos']})
```

- queryKey là các id của useQuery, dùng để chỉ định cần invalidte query nào
- exact: chỉ match chính xác - nghĩa là id phải là 1 mảng giống hệt thì mới invalidate
- predicate: hàm Điều kiện, có thể match theo logic của hàm

Cách dùng trong mutation - dùng với onSucess

```bash
const queryClient = useQueryClient()

const mutation = useMutation({
  mutationFn: addTodo,
  onSuccess: async () => {
    await queryClient.invalidateQueries({ queryKey: ['todos'] })

    // invalidate nhiều query
     await Promise.all([
      queryClient.invalidateQueries({ queryKey: ['todos'] }),
      queryClient.invalidateQueries({ queryKey: ['reminders'] }),
    ])
  },
})
```

- Khi thực hiện 1 thao tác thêm/xóa/sửa thì server sẽ trả về object cho api đó, ví dụ khi thêm thành công thì trả về user mới thêm, xóa thì trả về id vùa xóa, hay sửa cũng vậy, vậy thì chỉ cần thay đổi dữ liệu trong cache là đc, ko cần phải invalidate để refetch lại - tốn băng thông
  Cách dùng

```bash
const queryClient = useQueryClient()

const mutation = useMutation({
  mutationFn: editTodo,
  onSuccess: (data) => {
    // data = object todo mới từ server
    queryClient.setQueryData(['todo', { id: 5 }], data)
  },
})

mutation.mutate({
  id: 5,
  name: 'Do the laundry',
})
```

# Optimisics Update

- Mục đích: Khi mutation 1 dữ liệu thì cần thời gian để server xử lý - trả về dữ liệu mới - cập nhật UI
  --> Sẽ gây lag, thay vì vậy mình có thể tạo 1 kết quả ảo để cập nhật UI ngay lập tức rồi đợi server xử lý xong thì xác nhận lại
  --> Giúp trải nghiệm mượt hơn

- Như phần trên có kỹ thuật là cache data sau khi mutation thay vì refetch lại, nhưng để tăng trải nghiệm người dùng, mình có thể cache data truowcskhi mutation chạy với onMutate(), rồi cập nhật UI với cache đấy, Ui sẽ hiển thị ngay lậ tức trước cả khi mutation thành công, nhưng nó có thể gây lỗi, nên cần lưu dữ liệu trước khi mutation để rollback lại nếu lỗi

đọc thêm tại đây: [](https://tanstack.com/query/latest/docs/framework/react/guides/optimistic-updates)

# Query Cancellation

- Mục đích: Dùng để hủy giữa chừng khi đang fetch data với useQuery
- Dùng khi:
  - Người dùng lấy dữ liệu danh sách todolist nhưng mà nhiều quá load lâu quá người dùng muốn hủy ngang
  - Trong quá trình fetchData thì có mutation làm thay đổi dữ liệu rồi thì hủy giữa chừng cái fetchData đó đi

Đọc thêm tại: ()[https://tanstack.com/query/latest/docs/framework/react/guides/query-cancellation]

# Scroll restoration

- Khi người dùng cuộn tới giữa trang xong tắt, hôm sau mở lại nó sẽ cuộn tới đúng vị trí mà mình đã coi hôm qua

# Filters

- Dùng để lọc các query và mutation theo logic yêu cầu
- Trong dự án sẽ có nhiều query đang cache, nhiều mutation, giả sử trong 1 lúc nào đó cần lấy các query/mutation theo key hay theo predicate
- Lấy bằng cách `queryClient.<method>`
- ĐỌc thêm tại ()[https://tanstack.com/query/latest/docs/framework/react/guides/filters]

# Request waterfalls

- Request waterfall: nghĩa là việc các request thực hiện tuần tự, giả sử cần lòa 4 tài nguyên, request waterfalls sẽ laod tài nguyên 1 xong rồi load tài nguyên 2 xong tài nguyên 3 xong tài nguyên 4
- giữa mỗi lần request sẽ tốn 1 khoảng latency nên thời gian tải = 4 \* latency + thời gian tải thực tế
- Nếu bằng cách nào đó có thể load đồng thời cả 4 gói về thì `thời gian tải thực tế` vẫn vậy nhưng mà bơt sđc latency, đỡ lag hơn đáng kể
- Tanstack query nó ko giúp api load nhanh hơn, nhưng nó giúp cách laod api có thể đồng thời hơn, giảm waterfalls

Ví dụ cần load user, sau đó load các dự án của user đó --> waterfalls

```bash
// Lấy user trước
const { data: user } = useQuery({
  queryKey: ['user', email],
  queryFn: getUserByEmail,
})

// Lấy dự án của user sau khi có userId
const { data: projects } = useQuery({
  queryKey: ['projects', user?.id],
  queryFn: getProjectsByUser,
  enabled: !!user?.id, // query phụ thuộc userId
})
```

--> Có thể giải quyết bằng cách code thêm api phái server vừa getUser và dự án của họ 1 lúc luôn
