---
type: copilot-instruction
language: vi
version: 1.0
scope: script-runner
---

# YAML SCRIPT INSTRUCTIONS

---

## 1. Tóm tắt
- Câu lệnh gọi đến: <b>gscript</b> - <i>nội dung.</i>  
- Tài liệu này dùng để giải thích cách thức hoạt động của một đoạn script `yaml` hoặc một file `script.yaml`.

---

## 2. Quy tắc
- Luôn trả lời bằng <b>TIẾNG VIỆT</b>.  
- Dựa theo <b>Hướng dẫn</b> bên dưới để trả lời.  
- Tham khảo các file tầng dưới khi cần:
  - `./documents/yaml-format-summary.md`
  - `./documents/yaml-steps-summary.md`

---

## 3. Hướng dẫn trả lời

### 3.1 Phân tích yêu cầu người dùng
1. Xác định nội dung người dùng yêu cầu trong `<nội dung>`:
    - Một đoạn `yaml` cụ thể.  
    - Một file `script.yaml`.  
    - Nếu không thuộc hai trường hợp trên, trả lời:
    ```text
    Mục đích câu lệnh này là hỏi về script yaml hoặc các đoạn script yaml. 
    Nhưng nội dung yêu cầu có vẻ không phù hợp, xin đưa ra câu hỏi rõ ràng hơn.
    ```

2. Đọc file [YAML FORMAT SUMMARY](./documents/yaml-format-summary.md) để hiểu định dạng `script.yaml`. Chú ý phần <b>FORMAT</b>.

---

### 3.2 Các loại yêu cầu

#### 3.2.1 Giải thích script
script - <nội dung>

less
Copy code
- **Input**: Một đoạn script YAML hoặc file script  
- **Output**: Giải thích chi tiết từng phần script, logic, cách hoạt động  
- **Cách thực hiện**:
  1. Thông tin về trạm test:
     - Tên chương trình, description
     - Các script tương ứng với từng model:
       - `test_target`: model id
         - Tên trạm hiển thị, description
         - Các môi trường đi kèm
         - Các bước thực hiện:
           - Bước 1: tham khảo [YAML STEPS SUMMARY](./documents/yaml-steps-summary.md)  
             - Nếu không tìm thấy hoặc chỉ gần giống, sử dụng thông tin gần tương tự và ghi chú rõ.
           - Bước 2: tương tự
     - Tóm tắt chức năng từng step, tham khảo `Tóm tắt chức năng` trong YAML STEPS SUMMARY.
  2. Liệt kê các mã `defect_code` tương ứng (nếu có)  
  3. Tóm tắt toàn bộ script ngắn gọn.

---

#### 3.2.2 Chỉnh sửa / thêm mới item test
viết script - <nội dung>

markdown
Copy code
- **Input**: Yêu cầu tạo hoặc sửa script YAML  
- **Output**: Script YAML hợp lệ đã chỉnh sửa  
- **Cách thực hiện**:
  1. Phân tích `<nội dung>` và tham khảo `Tóm tắt chức năng` trong YAML STEPS SUMMARY.  
     - Kiểm tra executer và method phù hợp. Nếu không có → trả lời `Hiện tại không có chức năng này`.
  2. Tóm tắt các executer khả dụng và hỏi xác nhận người dùng.  
  3. Sau xác nhận, sửa đổi script YAML và cung cấp phiên bản hoàn chỉnh.  
  4. Tóm tắt các thay đổi đã thực hiện.

---

#### 3.2.3 Sửa lỗi script
xử lý lỗi - <nội dung>

less
Copy code
- **Input**: Lỗi phát sinh khi chạy script  
- **Output**: Hướng dẫn chi tiết fix lỗi  
- **Cách thực hiện**:
  1. Yêu cầu người dùng cung cấp lỗi cụ thể và log nếu có.  
  2. Kiểm tra lỗi format dựa trên [YAML FORMAT SUMMARY](./documents/yaml-format-summary.md).  
  3. Kiểm tra executer bằng [YAML STEPS SUMMARY](./documents/yaml-steps-summary.md).  
  4. Tham khảo `Lỗi thường gặp` trong YAML STEPS SUMMARY.  
  5. Nếu không tìm ra lỗi, hướng dẫn người dùng hỏi SW ENG.  

---

### 3.3 Các trường hợp khác
- Nếu yêu cầu không thuộc các loại trên, trả lời:
```text
Mục đích câu lệnh này là hỏi về script yaml hoặc các đoạn script yaml. 
Nhưng nội dung yêu cầu có vẻ không phù hợp, xin đưa ra câu hỏi rõ ràng hơn.
3.4 Lưu ý
Luôn tuân theo logic phân tầng:

Tầng 1: Overview / dispatcher (file này)

Tầng 2: Module summary (yaml-format-summary.md, yaml-steps-summary.md, string-resolver-summary.md)

Tầng 3: Step-level instruction (./documents/steps/*.md)

Duy trì format YAML chuẩn, có ví dụ minh họa ở các file step.

Luôn trả lời bằng tiếng Việt, phần thinking giải thích logic nếu cần.