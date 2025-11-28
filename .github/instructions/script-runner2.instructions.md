---
name: scriptrunner2-instructions
description:  'Giúp đỡ người dùng về cách sử dụng ScriptRunner2, cũng như xử lý xử cố với với chương trình.
---

# Giới thiệu
# # `# Tài liệu này là gì? Dùng để làm gì? Vì sao tồn tại?`
- Tài liệu này là tài liệu cung cấp thông tin GIỚI THIỆU về chương trình `ScriptRunner2`, cách mở chương trình, các chế độ mở chương trình, cung cấp các lỗi và phương pháp xử lý.

# 1. Tổng quan 
- `ScriptRunner2` là một chương trình giúp người dùng tự động hóa các tác vụ thông qua việc sử dụng các file `script.yaml`. Chương trình cho phép người dùng tạo, chỉnh sửa, và chạy các script để thực hiện các công việc lặp đi lặp lại một cách hiệu quả.

- `ScriptRunner2` sẽ dựa vào các thông tin `PRODUCT`, `STATION`, `MODEL_ID` để xác định và tải file `script.yaml` tương ứng.

- `ScriptRunner2` hỗ trợ nhiều chế độ mở chương trình khác nhau, bao gồm mở trực tiếp hoặc thông qua hệ thồng `MES`. Hoặc qua giao diện dòng lệnh (Command Line Interface - CLI).

# 2. Tài liệu này dành cho
## 2.1 Các chế độ mở chương trình
Chương trình hỗ trợ mở ở các chế độ khác nhau. 
<b> CHẾ ĐỘ TỰ ĐỘNG:</b>
- Sử dụng khi: Chế độ thường dùng, mở thông qua hệ thống `MES`.
- Cách hoạt động:  chương trình sẽ tự động lấy các tham số cần thiết từ hệ thống `MES` như `PRODUCT`, `STATION`, `MODEL_ID`, từ đó chọn và tải, mở script tương ứng.
- Lưu ý: không có.

<b> CHẾ ĐỘ THỦ CÔNG:</b>
- Sử dụng khi: 
  - Các trạm, script không có trong hệ thống `MES`.
  - Khi hệ thống `MES` chưa thêm được trạm này.

- Cách hoạt động: 
  - lệnh CMD: `ScriptRunner2.exe manual "<PRODUCT>" "<STATION>" "<MODEL_ID>"`. Thay thế `<PRODUCT>`, `<STATION>`, `<MODEL_ID>` bằng các giá trị tương ứng.
- Lưu ý:
  - Tên nếu có dấu cách thì sử dụng `""` để bao quanh tham số.
  - Có phân biệt chữ hoa, chữ thường.
  - Xin kiểm tra rõ thư mục: `/SFTP.conf.pe.02/V6/<PRODUCT>/<STATION>` và file `/SFTP.conf.pe.02/V6/<PRODUCT>/<STATION>/script.yaml` có tồn tại hay không.

<b> CHẾ ĐỘ THỦ CÔNG OFFLINE:</b>
- Sử dụng khi:
  - Khi không có kết nối mạng đến server SFTP.
  - Khi cần đóng gói chương trình và gửi đến người khác mà không có kết nối SFTP.
- Cách hoạt động:
  - lệnh CMD: `ScriptRunner2.exe manual "<PRODUCT>" "<STATION>" "<MODEL_ID>" --disable_sftp`. 

- Lưu ý:
  - Tên nếu có dấu cách thì sử dụng `""` để bao quanh tham số.
  - Có phân biệt chữ hoa, chữ thường.
  - Sử dụng file local trong thư mục `.\setup\<PRODUCT>\<STATION>\<MODEL_ID>\script.yaml`. Kiểm tra môi trường có tồn tại hay không.
- Cách thường sử dụng:
  - Mở chương trình bằng `CHẾ ĐỘ THỦ CÔNG` trước, để chương trình tự động tải xuống các files cần thiết.
  - Tắt chương trình, tạo file open_<PRODUC>_<STATION>_<MODEL_ID>.bat trong thư mục gốc của chương trình với nội dung:
    ```bash
    @echo off
    ScriptRunner2.exe manual <PRODUCT> "<STATION>" <MODEL_ID> --disable-sftp
    pause
    ```
  - Đóng gói chương trình, thư mục `setup` và file `open_<PRODUC>_<STATION>_<MODEL_ID>.bat` gửi đến người dùng khác.

## 2.2 Xử lý lỗi.
