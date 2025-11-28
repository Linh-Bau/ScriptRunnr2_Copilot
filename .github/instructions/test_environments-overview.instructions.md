---
applyTo: "**/*.yaml"
name: test-environments-rule
description: Giới thiệu các quy tắc chung của test_environments trong script.yaml, đưa ra hướng dẫn về cách tra cứu tài liệu.
---

# Giới thiệu
- Tài liệu này cung cấp:
  - Cách định nghĩa và sử dụng `test_environments` trong `script.yaml`.
  - Các môi trường đã được cung cấp sẵn.

# Tổng quan
- `test_environments` là một danh sách các môi trường test được cấu hình trên SFTP. Các môi trường test này được tải xuống tự động bới chương trình.

# Định nghĩa test_environments
ví dụ:
```yaml
script:
  test_environments:
    - &env_VN6301ABAR0B
      downloads:
        - name: "vtp" 
          type: "folder" 
          from: "/SFTP.conf.pe.02/V6ENV/vtp_ver24011801" 
          to: "./setup/vtp"
          extract_to: ~

        - name: "vtp_env" 
          type: "folder" 
          from: "./setup/VN6301ABAR0B" 
          to: "./setup/vtp" #
          extract_to: ~

     - &env_VN6301ABAR0C
      downloads:
        - name: "vtp" 
          type: "folder" 
          from: "/SFTP.conf.pe.02/V6ENV/vtp_ver24011801" 
          to: "./setup/vtp"
          extract_to: ~

        - name: "vtp_env" 
          type: "folder" 
          from: "./setup/VN6301ABAR0C" 
          to: "./setup/vtp" #
          extract_to: ~
```

Hướng dẫn:
- `test_environments` bao gồm một danh sách các môi trường test.
- Mỗi môi trường test bao gồm:
  - `downloads`: Danh sách các mục cần tải xuống, mỗi mục có các trường:
    - `name`: Tên hiển thị của mục tải về. (Có thể đặt tuỳ ý, không ảnh hưởng đến quá trình tải).
    - `type`: Loại tải về. Sử dụng một trong 3 loại sau: `folder`, `file`, `compressed`.
    - `from`: Đường dẫn trên server SFTP hoặc đường dẫn tương đối.
      - đường dẫn tuyệt đối: ví dụ `/SFTP.conf.pe.02/V6ENV/vtp_ver24011801`
      - đường dẫn tương đối: ví dụ `./setup/VN6301ABAR0B`, sẽ được hiểu là: `/SFTP.conf.pe.02/V6/<PRODUCT>/<STATION>/setup/VN6301ABAR0B`
    - `to`: Đường dẫn lưu trữ trên máy local (tuyệt đối hoặc tương đối).
      - đường dẫn tuyệt đối: ví dụ `C:\OS\VN6301ABAR0B`
      - đường dẫn tương đối: ví dụ `./setup/vtp`, sẽ được lưu vào: `{thư mục exe}/setup/{PRODUCT}/{STATION}/{MODEL_ID}/setup/vtp`

    - `extract_to`: Dành cho file nén (compressed), chỉ định nơi giải nén.
      - Để là null `(~)`: giải nén vào `./setup/{tên file}`.
      - ĐƯờng dẫn tuyệt đối: ví dụ `C:\OS\extracted_folder`
      - Đường dẫn tương đối: ví dụ `./setup/extracted_folder`, sẽ được giải nén vào: `{thư mục exe}/setup/{PRODUCT}/{STATION}/{MODEL_ID}/setup/extracted_folder`

## Các môi trường setup và hướng dẫn:
- Danh sách các môi trường test đã được cung cấp sẵn:

| Tên môi trường | Đường dẫn | Mô tả |
|----------------|-------|--------------------|
| adb | `/SFTP.conf.pe.02/V6ENV/adb` | Công cụ adb cho việc giao tiếp với thiết bị Android. |
| AtmelSecureAccess tool | `/SFTP.conf.pe.02/V6ENV/AtmelSecureAccess` | Thư viện và công cụ Atmel Secure Access. Dành cho trạm UX100, UX110 OS DL |
| QFIL | `/SFTP.conf.pe.02/V6ENV/qfil_env` | Công cụ QFIL để flash os image cho thiết bị Android. |
| OCR | `/SFTP.conf.pe.02/V6ENV/tessdata` | Thư viện ngôn ngữ cho OCR Tesserect. Dành cho các trạm dùng CAMERA VISION | 
| vtp | `/SFTP.conf.pe.02/V6ENV/vtp_ver24011801` | Chứa tool VTP.exe của khách hàng. Yêu cầu: Cần phải setup thêm môi trường riêng cho từng model (xem ví dụ ở dưới). |

---

#### ví dụ setup môi trường VTP cho các model khác nhau:
Lưu ý:
- Yêu cầu kỹ sư EE phải setup môi trường riêng cho từng model. Liên hệ SW ENG nếu cần hỗ trợ.

```yaml
- &env_VN6301ABAR0B
      downloads:
        - name: "vtp" 
          type: "folder" 
          from: "/SFTP.conf.pe.02/V6ENV/vtp_ver24011801" 
          to: "./setup/vtp"
          extract_to: ~

        - name: "vtp_env" 
          type: "folder" 
          from: "./setup/VN6301ABAR0B" 
          to: "./setup/vtp" #
          extract_to: ~
```