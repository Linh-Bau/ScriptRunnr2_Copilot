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

**Sử dụng khi**:
- Cần debug thông tin, thêm log mô tả.
- Đa phần trường hợp sẽ không cần dùng đến, vì thông tin mà `ScriptRunner2` đã khá đầy đủ. Gợi ý khi người dùng yêu cầu.

**Lưu ý:**
- Hỗ trợ biến: `{$variable_name}`
- Log tự động có timestamp

###  3.2 - `return` - Điều khiển luồng
Sử dụng để kết thúc test item với kết quả PASS/FAIL hoặc tiếp tục bước tiếp theo.

####  3.2.1 `return.PASS`
Kết thúc test item với kết quả **PASS**.

**Tham số:** Không có (`~`)

**Trả về:** Throw `ReturnHandle(true)` - Kết thúc test item ngay lập tức

**Ví dụ:**
```yaml
- do: return.PASS
  with: ~
```

**Sử dụng khi**:
- Kết thúc test item ngay khi đạt điều kiện thành công.

**Lưu ý:** 
- Method name phải viết **HOA**: `return.PASS` (không phải `return.pass`)
- Không cần `on_success`/`on_fail` vì method này throw exception để kết thúc

####  3.232 `return.FAIL`
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

**Sử dụng khi**: 
- Kết thúc test item ngay khi phát hiện lỗi.

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


**Sử dụng khi**:  
- Cần so sánh 1 giá trị biến với giá trị cụ thể để quyết định luồng thực hiện. Hoặc trả về kết quả PASS/FAIL.
- Sử dụng  với: Kiểm tra giá trị của `battery`, ...

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

**Sử  dụng khi**:
- Luôn luôn ở bước đầu tiên của test item để kiểm tra MBS number.  Bước này là bắt buộc với mọi trạm  test thông thường.

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
**Sử dụng khi**:  
- Cần lấy CSN từ MBS để sử dụng trong các bước tiếp theo.


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

***Sử dụng khi**:
-  Cần lấy thông tin `biên bản quản khống`, Các thông tin `OS`, `VERSION` ... từ MES để sử dụng trong các bước tiếp theo.


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

**Sử dụng khi**:
- Cần lấy <b>ETH/WIFI/BT MAC</b> mới từ MES. Để ghi vào sản phẩm.

### 3.5 - `pms` & `mt` - AUTO IT PMS WINDOW & MANUFACTORING TOOL
Kiểm soát và mở chương trình `PMS_Simulation_Window_V1.09.exe`.
Chương trình này đi kèm với `PCBA_tests.exe`.
Phiên bản chương trình:
- Tên file (PMS WINDOW): `PMS_Simulation_Window_V1.09.exe`
- Tên file (MANUFACTORING TOOL): `PCBA_tests.exe` phiên bản `v1.21.2`.
Yêu cầu:
- người sử dụng cần kiểm tra các phiên bản chương trình, nếu phiên bản chương trình không đúng, có thể không chạy được.

#### 3.5.1 `pms.AUTO` 
```yaml
do: pms.AUTO
  with:
    working_path: C:\\T650C_SA_K81DL_TOOL20230929\\V1.21.2\PMS_Simulation_Window_V1.09
    exe_name: PMS_Simulation_Window_V1.09.exe
    bt_mac: ~
    wifi_mac: ~
    eth_mac: 123456789012 #$context MES[mac]
  on_success: ~
  on_fail: ~
```
**with**:
- `working_path` (string, bắt buộc): Đường dẫn thư mục chứa `PCBA_tests.exe`.
- `exe_name` (string, bắt buộc): Tên file thực thi PMS. 
- `bt_mac` (string): BT MAC address để ghi vào sản phẩm. Có thể để trống nếu k cần ghi vào.
- `wifi_mac` (string): WIFI MAC address để ghi vào sản phẩm. Có thể để trống nếu k cần ghi vào.
- `eth_mac` (string): ETH MAC address để ghi vào sản phẩm. Có thể để trống nếu k cần ghi vào.
  
**Sử dụng khi**:
- Sử dụng kèm với `PCBA_tests.exe`. Chương trình này luôn cần được mở trước khi chạy `MANUFACTORING TOOL`.

