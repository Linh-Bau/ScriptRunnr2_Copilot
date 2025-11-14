## hướng dẫn config test_sequences trong script.yaml

## 📄 Ví dụ tham khảo (`script.yaml`)
```yaml
test_sequences: 
  - &t650_test_sequence
    test_items:
      - name: CHECK_MBS_NO
        steps: 
          - do: if.condition
            with:
              condition: $if {$user_config mes_on} == False
            on_success:
              - do: logger.info
                with:
                  message: Chế độ debug, bỏ qua check mbsno
              - do: return.PASS

          - do: mes.check_mbs_no 
            with: ~
            on_success:
              - do: return.PASS
                with: ~
            on_fail:
                - do: return.FAIL
                  with:
                    error_code: CHECK_MBSNO_FAIL

      - name: GET_MES_INFOMATION
        steps: 
          - do: mes.get_csn 
            with: ~
            on_success:
              - do: return.continue
                with: ~
            on_fail:
                - do: return.FAIL
                  with:
                    error_code: GET_CSN_FAIL
          - do: mes.get_control_table
            with: ~
            on_success:
              - do: return.continue
                with: ~
            on_fail:
                - do: return.FAIL
                  with:
                    error_code: GET_CONTROL_TABLE_FAIL
          - do: mes.get_mac_list_ex_from_sno_code
            with: ~
            on_success:
              - do: return.PASS
                with: ~
            on_fail:
                - do: return.FAIL
                  with:
                    error_code: GET_MAC_LIST_FAIL
``` 

## Giải thích các trường trong `test_sequences`:

- `test_sequences`:  
  - Định nghĩa các chuỗi test, bao gồm các bước (steps) cần thực hiện trong quá trình test.
  - Mỗi test sequence là một danh sách các test items được thực hiện tuần tự.
  - Sử dụng anchor (`&name`) để có thể tái sử dụng cho nhiều `test_targets`.

### `test_items`: 
  - Danh sách các mục kiểm tra (test items).
  - Mỗi test item đại diện cho một bước test cụ thể (ví dụ: kiểm tra MBS number, lấy thông tin MES, etc.).
  
  **Các trường:**
  - `name`: Tên của mục kiểm tra (bắt buộc). Đặt tên có ý nghĩa, viết hoa, sử dụng dấu gạch dưới `_`.
    - Ví dụ: `CHECK_MBS_NO`, `GET_MES_INFOMATION`, `CAMERA_CHECK_MODEL`.
  - `lower_limit`: Giới hạn dưới (tùy chọn). Dùng cho các test có kết quả số.
  - `upper_limit`: Giới hạn trên (tùy chọn). Dùng cho các test có kết quả số.
  - `steps`: Danh sách các bước thực hiện trong mục kiểm tra (bắt buộc).

### `steps`: 
  - Danh sách các bước thực hiện tuần tự trong một test item.
  - Mỗi step gọi một method từ các executer có sẵn.
  
  **Các trường:**
  - `do`: Hành động cần thực hiện (bắt buộc). Format: `{executer_name}.{method_name}`
    - Ví dụ: `mes.check_mbs_no`, `logger.info`, `cam.capture_image`, `return.PASS`
    - Theo format của lập trình viên: các hàm dạng `UPPER_CASE` sẽ trả về `PASS` hoặc `FAIL` luôn mà không nhảy xuống bước tiếp theo. ví dụ: `mes.CHECK_MBS_NO` sẽ trả về `PASS` hoặc `FAIL` ngay lập tức. Trong khi đó các hàm dạng `lower_case` sẽ cần có lệnh `return.PASS` hoặc `return.FAIL` để kết thúc test item.
    - Không phải lúc nào cũng có hàm `UPPER_CASE` và `lower_case` tương ứng. Các hàm hỗ trợ tham khảo `executers_reference.md`. 
  - `with`: Tham số cho hành động (bắt buộc, có thể là `~` nếu không có tham số).
    - Dạng dictionary với key-value.
    - Ví dụ: `message: "Test completed"`, `image_path: "./setup/image/sample.png"`
  - `on_success`: Các bước tiếp theo nếu hành động thành công (tùy chọn).
    - Là một danh sách các steps con.
    - Nếu không có, chương trình sẽ tiếp tục bước tiếp theo.
  - `on_fail`: Các bước tiếp theo nếu hành động thất bại (tùy chọn).
    - Là một danh sách các steps con.
    - Thường dùng để log lỗi và return fail với error_code.

## 📚 Tham khảo thêm:
- **[Danh sách Executers và Methods](./executers_reference.md)** - Tất cả các executer và method có sẵn với hướng dẫn sử dụng chi tiết

## 📝 Tóm tắt Executers có sẵn:

| Executer | Mục đích           | Method chính                                                   |
| -------- | ------------------ | -------------------------------------------------------------- |
| `logger` | Ghi log            | `info`                                                         |
| `return` | Điều khiển luồng   | `continue`, `pass`, `fail`                                     |
| `if`     | Kiểm tra điều kiện | `condition`                                                    |
| `mes`    | Tương tác MES API  | `check_mbs_no`, `get_csn`, `get_control_table`, `get_mac_list` |
| `cam`    | Camera/OCR         | `set_operation_image`, `capture_image`, `detect_ocr`           |
| `func`   | Hàm tiện ích       | `date_time_check`                                              |

Chi tiết đầy đủ về tham số và cách sử dụng xem tại [executers_reference.md](./executers_reference.md).

## Hướng dẫn viết test_sequences:

### 1. Cấu trúc cơ bản:
```yaml
test_sequences:
  - &sequence_name
    test_items:
      - name: TEST_ITEM_1
        steps:
          - do: executer.method
            with:
              param1: value1
            on_success:
              - do: return.PASS
            on_fail:
              - do: return.FAIL
                with:
                  error_code: ERROR_CODE_1
```

### 2. Thứ tự thực hiện:
- Các test items được thực hiện tuần tự từ trên xuống dưới.
- Trong mỗi test item, các steps được thực hiện tuần tự.
- Nếu một step return `return.FAIL`, test item kết thúc ngay với kết quả FAIL.
- Nếu một step return `return.PASS`, test item kết thúc ngay với kết quả PASS.
- Nếu một step return `return.continue`, chương trình tiếp tục step tiếp theo.

### 3. Sử dụng biến:
- Cú pháp: `{$variable_name}` để tham chiếu biến từ `script_configuration`.
- Ví dụ: `{$txt_model}`, `{$PORT_1}`, `{$txt_android_version}`.
- Chi tiết về biến xem tại [test_configurations.md](./test_configurations.md).


## Lưu ý:
- Tên test item nên ngắn gọn, rõ ràng, viết HOA.
- Mỗi test item nên tập trung vào một nhiệm vụ cụ thể.
- Sử dụng `return.continue` cho step trung gian, `return.PASS`/`return.FAIL` cho step cuối.
- Luôn xử lý `on_fail` cho các step quan trọng với `error_code` rõ ràng.
