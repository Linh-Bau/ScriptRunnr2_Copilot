# Summary
- Đây là tài liệu tóm tắt cho executer `cam`.

### `cam`
- **Tóm tắt chức năng**: Thực hiện các chức năng liên quan đến đọc ảnh từ camera. Sử dụng cho trạm dùng CAMERA VISION.

#### `cam.set_operation_image`
- **Tóm tắt chức năng**: Thiết lập ảnh thao tác (operation image) sẽ được hiển thị trên cửa sổ `CAMERA WINDOW`.
- **Cú pháp**:
```yaml
- do: cam.set_operation_image
  with:
    image_path: "<đường dẫn ảnh>" # đường dẫn tới file ảnh
  on_success: ~
  on_fail: ~
```

#### `cam.check_ocr_string`
- **Tóm tắt chức năng**: 
  - 1. Đọc text từ ảnh
  - 2. Kiểm tra chuỗi ký tự mong muốn có chứa chuỗi ký tự cần kiểm tra hay không. Nếu không thì lặp lại <b>1</b> cho đến timeout.
  - 3. Kiểm tra chuỗi ký tự có khớp với chuỗi ký tự mong muốn hay không.
  - 4. Thực hiện lưu ảnh nếu cần.
**Lưu ý**: Chuỗi ký tự có thể dùng string hoặc regex dạng `re_<pattern>`.
- **Cú pháp**:
```yaml
- do: cam.check_ocr_string
  with:
    wait_string:  <Chuỗi ký tự chờ đợi> # chuỗi ký tự chờ đợi xuất hiện trong ảnh, sử dụng string hoặc `re_<pattern>`
    timeout: <thời gian chờ> #double, thời gian chờ tối đa (tính bằng giây) 
    save_image_with_name: <tên ảnh lưu> # tên ảnh lưu (nếu cần lưu ảnh), không cần đường dẫn, ảnh sẽ được lưu trong thư mục kết quả của test, hoặc để ~ nếu k cần lưu.
    expect: <Chuỗi ký tự mong muốn> # chuỗi ký tự mong muốn kiểm tra trong ảnh, sử dụng string hoặc `re_<pattern>`
    # 
  on_success: ~
  on_fail: ~
```

#### `cam.get_ocr_string`
- **Tóm tắt chức năng**: 
  - 1. Đọc text từ ảnh
  - 2. Kiểm tra chuỗi ký tự mong muốn có chứa chuỗi ký tự cần kiểm tra hay không. Nếu không thì lặp lại <b>1</b> cho đến timeout.
  - 3. Lấy giá trị từ chuỗi ký tự đọc được dựa trên regex và lưu vào context với tên là <var_name>
  - 4. Thực hiện lưu ảnh nếu cần.

**Lưu ý**: Chuỗi ký tự có thể dùng string hoặc regex dạng `re_<pattern>`.

- **Cú pháp**:
```yaml
- do: cam.get_ocr_string
  with:
    wait_string:  <Chuỗi ký tự chờ đợi> # chuỗi ký tự chờ đợi xuất hiện trong ảnh, sử dụng string hoặc `re_<pattern>`
    timeout: <thời gian chờ> #double, thời gian chờ tối đa (tính bằng giây) 
    save_image_with_name: <tên ảnh lưu> # tên ảnh lưu (nếu cần lưu ảnh), không cần đường dẫn, ảnh sẽ được lưu trong thư mục kết quả của test, hoặc để ~ nếu k cần lưu.
    var_name: <tên biến> # tên biến lưu giá trị vào context với định dạng $context.<var_name>
  on_success: ~
  on_fail: ~
```