## Layer 2 Switching

> Tài liệu: Layer 2 Switching
> 
> Thực hiện: **Phạm Hoàng Nhu**
> 
> Cập nhật lần cuối: **05/02/2019**

### Mục lục
[1. Ethernet Networking](#ethernetnetworking)
- [1.1. Collision Domain vs. Broadcast Domain](#collisionvsbroadcast)
- [1.2. Transmission Mode](#transmissionmode)
- [1.3. MAC Address](#macaddress)
- [1.4. CAM Table](#camtable)

[2. Switching Devices](#switchingdevices)

[3. Các chức năng của Switch ở Layer 2](#chucnang)
- [3.1. Address Learning](#addresslearning)
	
- [3.2. Forward/Filter Decision](#forwardfilter)
	+ [2.2.2. Forward Frames](#forwardframes)
	+ [2.2.3. Filter Frames](#filterframes)
	
- [3.3. Chống Loop](#chongloop)

[4. Các phương thức chuyển mạch chính](#phuongthucchuyenmach)
- [4.1. Store and Forward](#storeandforward)
- [4.2. Cut-Through](#cutthrough)
- [4.3. Fragment-Free](#fragmentfree)

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
	- 3 byte đầu của nhà sản xuất hay còn gọi OUI (Organizational Unique Identifier). Trước khi sản xuất Ethernet NIC, các nhà sản xuất phải xin IEEE cấp cho giá trị này. Do vậy là nó là duy nhất trên thế giới.
	- 3 byte cuối là nhà sản xuất tự cấp cho sản phẩm của mình một giá trị duy nhất.
	![macaddress](https://github.com/nhuhp/CCNA/blob/master/Layer_2_Switching/img/macaddress.png)
	
* MAC Address có thể được biểu diễn theo nhiều dạng khác nhau.
	- XX-XX-XX-XX-XX-XX
	- XX:XX:XX:XX:XX:XX
	- XXXX.XXXX.XXXX

<a name="camtable"></a>
#### 1.4. CAM Table
* CAM (Content Addressable Memory) Table được Ethernet Switch sử dụng để lưu trữ các thông tin như MAC Address, Port hay VLAN.
* CAM Table có trên tất cả Switch để phục vụ quá trình Layer 2 Switching. Dó đó, các Switch có thể dễ dàng kết nối tốc độ cao và Full-Duplex với các thiết bị khác mà không cần quan tâm đến số lượng thiết bị.
![camtable_1](https://github.com/nhuhp/CCNA/blob/master/Layer_2_Switching/img/camtable_1.png)

* CAM Table hay còn được gọi là MAC Access Table.

![camtable_2](https://github.com/nhuhp/CCNA/blob/master/Layer_2_Switching/img/camtable_2.png)

<a name="switchingdevices"></a>
### 2. Switching Devices
![switching_devices](https://github.com/nhuhp/CCNA/blob/master/Layer_2_Switching/img/switching_devices.png)

<a name="chucnang"></a>
### 3. Các chức năng của Switch ở Layer 2
* Có 3 chức năng chính của Switch ở Layer 2:
	- Address Learning
	- Forward/Filter Decision
	- Chống Loop

<a name="addresslearning"></a>
#### 3.1. Address Learning
* Switch sẽ ghi nhớ **Source MAC** của mỗi frame nhận được trên một Interface và lưu vào bảng MAC.

![addresses_learning](https://github.com/nhuhp/CCNA/blob/master/Layer_2_Switching/img/addresses_learning.png)

<a name="forwardfilter"></a>
#### 3.2. Forward/Forward/Filter Decision

<a name="forwardframes"></a>
##### 3.2.1. Forward Frames
* Khi một frame đi tới switch từ một Interface, **Destination MAC** của frame đó sẽ được so sánh với các Entry trong bảng MAC.
![forward_1](https://github.com/nhuhp/CCNA/blob/master/Layer_2_Switching/img/forward_1.png)

* Nếu Destination MAC của frame đó đã biết và đã lưu vào bảng MAC, thì frame đó chỉ gửi ra Interface tương ứng.
![forward_2](https://github.com/nhuhp/CCNA/blob/master/Layer_2_Switching/img/forward_2.png)

* Nếu Destination MAC của frame đó không có mặt trong bảng MAC, frame đó sẽ được flood ra tất cả các Interface đang hoạt động, trừ Interface nhận frame đó. Nếu có thiết bị nào trả lời frame đã flood, bảng MAC sẽ được cập nhật lại, địa chỉ MAC của thiết bị với Interface tương ứng.
![forward_3](https://github.com/nhuhp/CCNA/blob/master/Layer_2_Switching/img/forward_3.png)

<a name="filterframes"></a>
##### 3.2.2. Filter Frames
* Trường hợp frame được gửi đi trong môi trường Hub.
![filter_1](https://github.com/nhuhp/CCNA/blob/master/Layer_2_Switching/img/filter_1.png)

* Khi đến Hub, frame sẽ được broadcast ra các Interface. Đến Switch, Source MAC của frame sẽ được lưu lại vào bảng MAC và Destination MAC sẽ được kiểm tra cho việc forward đi.
![filter_2](https://github.com/nhuhp/CCNA/blob/master/Layer_2_Switching/img/filter_2.png)

* Xét thấy, Source MAC và Destination MAC của frame đều đến từ 1 Interface nên Switch sẽ không forward frame đến Destination MAC như quá trình Forward nữa. Người ta gọi đây là quá trình Filter.
![filter_3](https://github.com/nhuhp/CCNA/blob/master/Layer_2_Switching/img/filter_3.png)

<a name="chongloop"></a>
#### 3.3. Chống Loop
* Switch sử dụng một số Protocol như Spanning Tree Protocol (STP) để chống hiện tượng Loop gây giảm hiệu suất của hệ thống.

<a name="phuongthucchuyenmach"></a>
4. Các phương thức chuyển mạch chính trên Cisco Switch
* Có 3 phương thức chuyển mạch trên Cisco Switch:
	- Store and Forward
	- Cut-Through
	- Fragment-Free

<a name="storeandforward"></a>
#### 4.1. Store and Forward
* Đây là phương thức chuyển mạch cơ bản nhưng có độ trễ cao.
* Switch sẽ buffer toàn bộ frame vào memory và chạy FCS (Frame Check Sequence) để kiểm tra lỗi.
* Một frame ít hơn 64 byte và lớn hơn 1518 byte được xem như là invalid. Các frame invalid sẽ bị drop.
* Các frame valid sẽ được xử lý và forward để đích.

<a name="cutthrough"></a>
#### 4.2. Cut-Through
* Cut-Through là phương thức có độ trễ thấp nhất.
* Switch sẽ đọc phần Destination Access của frame để xử lý và forward frame.
* Tuy nhiên, phương thức này không kiểm tra lỗi frame. Nên các frame lỗi cũng được xử lý và forward luôn.

<a name="fragmentfree"></a>
#### 4.3. Fragment-Free
* Phương thức này kiểm tra 64 byte đầu tiên của frame.
* Và Switch chỉ xử lý những frame nào có 64 byte đầu valid.

---

### Tài liệu tham khảo:

[1] Collision domain explained. https://geek-university.com/ccna/collision-domain-explained/

[2] Broadcast domain explained. https://geek-university.com/ccna/broadcast-domain-explained/

[3] Collision Detection in CSMA/CD. https://www.geeksforgeeks.org/collision-detection-csmacd/

[4] Transmission Modes in Computer Networks. https://www.geeksforgeeks.org/transmission-modes-computer-networks/

[5] Understanding Ethernet MAC Address. https://www.ccnahub.com/ip-fundamentals/understanding-ethernet-mac-addresses/

[6] Types of Cisco Switches. http://www.ciscopress.com/articles/article.asp?p=2348265&seqNum=2

[7] Difference Between Hub and Switch. https://techdifferences.com/difference-between-hub-and-switch.html

[8] https://www.slideshare.net/surenderrana007/ccna-report

[9] http://vishalp-network.blogspot.com/2010/05/switches.html

---

### Hết
