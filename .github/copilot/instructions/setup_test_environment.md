## hướng dẫn cài đặt môi trường để tự động tải xuống.

## 📄 Ví dụ tham khảo (`script.yaml`)

```yaml
  test_environments: # thiết lập cài đặt tải xuống
    - &t650m_environment
      downloads: 
        - name: "tesseract-ocr" 
          type: "folder" # loại tải về: folder, file, compressed
          from: "/SFTP.conf.pe.02/V6ENV/tessdata" 
          to: "./setup/tessdata" 
          extract_to: ~
        - name: "image" 
          type: "folder"
          from: "./setup/t650m_image" 
          to: "./setup/image"

     - &t650p_environment
      downloads: 
        - name: "tesseract-ocr" 
          type: "folder" # loại tải về: folder, file, compressed
          from: "/SFTP.conf.pe.02/V6ENV/tessdata" 
          to: "./setup/tessdata" 
          extract_to: ~
        - name: "image" 
          type: "folder"
          from: "./setup/t650m_image" 
          to: "./setup/image"
```

## Giải thích các trường trong `test_environments`:
- `test_environments`: tham khảo hướng dẫn cài đặt môi trường để tự động tải xuống: [setup_test_environment.md](./setup_test_environment.md)
  - Định nghĩa các môi trường test, bao gồm các file cần tải xuống, thư mục cần thiết lập trước khi chạy test. 
  - Có thể để trống nếu không cần thiết lập môi trường.
  - Ví dụ: Môi trường `t650_environment` tải xuống thư mục `tessdata` và `image`.
  - `downloads`: Danh sách các file/thư mục cần tải xuống.
    - `name`: Tên hiển thị, không ảnh hưởng đến quá trình tải xuống.
    - `type`: Loại, có thể là `file`, `folder`, hoặc `compressed`.
    - `from`: Đường dẫn SFTP để tải xuống.
      - đường dẫn tuyệt đối đến file, ví dụ thư mục: `/SFTP.conf.pe.02/V6ENV/adb` 
      - đường dẫn tương đối đến file, ví dụ thư mục: `./setup/adb`, tức là thư mục dạng `/SFTP.conf.pe.02/V6/{PRODUCT}/{STATION}/setup/adb`.
    - `to`: Đường dẫn local để tải xuống:
      - đường dẫn tuyệt đối, ví dụ: `C:\\venus\\tools\\adb`
      - đường dẫn tương đối, ví dụ: `./setup/adb`, tức là thư mục dạng `{current_working_dir}\\{PRODUCT}\\{STATION}\\setup\\adb`. Với `{current_working_dir}` là thư mục chứa chương trình ScriptRunner2.
    - `extract_to`: Nếu là file nén, chỉ định thư mục để giải nén. Nếu không giải nén, để `~`.
      - nếu là `null` và `type` là `compressed`, file sẽ được giải nén vào `{current_working_dir}\\{PRODUCT}\\{STATION}\\setup\\{tên file nén}`
      - nếu là đường dẫn dạng `./setup/os` và `type` là `compressed`, file sẽ được giải nén vào `{current_working_dir}\\{PRODUCT}\\{STATION}\\setup\\os`
      - cũng có thể sử dụng đường dẫn tuyệt đối như `C:\\venus\\tools\\os`

## các môi trường đã được thiết lập sẵn:
- `tessdata`: thư mục dữ liệu ngôn ngữ cho Tesseract OCR. Dùng cho các trạm camera vision. Đường dẫn : `/SFTP.conf.pe.02/V6ENV/tessdata`
- `adb`: công cụ Android Debug Bridge. Đường dẫn: `/SFTP.conf.pe.02/V6ENV/adb`
- `AtmelSecureAccess`: ứng dụng 3rd. Dùng để lập trình secure element cho các model có SE. Đường dẫn: `/SFTP.conf.pe.02/V6ENV/AtmelSecureAccess`
  
## Hướng dẫn
- Các môi trường mà dùng riêng cho từng model_id nên được để ở `./setup/{MODEL_ID}`
- Các môi trường mà dùng chung cho nhiều model, chương trình bên thứ 3, khách hàng nên để ở  `/SFTP.conf.pe.02/V6ENV`
- Yêu cầu kỹ sư EE setup các môi trường cần thiết trên SFTP.