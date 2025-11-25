# Summary
- Đây là tài liệu cho executer `pms` và `mt`
  
- `pms` và `mt` sẽ đi chung với nhau, để thực hiện tự động hóa điều khiển phầm mềm `PMS_Simulation_Window_V1.09.exe` (PMS WINDOW) và `PCBA_tests.exe` (MANUFACTORING TOOL).
  
---

## `pms`
- **Tóm tắt chức năng**: Dùng để tự động hóa việc chạy phần mềm PMS WINDOW, thường dùng để ghi MAC vào sản phẩm. Dùng trước khi chạy `mt`.

#### `pms.AUTO` 
- **Tóm tắt chức năng**: Thực hiện
1. Mở PMS WINDOW (nếu chưa mở).
2. Nhập các trường MAC (nếu có).

- **Cú pháp**:
```yaml
do: pms.AUTO
  with:
    working_path: C:\\T650C_SA_K81DL_TOOL20230929\\V1.21.2\PMS_Simulation_Window_V1.09 # đường dẫn đến thư mục chứa PMS_Simulation_Window_V1.09.exe, thư mục này và thư mục chứa MT tool nên cùng cấp.
    exe_name: PMS_Simulation_Window_V1.09.exe # tên file thực thi PMS
    bt_mac: ~ # bt mac cần ghi vào, thường là $context MES[mac], sử dụng mes.GET_MAC_LIST để lấy mac mới từ MES ở item trước đó.
    wifi_mac: ~ # tương tự như bt_mac   
    eth_mac: ~ # tương tự như bt_mac
  on_success: ~
  on_fail: ~
```

- **Lỗi thường gặp**:

---
## `mt`
- **Tóm tắt chức năng**: Dùng để tự động hóa việc chạy phần mềm MANUFACTORING TOOL (chương trình của khách hàng). 

---  
#### `mt.AUTO`
- **Tóm tắt chức năng**: Thực hiện
1. Mở MANUFACTORING TOOL (nếu chưa mở).
2. Nhập mbsno vào trường ICT_SN (tự động loại bỏ ký tự đầu tiên).
3. Ấn nút START.
4. Đợi chương trình test xong.

- **Cú pháp**:
```yaml
- name: MT_AUTO_TEST
  steps: 
    - do: mt.AUTO
      with: 
        working_path: C:\\IAC\\FISFrameworkSystem\\TestTool\\T650P_petro\\V1.21.2 # đường dẫn thư mục chứa PCBA_tests.exe, thư mục này và thư mục chứa PMS WINDOW nên cùng cấp.
        exe_name: PCBA_tests.exe # tên file thực thi MANUFACTORING TOOL
        ict_sn_01: $context input_str # mbsno để nhập vào trường ICT SN 01. Để ~ nếu không sử dụng. Nếu giá trị lấy từ MBSNO thì dùng $context input_str
        ict_sn_02: 8PEeevenus8386 # mbsno để nhập vào trường ICT SN 02. Để ~ nếu không sử dụng. Nếu giá trị lấy từ MBSNO thì dùng $context input_str
      on_success: ~
      on_fail: ~
```

- Chú ý:
  - 1 trong 2 trường `ict_sn_01` hoặc `ict_sn_02` phải có giá trị, nếu không chương trình sẽ báo lỗi.
  - Yêu cầu chương trình `PMS_Simulation_Window_V1.09.exe` đã được mở trước đó.

- **Lỗi thường gặp**:

---
#### `mt.check_log`
- **Tóm tắt chức năng**: Sử dụng kết hợp sau item `mt.AUTO` nhằm thực hiện check log kết quả từ MANUFACTORING TOOL.

- **Cú pháp**:
```yaml
- do: mt.check_log
  with: 
      expect: "ICT PASS"
  on_success: ~
  on_fail: 
    - do: return.FAIL
      with:
        error_code: MT_LOG_CHECK_FAIL
```
- **Lỗi thường gặp**: 

---
#### `mt.check_board_log`
- **Tóm tắt chức năng**: Sử dụng kết hợp sau item `mt.AUTO` nhằm thực hiện check log ô `board_id` kết quả từ MANUFACTORING TOOL.

- **Cú pháp**:
```yaml
- do: mt.check_board_log
  with: 
      board_id: 01 # 01 hoặc 02
      expect: any # chuỗi ký tự mong đợi xuất hiện trong log. Dùng string hoặc re_{string} để sử dụng regex.
  on_success: ~
  on_fail: 
    - do: return.FAIL
      with:
        error_code: MT_BOARD_LOG_CHECK_FAIL
```

- **Lỗi thường gặp**: 

---
#### `mt.check_tasklist`
- **Tóm tắt chức năng**: Sử dụng kết hợp sau item `mt.AUTO` nhằm thực hiện check tasklist từ MANUFACTORING TOOL cho board cụ thể.
  
- **Cú pháp**:
```yaml
- do: mt.check_tasklist
  with: 
    board_id: 01 # 01 hoặc 02
    expect: K81-Firmware,Complete # danh sách các task mong đợi, xem log phần `mt.AUTO` để biết chính xác cách đặt.
  on_success: 
    - do: return.PASS
  on_fail: 
    - do: return.FAIL
      with:
        error_code: K81-Firmware DOWNLOAD FAILED
```
- **Lỗi thường gặp**: