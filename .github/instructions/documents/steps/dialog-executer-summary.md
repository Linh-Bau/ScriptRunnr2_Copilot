# Summary
- Đây là tài liệu tóm tắt cho executer `dialog`.

### `dialog`
- **Tóm tắt chức năng**: Hiển thị các hộp thoại (dialog) để tương tác với người dùng.

#### `dialog.show`
- **Tóm tắt chức năng**: Hiển thị một hộp thoại thông báo với thông điệp và tiêu đề tùy chỉnh.
- **Cú pháp**:
```yaml
- do: dialog.show
  with:
    title: "Tiêu đề thông báo"
    message: "Nội dung thông báo"
```

#### `dialog.confirm`
- **Tóm tắt chức năng**: Hiển thị một hộp thoại xác nhận với các nút "YES" và "NO". Yes sẽ trả về `on_success`, No sẽ trả về `on_failure`.
- **Cú pháp**:
```yaml
- do: dialog.confirm
  with:
    title: "Tiêu đề xác nhận"
    message: "Bạn có chắc chắn không?"

### `dialog.input`
- **Tóm tắt chức năng**: Hiển thị một hộp thoại nhập liệu để người dùng nhập thông tin.

- **Cú pháp**:
```yaml
- do: dialog.input
  with:
    title: "Tiêu đề nhập liệu"
    message: "Vui lòng nhập thông tin:"
    var_name: "ten_bien_luu_tru" 
```