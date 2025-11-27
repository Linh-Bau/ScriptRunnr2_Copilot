

# GITHUB COPILOT INSTRUCTIONS

---

## 1. Mô tả
- Hướng dẫn này cung cấp các chỉ dẫn cụ thể để GitHub Copilot hỗ trợ trong việc tạo, sửa và quản lý các trạm test trong môi trường nhà máy sản xuất.  
- Copilot sẽ dựa trên các tài liệu này để đưa ra câu trả lời chính xác và theo format chuẩn của hệ thống <b>ScriptRunner</b>.

---

## 2. Quy tắc chung
1. Trả lời tất cả câu hỏi bằng <b>TIẾNG VIỆT</b>.  
   - Phần `thinking` cũng phải viết bằng <b>TIẾNG VIỆT</b> để người dùng có thể theo dõi logic.  
2. Luôn dựa trên hướng dẫn ở phần <b>3. Hướng dẫn trả lời</b> và các file tài liệu liên quan trong:
   - `./instructions/yaml-script-instructions.md`  
   - `./instructions/documents/`  
   - `./instructions/documents/steps/`

---

## 3. Hướng dẫn trả lời

### 3.1 Cấu trúc input format
Người dùng phải sử dụng format câu lệnh như sau:

```text
<câu lệnh> - <nội dung>
```
<câu lệnh>: Loại hành động Copilot phải thực hiện.
<nội dung>: Nội dung yêu cầu hoặc mô tả vấn đề

Copilot sẽ phân tích <câu lệnh> và <nội dung> để xác định tài liệu tham khảo và cách trả lời.

### 3.2 Các loại câu lệnh
#### 3.2.1 Giải thích script
```text
đọc script - <nội dung>
```

<b>INPUT:</b> Một đoạn script YAML hoặc file script

<b>OUTPUT:</b> Giải thích chi tiết từng phần script, logic và cách hoạt động

<b>Logic trả lời:</b>

- Tham khảo [tài liệu](./instructions/yaml-script-instructions.md)

- Khi cần chi tiết step, tham khảo các file trong ./documents/steps/

- Luôn trả lời bằng tiếng Việt, có phần thinking giải thích cách phân tích.

#### 3.2.2 Viết / sửa script
```text  
sửa script - <nội dung>
```

<b>INPUT:</b> Yêu cầu tạo mới hoặc sửa script YAML

<b>OUTPUT:</b> Script YAML hợp lệ, đúng format và logic

<b>Logic trả lời:</b>
- Dựa trên hướng dẫn trong yaml-script-instructions.md và yaml-format-summary.md

- Khi cần chi tiết step, tham khảo ./documents/steps/

- Luôn giữ tiếng Việt cho phần giải thích các thay đổi

#### 3.2.3 Xử lý lỗi
```text
xử lý lỗi - <nội dung>
```

<b>INPUT:</b> Lỗi phát sinh khi chạy script hoặc chương trình

<b>OUTPUT:</b> Hướng dẫn chi tiết để fix lỗi

<b>Logic trả lời:</b>

- Tham khảo các hướng dẫn trong [tài liệu](./instructions/yaml-script-instructions.md)

- Khi lỗi liên quan đến step cụ thể, tham khảo tài liệu tương ứng trong [thư mục](./instructions/documents/steps/)

- Trả lời tiếng Việt, giải thích rõ nguyên nhân và cách fix

### 3.3 Hướng dẫn bổ sung
- Luôn giữ format YAML chuẩn khi tạo script

- Nếu câu lệnh không đúng format, Copilot phải hướng dẫn người dùng theo format chuẩn:

```text
<lệnh> - <nội dung câu hỏi>
```
<b>Ví dụ minh họa:</b>
<i>
- script - Giải thích đoạn YAML chạy kiểm tra MES
- viết script - Tạo step logger ghi thông tin test
- xử lý lỗi - Fix lỗi step IF không thực thi
</i>


<b>Luôn tuân theo logic “tầng trên tóm tắt → tầng dưới chi tiết”:</b>

- <b>Tầng 1:</b> overview / dispatcher (file này)

- <b>Tầng 2:</b> module summary [yaml-script-summary](./instructions/yaml-script-instructions.md)

- <b>Tầng 3:</b> step-level instruction (./documents/steps/*.md)