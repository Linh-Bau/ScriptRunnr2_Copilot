# FORMAT RULERS
---

## 1 - Ruler cho promt
1. Sử dụng ngôn ngữ tiếng Việt.
2. Sử dụng cấu trúc câu rõ ràng, mạch lạc.
3. Tránh sử dụng từ ngữ chuyên ngành phức tạp, nếu cần thiết phải giải thích rõ ràng.
4. Chia nhỏ ý tưởng thành các đoạn văn ngắn để dễ đọc và hiểu.
5. Chỉ sử dụng các tài liệu đáng tin cậy được cung cấp trong project này. Tuyệt đối không sử dụng hoặc tạo ra các thông tin không được cung cấp.
   
---

## 2 - Format ruler cho yaml
1. **Indentation:** Chỉ sử dụng **2 khoảng trắng**, không dùng tab.  
2. **Comment:** Dòng bắt đầu bằng `#` sẽ được bỏ qua khi parse.  
3. **Anchor / Reference:**  
   - `&<name>` dùng để định nghĩa một cấu hình.  
   - `*<name>` dùng để tham chiếu lại cấu hình đó.  
4. **Null value:** Dùng `~` cho các trường không có giá trị.  

---

## 3 - Format ruler cho script.yaml 
### 3.1 - Chung
1. Tên file script luôn là `script.yaml`
2. Format script:
```yaml
# YAML SCRIPT: 
# Phiên bản hiện tại: 1.0.0.0
# Lịch sử và nguyên nhân chỉnh sửa:
# - 1.0.0.0: 
#   + khởi tạo
#   + chưa thử nghiệm
#
script:
  mes_defect_code: ~
  test_environments: ~
  test_configurations: ~
  test_sequences: ~                   
  test_targets: ~
```
Cấu trúc chung format luôn cần các trường:
- `mes_defect_code`: Mã lỗi từ hệ thống MES (Manufacturing Execution System). Nếu không có mã lỗi, sử dụng `~`. Tên mã lỗi là 4 ký tự, viết hoa.
- `test_environments`: Môi trường kiểm thử, nếu không có sử dụng `~`.
- `test_configurations`: Cấu hình kiểm thử, nếu không có sử dụng `~`.
- `test_sequences`: Danh sách các test item. Luôn cần.
- `test_targets`: Script tương ứng với mỗi `MODEL_ID`.
- Đề xuất có các note bổ xung về thay đổi và lý do thay đổi ở phần đầu file script. 

### 3.2 - Fomat rule cho mes_defect_code
- ví dụ: tham khảo [example.yaml](../../examples/example.yaml)
- Các `<MÃ_DEFECT_CODE>` phải là mã lỗi 4 ký tự viết hoa, có thể tìm kỹ sư `MES` để lấy mã lỗi hoặc tạo mã lỗi

### 3.3 - Fomat ruler cho test_environments
ví dụ:
```yaml 
test_environments: # thiết lập cài đặt tải xuống
    - &t650_environment
      downloads: 
        - name: "tesseract-ocr" 
          type: "folder" # loại tải về: folder, file, compressed
          from: "/SFTP.conf.pe.02/V6ENV/tessdata" 
          to: "./setup/tessdata" 
          extract_to: ~

        - name: "image" 
          type: "folder"
          from: "./setup/image" 
          to: "./setup/image"
          extract_to: ~
```
- Mỗi môi trường kiểm thử được định nghĩa bởi một anchor `&<environment_name>`.
- Mỗi trường có thể để trống bằng cách sử dụng `~`. 

### 3.4 - Fomat ruler cho test_configurations 
ví dụ: tham khảo [example.yaml](../../examples/example.yaml)
- Luôn cần thiết. mỗi cấu hình kiểm thử được định nghĩa bởi một anchor `&<configuration_name>`.
- Có thể sử dụng chung 1 môi trường cho nhiều `MODEL_ID` khác nhau. Nhưng cần cẩn trọng và xác nhận rằng các `MODEL_ID` đó thực sự có thể chia sẻ cùng một môi trường.

### 3.5 - Fomat ruler cho test_sequences
ví dụ: 
```yaml
  test_sequences: 
    - &t650c_test_sequence
      test_items:
        - name: CHECK_MBS_NO
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
                eth_mac: 123456789012 #$context MES[mac]
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
              on_success: 
                - do: return.continue
              on_fail: 
                - do: return.FAIL
                  with:
                    error_code: DOWNLOAD_K81_FIRMWARE_FAIL
            
            - do: mt.check_tasklist
              with: 
                board_id: 01
                expect: K81-APP,Complete
              on_success: 
                - do: return.continue
              on_fail: 
                - do: return.FAIL
                  with:
                    error_code: DOWNLOAD_K81_APP_FAIL
```
- Luôn cần thiết. Mỗi test sequence được định nghĩa bởi một anchor `&<test_sequence_name>`.
- Mỗi test sequence bao gồm nhiều `test_items`.
- Mỗi `test_item` bao gồm nhiều `steps`.
- Mỗi `step` bao gồm các trường:  
  - `do`: Tên hành động cần thực hiện. [CẦN]
  - `with`: Tham số cho hành động. Nếu không có tham số, sử dụng `~`. Dữ liệu dang danh sách `Dictionary<string,string>`.  [TÙY THUỘC VÀO HÀNH ĐỘNG]
  - `on_success`: Hành động khi thành công. Nếu không có hành động, sử dụng `~`.  [TÙY THUỘC VÀO HÀNH ĐỘNG]
  - `on_fail`: Hành động khi thất bại. Nếu không có hành động, sử dụng `~`.  [TÙY THUỘC VÀO HÀNH ĐỘNG]

### 3.6 - Fomat ruler cho test_targets
ví dụ: tham khảo [example.yaml](../../examples/example.yaml)
- Luôn cần thiết. Mỗi `MODEL_ID` sẽ tham chiếu đến một cấu hình kiểm thử và một test sequence.
- Mỗi `MODEL_ID` có thể tham chiếu đến cùng một cấu hình kiểm thử hoặc test sequence như các `MODEL_ID` khác để tiết kiệm thời gian và công sức.
- Có thể sử dụng nhiều `MODEL_ID` trong cùng một mục `test_targets`.
ví dụ:
```yaml
  test_targets: 
    - model_id: VN6501AACAMB | VN6501AACAMB1
      environment: ~
      test_config: *t650c_configuration 
      test_sequence: *t650c_test_sequence 
```