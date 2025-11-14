## Danh sách Executers và Methods

## Giới thiệu
Executers là các thành phần xử lý các hành động trong steps. Mỗi executer cung cấp một tập hợp các method để thực hiện các tác vụ cụ thể.

## Cách sử dụng
```yaml
- do: {executer_name}.{method_name}
  with:
    param1: value1
    param2: value2
  on_success: 
    - do: return.continue
      with: ~
  on_fail:
    - do: return.FAIL
      with:
        error_code: SOME_ERROR_CODE
```

## Giải thích
- **`do`**: Chỉ định executer và method cần gọi, theo định dạng `{executer_name}.{method_name}`.    
  - Ví dụ: `mes.check_mbs_no`, `logger.info`, `cam.set_operation_image`, `return.PASS`    
  - Các method viết **HOA** (`UPPER_CASE`) như `return.PASS`, `return.FAIL`, `mes.CHECK_MBS_NO` thường throw exception để kết thúc test item ngay lập tức, không cần `on_success`/`on_fail`.
  - Các method viết **thường** (`lower_case`) trả về `true`/`false`, cần xử lý với `on_success`/`on_fail`.
- **`with`**: Tham số cho method, dưới dạng key-value. Nếu không có tham số, sử dụng `~`.   
- **`on_success`**: Danh sách các bước thực hiện nếu method thành công (trả về `true`).
- **`on_fail`**: Danh sách các bước thực hiện nếu method thất bại (trả về `false`).

⚠️ **Lưu ý quan trọng:**
- Chỉ sử dụng các executer và method có sẵn được liệt kê dưới đây.
- Nếu người dùng yêu cầu hành động chưa được định nghĩa, hãy trả lời: **"Hành động này chưa được hỗ trợ. Vui lòng liên hệ SW ENG để bổ sung."**
---
## xử lý string
Xử lý giá trị biến with ở runtime khi biến with: tham khảo [StringResolver](string_resolver_reference.md)  
---
## 📋 Danh sách Executers

### 1. `logger` - Ghi log

#### `logger.info`
Ghi thông tin log vào file và hiển thị trên giao diện.

**Tham số:**
- `message` (string, bắt buộc): Nội dung cần ghi log.

**Trả về:** `true` (luôn thành công)

**Ví dụ:**
```yaml
- do: logger.info
  with:
    message: "Bắt đầu kiểm tra camera..."
```

```yaml
- do: logger.info
  with:
    message: "Model: {$txt_model}, Build: {$txt_android_build}"
```

**Lưu ý:**
- Hỗ trợ biến: `{$variable_name}`
- Log tự động có timestamp

---

### 2. `return` - Điều khiển luồng

#### `return.continue`
Tiếp tục thực hiện step tiếp theo.

**Tham số:** Không có (`~`)

**Trả về:** `true` (luôn thành công)

**Ví dụ:**
```yaml
- do: mes.get_csn
  with: ~
  on_success:
    - do: return.continue
      with: ~
```

---

#### `return.PASS`
Kết thúc test item với kết quả **PASS**.

**Tham số:** Không có (`~`)

**Trả về:** Throw `ReturnHandle(true)` - Kết thúc test item ngay lập tức

**Ví dụ:**
```yaml
- do: return.PASS
  with: ~
```

**Lưu ý:** 
- Method name phải viết **HOA**: `return.PASS` (không phải `return.pass`)
- Dùng cho bước cuối cùng hoặc khi muốn kết thúc test item sớm với kết quả thành công
- Không cần `on_success`/`on_fail` vì method này throw exception để kết thúc

---

#### `return.FAIL`
Kết thúc test item với kết quả **FAIL**.

**Tham số:**
- `error_code` (string, tùy chọn): Mã lỗi xác định nguyên nhân.

**Trả về:** Throw `ReturnHandle(false, error_code)` - Kết thúc test item ngay lập tức

**Ví dụ:**
```yaml
- do: return.FAIL
  with:
    error_code: CHECK_MBSNO_FAIL
```

