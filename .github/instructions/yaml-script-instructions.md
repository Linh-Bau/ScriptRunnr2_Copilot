# YAML SCRIPT INSTRUCTIONS
---
## Tóm tắt
- Câu lệnh gọi đến là: <b>gscript</b> - <i>nội dung.</i>
- Tài liệu này là để thực hiện giải thích về cách thức hoạt động của 1 đoạn script `yaml` hoặc 1 file `script.yaml`

## Quy tắc
- Luôn trả lời bằng <b>tiếng Việt</b>.
- Dựa theo <b>Hướng dẫn</b> tôi cung cấp cụ thể bên dưới để trả lời.

## Hướng dẫn
1. Phân tích yêu cầu người dùng đưa ra trong phần <i>nội dung</i>. Xem đang hỏi về:
    - 1 đoạn `yaml` cụ thể.
    - 1 file `script.yaml`.
    - nếu như đều không phải, trả lời: `Mục đích câu lệnh này là hỏi về script yaml hoặc các đoạn script yaml. Nhưng nội dung yêu cầu có vẻ là không phù hợp, xin đưa ra câu hỏi rõ ràng hơn`.
2. Đọc file [YAML FORMAT SUMMARY](./documents/yaml-format-summary.md) để hiểu về định dạng file `script.yaml`. Chú ý phần <b>FORMAT</b>
3. Phân tích xem yêu cầu <i>nội dung</i> của người dùng là về:
    - Giải thích script
      - Giải thích theo format sau
        Thông tin về trạm test:
        - tên chương trình, description
        - Có các script tương ứng với từng model như sau:
            - test_target: for model id
                - tên trạm hiện thị, thông tin description
                - các môi trường đi kèm
                - các bước thực hiện:
                - bước 1: tìm thông tin mô tả bước thực hiện ở [YAML STEPS SUMMARRY](../instructions/documents/yaml-steps-summary.md)
                    - Nếu không tìm thấy thông tin mô tả bước đó, hoặc chỉ thấy thông tin gần tương tự, hãy sử dụng thông tin gần tương tự đó để mô tả bước thực hiện. Và ghi chú rõ ràng rằng có thể script đang bị viết sai, hoặc thông tin document chưa được cập nhật.
                - bước 2: Tương tự các bước trên.
            Lưu ý: Giải thích tóm tắt lại chức năng, tham khảo [YAML STEPS SUMMARRY](../instructions/documents/yaml-steps-summary.md) ở từng item có <b>Tóm tắt chức năng</b>.
        - Liệt kê các mã defect_code tương ứng với từng lỗi (nếu có).
        - Tóm tắt lại toàn bộ một cách ngắn gọn.
        
    - Chỉnh sửa, thêm mới item test, step test vào script.
      - Xử lý theo format sau:
        - Phân tích yêu cầu người dùng,  tham khảo [YAML STEPS SUMMARRY](../instructions/documents/yaml-steps-summary.md) ở từng item có <b>Tóm tắt chức năng</b>. Xem có executer và method nào phù hợp với yêu cầu không. Nếu không có thì trả lời `Hiện tại không có chức năng này`.
        - Tóm tắt lại các executer đó, phản hồi lại nội dung người dùng muốn làm và hỏi xem người dùng có phải là ý này không.
        - Sau xác nhận từ người dùng, tiến hành sửa đổi, cung cấp đoạn script yaml hoàn chỉnh với các thay đổi đã thực hiện.
        - Tóm tắt lại các thay đổi đã thực hiện.
    - Sửa lỗi script.
      - Yêu cầu người dùng cung cấp lỗi cụ thể, log nếu có thể.
        - Kiểm tra xem phải lỗi format không với các quy tắc trong [YAML FORMAT SUMMARY](./documents/yaml-format-summary.md).
        - Kiểm tra xem executer có được viết chính xác không bằng cách tham khảo [YAML STEPS SUMMARRY](../instructions/documents/yaml-steps-summary.md).
        - Tham khảo các lỗi của từng step trong [YAML STEPS SUMMARRY](../instructions/documents/yaml-steps-summary.md) mục <b>Lỗi thường gặp</b>.
        - Nếu không tìm ra lỗi, yêu cầu người dùng hỏi trực tiếp SW ENG.
    - Nếu như không phải các yêu cầu trên, trả lời: `Mục đích câu lệnh này là hỏi về script yaml hoặc các đoạn script yaml. Nhưng nội dung yêu cầu có vẻ là không phù hợp, xin đưa ra câu hỏi rõ ràng hơn`.
