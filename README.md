# PBL2_DUT
động cơ 12-24V 20W 1A
adapter 24V 4A
tăng tốc 2-3 m/s

cần cập nhật lại danh sách linh kiện ở trong eassyeda
=================================================


### 1. Khối Cầu H & Nguồn Cấp 

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
| **LDO Hạ áp** | AP2112K-3.3(khó hàn nhưng tốt) hoặc HT7833( dễ hàn nhưng khó ổn định) | 1 | Ổn áp từ 3.7V-4.2V của pin xuống 3.3V cấp cho ESP32 và NRF24L01. |
| **Giao diện** | Biến trở 10kΩ | 1 | Vặn tay điều chỉnh tốc độ PWM. |
| **Nút nhấn** | Tact switch (Nút nhả) | 4 | Gán các chức năng: Start/Stop, Đảo chiều, v.v. |

---



----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Toàn bộ mạch điện được chia thành 4 khối chức năng phối hợp nhịp nhàng: **Khối Nguồn**, **Khối Điều khiển & Cách ly**, **Khối Kích Gate (Driver)** và **Khối Động lực Cầu H**.

---

### 1. Khối Nguồn (Buck 24V $\rightarrow$ 12V)

Khối này nhận nguồn chính 24V từ giắc `J_24V` và hạ áp xuống đường 12V ổn định để cấp riêng cho tầng kích Gate.

* **`F1 (Fuse 5x20)`:** Cầu chì bảo vệ ngắt mạch khi có sự cố quá dòng toàn hệ thống.
* **`D2 (SR560)`:** Diode Schottky chống cắm ngược cực nguồn 24V đầu vào.
* **`U2 (470µF/35V)`:** Tụ lọc nguồn đầu vào 24V, san phẳng các xung sụt áp khi đóng cắt.
* **`U21 (MC34063AP)`:** IC nguồn xung hạ áp (Buck Switching Regulator) tích hợp điều khiển PWM/Hysteresis.
* **`R14 (0.22Ω)`:** Điện trở cảm biến dòng đỉnh ($I_{pk}$) bảo vệ quá tải cho switch bên trong IC.
* **`U22 (470pF)`:** Tụ định thì ($C_T$) xác định tần số đóng cắt của MC34063.
* **`L1 (100µH)` & `D15 (1N5822)`:** Cuộn cảm trữ/xả năng lượng và diode Schottky thoát dòng trong chu kỳ ngắt switch.
* **`U3 (470µF/35V)`:** Tụ lọc ngõ ra, san phẳng điện áp DC 12V.
* **`R11 (10k)` & `R15 (1.2k)`:** Cầu phân áp hồi tiếp về Pin 5 để ghim áp ngõ ra ở mức $\approx 11.7\text{V} - 12\text{V}$.

---

### 2. Khối Điều Khiển & Cách Ly Quang

Khối này nhận tín hiệu logic 3.3V/5V từ vi điều khiển (MCU) và chuyển đổi sang mức điện áp kích mà vẫn cách ly hoàn toàn mass điều khiển (`GND_MCU`) với mass động lực (`GND`).

* **`U16 (Header)`:** Giắc cắm kết nối nhận các đường tín hiệu điều khiển `D0, D1, D2, D3` từ MCU.
* **`U24, U25, U26, U27 (PC817B)`:** Optocoupler cách ly quang, truyền tín hiệu bằng ánh sáng LED hồng ngoại.
* **`R16, R17, R20, R21 (250Ω)`:** Điện trở hạn dòng cho LED phát quang bên trong Opto khi nhận mức logic từ MCU.
* **`R18, R19 (10k)`:** Điện trở kéo xuống GND (Pull-down) cho nhánh Low-side, giữ $S0, S1 = 0\text{V}$ khi MCU không kích.
* **`R22, R23 (10k)`:** Điện trở kéo lên 24V (Pull-up) cho nhánh High-side, giữ $S2, S3 = 24\text{V}$ khi MCU không kích (đảm bảo P-MOS luôn khóa an toàn).

---

### 3. Khối Kích Gate Đệm Dòng (Push-Pull Totem-Pole)

MOSFET có tụ ký sinh $C_{iss}$ lớn. Khối BJT đệm dòng đóng vai trò nạp và xả điện tích cực cổng thật nhanh để MOSFET chuyển mạch dứt khoát.

* **Tầng High-Side (Q3/Q6 và Q4/Q8 - 8050 NPN / 8550 PNP):**
* Khi $S2/S3 = 24\text{V}$: Q3 dẫn, kéo Gate lên 24V $\rightarrow V_{GS} = 0\text{V} \rightarrow$ P-MOS **TẮT**.
* Khi $S2/S3 = 12\text{V}$ (Opto mở): Q6 dẫn, xả Gate xuống 12V $\rightarrow V_{GS} = -12\text{V} \rightarrow$ P-MOS **BẬT**.


