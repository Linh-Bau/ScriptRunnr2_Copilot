# # Summary
- Đây là tài liệu cho executer `vtp`

### `vtp`
- **Tóm tắt chức năng**: Dùng để tự động hóa việc chạy phần mềm VTP của khách hàng.
  
#### `vtp.AUTO`
- **Tóm tắt chức năng**: Thực hiện tự động hóa việc chạy VTP với các tham số được cung cấp. Chương trình sẽ kiểm tra kết quả sau khi chạy.
- **Cú pháp**:
```yaml
- do: vtp.AUTO
  with:
    working_path: "<đường dẫn thư mục chứa VTP>" # đường dẫn đến thư mục chứa VTP.exe
    exe_name: "<tên file thực thi VTP>" # ví dụ: VTP.exe
    xml_name: <file xml của vtp> # ví dụ: test.xml
    timeout: <thời gian chờ tối đa (giây)> # ví dụ: 300
  on_success: ~
  on_fail: ~
```

**Lưu ý**:
- vtp: sử dụng môi trường ở `/SFTP.conf.pe.02/V6ENV/vtp_ver24011801` kèm setup môi trường cho từng mã hàng. 
- Cần đảm bảo chương trình `VTP` đã được config chính xác. 
- Liên hệ SW ENG để biết thêm chi tiết.