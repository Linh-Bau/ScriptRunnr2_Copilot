# Summary
- Đây là tài liệu tóm tắt cho executer `atmel`.

### `atmel`
- **Tóm tắt chức năng**: Dành cho trạm UX100/UX110 OS DL, sử dụng tool `AtmelSecureAccess` để thực hiện các thao tác với thiết bị Atmel.


#### `atmel.AUTO`
- **Tóm tắt chức năng**: Thực hiện chạy tool AtmelSecureAccess để flash firmware. Tự động check log kết quả.
- **Cú pháp**:
```yaml
- do: atmel.AUTO
  with:
    working_path: "<đường dẫn thư mục làm việc>" # đường dẫn thư mục làm việc chứa tool AtmelSecureAccess và file cần flash
  on_success: ~
  on_fail: ~
```

**Lưu ý**: 
- Sử dụng môi trường `/SFTP.conf.pe.02/V6ENV/AtmelSecureAccess`.
- Cần config thủ công `AtmelSecureAccess` trước khi chạy. Config trong thư mục mà tool được tải xuống. 