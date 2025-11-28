# # Summary
- Đây là tài liệu cho executer `qfil`

### `qfil`
- **Tóm tắt chức năng**: Dùng để tự động hóa việc chạy phần mềm QFIL (Qualcomm Flash Image Loader) để flash firmware cho các thiết bị sử dụng chip Qualcomm.

#### `qfil.AUTO`
- **Tóm tắt chức năng**: Thực hiện load firmware vào QFIL và flash cho thiết bị. Tự động kiểm tra kết quả flash.
- **Cú pháp**:
```yaml
- do: qfil.AUTO
  with:
    working_path: "<đường dẫn thư mục chứa QFIL>" # đường dẫn đến thư mục chứa QFIL.exe
    comport: "<cổng COM của thiết bị>" # ví dụ: 17, 18
    image_path: "<đường dẫn file firmware>" 
    timeout: <thời gian chờ tối đa (giây)> # ví dụ: 300
  on_success: ~
  on_fail: ~
```

**Lưu ý**:
- comport: là số ví dụ `17`,`18` không ghi `COM17`,`COM18`.
- comport: để là ~ thì QFIL sẽ tự động tìm cổng (Nếu chương trình dùng nhiều fixture sẽ lỗi).
- image_path: đường dẫn đến thư mục OS chứa file `path.xml` hoặc file `prog_emmc_firehose_*.mbn`.
- timeout: thời gian chờ tối đa để flash xong, nếu quá thời gian này sẽ báo lỗi.
- QFIL: sử dụng môi trường ở `/SFTP.conf.pe.02/V6ENV/qfil_env`