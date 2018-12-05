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
	+ [3.4.1. Loop trong RIP](#looptrongrip)
	+ [3.4.2. Giới hạn Metric](#gioihanmetric)
	+ [3.4.3. Split - Horizon](#splithorizon)
	+ [3.4.4. Route Poisoning](#routepoisoning-poisonreverse)
	+ [3.4.5. Triggered Update](#triggeredupdate)
	+ [3.4.6. Hold-down Timers](#holddowntimers)
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

* Khi cấu hình định tuyến RIP, các Router kết nối trực tiếp sẽ trao đổi các gói tin với nhau, định kỳ 30s/lần. Gói tin này chứa thông tin (hoặc một phần) bản định tuyến của Router gửi đi.

* Các 2 loại gói tin cơ bản trong quá trình hoạt động của RIP:
	- **RIP Request**: Message được gửi bởi một Router tới một Router khác để yêu cầu gửi cho nó toàn bộ hoặc một phần bảng định tuyến.
	- **RIP Response**: Message được gửi bởi một Router, chứa các thông tin của toàn bộ hoặc một phần bảng định tuyến của nó. Gói tin này không chỉ phản hồi lại cho một RIP Request Message.
	- Lưu ý: Giao thức RIP gốc cũng định nghĩa một số loại gói tin khác. Tuy nhiên không còn được sử dụng và đã được loại bỏ từ RIP v2 và RIPng.
	
* Quá trình hoạt động cụ thể như sau (quá trình được theo dõi bằng cách capture các gói tin bằng Wireshark):
	- Cấu hình RIP:
	![rip_config](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/rip_config.png)

	- Khi cấu hình RIP, Router R1 sẽ gửi RIP Request đến địa chỉ Multicast 224.0.0.9 để yêu cầu các Router láng giềng gửi bảng định tuyến cho nó.  
	![rip_1](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/rip_1.png)
	
	- Và cứ 30s/lần, nó sẽ gửi RIP Response đến 224.0.0.9, chứa thông tin bảng định tuyến của nó. 
	![rip_2](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/rip_2.png)
	
	- Router 2, khi cấu hình RIP, cũng sẽ gửi RIP Request đến 224.0.0.9.
	![rip_3](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/rip_3.png)
	
	- Khi đó, Router 1 và Router 2 sẽ đồng thời gửi cho nhau RIP Response chứa thông tin bảng định tuyến của chúng. 
		+ Khi nhận đươc thông tin bảng định tuyến của Router láng giềng, chúng sẽ lập tức cập nhật lại bảng định tuyến. 
		+ Những mạng nào đã có trong bảng định tuyến sẽ được giữ nguyên.
	![rip_4](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/rip_4.png)
	
	- Cứ như thế, cứ 30s/lần, các Router lại gửi RIP Response đến 224.0.0.9 để cập nhật bảng định tuyến.
	![rip_5](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/rip_5.png)
	
	- Quá trình diễn ra tương tự đối với Router R3. Lưu ý, Router R1 không gửi bảng định tuyến để Router R3 vì R3 không phải là Router kết nối trực tiếp với R1. 
	![rip_6](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/rip_6.png)
	
	![rip_7](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/rip_7.png)
	
	- Router R2 gửi RIP Response cho Router R3.
	![rip_8](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/rip_8.png)
	
	- Router R1 gửi RIP Response cho Router R1.
	![rip_9](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/rip_9.png)
	
	- Cuối cùng, bảng định tuyến của các Router sẽ như sau.
	![rip_after](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/rip_after.png)
	
<a name="metric"></a>
#### 3.3. Cách tính Metric trong RIP
* Đối với RIP, metric được tính bằng Hop-count. Nghĩa là số node layer 3 trên đường đi đến đích. 
* Metric bắt đầu từ 0 cho các mạng connected. Metric sẽ tăng lên **1** khi qua một node.	
![rip_metric](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/rip_metric.png)

<a name="quytacchongloop"></a>
#### 3.4. Bộ quy tắc chống loop của RIP

<a name="looptrongrip"></a>
##### 3.4.1. Loop trong RIP
* Giả sử R3 mất mạng 10.4.0.0, trong bảng định tuyến chỉ còn 10.1.0.0, 10.2.0.0, 10.3.0.0.
![rip_loop_1](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/rip_loop_1.png)

* R2 gửi Update, sẽ gửi lại route đến 10.4.0.0 cho R3 và tăng metric của route này. R3 sẽ học thêm mạng 10.4.0.0 thông qua cổng E0.
![rip_loop_2](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/rip_loop_2.png)

* Tiếp theo, R3 cập nhật bảng định tuyến và gửi cho R2. Lúc này mạng 10.4.0.0 được đưa đến R2 với metric bằng 3.
![rip_loop_3](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/rip_loop_3.png)

* Sau đó, R2 gửi bảng định tuyến tới các Router láng giềng.
![rip_loop_4](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/rip_loop_4.png)

* Cứ tiếp tục như thế thì bảng định tuyến của các Router sẽ thay đổi Metric liên tục. Các route liên tục gửi theo vòng tròn, tạo thành Loop trong mạng.
* Hiện tượng loop ảnh hưởng đến CPU, performance của thiết bị, khiến hệ thống không hoạt động được.
* Vì vậy, để khắc phục hiện tượng Loop khi sử dụng RIP, người ta đã đưa ra các cơ chế chống Loop cho RIP:
	- Giới hạn Metric
	- Split - Horizon
	- Route Poisoning
	- Poison Reverse
	- Triggered Update
	- Các bộ đếm Timers
	
* Lưu ý: Khi bật RIP thì mặc định các cơ chế chống Loop cũng đã hoạt động.

<a name="gioihanmetric"></a>
##### 3.4.2. Giới hạn Metric
* Metric được giới hạn đến 16. Vậy thì Router bật RIP mà thấy mạng nào có Metric = 16 thì mạng sẽ bị loại bỏ mặc dù là mạng Connected.
* Metric = 16 được gọi là **Infinite Metric**. Mỗi Routing Protocol định nghĩa giá trị Infinite Metric khác nhau. Với RIP là 16.
* Do bị giới hạn về metric, nên RIP chỉ được sử dụng cho hạ tầng mạng nhỏ (đường kính < 16 Router).

<a name="splithorizon"></a>
##### 3.4.3. Split - Horizon
* **Split Horizon** là một trong những cơ chế chống loop được sử dụng bởi các Distance-Vector Routing Protocol.
* Nguyên tắc: *Một router sẽ không quảng bá một route trở lại interface mà nó đã học route này từ đó.*
* Mô tả:
	- R3 kết nối trực tiếp với 10.4.0.0/24. Các Router đều chạy RIP, R3 quảng bá mạng 10.4.0.0/24 cho các Router còn lại. R2 nhận được route đến 10.4.0.0/24, cập nhật bảng định tuyến và tiếp tục quảng bá đến R1.
	![split_horizon_1](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/split_horizon_1.png)
	
	- Ta thấy rằng, R2 gửi bảng định tuyến của mình, bao gồm cả mạng 10.4.0.0/24 cho R1 thông qua cổng E0. Khi R1 cập nhật bảng định tuyến, và tiếp tục gửi bảng định tuyến cho R2, Split Horizon sẽ ngăn cản R1 quảng bá 10.4.0.0/24 ngược lại cho R2 thông qua cổng E0 của mình.
	![split_horizon_2](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/split_horizon_2.png)

	- Gói tin RIP Response bắt được trên cổng E0 của R1.
	![split_horizon_3](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/split_horizon_3.png)
	
	- Cơ chế diễn ra tương tự với mạng 10.3.0.0/24.
	
* Split Horizon mặc định enable trên interface. Dùng lệnh "**show ip interface [tên_interface]**" để kiểm tra.
![split_horizon_4](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/split_horizon_4.png)

* Cấu hình:
	```
	Router(config-if)# [no] ip split-horizon
	```
<a name="routepoisoning-poisonreverse"></a>
##### 3.4.4. Route Poisoning và Poison Reverse	
* **Route Poisoning** cũng là một cơ chế chống Loop được sử dụng bởi các Distance-Vector Routing Protocol.
* Nguyên tắc: *Khi một Router xác định được một route connect của nó bị down, nó sẽ quảng bá route bị down này với infinite metric (metric = 16). Những Router nào nhận được quảng bá này, sẽ xét route bị down và loại nó ra khỏi bảng định tuyến của mình.*
* Mô tả:
	- Giả sử mạng 10.4.0.0/24 kết nối với R3 bị down. R3 sẽ chuyển metric của route tới 10.4.0.0/24 thành 16 và quảng bá một "Poisoning Message" cho R2. 
	![routepoisoning_1](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/routepoisoning_1.png)
	
	- Gói tin RIP Response bắt được trên cổng E0 của R3.
	![routepoisoning_2](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/routepoisoning_2.png)
	
	- R2 nhận được quảng bá này, R2 sẽ thực hiện 3 việc:
		+ Xét route tới 10.4.0.0/24 thành Possibly Down.
		![routepoisoning_3](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/routepoisoning_3.png)
		
		+ Gửi Update cho R3 để thông báo rằng nó đã nhận được thông tin 10.4.0.0/24 bị down. Đây chính là cơ chế **Poison Reverse**.
		![routepoisoning_4](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/routepoisoning_4.png)
		
		+ Sau đó, quảng bá cho R1.
		![routepoisoning_5](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/routepoisoning_5.png)
		
		+ R1 nhận Update và trả lời bằng "Poison Reverse".
		![routepoisoning_6](https://github.com/nhuhp/CCNA/blob/master/Dynamic_Routing_RIP/img/routepoisoning_6.png)
		
<a name="triggeredupdate"></a>
##### 3.4.5. Triggered Update
* Router sẽ gửi cập nhật bảng định tuyến khi có sự thay đổi diễn ra trong bảng định tuyến của nó mà không cần phải đợi 30s.

<a name="holddowntimers"></a>
##### 3.4.6. Hold-down Timers

<a name="cacbotimer"></a>
#### 3.5. Các bộ Timers sử dụng trong RIP

<a name="cauhinh"></a>
#### 3.6. Cấu hình RIP

<a name="passiveinterface"></a>
#### 3.7. Passive-interface trong RIP

<a name="ripv1vsripv2"></a>
#### 3.8. RIPv1 vs. RIPv2

---

### Tài liệu tham khảo:

[1] Tìm hiểu giao thức định tuyến RIP. http://svuit.vn/threads/bai-13-tim-hieu-ve-giao-thuc-dinh-tuyen-rip-78/

[2] Routing Information Protocol. https://en.wikipedia.org/wiki/Routing_Information_Protocol

[3] Split Horizon explained. https://geek-university.com/ccna/split-horizon-explained/

[4] Distance Vector and Link State Protocols. https://www.youtube.com/watch?v=ygxBBMztT4U

[5] Distance Vector - Split Horizon, Poison Reverse, Route Poisoning Explained. https://www.youtube.com/watch?v=SkvPNnVzY5A 

---

### Hết



