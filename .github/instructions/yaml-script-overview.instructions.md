---
applyTo: "**/*.yaml"
name: yaml-script-rule
description: Giới thiệu các quy tắc chung của script.yaml, đưa ra hướng dẫn về cách tra cứu tài liệu.
---
# Giới thiệu
- Tài liệu này cung cấp:
  - Format chung của file `script.yaml`.
  - Các hướng dẫn về tài liệu tham khảo.

# Tổng quan về script.yaml
- `script.yaml` là file cấu hình chính được sử dụng bởi chương trình `ScriptRunner2` để xác định các bước kiểm tra, các tham số cần thiết, và các hành động mà chương trình sẽ thực hiện.

**FORMAT CHUNG CỦA FILE script.yaml**
- Thụt lề: Sử dụng 2 khoảng trắng cho mỗi cấp độ thụt lề. KHÔNG sử dụng tab.
- Đặt tên khoá (key): sử dụng lower_case với dấu gạch dưới (_) để phân tách từ.
- Comment: Sử dụng dấu `#` để thêm comment vào file yaml. Comment nên được đặt trên dòng riêng hoặc sau giá trị của một khoá.

**CẤU TRÚC CHÍNH CỦA script.yaml**
```yaml
script:
  mes_defect_code: ~
  test_environments: ~ 
  test_configurations: ~
  test_sequences: ~
  test_targets: ~    

```
Hướng dẫn:
- `script.yaml` bao gồm một khoá chính `script`, bên trong có các khoá con như sau:
  - `mes_defect_code`: Danh sách mã lỗi MES.
  - `test_environments`: Các môi trường tải xuống từ SFTP.
  - `test_configurations`: Cấu hình test cho các thiết bị.
  - `test_sequences`: Các bước kiểm tra cụ thể.
  - `test_targets`: Các mục tiêu kiểm tra với các model khác nhau.


Tham khảo:
| Tài liệu | Hướng dẫn cho |
|----------|---------------|
| [mes-defect-code-overview.instructions.md](./mes-defect-code-overview.instructions.md) | Cách định nghĩa và sử dụng mã lỗi MES trong `mes_defect_code`. |
| [test-environments-overview.instructions.md](./test_environments-overview.instructions.md) | Cách định nghĩa và sử dụng `test_environments`. |
| [test-configurations-overview.instructions.md](./test_configurations-overview.instructions.md) | Cách định nghĩa và sử dụng `test_configurations`. |


