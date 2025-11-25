# Summary
- Đây là tài liệu tóm tắt cho executer `logger`.

---


### logger
- **Tóm tắt chức năng**: Ghi log thông tin trong quá trình thực thi, thích hợp khi cần debug một thông tin cụ thể.
- **Cú pháp**:
```yaml 
# Ghi log info, luôn luôn thành công.
- do: c.info
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
