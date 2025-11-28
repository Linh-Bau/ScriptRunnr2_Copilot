---
applyTo: "**/*.yaml"
name: yaml-script-test-sequence-rule
description: Các quy tắc chung của test-sequence trong script.yaml.
---

# Giới thiệu
- Tài liệu này cung cấp:
  - Cách định nghĩa và sử dụng `test_sequences` trong `script.yaml`.

# Tổng quan
- `test_sequences` là một danh sách các bước kiểm tra cụ thể được thực hiện trên thiết bị trong quá trình test. Mỗi bước kiểm tra xác định các hành động, điều kiện và tham số cần thiết để thực hiện kiểm tra.

# Định nghĩa test_sequences
**format chuẩn:**
- Tên key phải là dạng lower_case với dấu gạch dưới (_) để phân tách từ.
- Sử dụng 2 khoảng trắng cho mỗi cấp độ thụt lề. KHÔNG sử dụng tab.
**ví dụ**:
```yaml
test_sequences: 
    - &t650c_test_sequence
      test_items:
        - name: CHECK_MBS_NO 
          lower_limit: ~ 
          upper_limit: ~ 
          steps: 
            - do: mes.CHECK_MBS_NO 
              with: ~
              on_success: ~
              on_fail: ~

        - name: MES_GET_ETH_MAC
          steps: 
            - do: mes.GET_MAC_LIST
              with: ~
              on_success: ~
              on_fail: ~

        - name: PMS_AUTO_TEST
          steps: 
            - do: pms.AUTO
              with:
                working_path: C:\\T650C_SA_K81DL_TOOL20230929\\V1.21.2\PMS_Simulation_Window_V1.09
                exe_name: PMS_Simulation_Window_V1.09.exe
                bt_mac: ~
                wifi_mac: ~
                eth_mac: 123456789012 
              on_success: ~
              on_fail: ~

        - name: MT_AUTO_TEST
          steps: 
            - do: mt.AUTO
              with: 
                working_path: C:\T650C_SA_K81DL_TOOL20230929\V1.21.2
                exe_name: PCBA_tests.exe
                ict_sn_01: $context input_str
                ict_sn_02: ~
              on_success: ~
              on_fail: ~

        - name: CHECK_RESULT
          steps: 
            - do: mt.check_tasklist
              with: 
                board_id: 01
                expect: K81-Firmware,Complete
              on_success: ~ 
              on_fail: 
                - do: return.FAIL
                  with:
                    error_code: DOWNLOAD_K81_FIRMWARE_FAIL
            
            - do: mt.check_tasklist
              with: 
                board_id: 01
                expect: K81-APP,Complete
              on_success: 
                - do: return.PASS
                  with: ~
              on_fail: 
                - do: return.FAIL
                  with:
                    error_code: DOWNLOAD_K81_APP_FAIL
```

- `test_sequences` bao gồm một danh sách chứa các danh sách test_item cần kiểm tra (`test_items`).

- `test_items` là danh sách các bước kiểm tra, mỗi bước bao gồm:
  - `name`: Tên của bước kiểm tra.
  - `lower_limit` và `upper_limit`: Giới hạn dưới và trên cho kết quả kiểm tra (có thể null). Hoặc bỏ qua.
  - `steps`: Danh sách các hành động cụ thể để thực hiện trong bước kiểm tra.
    - Mỗi hành động bao gồm:
      - `do`: Tên của executer và method cần thực hiện (ví dụ: `mes.CHECK_MBS_NO`).
      - `with`: Tham số để thực hiện hành động (có thể null). Là một danh sách key-value (string-string).
      - `on_success`: Hành động khi bước kiểm tra thành công (có thể null hoặc danh sách hành động).
      - `on_fail`: Hành động khi bước kiểm tra thất bại (có thể null hoặc danh sách hành động).

**Bổ xung**:
- Các method được quy định với format như sau:
  - Tên `UPPER_CASE_WITH_UNDERSCORES`: item này sẽ không thực hiện các hành động `on_success` hoặc `on_fail`, nó sẽ return PASS hoặc FAIL luôn.
  - Tên `lower_case_with_underscores`: item này sẽ thực hiện các hành động `on_success` hoặc `on_fail` dựa trên kết quả của method.
  - Trong `with`, các giá trị có thể tham chiếu đến các ngữ cảnh hiện tại. Xem phần `with` bên dưới.

# Tham chiếu ngữ cảnh trong `with`
- Tham khảo: [string-resolver-summary](./documents/string-resolver-summary.md)

# các step và method được hỗ trợ
- Tham khảo: [yaml-step-summary](./documents/yaml-steps-summary.md)
- tham khảo thư mục [steps](./documents/steps) để biết chi tiết về từng executer và method.