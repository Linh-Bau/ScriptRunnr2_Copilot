# Summary
- Đây là tài liệu tóm tắt cho executer `if`.
- tham khảo: [STRING RESOLVER SUMMARY](./string-resolver-summary.md) để biết về biểu thức điều kiện.
  
---
### if
- **Tóm tắt chức năng**: Thực hiện điều kiện rẽ nhánh trong quá trình test
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