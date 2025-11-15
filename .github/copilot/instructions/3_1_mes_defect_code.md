# Hướng dẫn định nghĩa `mes_defect_code`.

## 1 - Mục đích
Định nghĩa format chuẩn cho trường `mes_defect_code` trong file `script.yaml` được sử dụng trong chương trình **ScriptRunner2**.

## 2 - Định nghĩa chung
Ví dụ: tham khảo [example.yaml](../../examples/example.yaml)

Trường `mes_defect_code` dùng để định nghĩa các mã lỗi từ hệ thống MES (Manufacturing Execution System). Mỗi mã lỗi được định nghĩa dưới dạng cặp khóa-giá trị.

- Khóa (key): Tên mã lỗi (defect code name), thường là một chuỗi mô tả ngắn gọn về lỗi.
  
- Giá trị (value): Mã lỗi thực tế (defect code), là một chuỗi gồm 4 ký tự viết hoa. Tham khảo kỹ sư MES để lấy mã lỗi hoặc tạo mã lỗi mới nếu cần.
  
- Nếu như không có mã lỗi, chương trình sẽ không gửi  kết quả `FAIL` lên hệ thống MES. Kết quả `PASS` sẽ luôn được gửi lên MES bất kể trường `mes_defect_code` có được định nghĩa hay không.