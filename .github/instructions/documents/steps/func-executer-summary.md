# Summary
- Đây là tài liệu tóm tắt cho executer `func`.


### `func`
- **Tóm tắt chức năng**: Thực hiện hàm dùng chung.


#### `func.sleep`

- **Tóm tắt chức năng**: Tạm dừng thực thi trong một khoảng thời gian nhất định.
- 
- **Cú pháp**:
```yaml
- do: func.sleep
  with:
    time: <thời gian tạm dừng> # double, thời gian tạm dừng tính bằng giây
  on_success: ~
  on_fail: ~
```

#### `func.date_time_check`
- **Tóm tắt chức năng**: Kiểm tra ngày giờ hiện tại có nằm trong khoảng thời gian đã định hay không.
- **Cú pháp**:
```yaml
- do: func.date_time_check
  with:
    prod_date_time: <thời gian> # dạng string
    prod_date_time_format: <định dạng thời gian> # định dạng thời gian c# cho prod_date_time
    source: <nguồn lấy thời gian so sánh> # pc hoặc mes
    tolerance: <độ lệch cho phép> # double, độ lệch cho phép tính bằng giây
  on_success: ~
  on_fail: ~
```

#### `func.string_match`
- **Tóm tắt chức năng**: Kiểm tra chuỗi ký tự có khớp với chuỗi ký tự mong muốn hay không. Hỗ trợ kiểm tra bằng string hoặc regex.
- **Cú pháp**:
```yaml
- do: func.string_match
  with:
    source: <chuỗi ký tự nguồn> # chuỗi ký tự nguồn
    expect: <chuỗi ký tự mong muốn> # chuỗi ký tự mong muốn, sử dụng string hoặc `re_<pattern>`
  on_success: ~
  on_fail: ~
```