```yaml
# Nếu không có error_code, sẽ lấy từ context["error_code"]
- do: return.FAIL
  with: ~
```

**Lưu ý:** 
- Method name phải viết **HOA**: `return.FAIL` (không phải `return.fail`)
- `error_code` nên viết HOA, dùng `_`
- Nếu không có `error_code` trong `with`, sẽ tìm trong `context["error_code"]`
- Nếu cả hai đều không có, error_code = `null`
- Không cần `on_success`/`on_fail` vì method này throw exception để kết thúc

---

### 3. `if` - Kiểm tra điều kiện

#### `if.condition`
Kiểm tra biểu thức điều kiện boolean.

**Tham số:**
- `condition` (string, bắt buộc): Biểu thức điều kiện, kết quả phải là `"True"` hoặc `"False"`.

**Trả về:** 
- `true` nếu condition = `"True"`
- `false` nếu condition = `"False"`

**Ví dụ:**
```yaml
- do: if.condition
  with:
    condition: $if {$user_config mes_on} == False
  on_success:
    - do: logger.info
      with:
        message: "Chế độ debug, bỏ qua MES"
    - do: return.PASS
```

**Cú pháp điều kiện:**
- `$if {$variable} == value`: So sánh bằng
- `$if {$variable} != value`: So sánh khác

---

### 4. `mes` - Tương tác MES API

#### `mes.CHECK_MBS_NO` (Khuyến nghị sử dụng)
Kiểm tra MBS number và tự động return PASS/FAIL.

**Tham số:** Không có (`~`)

**Hành vi:**
- Nếu debug mode (`mes_on = false`): Tự động PASS
- Nếu MBS hợp lệ: Tự động PASS
- Nếu MBS không hợp lệ: Tự động FAIL với error_code = "CHECK_MBS_NO"

**Ví dụ:**
```yaml
- name: CHECK_MBS_NO
  steps:
    - do: mes.CHECK_MBS_NO
      with: ~
```

**Lưu ý:** 
- Method HOA, không cần `on_success`/`on_fail`
- **Ưu tiên sử dụng method này** thay vì `mes.check_mbs_no` vì code ngắn gọn hơn và tự động xử lý debug mode

---

#### `mes.check_mbs_no`
Kiểm tra MBS number có hợp lệ và ở trạm hiện tại không.

**Tham số:** Không có (`~`)

**Dữ liệu đầu vào:** Lấy MBS number từ `context["input_str"]`

**Trả về:** 
- `true`: MBS hợp lệ
- `false`: MBS không hợp lệ

**Ví dụ:**
```yaml
- do: mes.check_mbs_no
  with: ~
  on_success:
    - do: return.PASS
      with: ~
  on_fail:
    - do: return.FAIL
      with:
        error_code: CHECK_MBSNO_FAIL
```

---

#### `mes.get_csn`
Lấy CSN (Customer Serial Number) từ MBS number.

**Tham số:** Không có (`~`)

**Dữ liệu đầu vào:** Lấy MBS từ `context["input_str"]`

**Kết quả:** CSN được lưu vào `context["MES[csn]"]`

**Trả về:** 
- `true`: Lấy thành công
- `false`: Lấy thất bại

**Ví dụ:**
```yaml
- do: mes.get_csn
  with: ~
  on_success:
    - do: logger.info
      with:
        message: "CSN: {$context MES[csn]}"
    - do: return.continue
  on_fail:
    - do: return.FAIL
      with:
        error_code: GET_CSN_FAIL
```

---

#### `mes.get_control_table`
Lấy control table (bảng kiểm soát phiên bản) từ MES.

**Tham số:** Không có (`~`)

**Dữ liệu đầu vào:** Lấy MBS từ `context["input_str"]`

**Kết quả:** Các item và value từ ECO Version được lưu vào context với format `MES[item_name]`

**Trả về:** 
- `true`: Lấy thành công
- `false`: Lấy thất bại

**Ví dụ:**
```yaml
- do: mes.get_control_table
  with: ~
  on_success:
    - do: return.continue
  on_fail:
    - do: return.FAIL
      with:
        error_code: GET_CONTROL_TABLE_FAIL
```

