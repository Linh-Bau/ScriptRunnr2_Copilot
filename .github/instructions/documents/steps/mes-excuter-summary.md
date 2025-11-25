# Summary
- Đây là tài liệu cho executer `mes`

***

## mes
- **Tóm tắt chức năng**: Dùng để tra cứu, ghép thông tin sản phẩm từ/lên hệ thống MES (Manufacturing Execution System).

---
#### `mes.CHECK_MBS_NO`
- **Tóm tắt chức năng**: Kiểm tra đầu vào trạm, xem `MBSNO` có ở trạm này không. Nếu chạy ở chế độ `OFFLINE` thì sẽ luôn PASS item này.
- **Cú pháp**:
```yaml
- do: mes.CHECK_MBS_NO
  with: ~
```

- **Lỗi thường gặp**:
  - <b>CHECK_MBS_NO</b>: MBSNO không ở trạm này.

---
#### `mes.GET_CSN`
- **Tóm tắt chức năng**: Lấy `CSN` từ hệ thống MES dựa trên `MBSNO`, lưu vào `context` với tên `MES[csn]`. Chế độ `OFFLINE` có thể sẽ lỗi.
- **Cú pháp**:
```yaml
- do: mes.GET_CSN
  with: ~

```
- **Lỗi thường gặp**:
  - <b>MES_GET_CSN</b>: Lấy CSN từ MES thất bại.

---
####  `mes.GET_CONTROL_TABLE`
- **Tóm tắt chức năng**: Lấy control table (bảng kiểm soát phiên bản) từ MES. Bảng này chứa các thông tin `OS`, `VERSION` ... Lưu các giá trị item và value từ ECO Version vào context với format `MES[item_name]`. Chế độ `OFFLINE` có thể sẽ lỗi.

**Cú pháp**
```yaml
- do: mes.GET_CONTROL_TABLE
  with: ~
```
---

---
#### 3.4.4 `mes.GET_MAC_LIST`

- Lấy <b>ETH/WIFI/BT MAC</b> <b>mới</b> từ MES. Để ghi vào sản phẩm. Mac được lưu vào `context["MES[mac]"]`.

**Cú pháp**
```yaml
- do: mes.GET_MAC_LIST
  with: ~
  on_success: ~
  on_fail: ~
```

**Sử dụng khi**: