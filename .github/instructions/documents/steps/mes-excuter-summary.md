# Summary
- Đây là tài liệu cho executer `mes`

***

## mes
- **Tóm tắt chức năng**: Dùng để tra cứu, ghép thông tin sản phẩm từ/lên hệ thống MES (Manufacturing Execution System).

---
#### `mes.is_online`
- **Tóm tắt chức năng**: Kiểm tra xem có đang chạy ở chế độ `ONLINE` hay không. `on_success` nếu ở chế dộ `ONLINE`, `on_fail` nếu ở chế độ `OFFLINE`.
- **Cú pháp**:
```yaml
- do: mes.is_online
  with: ~
```


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
#### `mes.GET_MAC_LIST`

- Lấy <b>ETH/WIFI/BT MAC</b> <b>mới</b> từ MES. Để ghi vào sản phẩm. Mac được lưu vào `context["MES[mac]"]`.

**Cú pháp**
```yaml
- do: mes.GET_MAC_LIST
  with: ~
  on_success: ~
  on_fail: ~
```

#### `mes.GET_NEW_IMEI`
- Lấy <b>IMEI mới</b> từ MES. Để ghi vào sản phẩm. IMEI được lưu vào `context["MES[imei]"]`.
- **Cú pháp**
```yaml
- do: mes.GET_NEW_IMEI
  with: ~
  on_success: ~
  on_fail: ~
```

#### `mes.UPLOAD_BT_MAC`
- Upload <b>BT MAC</b> từ sản phầm lên MES.
- **Cú pháp**
```yaml
- do: mes.UPLOAD_BT_MAC
  with: 
    mac: $context PROD[bt_mac] # BT MAC cần upload, lấy từ context PROD[bt_mac], với PROD[bt_mac] đã được lấy và lưu trước đó
  on_success: ~
  on_fail: ~
```

#### `UPLOAD_WIFI_MAC`
- Upload <b>WIFI MAC</b> từ sản phầm lên MES.
- **Cú pháp**
```yaml
- do: mes.UPLOAD_WIFI_MAC
  with: 
    mac: $context PROD[wifi_mac] # WIFI MAC cần upload, lấy từ context PROD[wifi_mac], với PROD[wifi_mac] đã được lấy và lưu trước đó
  on_success: ~
  on_fail: ~
```

#### `mes.UPLOAD_ETH_MAC`
- Upload <b>ETH MAC</b> từ sản phầm lên MES.
- **Cú pháp**
```yaml
- do: mes.UPLOAD_ETH_MAC
  with: 
    mac: $context PROD[eth_mac] # ETH MAC cần upload, lấy từ context PROD[eth_mac], với PROD[eth_mac] đã được lấy và lưu trước đó
  on_success: ~
  on_fail: ~
```