* **Tầng Low-Side (Q5/Q1 và Q10/Q14 - 8050 NPN / 8550 PNP):**
* Khi $S0/S1 = 0\text{V}$: Q1/Q14 dẫn, xả Gate xuống GND $\rightarrow V_{GS} = 0\text{V} \rightarrow$ N-MOS **TẮT**.
* Khi $S0/S1 = 12\text{V}$ (Opto mở): Q5/Q10 dẫn, nạp Gate lên 12V $\rightarrow V_{GS} = +12\text{V} \rightarrow$ N-MOS **BẬT**.


* **`R9, R10, R24, R13 (47Ω)`:** Điện trở nối tiếp Gate (Gate Resistor) chống dao động ký sinh (Ringing) và giới hạn dòng nạp đỉnh tức thời.

---

### 4. Khối Động Lực Cầu H

* **`U17, U18 (IRF9540 - P-MOSFET)`:** 2 khóa công suất nhánh trên (High-Side), nối trực tiếp với ray nguồn 24V.
* **`Q2, Q12 (IRF540 - N-MOSFET)`:** 2 khóa công suất nhánh dưới (Low-Side), nối trực tiếp về GND.
* **`U4 (1000µF/35V)`:** Tụ Bulk dung lượng lớn đặt sát Cầu H để bù năng lượng tức thời khi băm xung động cơ.
* **`R2, R3, R6, R4 (10k)`:** Điện trở xả/giữ trạng thái an toàn cho Gate-Source ($V_{GS} = 0\text{V}$) khi mất nguồn điều khiển.
* **`D1, D11, D12, D17 (1N4742 - Zener 12V)`:** Diode Zener ghim áp, đảm bảo $V_{GS}$ không bao giờ vượt quá ngưỡng an toàn $\pm20\text{V}$.
* **`D9, D10, D13, D14 (SR560 - 5A Schottky)`:** Dàn Diode dập xung (Freewheeling Diode) xả năng lượng phản kháng (Flyback) từ cuộn dây động cơ về nguồn khi ngắt FET, bảo vệ MOSFET khỏi bị đánh thủng bởi gai điện áp cao.
* **`U19 (Domino 2 chân)`:** Cổng kết nối đưa điện áp đảo chiều ra 2 cực của động cơ DC (`MOTO1`, `MOTO2`).

---
Để thấy rõ bức tranh toàn cảnh, ta hãy bóc tách chi tiết từng mili-ampe dòng điện chạy qua các chân linh kiện trong chu trình **Quay Thuận** ($D2=1, D1=1$) và cơ chế **Dập dòng xả năng lượng** khi ngắt PWM.

---

### Giai đoạn 1: Kích mở nửa Cầu H bên TRÁI (Kích dẫn P-MOSFET U17)

**1. Khối Cách ly (U26 - PC817):**

* MCU xuất tín hiệu mức cao ($3.3\text{V}$) vào chân $D2$.
* Dòng điện từ MCU chạy qua điện trở hạn dòng $R20 (250\Omega) \rightarrow$ vào chân 1 (Anode LED) $\rightarrow$ thoát ra chân 2 (Cathode LED) $\rightarrow$ về $GND\_MCU$. LED bên trong phát sáng.
* Phototransistor bên trong nhận ánh sáng và dẫn bão hòa: dòng điện chạy từ chân 4 (đang nối nguồn $24\text{V}$ qua trở treo $R22$) $\rightarrow$ dẫn thẳng qua chân 3 (nối về $12\text{V}$).
* **Kết quả:** Điểm $S2$ bị kéo tụt từ $24\text{V}$ xuống mức **$12\text{V}$**.

**2. Khối Đệm dòng Push-Pull (Q3 / Q6):**

* Điểm $S2 = 12\text{V}$ cắm vào cực Base của cả $Q3$ (NPN) và $Q6$ (PNP):
* $Q3$ (8050 NPN): Chân Emitter đang ở mức cao hơn Base $\rightarrow Q3$ **khóa ngắt hoàn toàn**.
* $Q6$ (8550 PNP): Chân Emitter (nối với Gate của U17) có áp ban đầu $24\text{V}$, chân Base ở $12\text{V}$ ($V_{EB} > 0.7\text{V}$) $\rightarrow Q6$ **mở bão hòa**.


* **Đường xả Gate:** Điện tích từ tụ ký sinh cực Gate của $U17$ phóng qua $R9 (47\Omega) \rightarrow$ đi từ Emitter sang Collector của $Q6 \rightarrow$ thoát về nguồn **$12\text{V}$**.
* **Kết quả:** Điện áp chân Gate của $U17$ bị kéo tụt xuống mức **$12\text{V}$**.

**3. Khối Động lực (P-MOSFET U17):**

