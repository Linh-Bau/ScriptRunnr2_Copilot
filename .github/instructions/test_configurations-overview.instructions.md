---
applyTo: "**/*.yaml"
name: test-configurations-rule
description: Giới thiệu các quy tắc chung của test_configurations trong script.yaml, đưa ra hướng dẫn về cách tra cứu tài liệu.
---

# Giới thiệu
- Tài liệu này cung cấp:
  - Cách định nghĩa và sử dụng `test_configurations` trong `script.yaml`.

# Tổng quan
- `test_configurations` là một danh sách các cấu hình kiểm tra cho các `test_sequences` khác nhau. Mỗi cấu hình xác định các tham số và điều kiện cụ thể để thực hiện các bước kiểm tra.


# Định nghĩa test_configurations
**format chuẩn:**
- Tên key phải là dạng lower_case với dấu gạch dưới (_) để phân tách từ.
- Sử dụng 2 khoảng trắng cho mỗi cấp độ thụt lề. KHÔNG sử dụng tab.
```yaml
test_configurations: 
    - &t650c_configuration
      flags: ~
      script_information: 
        station_name: STATION NAME
        description: DESCRIPTION
      script_configuration:
        general: ~
        fixture_configuration: ~
```

`test_configurations` bao gồm một danh sách các cấu hình kiểm tra.
Mỗi cấu hình kiểm tra bao gồm

### `flags`
- Các cờ tuỳ chọn. Có thể null (`~`).
Các cờ hỗ trợ:

| Tên cờ | Mô tả |
|---------|-------|
| `enable_camera_window` | Mở cửa sổ dành cho camera. Dùng cho trạm CAMERA VISION |
| `skip_upload_defect_code` | Bỏ qua bước upload mã lỗi lên MES nếu test FAIL. |
| `fixture_control` | Gửi mã fixture_code lên MES. |

  
### `script_information`: 
Thông tin mô tả về cấu hình, KHÔNG được null, bao gồm:
  - `station_name`: Tên của trạm kiểm tra.
  - `description`: Mô tả ngắn gọn về cấu hình.
### `script_configuration`: 
Các thiết lập cụ thể cho cấu hình, bao gồm:
  - `general`: Cấu hình chung (có thể null). Dạng key-value (string-string).
  - `fixture_configuration`: Cấu hình thiết bị kiểm tra, dạng danh sách object. Mỗi object là một danh sách key-value (string-string).

**ví dụ:**
```yaml
script:
  test_configurations: 
    - &t650c_configuration
      flags: ~
      script_information: 
        station_name: T650C_TEST_STATION
        description: Configuration for T650C testing
      script_configuration:
        general: 
          working_dir: "C:\Test\T650C"
        fixture_configuration:
          - fixture_1:
              port: "COM3"
              baud_rate: "9600"
          - fixture_2:
              port: "COM4"
              baud_rate: "115200"
```