# YAML STEP SUMMARY
---
## Tóm tắt
- Tài liệu này cung cấp thông tin về các bước (steps) có thể sử dụng trong file `script.yaml`.

## Cấu trúc 1 bước test.
Tham khảo [YAML FORMAT SUMMARY](./yaml-format-summary.md) để hiểu về cấu trúc chung của 1 bước test trong file `script.yaml`. Mỗi bước test sẽ bao gồm các trường chính như sau:
```yaml
do: <executer>.<action>  # Tên executer thực hiện bước test
with:
  # danh sách key-value
  param1: value1         # Tham số truyền vào cho executer
  param2: value2
    ...
on_success: có thể null
  # danh sách các bước thực hiện khi thành công
  - do: <executer>.<action>
    with:
      param1: value1
      ...
on_fail: # có thể null
    # danh sách các bước thực hiện khi thất bại
    - do: <executer>.<action>
        with:
        param1: value1
        ...
```
Các tham số ở bước `with` có thể được xử lý để chạy ở `runtime`. Xem hướng dẫn ở phần [STRING RESOLVER SUMMARY](./string-resolver-summary.md).

---


## Danh sách các bước test (steps) được hỗ trợ.
### Lưu ý chung.
- `<executer>.<method>` với method tên `VIET_HOA_GACH_DUOI` sẽ luôn trả về `PASS` hoặc `FAIL` ngay mà không thực thi các steps bên dưới, cũng không thực thi `on_success`, `on_fail`.
  
- `<executer>.<method>` với method tên `viet_thuong_gach_duoi` sẽ trả về `true` hoặc `fail`. Nếu `true` sẽ thực thi `on_success`, nếu `false` sẽ thực thi `on_fail`.
  
- Nếu `on_success`, `on_fail` là `~` sẽ thực hiện bước tiếp theo trong `steps`. Nếu nó là bước cuối cùng sẽ báo lỗi khi chạy.
  
---
### logger
- **Tóm tắt chức năng**: Ghi log thông tin trong quá trình thực thi, thích hợp khi cần debug một thông tin cụ thể.
- **Cú pháp**:
```yaml 
# Ghi log info, luôn luôn thành công.
- do: logger.info
  with:
    message: "This is an info message" 
```
- **ví dụ**:
```yaml
# Ghi log 
- do: logger.debug
  with:
    message: TEST PASSED

# Ghi log với tên biến runtime
- do: logger.debug
  with:
    message: $str PRODUCT BATTERY LEVEL - {$context PROD[battery_level]}% # xem STRING RESOLVER SUMMARY
```

---
### return
- **Tóm tắt chức năng**: Điều khiển luồng test. Trả về PASS hoặc FAIL luôn cho `test item` này, bỏ qua các `steps` còn lại trong `test item`.
- **Cú pháp**:
```yaml
# Trả về PASS luôn
- do: return.PASS # chú ý, là PASS, không phải pass
  with: ~ # Có thể xóa luôn with, mặc định đã là null r.

# Trả về FAIL luôn
- do: return.FAIL # chú ý, là FAIL, không phải fail
  with: # có thể null. Nhưng nên cung cấp lỗi cụ thể. Nếu không lỗi sẽ là UNDEFINE.
    error_code: FLASH_OS_FAIL # tên lỗi, đặt tên lỗi theo ngữ cảnh
```

---
### if
- **Tóm tắt chức năng**: Thực hiện điều kiện rẽ nhánh trong quá trình test.
- **Cú pháp**:
```yaml
# cú pháp kiểm tra điều kiện
- do: if.condition
  with:
    condition: "<biểu thức điều kiện>" # sử dụng biểu thức điều kiện ở STRING RESOLVER SUMMARY
  on_success:
    - do: <executer>.<action> # bước thực hiện nếu điều kiện đúng
      ...
  on_fail:
      - do: <executer>.<action> # bước thực hiện nếu điều kiện sai
        ...    
```
- **Ví dụ**:
```yaml
# kiểm tra giá trị battery_level trong context PROD[battery_level] có lớn hơn 75 không
- do: if.condition
  with:
    condition: "$if {$context PROD[battery_level]} > 75"
  on_success:
    - do: logger.info
      with:
        message: "Check batterry level success."
    - do: return.PASS
  on_fail:
    - do: logger.info
      with:
        message: "Battery level is low."
    - do: return.FAIL
      with:
        error_code: BATTERY_LEVEL_LOW

```
---
### mes
- **Tóm tắt chức năng**: Dùng để tra cứu, ghép thông tin sản phẩm từ/lên hệ thống MES (Manufacturing Execution System).

#### `mes.CHECK_MBS_NO`
- **Tóm tắt chức năng**: Kiểm tra đầu vào trạm, xem `MBSNO` có ở trạm này không. Nếu chạy ở chế độ `OFFLINE` thì sẽ luôn PASS item này.
- **Cú pháp**:
  
```yaml
- do: mes.CHECK_MBS_NO
  with: ~

```
- **Lỗi thường gặp**:
  - <b>CHECK_MBS_NO</b>: MBSNO không ở trạm này.

#### `mes.GET_CSN`
- **Tóm tắt chức năng**: Lấy `CSN` từ hệ thống MES dựa trên `MBSNO`, lưu vào `context` với tên `MES[csn]`. Chế độ `OFFLINE` có thể sẽ lỗi.
- **Cú pháp**:
```yaml
- do: mes.GET_CSN
  with: ~

```
- **Lỗi thường gặp**:
  - <b>MES_GET_CSN</b>: Lấy CSN từ MES thất bại.


####  `mes.GET_CONTROL_TABLE`
- **Tóm tắt chức năng**: Lấy control table (bảng kiểm soát phiên bản) từ MES. Lưu các giá trị item và value từ ECO Version vào context với format `MES[item_name]`. Chế độ `OFFLINE` có thể sẽ lỗi.

**Cú pháp**
```yaml
- do: mes.GET_CONTROL_TABLE
  with: ~
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