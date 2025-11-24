#  Xử lý String trong Script YAML
--- 

## 1 -  Giới thiệu
Hệ thống ScriptRunner2 hỗ trợ xử lý string động thông qua **String Resolver System**. Điều này cho phép sử dụng biến, điều kiện, và các hàm đặc biệt để thay cho các giá trị trong `with`.

## Cách hoạt động

### Quy trình xử lý:
1. Hệ thống quét string tìm các pattern `{$...}`
2. Tìm resolver phù hợp dựa trên **HeaderKey** (`$context`, `$script_config`, etc.)
3. Resolver xử lý và trả về giá trị
4. Thay thế pattern bằng giá trị đã resolve
5. Lặp lại cho đến khi không còn pattern nào

### Điều kiện để string được xử lý:
- String phải bắt đầu bằng `$` (ví dụ: `$context`, `$if`, `$path`)
- Nếu có nested pattern, dùng dấu `{}` để bao bọc
- **Quan trọng:** Khi muốn resolve nhiều biến trong một string, phải bắt đầu bằng `$` và có khoảng trắng sau đó. Ví dụ: `"$ MBS: {$context input_str} | CSN: {$context MES[csn]}"`

---

##  2 - 📋 Các Resolver có sẵn

### 2.1 - `$context` Lấy giá trị từ Test Context

Lấy giá trị từ runtime context của test item hiện tại.

**Cú pháp:**
```
$context <key>
```

**Ví dụ:**
```yaml
# Lấy CSN từ context
- do: logger.info
  with:
    message: "$ CSN: {$context MES[csn]}"

# Lấy input string (MBS number)
- do: logger.info
  with:
    message: "$ Testing device: {$context input_str}"

# Lấy slot ID
- do: logger.info
  with:
    message: "$ Slot: {$context slot_id}"

# Resolve nhiều biến trong một string
- do: logger.info
  with:
    message: "$ MBS: {$context input_str} | CSN: {$context MES[csn]} | Slot: {$context slot_id}"
```

**Các key thường dùng:**
- `input_str` - MBS number được nhập vào
- `slot_id` - ID của fixture (1, 2, 3, ...)
- `MES[csn]` - CSN từ MES
- `MES[mac]` - MAC address từ MES
- `MES[<item>]` - Các item từ control table
- `log_folder` - Đường dẫn folder log
- `error_code` - Mã lỗi hiện tại

**Lưu ý:**
- Nếu key không tồn tại → throw error `CONTEXT_KEY_NOT_FOUND`
- Giá trị trả về là string

### 2.2  - `$script_config` Lấy giá trị từ Script Configuration

Lấy giá trị từ `script_configuration` trong YAML (general hoặc fixture_configuration).

**Cú pháp:**
```
$script_config <key>
```

**Thứ tự ưu tiên:**
1. Kiểm tra `fixture_configuration[slot_id]` trước
2. Nếu không có, tìm trong `general`
3. Nếu cả hai không có → throw error `SCRIPT_CONFIG_KEY_NOT_FOUND`

**Ví dụ:**
```yaml
# Trong script.yaml
script_configuration:
  general:
    txt_model: 650m
    txt_android_version: 810
    txt_part_number: 410-32-EUC-6
  
  fixture_configuration:
    - PORT_1: COM1
      PORT_2: COM10
    - PORT_1: COM2
      PORT_2: COM20
```

```yaml
# Sử dụng trong steps
- do: logger.info
  with:
    message: "$ Model: {$script_config txt_model}"

- do: logger.info
  with:
    message: "$ Android Version: {$script_config txt_android_version}"

# Lấy PORT_1 - Fixture 1 sẽ lấy COM1, Fixture 2 sẽ lấy COM2
- do: serial.open
  with:
    port: "{$script_config PORT_1}"
    baudrate: 115200
```

**Lưu ý:**
- Mỗi fixture có thể có giá trị khác nhau (fixture_configuration)
- Biến trong `fixture_configuration` sẽ override biến trong `general`


### 2.3 - `$user_config` Lấy giá trị từ User Configuration

Lấy cấu hình runtime từ user config (hiện tại chỉ hỗ trợ `mes_on`).

**Cú pháp:**
```
$user_config <key>
```

**Key hỗ trợ:**
- `mes_on` - Trả về `True` hoặc `False`

**Ví dụ:**
```yaml
# Kiểm tra chế độ debug
- do: if.condition
  with:
    condition: $if {$user_config mes_on} == False
  on_success:
    - do: logger.info
      with:
        message: "Debug mode - MES is OFF"
    - do: return.PASS
```

**Lưu ý:**
- Nếu key không phải `mes_on` → throw error `RESOLVER_USER_CONFIG_KEY_NOT_FOUND`
- Trả về giá trị boolean dạng string: `"True"` hoặc `"False"`


