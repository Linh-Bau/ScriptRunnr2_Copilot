# Summary
- Đây là tài liệu tóm tắt cho executer `qcm`.

### `qcm`
- **Tóm tắt chức năng**: Dành cho các trạm viết imei sử dụng tool `QCM Write Imei TOOL`.
  
#### `qcm.AUTO`
- **Tóm tắt chức năng**: Thực hiện chạy tool QCM Write Imei TOOL để viết IMEI cho thiết bị. Tự động check log kết quả.
- **Cú pháp**:
```yaml
- do: qcm.AUTO
  with:
    imei: "<imei cần viết>" # ví dụ: $context MES[imei]
  on_success: ~
  on_fail: ~
```
**Lưu ý**:
- Chương trình `QCM` cần được mở bằng tay trước khi chạy.
- Chương trình `ScriptRunner2.exe` cần quyền admin để chạy.

#### `qcm.auto`
- **Tóm tắt chức năng**: Thực hiện chạy tool `QCM Write Imei TOOL` để viết IMEI cho thiết bị, check log. Kết quả PASS sẽ thực hiện `on_success`, kết quả FAIL sẽ thực hiện `on_fail`.

- **Cú pháp**:
```yaml
- do: qcm.auto
  with:
    imei: "<imei cần viết>" # ví dụ: $context MES[imei]
  on_success: ~
  on_fail: ~
```
**Lưu ý**:
- Cần khi muốn thoát chế độ `AT` ở sản phẩm. Xem ví dụ bên dưới.

**ví dụ**:
```yaml
- do: qcm.auto
  with:
    imei: "$context MES[imei]"
  on_success:
    - do: com.open
      with:
        name: com
        port: "COM1"
        baudrate: 115200
      on_success: ~
      on_fail: ~
    - do: com.send_string
      with:
        name: com
        command: ~
        sleep: 2
      on_success: ~
      on_fail: ~
    - do: com.close
      with:
        name: com
      on_success: ~
      on_fail: ~
    - do: return.PASS
  on_fail: 
    - do: return.FAIL
      with:
        error_code: "QCM_WRITE_IMEI_FAIL"
```