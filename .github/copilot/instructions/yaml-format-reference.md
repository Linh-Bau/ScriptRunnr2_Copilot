# YAML Format Reference for ScriptRunner2

Xem file [company_term.md](./company_term.md) để biết các thuật ngữ được sử dụng trong tài liệu này.

## 📘 Giới thiệu
Tài liệu này định nghĩa **format chuẩn** cho file `script.yaml` được sử dụng trong chương trình **ScriptRunner2**.

Mọi file `script.yaml` phải tuân thủ đúng cấu trúc được mô tả bên dưới để đảm bảo:
- Có thể load và parse được trong ScriptRunner2.
- Hỗ trợ liên kết `anchor` (&) và `reference` (*) của YAML.

---

## ⚙️ Quy tắc định dạng (Format Rules)
1. **Indentation:** Chỉ sử dụng **2 khoảng trắng**, không dùng tab.  
2. **Comment:** Dòng bắt đầu bằng `#` sẽ được bỏ qua khi parse.  
3. **Anchor / Reference:**  
   - `&<name>` dùng để định nghĩa một cấu hình.  
   - `*<name>` dùng để tham chiếu lại cấu hình đó.  
4. **Null value:** Dùng `~` cho các trường không có giá trị.  
5. **Trường bắt buộc:**  
   - `script.test_environments`  
   - `script.test_configurations`  
   - `script.test_sequences`  
   - `script.test_targets`
---

## 📄 Ví dụ tham khảo (`script.yaml`)
> Đây là ví dụ hoàn chỉnh minh họa cấu trúc chuẩn.
> Khi Copilot cần hiểu hoặc gợi ý file YAML, hãy tuân theo ví dụ này.
> Không gợi ý các trường hoặc cấu trúc không có trong ví dụ này. Nếu được yêu cầu xây dựng những thứ không có trong ví dụ, hãy trả lời "Trong tài liệu k có hướng dẫn cụ thể về điều này, vui lòng liên hệ SW ENG để bổ xung!". 

```yaml
# FILE cấu hình chung cho script
script:
  test_environments: # thiết lập cài đặt tải xuống
    - &t650_environment
      downloads: 
        - name: "tesseract-ocr" 
          type: "folder"
          from: "/SFTP.conf.pe.02/V6ENV/tessdata" 
          to: "./setup/tessdata" 
          extract_to: ~
        - name: "image" 
          type: "folder"
          from: "./setup/image" 
          to: "./setup/image"

  test_configurations: # thiết lập config  cho script
    - &t650_configuration
      flags: #
        - enable_camera_window
        - skip_upload_defect_code
      script_information: 
        station_name: "CAMERA STATION" 
        description: "T650P CHECK INFOMATION"

      script_configuration:
        general:
          txt_model: 650m 
          txt_android_build: 3357
          txt_secured_os_core: 015
          txt_android_version: 810
          txt_part_number: 410-32-EUC-6

        fixture_configuration: 
          - nothing: ~

    - &VN6500ABER01_configuration
      flags:
        - enable_camera_window
        - skip_upload_defect_code

      script_information: 
        station_name: "CAMERA STATION" 
        description: "T650P CHECK INFOMATION"

      script_configuration:
        general:
          txt_model: 650m 
          txt_android_build: 3357
          txt_secured_os_core: 015
          txt_android_version: 810
          txt_part_number: 410-32-EUC-6

        fixture_configuration: 
          - nothing: ~    
    
  test_sequences: 
    - &t650_test_sequence
      test_items:
        - name: CHECK_MBS_NO
          steps: 
            - do: if.condition
              with:
                condition: $if {$user_config mes_on} == False
              on_success:
                - do: logger.info
                  with:
                    message: Chế độ debug, bỏ qua check mbsno
                - do: return.PASS

            - do: mes.check_mbs_no 
              with: ~
              on_success:
                - do: return.PASS
                  with: ~
              on_fail:
                  - do: return.FAIL
                    with:
                      error_code: CHECK_MBSNO_FAIL

        - name: GET_MES_INFOMATION
          steps: 
            - do: mes.get_csn 
              with: ~
              on_success:
                - do: return.continue
                  with: ~
              on_fail:
                  - do: return.FAIL
                    with:
                      error_code: GET_CSN_FAIL
            - do: mes.get_control_table
              with: ~
              on_success:
                - do: return.continue
                  with: ~
              on_fail:
                  - do: return.FAIL
                    with:
                      error_code: GET_CONTROL_TABLE_FAIL
            - do: mes.get_mac_list_ex_from_sno_code
              with: ~
              on_success:
                - do: return.PASS
                  with: ~
              on_fail:
                  - do: return.FAIL
                    with:
                      error_code: GET_MAC_LIST_FAIL
                      
  test_targets: 
    - model_id: "VN6500ABKW0B"
      environment: *t650_environment 
      test_config: *t650_configuration 
      test_sequence: *t650_test_sequence 

    - model_id: "VN6500ABER01"
      environment: *t650_environment 
      test_config: *VN6500ABER01_configuration 
      test_sequence: *t650_test_sequence 
```

## 📚 tham khảo
- `test_environments`: tham khảo hướng dẫn cài đặt môi trường để tự động tải xuống: [setup_test_environment.md](./setup_test_environment.md)

- `test_configurations`: tham khảo hướng dẫn cài đặt cấu hình test: [test_configurations.md](./test_configurations.md)  


  - `test_sequences`:  
    - Định nghĩa các chuỗi test, bao gồm các bước (steps) cần thực hiện trong quá trình test.
    - `test_items`: Danh sách các mục kiểm tra (test items).
      - `name`: Tên của mục kiểm tra.
      - `lower_limit`: giới hạn dưới (nếu có).
      - `upper_limit`: giới hạn trên (nếu có).
      - `steps`: Danh sách các bước thực hiện trong mục kiểm tra.
        - `do`: Hành động cần thực hiện.
        - `with`: Tham số cho hành động.
        - `on_success`: Các bước tiếp theo nếu hành động thành công.
        - `on_fail`: Các bước tiếp theo nếu hành động thất bại.
  
  - `test_targets`:  
    - Định nghĩa các mục tiêu test, liên kết model cụ thể với các cấu hình và chuỗi test đã định nghĩa.
    - `model_id`: Mã model thiết bị cần test.
    - `environment`: Tham chiếu đến môi trường test đã định nghĩa trong `test_environments`.
    - `test_config`: Tham chiếu đến cấu hình test đã định nghĩa trong `test_configurations`.
    - `test_sequence`: Tham chiếu đến chuỗi test đã định nghĩa trong `test_sequences`.

## Tham khảo
- hướng dẫn cài đặt các môi trường để tải xuống: [setup_test_environment.md](./setup_test_environment.md)
- hướng dãn viết các bước (steps) trong test sequence: [test_steps_reference.md](./test_steps_reference.md)