### 2.4 - `$if` Điều kiện logic

Đánh giá biểu thức điều kiện và trả về `"True"` hoặc `"False"`.

**Cú pháp:**
```
$if <left_value> <operator> <right_value>
```

**Toán tử hỗ trợ:**
- `==` - Bằng (so sánh string)
- `!=` - Khác (so sánh string)
- `>` - Lớn hơn (so sánh số)
- `<` - Nhỏ hơn (so sánh số)
- `>=` - Lớn hơn hoặc bằng (so sánh số)
- `<=` - Nhỏ hơn hoặc bằng (so sánh số)

**Ví dụ:**

**1. So sánh string:**
```yaml
- do: if.condition
  with:
    condition: $if {$script_config txt_model} == 650m
  on_success:
    - do: logger.info
      with:
        message: "Model is T650M"

- do: if.condition
  with:
    condition: $if {$user_config mes_on} == True
  on_success:
    - do: mes.check_mbs_no
      with: ~
```

**2. So sánh số:**
```yaml
- do: if.condition
  with:
    condition: $if {$script_config txt_android_version} >= 810
  on_success:
  - do: logger.info
    with:
      message: "$ Android version is 8.1.0 or higher"

- do: if.condition
  with:
    condition: $if {$context slot_id} < 3
  on_success:
    - do: logger.info
      with:
        message: "Running on Slot 1 or 2"
```

**Lưu ý:**
- Toán tử `==` và `!=`: So sánh string
- Toán tử `>`, `<`, `>=`, `<=`: Chuyển đổi sang số để so sánh
- Trả về: `"True"` hoặc `"False"` (string)
- Throw error `IF_CONDITION_WRONG_FORMAT` nếu cú pháp sai


### 2.5 - `$path` Chuyển đổi relative path sang absolute path

Chuyển đổi đường dẫn tương đối sang đường dẫn tuyệt đối.

**Cú pháp:**
```
$path ./<relative_path>
```

**Ví dụ:**
```yaml
# Setup image path
- do: cam.set_operation_image
  with:
    image_path: "{$path ./setup/image/instruction_step1.png}"

# Đường dẫn sẽ được convert thành:
# {working_dir}\{PRODUCT}\{STATION}\setup\image\instruction_step1.png
```

**Lưu ý:**
- Đường dẫn phải bắt đầu bằng `./`
- Sử dụng `/` hoặc `\` đều được (tự động convert)
- Throw error `PATH_KEY_RESOLVE` nếu không đúng format


### 2.6 - `$func` Hàm xử lý các string
// TODO: Chưa hoàn thiện

**Trạng thái:** Đang phát triển

**Cú pháp:**
```
$func <function_body>
```

**Lưu ý:** Throw `NotImplementedException` khi sử dụng.

### 2.7 - `$str ` Dùng để tạo 1 string có nhiều biến
Dùng để tạo một string có nhiều biến cần resolve. Rất thích hợp để tạo log message.
Ví dụ:
```yaml
- do: logger.info
  with:
    message: "$str Testing Device - MBS: {$context input_str}, Slot: {$context slot_id}, Model: {$script_config txt_model}, Android: {$script_config txt_android_version}"
```



## 3 - 🔄 Nested Resolution (Lồng ghép)

Hệ thống hỗ trợ xử lý nested pattern bằng cách dùng dấu `{}`.

**Ví dụ:**

### 3.1 - Kết hợp `$if` với `$context`
```yaml
- do: if.condition
  with:
    condition: $if {$context MES[csn]} == ABC123
  on_success:
    - do: logger.info
      with:
        message: "CSN matched!"
```

**Quy trình xử lý:**
1. Resolve `{$context MES[csn]}` → `"ABC123"`
2. String trở thành: `$if ABC123 == ABC123`
3. Resolve `$if ABC123 == ABC123` → `"True"`

### 3.2 - Kết hợp `$if` với `$script_config`
```yaml
- do: if.condition
  with:
    condition: $if {$script_config txt_model} == 650m
```

**Quy trình:**
1. `{$script_config txt_model}` → `"650m"`
2. `$if 650m == 650m` → `"True"`

### 3.3 - Kết hợp `$if` với `$user_config`
```yaml
- do: if.condition
  with:
    condition: $if {$user_config mes_on} == False
```

## 🎯 Best Practices

### ✅ Nên làm:
1. **Đặt tên biến rõ ràng:** Sử dụng `txt_model`, `txt_android_version` thay vì `m`, `v`
2. **Dùng nested khi cần:** `$if {$context var} == value`

### ❌ Không nên:
1. **Quên dấu `{}`:** Viết `$if $context var == value` → Sai
2. **Đặt tên trùng:** Tránh trùng tên giữa `general` và `fixture_configuration`
3. **So sánh số bằng `==`:** Dùng `>=`, `<=` cho số, `==` cho string
