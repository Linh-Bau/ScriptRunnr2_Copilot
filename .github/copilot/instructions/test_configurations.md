## hướng dẫn config test_configuration trong script.yaml

## 📄 Ví dụ tham khảo (`script.yaml`)
```yaml
test_configurations: # thiết lập config  cho script
    - &t650_configuration
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
``` 


## Giải thích các trường trong `test_configurations`:

- `test_configurations`:  
  - Định nghĩa các cấu hình test, bao gồm các cờ (flags) để kiểm soát chương trình và thông tin cấu hình cụ thể cho từng trạm (station).
  - `flags`: Danh sách các cờ để điều chỉnh hành vi của script.
      - `enable_camera_window`: Chỉ dùng cho trạm sử dụng camera vision.
      - `skip_upload_defect_code`: Bỏ qua việc upload mã lỗi lên MES.
      - `fixture_control`: Dùng để gửi mã FIXTURE_CODE đến MES.
  
  - `script_information`: Thông tin mô tả về script.
    - `station_name`: Tên trạm test. Được hiển thị trên chương trình.
    - `description`: Mô tả, được hiển thị trên chương trình.
  
  - `script_configuration`: Cấu hình, các tham số test sử dụng cho các `steps` trong `test_sequence`.
    
    - `general`: biến dùng chung cho tất cả các fixture. Tên biến là tùy ý do người dùng đặt. Tuy nhiên, nên đặt tên có ý nghĩa để dễ hiểu, tên để theo `lower_case`.
    
    - `fixture_configuration`: Cấu hình riêng cho từng fixture. Dạng danh sách. ví dụ: 
    ```yaml  
      fixture_configuration: 
        - PORT_1: COM1
          PORT_2: COM10
        - PORT_1: COM2
          PORT_2: COM20
        - PORT_1: COM3
          PORT_2: COM30
    ```
      - Tên biến là tùy ý do người dùng đặt. Tuy nhiên, nên đặt tên có ý nghĩa để dễ hiểu, tên để theo `lower_case`.
      - Nếu ở `general`, tên biến trùng với tên biến ở `fixture_configuration`, khi lấy giá trị sẽ ưu tiên lấy giá trị từ `fixture_configuration`. Lưu ý: không nên đặt tên biến trùng nhau như này.

  
## Hướng dẫn
- tên biến trong `script_configuration` có thể được sử dụng trong các bước (steps) của `test_sequence`.
- Các biến trong `general` áp dụng chung cho tất cả các fixture.
- Các biến trong `fixture_configuration` áp dụng riêng cho từng fixture. Ví dụ ở FIXTURE 1 lấy giá trị `PORT_1` là `COM1`, FIXTURE 2 lấy giá trị `PORT_1` là `COM2`.
- Các biến đặt nên có ý nghĩa.