* Điện áp Source $V_S = 24\text{V}$, điện áp Gate $V_G = 12\text{V} \rightarrow$ Điện áp điều khiển đạt $V_{GS} = 12\text{V} - 24\text{V} = \mathbf{-12\text{V}}$.
* Với $V_{GS} = -12\text{V}$ (âm hơn ngưỡng mở $V_{GS(th)}$), kênh dẫn $P$ mở toang: $U17$ chuyển sang trạng thái dẫn bão hòa hoàn toàn.
* Cực Drain của $U17$ (điểm $MOTO2$) được nối thẳng lên đường nguồn **$24\text{V}$**.

---

### Giai đoạn 2: Kích mở nửa Cầu H bên PHẢI (Kích dẫn N-MOSFET Q12)

**1. Khối Cách ly (U24 - PC817):**

* MCU xuất tín hiệu mức cao ($3.3\text{V}$) vào chân $D1$.
* Dòng kích chạy qua $R16 (250\Omega) \rightarrow$ làm sáng LED bên trong $U24$.
* Phototransistor mở: kéo dòng từ nguồn $12\text{V}$ (chân 4) $\rightarrow$ tràn qua chân 3 $\rightarrow$ nạp vào điểm $S1$.
* **Kết quả:** Điểm $S1$ dâng từ $0\text{V}$ lên mức **$12\text{V}$** (vượt qua điện trở xả $R18$).

**2. Khối Đệm dòng Push-Pull (Q10 / Q14):**

* Điểm $S1 = 12\text{V}$ cắm vào cực Base của $Q10$ (NPN) và $Q14$ (PNP):
* $Q14$ (8550 PNP): Base cao hơn Emitter $\rightarrow Q14$ **khóa ngắt**.
* $Q10$ (8050 NPN): Base nhận $12\text{V}$ trong khi Emitter đang ở $0\text{V}$ ($V_{BE} > 0.7\text{V}$) $\rightarrow Q10$ **mở bão hòa**.


* **Đường nạp Gate:** Dòng từ nguồn $12\text{V}$ chạy qua Collector $\rightarrow$ sang Emitter của $Q10 \rightarrow$ qua trở hạn dòng $R13 (47\Omega) \rightarrow$ nạp thẳng vào chân Gate của $Q12$.
* **Kết quả:** Chân Gate của $Q12$ được nạp đầy lên mức **$12\text{V}$**.

**3. Khối Động lực (N-MOSFET Q12):**

* Điện áp Gate $V_G = 12\text{V}$, điện áp Source $V_S = 0\text{V}$ ($GND$) $\rightarrow V_{GS} = \mathbf{+12\text{V}}$.
* Kênh dẫn $N$ mở bão hòa hoàn toàn.
* Cực Drain của $Q12$ (điểm $MOTO1$) được kéo sập thẳng xuống đường **$GND$ ($0\text{V}$)**.

---

### Giai đoạn 3: Dòng điện động lực chạy qua Động cơ

Khi cả $U17$ và $Q12$ cùng mở:

* **Hành trình dòng điện:**

$$\text{Nguồn 24V} \longrightarrow \text{Chân S qua D của U17} \longrightarrow \text{Cọc 2 Domino (MOTO2)} \longrightarrow \text{Cuộn dây Động cơ} \longrightarrow \text{Cọc 1 Domino (MOTO1)} \longrightarrow \text{Chân D qua S của Q12} \longrightarrow \text{GND}$$


* Động cơ nhận trọn vẹn điện áp $24\text{V}$ và quay thuận hết công suất.

---

### Giai đoạn 4: Khi ngắt PWM (Bảo vệ dập xung cuộn cảm)

Khi MCU hạ tín hiệu $D1$ xuống $0\text{V}$ (ngắt $Q12$ để điều tốc):

1. **Khóa FET:** $S1$ tụt về $0\text{V} \rightarrow Q14$ dẫn kéo Gate $Q12$ xả xuống $GND \rightarrow Q12$ lập tức **TẮT**.
2. **Hiện tượng cảm ứng:** Cuộn dây bên trong động cơ tích trữ từ trường sẽ sinh ra một suất điện động tự cảm chống lại sự giảm dòng, khiến cực $MOTO1$ bị đẩy điện áp vọt lên cực cao ($> 40\text{V}$).
3. **Đường xả dập xung qua Diode D10:**
* Điện áp tại $MOTO1$ cao hơn $24\text{V} \rightarrow$ Diode Schottky **$D10 (SR560)$** lập tức bị phân cực thuận.
* Dòng điện tự cảm phóng thẳng từ $MOTO1 \rightarrow$ qua $D10 \rightarrow$ trả ngược năng lượng về thanh ray nguồn $24\text{V}$ và nạp vào tụ đệm $U4 (1000\mu\text{F})$.
* Toàn bộ gai điện áp cao được dập tắt an toàn, bảo vệ cực Drain của $Q12$ không bị đánh thủng.



