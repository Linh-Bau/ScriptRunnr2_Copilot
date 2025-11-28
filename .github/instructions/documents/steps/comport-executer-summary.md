# Summary
- Đây là tài liệu tóm tắt cho executer `com`.

### `com`
- **Tóm tắt chức năng**: Thực hiện đọc/gửi dữ liệu qua cổng COM SERIAL PORT.
  
#### `com.open`
- **Tóm tắt chức năng**: Mở cổng COM SERIAL PORT.
- **Cú pháp**:
```yaml
- do: com.open
  with:
    name: <name> # tên định danh cho cổng COM, sẽ truy cập qua $context <name>
    port: "<tên cổng COM>" # ví dụ: COM1, COM2, ...
    baudrate: <tốc độ truyền> # ví dụ: 9600, 115200, ...
  on_success: ~
  on_fail: ~
```

#### `com.close`
- **Tóm tắt chức năng**: Đóng cổng COM SERIAL PORT.
- **Cú pháp**:
```yaml
- do: com.close
  with:
    name: <name> # tên định danh cho cổng COM
  on_success: ~
  on_fail: ~
```

#### `com.wait_bytes_count`
- **Tóm tắt chức năng**: Chờ cho đến khi có đủ số byte dữ liệu nhận được từ cổng COM SERIAL PORT.
- **Cú pháp**:
```yaml
- do: com.wait_bytes_count
  with:
    name: <name> # tên định danh cho cổng COM
    expect_count: <số byte> # số byte cần chờ
    timeout: <thời gian chờ> # thời gian chờ tối đa (tính bằng giây)
  on_success: ~
  on_fail: ~
```

#### `com.wait_string`
- **Tóm tắt chức năng**: Chờ cho đến khi nhận được chuỗi ký tự cụ thể từ cổng COM SERIAL PORT. Chuỗi ký tự có thể là chuỗi `string` bình thường, hoặc regex với `re_pattern`.
- **Cú pháp**:
```yaml
- do: com.wait_string
  with:
    name: <name> # tên định danh cho cổng COM
    expect: "<chuỗi ký tự>" # chuỗi ký tự cần chờ, hoặc regex với re_pattern
    timeout: <thời gian chờ> # thời gian chờ tối đa (tính bằng giây)
  on_success: ~
  on_fail: ~
```

#### `com.send_string`
- **Tóm tắt chức năng**: Gửi chuỗi ký tự qua cổng COM SERIAL PORT.
- **Cú pháp**:
```yaml
- do: com.send_string
  with:
    name: <name> # tên định danh cho cổng COM
    command: "<chuỗi ký tự>" # chuỗi ký tự cần gửi
    sleep: <thời gian nghỉ> # thời gian nghỉ sau khi gửi (tính bằng giây), double
  on_success: ~
  on_fail: ~
```

#### `com.send_wait`
- **Tóm tắt chức năng**: Gửi chuỗi ký tự qua cổng COM SERIAL PORT và chờ phản hồi chuỗi ký tự cụ thể. Chuỗi chờ phản hồi có thể là chuỗi `string` bình thường, hoặc regex với `re_pattern`.
- **Cú pháp**:
```yaml
- do: com.send_wait
  with:
    name: <name> # tên định danh cho cổng COM
    command: "<chuỗi ký tự>" # chuỗi ký tự cần gửi
    retry_times: <số lần thử lại> # nếu fail, sẽ thử lại n lần. 
    expect: "<chuỗi ký tự>" # chuỗi ký tự cần chờ, hoặc regex với re_pattern
    timeout: <thời gian chờ> # thời gian chờ tối đa (tính bằng giây)
  on_success: ~
  on_fail: ~
```
**ví dụ**:
```yaml
- do: com.send_wait
  with:
    name: "com"
    command: X
    retry_times: 3
    expect: TOP MENU
    timeout: 5
  on_success: ~
  on_fail:
    - do: return.FAIL
      with:
        error_code: COM_COMMAND_FAIL
```

#### `com.check_log`
- **Tóm tắt chức năng**: Kiểm tra xem chuỗi ký tự cụ thể có xuất hiện trong log nhận được từ cổng COM SERIAL PORT hay không. Chuỗi ký tự có thể là chuỗi `string` bình thường, hoặc regex với `re_pattern`.
- **Cú pháp**:
```yaml
- do: com.check_log
  with:
    name: <name> # tên định danh cho cổng COM
    expect: "<chuỗi ký tự>" # chuỗi ký tự cần kiểm tra, hoặc regex với re_pattern
  on_success: ~
  on_fail: ~
```

#### `com.read_value`
- **Tóm tắt chức năng**: Đọc giá trị từ log cổng com với chuỗi ký tự định danh và lưu vào context.
- **Cú pháp**:
```yaml
- do: com.read_value
  with:
    name: <name> # tên định danh cho cổng COM
    expect: "<chuỗi ký tự định danh>" # chuỗi ký tự định danh để tìm 
    match_index: <chỉ số nhóm regex> # chỉ số nhóm regex để lấy giá trị
    replace: "<chuỗi thay thế>" # chuỗi thay thế (nếu có)
    replace_with: "<chuỗi thay thế bằng>" # chuỗi thay thế bằng (nếu có)
    var_name: <tên biến> # tên biến lưu giá trị vào context với định dạng $context <var_name>
  on_success: ~
  on_fail: ~
```

**ví dụ**:
```yaml
# đọc giá trị battery_level từ log cổng com với định dạng "Battery Level: XX%"
- do: com.read_value
  with:
    name: "com"
    expect: "Battery Level: (\d+)%"
    match_index: 1
    var_name: battery_level
  on_success: ~
  on_fail:
    - do: return.FAIL
      with:
        error_code: COM_READ_VALUE_FAIL
```

#### `com.wait_check`
- **Tóm tắt chức năng**: Chờ một chuỗi ký tự kết thúc, kiểm tra ở log mới xuất hiện có chuỗi ký tự mong muốn không. Các chuỗi có thể là chuỗi `string` bình thường, hoặc regex với `re_pattern`.
- **Cú pháp**:
```yaml
- do: com.wait_check
  with:
    name: <name> # tên định danh cho cổng COM
    expect_wait: "<chuỗi kết thúc>" # chuỗi ký tự kết thúc để dừng chờ
    expect_check: "<chuỗi ký tự>" # chuỗi ký tự cần kiểm tra, hoặc regex với re_pattern
    timeout: <thời gian chờ> # thời gian chờ tối đa (tính bằng giây)
  on_success: ~
  on_fail: ~
```
**ví dụ**:
```yaml
- do: com.wait_check
  with:
    name: "com"
    expect_wait: "END"
    expect_check: "RESULT: PASS"
    timeout: 10
  on_success: ~
  on_fail:
    - do: return.FAIL
      with:
        error_code: COM_WAIT_CHECK_FAIL
```

