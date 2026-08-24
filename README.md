# PBL2_DUT
động cơ 12-24V 20W 1A

### 1. Khối Công Suất (Cầu H Rời - Nguồn Adapter 24V 4A)

Đây là khối gánh dòng trực tiếp, cần linh kiện chính hãng hoặc chất lượng tốt.

| Linh kiện | Thông số / Tên mã | Số lượng | Ghi chú |
| --- | --- | --- | --- |
| *P-MOSFET* | IRF9540 (TO-220) | 2 | Cầu trên (High-side) |
| *N-MOSFET* | IRF540 (TO-220) | 2 | Cầu dưới (Low-side) |
| *BJT NPN* | S8050 hoặc 2N2222 | 4 | Tầng đệm Push-Pull nạp/xả Gate |
| *BJT PNP* | S8550 hoặc 2N2907 | 4 | Tầng đệm Push-Pull nạp/xả Gate |
| *Cách ly quang* | PC817 | 4 | Bắt buộc để bảo vệ an toàn cho ThinkPad T440s |
| *Diode Zener* | 1N4742 (12V - 1W) | 4 | Ghim áp $V_{GS}$ bảo vệ Gate MOSFET |
| *Diode dập xung* | SS34 (SMD) hoặc 1N5822 / UF4007 | 4 | Mắc song song D-S dập xung ngược động cơ |
| *IC Ổn áp* | buck 24V → 12V | 1 | Lấy 24V tạo mức 12V tham chiếu cho Push-pull |
| *Tụ hóa (Bulk cap)* | 1000µF / 35V - 50V | 2 | Lọc nguồn tổng 24V (đặt sát cầu H) |
| *Tụ gốm* | 100nF (104) | 6 | Lọc nhiễu cao tần cho 7812 và chân cấp nguồn |
| *Trở Gate ($R_g$)* | 22Ω - 47Ω (1/4W) | 4 | Chống tự kích dao động chân Gate |
| *Trở hạn dòng / Phân cực* | 220Ω, 4.7kΩ, 10kΩ | ~15 | Hạn dòng LED PC817, định thiên Pull-up/down |
| *Phụ kiện cơ khí* | Terminal 2P 5.08mm | 3 | Cọc cắm dây 24V IN và Động cơ OUT |
| *Tản nhiệt* | Nhôm chữ U + Lót cách điện | 5 | Cho 4 con MOSFET và IC LM7812 |

---

### 2. Khối Điều Khiển Trung Tâm (STM32 - Nguồn USB Laptop)

Khối này nhận nguồn 5V trực tiếp từ cáp USB trong lúc bạn nạp code/debug.

| Linh kiện | Thông số / Tên mã | Số lượng | Ghi chú |
| --- | --- | --- | --- |
| *Vi điều khiển* | Kit STM32F407VET6 | 1 | Xử lý trung tâm |
| *Màn hình* | TFT LCD ILI9488 320x480 | 1 | Nhớ chọn loại hỗ trợ giao tiếp parallel/FSMC |
| *Module RF* | NRF24L01 | 1 | Giao tiếp không dây với tay cầm |
| *Tụ lọc RF* | 10µF - 100µF (Tantalum/Gốm) | 1 | Hàn trực tiếp sát chân VCC/GND của NRF24L01 |
| *Cảm biến đếm vật* | E18-D80NK (NPN) | 1 | Mắt phát hiện phôi trên băng chuyền |
| *Cách ly cảm biến* | PC817 + Trở 10kΩ + Tụ 10nF | 1 | Chuyển mức logic từ cảm biến về 3.3V cho MCU |


---

### 3. Khối Tay Cầm Remote (ESP32 - Nguồn Pin 3.7V)

| Linh kiện | Thông số / Tên mã | Số lượng | Ghi chú |
| --- | --- | --- | --- |
| *Vi điều khiển* | ESP32 (NodeMCU hoặc WROOM) | 1 | Đọc biến trở, nút nhấn và gửi data |
| *Module RF* | NRF24L01 | 1 | Gửi data sang STM32 (Nhớ hàn tụ lọc 10µF) |
| *Nguồn cấp* | Pin Li-ion ICR18650 (3.7V) + Đế | 1 | Nguồn di động cho tay cầm |
| *Mạch sạc & Bảo vệ* | TP4056 (Bản có DW01A + 8205A) | 1 | Bắt buộc dùng bản có IC bảo vệ xả sâu |
| *IC LDO Hạ áp* | AP2112K-3.3 (hoặc LDO dòng cao) | 1 | Dòng $I_{out}$ tối thiểu 600mA để gánh dòng đỉnh ESP32 |
| *Giao diện điều khiển* | Biến trở xoay 10kΩ | 1 | Nối vào ADC để chỉnh tốc độ |
| *Giao diện điều khiển* | Nút nhấn nhả (Tact switch) | 4 | Start/Stop, Đảo chiều, Auto/Manual... |



=================================================
chỉnh sủa lần 2
ở mạch H
buck 24V → 5V hơn LM7805






### 1. Khối Cầu H & Nguồn Cấp (Phần "Cơ bắp")

Khối này đã loại bỏ hoàn toàn diode 1N4007 chậm chạp, giữ lại cặp BJT Push-Pull sống còn và dùng 2 module Buck để tối ưu nhiệt lượng.

