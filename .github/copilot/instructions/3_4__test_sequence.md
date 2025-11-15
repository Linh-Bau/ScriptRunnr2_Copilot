# Hướng dẫn định nghĩa `test_sequence`.    
- Tham khảo ruler chung về định dạng YAML: [1_format-ruler.md](./1_format-ruler.md)
- Ví dụ: Tham khảo [example.yaml](../../examples/example.yaml)

---

## 1 - Giải thích các trường trong `test_sequences`:
- `test_sequences`:  
  - Định nghĩa các chuỗi test, bao gồm các bước (steps) cần thực hiện trong quá trình test.
  - Mỗi test sequence là một danh sách các test items được thực hiện tuần tự.
  - Sử dụng anchor (`&name`) để có thể tái sử dụng cho nhiều `test_targets`.

### 1.1 - `test_items`: 
  - Bao gồm Danh sách các mục kiểm tra `test_items`.
  - Mỗi test item đại diện cho một bước test cụ thể (ví dụ: kiểm tra MBS number, lấy thông tin MES, etc.).
  
### 1.2 - `test_item`:
  **Các trường:**
  - `name`: Tên của mục kiểm tra (bắt buộc). Đặt tên có ý nghĩa, viết hoa, sử dụng dấu gạch dưới `_`.
    - Ví dụ: `CHECK_MBS_NO`, `GET_MES_INFOMATION`, `CAMERA_CHECK_MODEL`.
  - `lower_limit`: Giới hạn dưới (tùy chọn). Dùng cho các test có kết quả số.
  - `upper_limit`: Giới hạn trên (tùy chọn). Dùng cho các test có kết quả số.
  - `steps`: Danh sách các bước thực hiện trong mục kiểm tra (bắt buộc).

### 1.3 - `steps`: 
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

## 2 - Hướng dẫn viết test_sequences:

### 2.1 - Cấu trúc cơ bản:
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

### 2.2 - Thứ tự thực hiện:
- Các test items được thực hiện tuần tự từ trên xuống dưới.
- Trong mỗi test item, các steps được thực hiện tuần tự.
- Nếu một step return `return.FAIL`, test item kết thúc ngay với kết quả FAIL.
- Nếu một step return `return.PASS`, test item kết thúc ngay với kết quả PASS.
- Nếu một step return `return.continue`, chương trình tiếp tục step tiếp theo.

### 2.3 - Sử dụng biến:
- Cú pháp: `{$variable_name}` để tham chiếu biến từ `script_configuration`.
- Ví dụ: `{$txt_model}`, `{$PORT_1}`, `{$txt_android_version}`.


##  2.4  - Lưu ý:
- Tên test item nên ngắn gọn, rõ ràng, viết HOA.
- `error_code` ưu tiên viết HOA_GACH_DUOI
- Mỗi test item nên tập trung vào một nhiệm vụ cụ thể.
- Sử dụng `return.continue` cho step trung gian, `return.PASS`/`return.FAIL` cho step cuối.
- Luôn xử lý `on_fail` cho các step quan trọng với `error_code` rõ ràng.

## 3 - Hệ thống xử lý string trong `test_sequences`:
- Sử dụng `with` với tham số, tham khảo: [3_4_1_string_resolver_reference](./3_4_1_string_resolver_reference.md)
- Các executer và method hỗ trợ tham khảo: [3_4_2_executers_reference.md](./3_4_2_executers_reference.md) 