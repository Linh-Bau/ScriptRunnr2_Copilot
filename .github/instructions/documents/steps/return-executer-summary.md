# Summary
- Đây là tài liệu tóm tắt cho executer `return`.

***

## return
- **Tóm tắt chức năng**: Điều khiển luồng test. Trả về PASS hoặc FAIL luôn cho `test item` này, bỏ qua các `steps` còn lại trong `test item`.
  
---
#### `return.PASS`
- **Tóm tắt chức năng**: Trả về kết quả PASS cho `test item` này, bỏ qua các `steps` còn lại trong `test item`.

- **Cú pháp**:
```yaml
# Trả về PASS luôn
- do: return.PASS # chú ý, là PASS, không phải pass
  with: ~ # Có thể xóa luôn with, mặc định đã là null r.
```

- **Sử dụng khi**:
  - Muốn kết thúc `test item` sớm với kết quả PASS, bỏ qua các bước còn lại.

---
#### `return.FAIL`
- **Tóm tắt chức năng**: Trả về kết quả FAIL cho `test item` này, bỏ qua các `steps` còn lại trong `test item`.

- **Cú pháp**:
```yaml
# Trả về FAIL luôn
- do: return.FAIL # chú ý, là FAIL, không phải fail
  with: # có thể null. Nhưng nên cung cấp lỗi cụ thể. Nếu không lỗi sẽ là UNDEFINE.
    error_code: FLASH_OS_FAIL # tên lỗi, đặt tên lỗi theo ngữ cảnh
```