**Lưu ý:** Sau khi gọi, có thể truy cập các giá trị như `{$context MES[ANDROID_BUILD]}`, `{$context MES[PART_NUMBER]}`, etc.

---

#### `mes.get_mac_list`
Lấy danh sách MAC address từ MBS number.

**Tham số:** Không có (`~`)

**Dữ liệu đầu vào:** Lấy MBS từ `context["input_str"]`

**Kết quả:** MAC list được lưu vào `context["MES[mac]"]`

**Trả về:** 
- `true`: Lấy thành công
- `false`: Lấy thất bại

**Ví dụ:**
```yaml
- do: mes.get_mac_list
  with: ~
  on_success:
    - do: logger.info
      with:
        message: "MAC List: {$context MES[mac]}"
    - do: return.PASS
  on_fail:
    - do: return.FAIL
      with:
        error_code: GET_MAC_LIST_FAIL
```

---

### 5. `cam` - Camera và OCR

#### `cam.set_operation_image`
Hiển thị ảnh hướng dẫn operator trên Camera Window.

**Tham số:**
- `image_path` (string, bắt buộc): Đường dẫn file ảnh.

**Trả về:** 
- `true`: Hiển thị thành công
- Throw exception nếu file không tồn tại

**Ví dụ:**
```yaml
- do: cam.set_operation_image
  with:
    image_path: "./setup/image/check_label_instruction.png"
```

```yaml
- do: cam.set_operation_image
  with:
    image_path: "C:\\venus\\images\\step1.jpg"
```

**Lưu ý:** 
- Chỉ hoạt động khi có flag `enable_camera_window`
- Hỗ trợ: .png, .jpg, .jpeg, .bmp
- Throw error `FILE_NOT_FOUND` nếu file không tồn tại

---

#### `cam.CheckOcrString` (chưa có StepMethod attribute - cần kiểm tra)
Nhận dạng text từ camera và so sánh với pattern mong đợi.

**Tham số:**
- `regex` (string, bắt buộc): Regex để tìm text trong OCR result
- `timeout_seconds` (int, bắt buộc): Thời gian timeout (giây)
- `expect_regex` (string, bắt buộc): Regex để kiểm tra text đã tìm được
- `save_image_with_name` (string, tùy chọn): Tên file để lưu ảnh

**Hành vi:**
- Lặp lại việc OCR mỗi 1 giây cho đến khi timeout
- Tìm text khớp với `regex`
- Kiểm tra text có khớp với `expect_regex` không

**Trả về:** 
- `true`: Text khớp với expect_regex
- `false`: Không tìm thấy hoặc không khớp
- Throw exception nếu camera không chạy hoặc OCR service không sẵn sàng

**Ví dụ:**
```yaml
- do: cam.CheckOcrString
  with:
    regex: "Model:\\s*(\\S+)"
    timeout_seconds: 30
    expect_regex: "^650[MP]$"
    save_image_with_name: "model_check.png"
  on_success:
    - do: return.PASS
  on_fail:
    - do: return.FAIL
      with:
        error_code: OCR_MODEL_MISMATCH
```

---

#### `cam.GetOcrString` (chưa có StepMethod attribute - cần kiểm tra)
Nhận dạng text từ camera và lưu vào context variable.

**Tham số:**
- `regex` (string, bắt buộc): Regex để tìm text trong OCR result
- `timeout_seconds` (int, bắt buộc): Thời gian timeout (giây)
- `var_name` (string, tùy chọn): Tên biến để lưu kết quả vào context
- `save_image_with_name` (string, tùy chọn): Tên file để lưu ảnh

**Hành vi:**
- Lặp lại việc OCR mỗi 1 giây cho đến khi timeout
- Tìm text khớp với `regex`
- Lưu text vào `context[var_name]` nếu có

**Trả về:** 
- `true`: Tìm thấy text khớp regex
- `false`: Không tìm thấy trong thời gian timeout
- Throw exception nếu camera không chạy hoặc OCR service không sẵn sàng

