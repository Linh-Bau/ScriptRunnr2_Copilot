---
applyTo: "**/*.yaml"
name: mes-defect-code-rule
description: Giới thiệu các quy tắc chung của mes_defect_code trong script.yaml, đưa ra hướng dẫn về cách tra cứu tài liệu.
---
# Giới thiệu
- Tài liệu này cung cấp:
  - Cách định nghĩa và sử dụng mã lỗi MES trong `mes_defect_code`.

# Tổng quan
- `mes_defect_code` là một danh sách key-value dạng (string-string) được sử dụng để gửi mã lỗi lên hệ thống `MES`.

# Định nghĩa mes_defect_code
ví dụ:
```yaml
script:
  mes_defect_code:
    DOWNLOAD_K81_FIRMWARE_FAIL : UN01
    DOWNLOAD_K81_APP_FAIL : UN02
```

- Mỗi mục trong `mes_defect_code` bao gồm:
  - Key: Tên lỗi (string) - mô tả lỗi. Dựa theo chương trình.
  - Value: Mã defect code (string) - mã do `MES` cung cấp.