**Ví dụ minh hoạ đầy đủ.
```yaml
script:
  # danh sách mã lỗi, lấy theo <tên lỗi>:<mã defect code>
  # mes_defect_code có thể null. mes_defect_code: ~
  mes_defect_code:
    DOWNLOAD_K81_FIRMWARE_FAIL : UN01
    DOWNLOAD_K81_APP_FAIL : UN02

  # danh sách môi trường tải xuống từ SFTP
  # test_environments có thể null. test_environments: ~
  # có thể để nhiều môi trường test khác nhau. Ví dụ:
  # test_environments: 
  # - &env_1: ...
  # - &env_2: ...
  test_environments: 
    - &env
      # những item sẽ được tải xuống.
      # là 1 danh sách object có các trường: name, type, from,  to, extract_to
      # có thể dùng &<name> để tham chiếu
      downloads:
        - name: "vtp" 
          # name: tên hiển thị của item tải về, không ảnh hưởng đến quá trình tải
          type: "folder" # loại tải về: folder, file, compressed
          # type có thể là: folder, file, compressed
          from: "/SFTP.conf.pe.02/V6ENV/vtp_ver24011801" 
          # from: đường dẫn trên server sftp, có thể tuyệt đối hoặc tương đối
          # tuyệt đối dạng: /SFTP.conf.pe.02/V6ENV/vtp_ver24011801
          # tương đối dạng: ./setup/vtp_ver24011801
          # kỹ sư EE sẽ tự verify đường dẫn này trên server sftp.
          to: "./setup/vtp" # đường dẫn lưu trữ trên máy local
          # to: đường dẫn lưu trữ trên máy local. Có thể sử dụng tuyệt đối hoặc tương đối
          # tương đối sẽ lưu vào: {thư mục exe}/setup/{PRODUCT}/{STATION}/{MODEL_ID}/{đường dẫn tương đối}
          extract_to: ~
          # dành cho file nén (compressed).
          # để là null (~). Giải nén vào ./setup/{tên file}
          # đường dẫn tương đối
          # đường dẫn tuyệt đối

        # ví dụ item thứ 2.
        - name: "vtp_env" #  thư mục vtp chỉ chứa file vtp, môi trường vtp nên configure riêng theo từng model
          type: "folder" 
          from: "./setup/VN6301ABAR0B" # đường dẫn tương đối
          to: "./setup/vtp" # đường dẫn lưu trữ trên máy local
          extract_to: ~

  # danh sách các cấu hình test
  # có thể có nhiều cấu hình test khác nhau. Ví dụ:
  # test_configurations: 
  #   - &config_1: ...
  #   - &config_2: ...
  # không cho phép null (~)
  test_configurations: 
    - &t650c_configuration
      # Cờ tương tác với ScriptRunner2. 
      # Có thể null.
      # Giá trị là danh sách string. Ví dụ:
      # flags:
      #   - enable_camera_window
      #   - skip_upload_defect_code
      # Các cờ có thể sử dụng:
      # - enable_camera_window: Mở cửa sổ camera. Dành cho trạm sử dụng computer vision.
      # - skip_upload_defect_code: Bỏ qua bước upload mã lỗi lên MES nếu test FAIL.
      flags: ~
      # Thông tin về script
      # Không cho phép null
      script_information: 
        # tên trạm test được hiển thị
        station_name: "T650C K81 DOWNLOAD" 
        # mô tả trạm test
        description: "T650C"
    
      # Cấu hình chung và cấu hình thiết bị, không cho phép null
      script_configuration:
        # cấu hình chung cho toàn bộ Fixture
        general: ~
        # cấu hình riêng cho từng Fixture
        fixture_configuration: ~

  # danh sách các bước test
  # không cho phép null
  test_sequences: 
    - &t650c_test_sequence
      # danh sách các bước test
      # không cho phép null
      test_items:
        # Một item test cụ thể
        - name: CHECK_MBS_NO # tên bước test
          # Giới hạn dưới, có thể null. Hoặc xóa dòng này nếu không cần.
          lower_limit: ~ 
          # Giới hạn trên, có thể null. Hoặc xóa dòng này nếu không cần.
          upper_limit: ~ 
          # Danh sách các bước thực hiện trong item test này
          # không cho phép null
          # Logic luôn phải kết thúc với return.PASS hoặc return.FAIL
          steps: 
            # hành động thực hiện, dạng <executer>.<method> 
            # không cho phép null
            # hàm tên UPPER_CASE sẽ tự return.PASS hoặc return.FAIL
            - do: mes.CHECK_MBS_NO 
              
              # tham số để thực hiện bước test này
              # tùy theo method sẽ có các tham số khác nhau
              # giá trị có thể là null
              # nếu không null, giá trị là danh sách key-value string-string        
              with: ~

              # Có thể null
              # Nếu không null, thì nó là danh sách các hành động cụ thể. Ở dưới có ví dụ.
              on_success: ~
              
              # Có thể null
              on_fail: ~

        - name: MES_GET_ETH_MAC
          steps: 
            - do: mes.GET_MAC_LIST
              with: ~
              on_success: ~
              on_fail: ~

        - name: PMS_AUTO_TEST
          steps: 
            # Một bước test với executer và method cụ thể khác.
            - do: pms.AUTO

              # các tham số để thực hiện item này.
              with:
                working_path: C:\\T650C_SA_K81DL_TOOL20230929\\V1.21.2\PMS_Simulation_Window_V1.09
                exe_name: PMS_Simulation_Window_V1.09.exe
                bt_mac: ~
                wifi_mac: ~
                # cho phép comment
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
              on_success: ~ # nếu method thành công và on_success là null, thì tiếp tục bước tiếp theo
              on_fail: 
                # hành động khi bước test này thất bại
                - do: return.FAIL
                  with:
                    # mã lỗi tự chỉ định.
                    error_code: DOWNLOAD_K81_FIRMWARE_FAIL
            
            - do: mt.check_tasklist
              with: 
                board_id: 01
                expect: K81-APP,Complete
              on_success: 
                # bước test cuối cùng luôn cần phải return.PASS hoặc return.FAIL
                # nếu không có sẽ báo lỗi.
                - do: return.PASS
                  with: ~
              on_fail: 
                - do: return.FAIL
                  with:
                    error_code: DOWNLOAD_K81_APP_FAIL
                      
# danh sách các target test
# không cho phép null
# có thể dùng nhiều target test khác nhau với các model khác nhau.
# Cho phép trộn linh hoạt các env, configuration, test_sequence.
  test_targets: 
    # danh sách các target test cụ thể
    - model_id: VN6501AACAMB | VN6501AACAMB0B # cho phép nhiều model_id, cách nhau bởi dấu |
      environment: ~ # có thể null, hoặc tham chiếu đến môi trường test đã định nghĩa ở trên test_environment: *env
      test_config: *t650c_configuration # không được null
      test_sequence: *t650c_test_sequence # không được null
```