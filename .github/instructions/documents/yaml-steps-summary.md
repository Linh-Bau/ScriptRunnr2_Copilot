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
  
### Bảng tóm tắt các executer và method.
Lưu ý:
- Tham khảo tài liệu để biết chi tiết hơn về cách sử dụng.
- Tên cần viết chính xác, phân biệt chứ hoa và thường.
  
| <b>Tài liệu</b> | <b>Executer</b> | <b>Chức năng</b> | <b>Tài liệu</b> |
|------------------|----------------|------------------|------------------|
| [Xem chi tiết](./steps/return-executer-summary.md)  | `return`  | `PASS` | trả về kết quả `PASS` cho `test item` này. |
| | | `FAIL` | trả về kết quả `FAIL` cho `test item` này. |
| [Xem chi tiết](./steps/mes-excuter-summary.md)  | `mes`  | `CHECK_MBS_NO` | Kiểm tra đầu vào trạm. |
| | | `GET_CSN` | Lấy CSN từ hệ thống MES. |
| | | `GET_CONTROL_TABLE` | Lấy thông tin  `control table` (biên bản quản khống) từ MES. |
| | | `GET_MAC_LIST` | Lấy `ETH \| WIFI \| BT`  mac mới từ hệ thống MES, thường lưu vào `context["MES[mac]]`. Thường dùng cho các trạm cần ghi thông tin.|
| [Xem chi tiết](./steps/logger-executer-summary.md)  | `logger`  | `info` | Ghi log bổ xung thông tin. |
| [xem chi tiết](./steps/pms-and-executer-summary.md) | `pms` |`AUTO` | |