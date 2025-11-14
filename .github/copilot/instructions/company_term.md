# Các thuật ngữ dùng trong script

📑 <b>DANH SÁCH</b>
- MES:
  - Hệ thống MES, có các API để lấy thông tin của sản phẩm, gửi các kết quả, cập nhật dữ liệu lên hệ thống.
  - PRODUCT: thông tin product của trạm test đã được đăng nhập.
  - STATION: thông tin trạm test đã được đăng nhập.
  - MODEL_ID: mã model của trạm test đã được đăng nhập.
  - script.yaml: file script, định nghĩa các bước, môi trường test cho một PRODUCT/STATION/MODEL_ID cụ thể.
  - ScriptRunner2: Hệ thống chạy script, thực thi các bước test được định nghĩa trong script.yaml. Tự động cài đặt môi trường test, thu thập kết quả, gửi dữ liệu lên MES.
  - FIXTURE: ScriptRunner2 có thể chạy đa luồng, mỗi luồng gọi là một FIXTURE, các luồng có thể có các ngữ cảnh test khác nhau.
  - FIXTURE_CODE: Mã thiết bị phần cứng, được gán cho từng FIXTURE.