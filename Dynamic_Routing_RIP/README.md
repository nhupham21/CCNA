## Dynamic Routing, RIP

> Tài liệu: Dynamic Routing, RIP
> 
> Thực hiện: **Phạm Hoàng Nhu**
> 
> Cập nhật lần cuối: **25/11/2018**

### Mục lục
[1. Dynamic Routing](#dynamicrouting)
			
[2. Distance Vector Protocol](#distancevectorprotocol)

[3. RIP](#rip)
- [3.1. Tổng quan về RIP](#tongquanverip)
- [3.2. Nguyên lý hoạt động của RIP](#nguyenlyhoatdong)
- [3.3. Cách tính Metric trong RIP](#metric)
- [3.4. Bộ quy tắc chống loop của RIP](#quytacchongloop)
	+ [3.4.1. Split - Horizon](#splithorizon)
	+ [3.4.2. Route Poisoning](#routepoisoning)
	+ [3.4.3. Poison Reverse](#poisonreverse)
	+ [3.4.4. Triggered Update](#triggeredupdate)
	+ [3.4.5. Hold-down Timers](#holddowntimers)
- [3.5. Các bộ Timers sử dụng trong RIP](#cacbotimer)
- [3.6. cấu hình RIP](#cauhinh)
- [3.7. Passive-interface trong RIP](#passiveinterface)
- [3.8. RIPv1 vs. RIPv2](#ripv1vsripv2)

---

<a name="dynamicrouting"></a>
### 1. Dynamic Routing
* Dynamic Routing: là phương thức định tuyến sử dụng các giao thức định tuyến để trao đổi thông tin định tuyến cho hệ thống mạng. Dynamic Routing sử dụng 2 loại giao thức:
	- EGP (exterior gateway protocol): giao thức định tuyến được sử dụng để trao đổi thông tin định tuyến giữa các AS khác nhau. 
		Path-Vector: chứa các thông tin về gói tin đường đi đến một mạng cụ thể. Ví dụ: BGP.
	- IGP (Interior Gateway Protocol): giao thức định tuyến được sử dụng để trao đổi thông tin định tuyến trong cùng một AS. Trong IGP, được chia thành một số loại:
		+ Distance-Vector: trao đổi thông tin định tuyến bằng cách gửi theo định kỳ, toàn bộ bảng định tuyến sang cho láng giềng kết nối trực tiếp với nó. Ví dụ: RIP.
		+ EIGRP là một advanced Distance-Vector, được cải tiến hơn so với distance-vector điển hình, chẳng hạn như sử dụng cơ chế triggered update, hội tụ nhanh, sử dụng thuật toán DUAL để lựa chọn đường đi,...
		+ Link-state: giao thức định tuyến sử dụng các LSA quảng bá các mạng mà router biết cho nhau, từ đó xây dựng nên được một bản đồ mạng, sử dụng thuật toán Dijkstra để tìm đường đi. Ví dụ như OSPF, IS-IS
		
<a name="distancevectorprotocol"></a>
### 2. Distance Vector Protocol
* Là giao thức Router sử dụng để trao đổi thông tin định tuyến bằng cách gửi theo định kỳ, toàn bộ bảng định tuyến sang cho láng giềng kết nối trực tiếp với nó. Các Router đó sau đó so sánh với bảng định tuyến mà mình hiện có và kiểm tra lại các route của mình với các route mới nhận được, route nào tối ưu hơn sẽ được đưa vào bảng định tuyến. 
* Các giao thức điển hình cho loại này như: RIP, IGRP,...
* Các gói tin update sẽ được gửi theo định kỳ (RIP: 30s, IGRP: 90s).
* Ưu điểm:
	- Dễ cấu hình.
	- Router không phải xử lý nhiều nên không tốn nhiều dung lượng bộ nhớ và CPU có tốc độ xử lý nhanh hơn.
* Nhược điểm:
	- Hệ thống metric quá đơn giản, dẫn đến việc các route được chọn vào bảng định tuyến chưa phải là route tốt nhất.
	- Do các gói tin update được gửi theo định kỳ nên một lượng bandwidth đáng kể sẽ bị chiếm (mặc dù mạng không có nhiều thay đổi).
	- Do Router hội tụ chậm, dẫn đến việc sai lệch trong bảng định tuyến gây nên hiện tượng loop.
	
<a name="rip"></a>
### 3. RIP

<a name="tongquanverip"></a>
#### 3.1. Tổng quan về RIP
* Lịch sử:
	- Xuất hiện vào năm 1970 bởi Xerox như là một phần của bộ giao thức Xerox Networking Services (XNS).
	- RIP được chấp nhận rộng rải trước khi có một chuẩn chính thức được xuất bản.
	- Đến năm 1988 RIP mới được chính thức ban bố trong RFC1058 bởi Charles Hedrick.

* Tổng quan:
	- RIP là giao thức chuẩn mở của IEEE.
	- RIP là một Distance-vector Protocol.
	- RIP sử dụng UDP port 520.
	- RIP có 2 version là RIPv1 (hỗ trợ mạng classful) và RIPv2 (hỗ trợ mạng classless).
	- AD = 120, Metric = Hop-count.
	- Gủi gói update định kỳ 30s/1 lần.
	
<a name="nguyenlyhoatdong"></a>
#### 3.2. Nguyên lý hoạt động của RIP
* Khi chưa cấu hình định tuyến, bảng định tuyến của các Router chỉ chứa các lớp mạng connected.

![rip_connected](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/rip_connected.png)

* Khi cấu hình định tuyến RIP.


<a name="metric"></a>
#### 3.3. Cách tính Metric trong RIP

<a name="quytacchongloop"></a>
#### 3.4. Bộ quy tắc chống loop của RIP

<a name="splithorizon"></a>
##### 3.4.1. Split - Horizon

<a name="routepoisoning"></a>
##### 3.4.2. Route Poisoning

<a name="poisonreverse"></a>
##### 3.4.3. Poison Reverse

<a name="triggeredupdate"></a>
##### 3.4.4. Triggered Update

<a name="holddowntimers"></a>
##### 3.4.5. Hold-down Timers

<a name="cacbotimer"></a>
#### 3.5. Các bộ Timers sử dụng trong RIP

<a name="cauhinh"></a>
#### 3.6. cấu hình RIP

<a name="passiveinterface"></a>
#### 3.7. Passive-interface trong RIP

<a name="ripv1vsripv2"></a>
#### 3.8. RIPv1 vs. RIPv2

---

### Tài liệu tham khảo:

[1] Tìm hiểu giao thức định tuyến RIP. http://svuit.vn/threads/bai-13-tim-hieu-ve-giao-thuc-dinh-tuyen-rip-78/

[2] Routing Information Protocol. https://en.wikipedia.org/wiki/Routing_Information_Protocol

[3] 

---

### Hết