**Ví dụ:**
```yaml
- do: cam.GetOcrString
  with:
    regex: "SN:\\s*(\\S+)"
    timeout_seconds: 20
    var_name: "serial_number_from_label"
    save_image_with_name: "serial_capture.png"
  on_success:
    - do: logger.info
      with:
        message: "Serial: {$context serial_number_from_label}"
    - do: return.continue
  on_fail:
    - do: return.FAIL
      with:
        error_code: OCR_SERIAL_NOT_FOUND
```

---

### 6. `func` - Hàm tiện ích

#### `func.date_time_check`
Kiểm tra ngày giờ (đang phát triển).

**Tham số:** Chưa định nghĩa

**Ví dụ:**
```yaml
- do: func.date_time_check
  with: ~
```

**Lưu ý:** Method này chưa được implement (NotImplementedException).

---

## 📊 Bảng tổng hợp

| Executer | Method                | Tham số chính                              | Kết quả         | Ghi chú             |
| -------- | --------------------- | ------------------------------------------ | --------------- | ------------------- |
| `logger` | `info`                | `message`                                  | Luôn true       | Ghi log             |
| `return` | `continue`            | -                                          | true            | Tiếp tục step       |
| `return` | `PASS`                | -                                          | Throw exception | Kết thúc PASS       |
| `return` | `FAIL`                | `error_code`                               | Throw exception | Kết thúc FAIL       |
| `if`     | `condition`           | `condition`                                | true/false      | Kiểm tra điều kiện  |
| `mes`    | `CHECK_MBS_NO`        | -                                          | Throw exception | HOA - tự return     |
| `mes`    | `check_mbs_no`        | -                                          | true/false      | Kiểm tra MBS        |
| `mes`    | `get_csn`             | -                                          | true/false      | Lưu vào `MES[csn]`  |
| `mes`    | `get_control_table`   | -                                          | true/false      | Lưu vào `MES[item]` |
| `mes`    | `get_mac_list`        | -                                          | true/false      | Lưu vào `MES[mac]`  |
| `cam`    | `set_operation_image` | `image_path`                               | true/exception  | Hiển thị ảnh        |
| `cam`    | `CheckOcrString`      | `regex`, `timeout_seconds`, `expect_regex` | true/false      | OCR + so sánh       |
| `cam`    | `GetOcrString`        | `regex`, `timeout_seconds`, `var_name`     | true/false      | OCR + lưu biến      |
| `func`   | `date_time_check`     | -                                          | -               | Chưa implement      |

---

## 📝 Lưu ý khi sử dụng

### 1. Quy tắc đặt tên method:
- **HOA (`UPPER_CASE`)**: Tự động return PASS/FAIL, không cần `on_success`/`on_fail`
- **Thường (`lower_case`)**: Trả về `true`/`false`, cần xử lý với `on_success`/`on_fail`

### 2. Context variables:
- MES methods lưu kết quả vào context với prefix `MES[...]`
- Truy cập bằng: `{$context MES[csn]}`, `{$context MES[mac]}`

### 3. Error handling:
- Throw `ReturnHandle(true)` → Test PASS
- Throw `ReturnHandle(false, "ERROR_CODE")` → Test FAIL
- Return `true` → Step success, chạy `on_success`
- Return `false` → Step fail, chạy `on_fail`

### 4. Camera methods:
- Cần flag `enable_camera_window` trong test_configuration
- Kiểm tra camera running và OCR service ready
- Tự động retry mỗi 1 giây cho đến timeout

---

## 🔗 Tham khảo thêm
- [Hướng dẫn viết Steps](./steps_reference.md) - Pattern và best practices
- [Test Sequences](./test_sequences.md) - Cấu trúc test_sequences
- [Test Configurations](./test_configurations.md) - Cấu hình biến

---

## ⚠️ Liên hệ
Nếu cần thêm executer hoặc method mới, vui lòng liên hệ **SW ENG** để bổ sung vào hệ thống.