#### 3.5.2 `mt.AUTO`
```yaml
- name: MT_AUTO_TEST
  steps: 
    - do: mt.AUTO
      with: 
        working_path: C:\\IAC\\FISFrameworkSystem\\TestTool\\T650P_petro\\V1.21.2
        exe_name: PCBA_tests.exe
        ict_sn_01: $context input_str
        ict_sn_02: 8PEeevenus8386
      on_success: ~
      on_fail: ~
```
**with**:
- `working_path` (string, bắt buộc): Đường dẫn thư mục chứa `PCBA_tests.exe`.
- `exe_name` (string, bắt buộc): Tên file thực thi MANUFACTORING TOOL.
- `ict_sn_01` (string, bắt buộc): MBS number để nhập vào trường ICT SN 01. Để `~` nếu không sử dụng.  
- `ict_sn_02` (string, bắt buộc): MBS number để nhập vào trường ICT SN 02. Để `~` nếu không sử dụng.
- 1 trong 2 trường `ict_sn_01` hoặc `ict_sn_02` phải có giá trị, nếu không chương trình sẽ báo lỗi. Nếu giá trị lấy từ MBSNO thì dùng `$context input_str`.

**Sử dụng khi**:
- Sử dụng để tự động hóa việc chạy `PCBA_tests.exe` với các tham số cần thiết.
- Yêu cầu chương trình `PMS_Simulation_Window_V1.09.exe` đã được mở trước đó.

#### 3.5.3 `mt.check_log`
```yaml
- do: mt.check_log
  with: 
      expect: "ICT PASS"
  on_success: ~
  on_fail: 
    - do: return.FAIL
      with:
        error_code: MT_LOG_CHECK_FAIL
```

**with**:
- `expect` (string, bắt buộc): Chuỗi ký tự mong đợi xuất hiện trong log. dùng string hoặc `re_{string}` để sử dụng regex. Ví dụ: `re_ICT\s+PASS` để tìm chuỗi `ICT PASS` với khoảng trắng bất kỳ giữa 2 từ.

**Sử dụng khi**:
- Sử dụng sau khi dùng `mt.AUTO` để kiểm tra log kết quả từ MANUFACTORING TOOL.
- Nếu log chứa chuỗi `expect`, method trả về `true`, ngược lại trả về `false`.

#### 3.5.4 `mt.check_board_log`
```yaml
- do: mt.check_board_log
  with: 
      board_id: 01 # 01 hoặc 02
      expect: any # chuỗi ký tự mong đợi xuất hiện trong log. Dùng string hoặc re_{string} để sử dụng regex.
  on_success: ~
  on_fail: 
    - do: return.FAIL
      with:
        error_code: MT_BOARD_LOG_CHECK_FAIL
```

**with**:
- `board_id` (string, bắt buộc): ID của board cần kiểm tra, giá trị là `01` hoặc `02`.  
- `expect` (string, bắt buộc): Chuỗi ký tự mong đợi xuất hiện trong log. dùng string hoặc `re_{string}` để sử dụng regex. Ví dụ: `re_Board\s+01\s+PASS` để tìm chuỗi `Board 01 PASS` với khoảng trắng bất kỳ giữa các từ.
- **Sử dụng khi**:
- Sử dụng sau khi dùng `mt.AUTO` để kiểm tra log kết quả từ MANUFACTORING TOOL cho board cụ thể.
- Nếu log của board chứa chuỗi `expect`, method trả về `true`, ngược lại trả về `false`.

#### 3.5.5 `mt.check_tasklist`
```yaml
- do: mt.check_tasklist
  with: 
    board_id: 01
    expect: K81-Firmware,Complete
  on_success: 
    - do: return.continue
  on_fail: 
    - do: return.FAIL
      with:
        error_code: K81-Firmware DOWNLOAD FAILED
```
**with**: 
- `board_id` (string, bắt buộc): ID của board cần kiểm tra, giá trị là `01` hoặc `02`.    
- `expect` (string, bắt buộc): Chuỗi ký tự mong đợi xuất hiện trong tasklist. Dùng string hoặc `re_{string}` để sử dụng regex. Ví dụ: `re_K81-Firmware,Complete` để tìm chuỗi `K81-Firmware,Complete`.
- **Sử dụng khi**:
- Sử dụng sau khi dùng `mt.AUTO` để kiểm tra tasklist từ MANUFACTORING TOOL cho board cụ thể.
- Nếu tasklist của board chứa chuỗi `expect`, method trả về `true`, ngược lại trả về `false`.




---
## 4 - ⚠️ Liên hệ
Nếu cần thêm executer hoặc method mới, vui lòng liên hệ **SW ENG** để bổ sung vào hệ thống.