| Linh kiện | Tên mã / Thông số | Số lượng | Vai trò trong mạch |
| --- | --- | --- | --- |
| **P-MOSFET** | IRF9540 (TO-220) | 2 | Cầu trên (High-side) điều khiển áp 24V. |
| **N-MOSFET** | IRF540 (TO-220) | 2 | Cầu dưới (Low-side) kéo xuống GND. |
| **BJT NPN** | S8050 | 4 | Nửa trên của tầng đệm Push-pull (bơm dòng mở Gate). |
| **BJT PNP** | S8550 | 4 | Nửa dưới của tầng đệm Push-pull (hút dòng xả Gate). |
| **Cách ly quang** | PC817 | 4 | Cách ly tín hiệu, bảo vệ an toàn tuyệt đối cho vi điều khiển. |
| **Diode dập xung** | 1N5822 | 4 | (Thay thế 1N4007) Xả dòng cảm kháng từ động cơ 24V cực nhanh. |
| **Diode Zener** | 1N4742 (12V - 1W) | 4 | Ghim điện áp $V_{GS}$ ở mức 12V, chống nổ Gate MOSFET. |
| **Module Buck 1** | LM2596 (24V $\rightarrow$ 12V) | 1 | Cấp nguồn 12V tiêu chuẩn cho dàn BJT Push-Pull. |
| **Module Buck 2** | LM2596 (24V $\rightarrow$ 5V) | 1 | Cấp nguồn cho khối STM32, màn hình và cảm biến (khi chạy độc lập). |
| **Tụ hóa (Lọc)** | 1000µF / 35V - 50V | 2 | Lọc nguồn tổng 24V, đặt càng sát mạch cầu H càng tốt. |
| **Tụ gốm / Kẹo** | 104 (100nF) | 6 | Lọc nhiễu cao tần ở các chân cấp nguồn. |
| **Điện trở** | 22Ω - 47Ω (1/4W) | 4 | Nối từ điểm chung BJT vào chân Gate chống dao động. |
| **Điện trở** | 220Ω, 1kΩ, 4.7kΩ, 10kΩ | ~20 | Hạn dòng cho LED, phân cực BJT, kéo Pull-up/Pull-down. |
| **Cơ khí / Tản nhiệt** | Terminal 2P, Nhôm chữ U | Tùy ý | Gắn cọc dây, tản nhiệt cho dàn MOSFET. |

---

### 2. Khối Điều Khiển Trung Tâm (Phần "Não bộ")

| Linh kiện | Tên mã / Thông số | Số lượng | Vai trò trong mạch |
| --- | --- | --- | --- |
| **Vi điều khiển** | Kit STM32F407VET6 | 1 | Não bộ trung tâm xử lý dữ liệu và băm xung PWM. |
| **Màn hình** | TFT LCD ILI9488 320x480 | 1 | Giao tiếp chuẩn Parallel/FSMC để quét hình tốc độ cao. |
| **Module RF** | NRF24L01 | 1 | Nhận tín hiệu điều khiển không dây. |
| **Tụ hóa (RF)** | 10µF - 100µF | 1 | **Bắt buộc:** Hàn trực tiếp sát chân VCC/GND của NRF24L01 để chống nhiễu, rớt sóng. |
| **Cảm biến** | E18-D80NK (NPN) | 1 | Phát hiện vật/phôi. Chạy nguồn 5V. |
| **Cách ly cảm biến** | PC817 + Trở 10kΩ | 1 | Chuyển mức logic 5V từ cảm biến về mức 3.3V an toàn cho chân STM32. |

---

### 3. Khối Tay Cầm Remote (Phần "Điều khiển")

| Linh kiện | Tên mã / Thông số | Số lượng | Vai trò trong mạch |
| --- | --- | --- | --- |
| **Vi điều khiển** | ESP32 (NodeMCU / WROOM) | 1 | Đọc biến trở, nút nhấn. |
| **Module RF** | NRF24L01 | 1 | Gửi data sang STM32 (Cũng cần hàn thêm tụ 10µF sát chân nguồn). |
| **Nguồn di động** | Pin Li-ion 18650 (3.7V) + Đế | 1 | Cấp năng lượng cho tay cầm. |
| **Mạch sạc pin** | TP4056 (Bản có bảo vệ) | 1 | Có chip DW01A bảo vệ xả sâu, sạc qua cổng Type-C/MicroUSB. |
| **LDO Hạ áp** | AMS1117-3.3 hoặc AP2112K | 1 | Ổn áp từ 3.7V-4.2V của pin xuống 3.3V cấp cho ESP32 và NRF24L01. |
| **Giao diện** | Biến trở 10kΩ | 1 | Vặn tay điều chỉnh tốc độ PWM. |
| **Nút nhấn** | Tact switch (Nút nhả) | 4 | Gán các chức năng: Start/Stop, Đảo chiều, v.v. |

---

**Một chút lưu ý nhỏ trước khi cắm mỏ hàn:**
Nhớ mua dư MOSFET (IRF540/9540) và cách ly quang (PC817) thêm khoảng 2-3 con mỗi loại nhé. Đồ án phần công suất,







