## Layer 2 Switching

> Tài liệu: Layer 2 Switching
> 
> Thực hiện: **Phạm Hoàng Nhu**
> 
> Cập nhật lần cuối: **15/01/2019**

### Mục lục
[1. Ethernet Networking](#ethernetnetworking)
- [1.1. Collision Domain vs. Broadcast Domain](#collisionvsbroadcast)
- [1.2. Transmission Mode](#transmissionmode)
- [1.3. MAC Address](#macaddress)
- [1.4. CAM Table](#camtable)

[2. Switching Devices](#switchingdevices)

[3. Các chức năng của Switch ở Layer 2](#chucnang)
- [3.1. Address Learning](#addresslearning)
	
- [2.2. Forward/Filter Decision](#forwardfilter)
	+ [2.2.2. Forward Frames](#forwardframes)
	+ [2.2.3. Filter Frames](#filterframes)
	
- [3.3. Chống Loop](#chongloop)

[3. Các phương thức chuyển mạch chính](#phuongthucchuyenmach)

[4. Mô hình phân cấp](#mohinhphancap)

[5. ARP](#arp)

---

<a name="ethernetnetworking"></a>
### 1. Ethernet Networking

<a name="collisionvsbroadcast"></a>
#### 1.1. Collision Domain vs. Broadcast Domain
* **Collision Domain** là vùng Mạng nơi mà các Packet có thể xảy ra va chạm với nhau. Va chạm xảy ra khi 2 thiết bị gửi ra 1 Packet cùng một lúc trên cùng một Segment mạng. Các Packet bị va chạm và các thiết bị phải gửi lại, làm giảm hiệu suất Mạng.
	```
	- Collision thường xảy ra trong môi trường Hub, vì mỗi port của Hub đều cùng một Collision Domain.
	- Ngược lại, mỗi port của Bridge, Switch hay Router là một Collision Domain khác nhau.
	```

	![collision](https://github.com/nhuhp/CCNA/blob/master/Layer_2_Switching/img/collision.png)

* **Broadcast Domain** là vùng Mạng chứa các thiết bị có thể đến được với nhau bằng Ethernet Broadcast. Broadcast gửi bởi thiết bị một thiết bị trong Broadcast Domain sẽ không được forward tới thiết bị nằm trong Broadcast Domain khác.
	```
	- Mỗi port của Switch, Hub hay Bridge đều cùng một Broadcast Domain.
	- Mỗi port trên Router là một Broadcast Domain khác nhau.
	```
	![broadcast](https://github.com/nhuhp/CCNA/blob/master/Layer_2_Switching/img/broadcast.png)
	
* **CSMA/CD** (Carrier Sense Multiple Access/Collision Detection) là phương thức MAC (media-access control) được sử dụng rộng rãi trong công nghệ Ethernet/LAN.
	- Giả sử trường hợp có nhiều thiết bị trong một link và chúng có nhu cầu trao đổi dữ liệu với nhau thông qua đường link.
	![csma_1](https://github.com/nhuhp/CCNA/blob/master/Layer_2_Switching/img/csma_1.png)
	
	- Giả sử 2 thiết bị truyền dữ liệu cùng một thời điểm.
	![csma_2](https://github.com/nhuhp/CCNA/blob/master/Layer_2_Switching/img/csma_2.png)
	
	- Va chạm sẽ xảy ra.
	![csma_3](https://github.com/nhuhp/CCNA/blob/master/Layer_2_Switching/img/csma_3.png)
	
	- CSMA/CD sẽ hoạt động như sau:
		+ Đầu tiên, kiểm tra thiết bị đã sẵn sàng truyền dữ liệu hay chưa?
		+ Tiếp theo, lắng nghe đường link có đang bị chiếm hay không? Nếu có, nó sẽ đợi cho tới khi đường link không bị chiếm nữa rồi mới tiếp tục.
		+ Truyền dữ liệu trên đường link.
		+ Khi có va chạm xảy ra, quá trình truyền sẽ dừng lại. 
		+ Các thiết bị đang gửi dữ liệu sẽ gửi thêm một tín hiệu tắc nghẽn (jamming signal) để nói với các host trong Segment rằng có va chạm xảy ra.
		+ Các thiết bị nhận được tín hiệu báo tắc nghẽn sẽ bắt đầu chạy một bộ Timer có giá trị ngẫu nhiên.
		+ Hết bộ Timer này, thiết bị sẽ thử truyền lại dữ liệu và lặp quá trình trên nếu tiếp tục gặp va chạm.
		
	![csma_4](https://github.com/nhuhp/CCNA/blob/master/Layer_2_Switching/img/csma_4.png)	
		
<a name="transmissionmode"></a>
#### 1.2. Transmission Mode
* Transmission Mode là cách truyền dữ liệu giữa 2 thiết bị.
* Có 3 cách truyền:
	- **Simplex Mode**.
	- **Half-Duplex Mode**.
	- **Full-Duplex Mode**.

* Trong Simplex Mode:
	- Hướng giao tiếp là một chiều. Chỉ một trong hai thiết bị có thể truyền dữ liệu, thiết bị còn lại chỉ có thể nhận.
	- Simplex Mode có thể sử dụng toàn bộ khả năng của kênh truyền để gửi dữ liệu theo 1 chiều.
	- Ví dụ: Bàn phím và màn hình truyền thống.
	![simplex_mode](https://github.com/nhuhp/CCNA/blob/master/Layer_2_Switching/img/simplex_mode.png)
	
* Trong Half-Duplex Mode:
	- Mỗi thiết bị có thể truyền và nhận dữ liệu, nhưng không cùng một lúc.
	- Khi một thiết bị đang gửi, thiết bị còn lại chỉ có thể nhận và ngược lại.
	- Half-Duplex Mode được sử dụng trong trường hợp việc truyền dữ liệu theo 2 chiều cùng 1 lúc là không cần thiết.
	- Toàn bộ khả năng truyền của kênh truyền có thể được sử dụng cho mỗi lần truyền.
	- Ví dụ: Bộ đàm không dây.
	![half_duplex_mode](https://github.com/nhuhp/CCNA/blob/master/Layer_2_Switching/img/half_duplex_mode.png)
	
* Trong Full-Duplex Mode:
	- Cả hai thiết bị có thể truyền và nhận đồng thời.
	- Tín hiệu đi theo chiều này chia sẻ khả năng truyền của kênh truyền với tín hiệu đi chiều ngược lại.
	- Ví dụ: Tín hiệu điện thoại.
	![full_duplex_mode](https://github.com/nhuhp/CCNA/blob/master/Layer_2_Switching/img/full_duplex_mode.png)

<a name="macaddress"></a>
#### 1.3. MAC Address
* Ethernet dùng **MAC (Media Access Control) Address** để định danh duy nhất cho một host và để giao tiếp trong môi trường Ethernet.
* MAC Address nằm ở Layer 2 trong mô hình TCP/IP.
* Mỗi Ethernet NIC (Network Interface Card) đều có một MAC Address được đưa vào Firmware, dó đó MAC Address thường còn được gọi là Hardware Address, Physical Address hay Burned-in Address.
* MAC Address dài 48 bit (6 byte).
	- 3 byte đầu của nhà sản xuất.
<a name="switchingdevices"></a>
### 2. Switching Devices


---

### Tài liệu tham khảo:

[1] Collision domain explained. https://geek-university.com/ccna/collision-domain-explained/

[2] Broadcast domain explained. https://geek-university.com/ccna/broadcast-domain-explained/

[3] Collision Detection in CSMA/CD. https://www.geeksforgeeks.org/collision-detection-csmacd/

[4] Transmission Modes in Computer Networks. https://www.geeksforgeeks.org/transmission-modes-computer-networks/

---

### Hết



