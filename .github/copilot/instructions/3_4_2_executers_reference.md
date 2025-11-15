# Danh sách Executers và Methods

## Giới thiệu
- tham khảo [1_format-ruler.md](./1_format-ruler.md) để biết quy tắc định dạng chung.
- Các executers và methods được sử dụng trong `test_sequences` để thực hiện các hành động cụ thể.
- Mỗi executer đại diện cho một nhóm chức năng (ví dụ: `mes`, `logger`, `cam`, etc.).
- Mỗi method là một hành động cụ thể mà executer có thể thực hiện (ví dụ: `check_mbs_no`, `info`, `set_operation_image`, etc.).

---

## 1 - Cách sử dụng
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

**Giải thích**
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
## 2 - Xử lý string
Xử lý giá trị biến with ở runtime khi biến with: tham khảo [3_4_1_string_resolver_reference](./3_4_1_string_resolver_reference.md)  
---
## 3 - 📋 Danh sách Executers

### 3.1 - `logger` Ghi log

#### 3.1.1 `logger.info`
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

###  3.2 - `return` - Điều khiển luồng

####  3.2.1 `return.continue`
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

####  3.2.2 `return.PASS`
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

####  3.2.3 `return.FAIL`
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


### 3.3 - `if` - Kiểm tra điều kiện

#### 3.3.1  `if.condition`
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


###  3.4 - `mes` - Tương tác MES API

#### 3.4.1 `mes.CHECK_MBS_NO` (Khuyến nghị sử dụng)
SCRIPT test luôn luôn cần  bắt đầu  với item này. Khuyến nghị:
```yaml
- name: CHECK_MBS_NO
  steps:
    - do: mes.CHECK_MBS_NO
      with: ~
      on_success: ~
      on_fail: ~
```
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


#### 3.4.2 - `mes.GET_CSN`
Lấy CSN (Customer Serial Number) từ MBS number.

**with:** Không có (`~`)

**Dữ liệu đầu vào:** Lấy MBS từ `context["input_str"]`

**Kết quả:** CSN được lưu vào `context["MES[csn]"]`

**Ví dụ:**
```yaml
- do: mes.GET_CSN
  with: ~
  on_success: ~
  on_fail: ~
```

####  3.4.3 `mes.GET_CONTROL_TABLE`
Lấy control table (bảng kiểm soát phiên bản) từ MES.

**with:** Không có (`~`)

**Dữ liệu đầu vào:** Lấy MBS từ `context["input_str"]`

**Kết quả:** Các item và value từ ECO Version được lưu vào context với format `MES[item_name]`

**Ví dụ:**
```yaml
- do: mes.GET_CONTROL_TABLE
  with: ~
  on_success: ~
  on_fail: ~
```

**Lưu ý:** Sau khi gọi, có thể truy cập các giá trị như `{$context MES[ANDROID_BUILD]}`, `{$context MES[PART_NUMBER]}`, etc.

---

#### 3.4.4 `mes.GET_MAC_LIST`
Lấy danh sách MAC address từ MBS number.

**with:** Không có (`~`)

**Dữ liệu đầu vào:** Lấy MBS từ `context["input_str"]`

**Kết quả:** MAC list được lưu vào `context["MES[mac]"]`

**Trả về:** 
- `true`: Lấy thành công
- `false`: Lấy thất bại

**Ví dụ:**
```yaml
- do: mes.GET_MAC_LIST
  with: ~
  on_success: ~
  on_fail: ~
```


### 5. `cam` - Camera và OCR  

TODO: Cập nhật chi tiết executer camera và OCR
Đang cập nhật ...
---

### 6. `func` - Hàm tiện ích
TODO: Cập nhật chi tiết executer func
Đang cập nhật ... 


## 4 - ⚠️ Liên hệ
Nếu cần thêm executer hoặc method mới, vui lòng liên hệ **SW ENG** để bổ sung vào hệ thống.