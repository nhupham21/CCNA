## EIGRP

> Tài liệu: EIGRP
> 
> Thực hiện: **Phạm Hoàng Nhu**
> 
> Cập nhật lần cuối: **14/01/2019**

### Mục lục
[1. Tổng quan về EIGRP](#tongquan)

[2. RTP trong EIGRP](#rtp)
	
[3. Nguyên lý hoạt động của EIGRP](#nguyenlyhoatdong)
- [3.1. Thiết lập Adjacency](#thietlapadjacency)
- [3.2. Đưa vào bảng Topology](#duavaobangtopolpgy)
- [3.3. Tìm đường đi](#timduongdi)
	
[4. Các tính metric trong EIGRP](#metric)

[5. Cấu hình EIGRP](#cauhinh)

[6. Load Balancing trong EIGRP](#loadbalancing)

[7. Passive-interface trong EIGRP](#passiveinterface)

[8. Authentication trong EIGRP](#authentication)

---

<a name="tongquan"></a>
### 1. Tổng quan về EIGRP
* Là IGP.
* Là giao thức định tuyến độc quyền của Cisco.
* Là Advanced Distance Vector Routing Protocol.
	- Thiết lập quan hệ với các Router láng giềng.
	- Gửi các thay đổi trong các gói Update.
* Hội tụ rất nhanh.
* Hỗ trợ mạng Classless với các kỹ thuật VLSM, Route Summarization,...
* Sử dụng Reliable Transport Protocol (RTP) của Cisco để vận chuyển các EIGRP Packet đến các Neighbor.
* Sử dụng địa chỉ Multicast (224.0.0.10) và Unicast để trao đổi thông tin định tuyến.
* Protocol ID number = 88.
* AD = 90.
* Hỗ trợ nhiều Protocol Network-layer: IP, Appletalk, ...
* Có thể Load Balancing trên những đường có Metric không bằng nhau.
* Sử dụng thuật toán DUAL để tính toán đường đi tốt nhất.
* Sử dụng 3 bảng để lưu trữ thông tin:
	- Neighbor Table: Chứa thông về EIGRP Neighbor.
	- Topology Table: Chứa thông tin định tuyến được học tuyến các EIGRP Neighbor.
	- Routing Table: Chứa đường đi tốt nhất.

<a name="rtp"></a>
### 2. RTP trong EIGRP
* EIGRP gửi các message mà không sử dụng UDP hay TCP. Thay vào đó, nó sử dụng một Protocol gọi là **Reliable Transport Protocol** (RTP) của Cisco, để giao tiếp với các EIGRP Neighbor. Tránh nhầm lẫn với **Real-Time Transport Protocol** (cũng viết tắt là RTP) được dùng trong VoIP.
* Reliability là tính năng quan trọng của Protocol này, nó được thiết kế để có thể cho phép vận chuyển các gói Update nhanh chóng và theo dõi quá trình nhận dữ liệu.
* Nó cung cấp cơ chế Sequencing và Acknowledgment cho các EIGRP Packet truyền giữa các EIGRP Neighbor mà không có thêm các Windowing hay Congestion Control (Điều khiển Tắc nghẽn), tức là chỉ có 1 Packet có thể gửi đi tại 1 thời điểm.
![rtp](https://github.com/nhupham21/CCNA/blob/master/EIGRP/img/rtp.png)

<a name="nguyenlyhoatdong"></a>
### 3. Nguyên lý hoạt động của EIGRP
* EIGRP hoạt động trải qua 3 giai đoạn.
	- Thiết lập Adjacency
	- Đưa vảo bảng Topology
	- Tìm đường đi
	
<a name="thietlapadjacency"></a>
#### 3.1. Thiết lập Adjacency
* Các Router sẽ gửi gói Hello Packet đến địa chỉ Multicast **224.0.0.10** liên tục mặc định là 5s/lần.
* Các Router nhận được sẽ kiểm tra các thông số và một số Option trong Hello Packet. Nếu các thông số match với nhau, các Router sẽ thiết lập quan hệ Adjacency.
	- **Autonomous System Number**
	- Subnet
	- Tham số K (K1 = K3 = 1, K2 = K4 = K5 = 0)

* Sau khi thiết lập quan hệ Adjacency, các Router sẽ đưa Adjacency của mình vào **Neighbor Table**.
```
Router#show ip eigrp neighbors
```
![neighborstable](https://github.com/nhupham21/CCNA/blob/master/EIGRP/img/neighborstable.png)

<a name="duavaobangtopolpgy"></a>
#### 3.2. Đưa vào bảng Topology
* EIGRP định nghĩa một số khái niệm để tính toán đường đi.
	- **Feasible Distance** (FD): là Metric của Route tính từ Router đang xét đến đích.
	- **Advertised Distance** (AD) hoặc **Reported Distance** (RD): là Metric của Route tính từ Neighbor của Router đang xét đến đích.
	![topology_1](https://github.com/nhupham21/CCNA/blob/master/EIGRP/img/topology_1.png)
	
	- **Successor**: Route có Metric tốt nhất đến mạng đích. Route này sẽ được đặt vào bảng định tuyến.
	- **Feasible Successor**: Route dự phòng trong trường Successor bị down. Router này sẽ được đặt trong bảng Topology.
	- Điều kiện để chọn Successor: Route đến đích có FD nhỏ nhất hay FDmin.
	- Điều kiện để Feasible Successor: 2 điều kiện phải cùng xảy ra:
		+ Route có FD lớn hơn FDmin: FD > FDmin.
		+ Route có AD nhỏ hơn FDmin: AD < FDmin.

* Sau khi thiết lập Adjacency, các Router sẽ tính toán FD và AD của mỗi mạng mà nó có thể tới.
![topology_2](https://github.com/nhupham21/CCNA/blob/master/EIGRP/img/topology_2.png)

* Và sẽ đưa vào bảng Topology.

![topology_3](https://github.com/nhupham21/CCNA/blob/master/EIGRP/img/topology_3.png)

* Dùng lệnh để show các Successor và Feasible Successor. 
```
Router#show ip eigrp topology
```

hoặc lệnh này để show tất các Route có thể có.
```
Router#show ip eigrp topology all-links
```

![topology_4](https://github.com/nhupham21/CCNA/blob/master/EIGRP/img/topology_4.png)

<a name="timduongdi"></a>
#### 3.3. Tìm đường đi
* EIGRP dùng **thuật toán DUAL**, để chọn Successor và Feasible Successor.
* Successor sẽ được đưa vào bảng định tuyến.
![successor](https://github.com/nhupham21/CCNA/blob/master/EIGRP/img/successor.png)

* Nếu có Feasible Successor, thì sẽ được thể hiện trên bảng Topology.
![feasible_successor](https://github.com/nhupham21/CCNA/blob/master/EIGRP/img/feasible_successor.png)

* Trong trường hợp Successor bị down, Feasible Successor sẽ lên làm Successor. Nếu không có Feasible Successor, các Router sẽ tính toán lại đường đi tốt nhất.
* EIGRP có cơ chế **Preempt**. Nghĩa là Successor down, sau đó up lại thì vẫn tiếp tục được làm Successor.

<a name="metric"></a>
### 4. Cách tính Metric trong EIGRP
* Trong EIGRP, Metric được tính dựa trên các thành phần tương tự như IGRP.
	- Bandwidth (Kbps).
	- Delay (10 microseconds).
	- Reliability.
	- Loading.
	- MTU (bytes).

* Tham số K.
	- K1 = K3 = 1
	- K2 = K4 = K5 = 0

* Metric được tính bằng công thức:

![metric_1](https://github.com/nhupham21/CCNA/blob/master/EIGRP/img/metric_1.png)

* Mỗi loại Interface sẽ có các giá trị mặc định Bandwidth và Delay khác nhau.

|Loại Interface|Bandwidth(Kbps)|Delay(10microsecond)|
|:----|----:|----:|
|Serial|1544|2000|
|Ethernet|10000|100|
|Fast Ethernet|100000|10|
|Loopback|8000000|500|

* Ví dụ:
![metric_2](https://github.com/nhupham21/CCNA/blob/master/EIGRP/img/metric_2.png)

* Kiểm tra các thông số của Interface bằng lệnh.
```
Router#show interface [interface-name]
```

![metric_3](https://github.com/nhupham21/CCNA/blob/master/EIGRP/img/metric_3.png)

<a name="cauhinh"></a>
### 5. Cấu hình EIGRP
* Bật EIGRP.
```
Router(config)#router eigrp [as-number]
```

* Cấu hình các Mạng tham gia vào EIGRP.
```
Router(config-router)#network [network-number]
```

hoặc
```
Router(config-router)#network [network-number] [wildcard-mask]
```

* Cấu hình No auto-summary
```
Router(config-router)#no auto-summary
```

* Kiểm tra EIGRP trên bảng định tuyến.
```
Router#show ip route eigrp
```
* Kiểm tra các thông số của EIGRP.
```
Router#show ip protocols
```

<a name="loadbalancing/"></a>
### 6. Load Balancing trong EIGRP
* Mặc định, EIGRP có thể hỗ trợ cân bằng tải trên 4 đường có Metric bằng nhau.
* Ngoài ra, một trong các tính năng của EIGRP là hỗ trợ cân bằng tải đối với những đường có Metric không bằng nhau. Tính năng này chỉ áp dụng cho Successor và Feasible Successor.
* Điều kiện để EIGRP hỗ trợ cân bằng tải trên những đường có Metric không bằng nhau:
	- Các Route có FD < **Variance** X FDmin

* Có thể điều chỉnh giá trị **Variance** này bằng lệnh.
```
Router(config)#router eigrp [as-number]
Router(config-router)#variane [<1-128>]
```


<a name="passiveinterface"></a>
### 7. Passive-interface trong EIGRP
* Tương tự như OSPF, các Router sẽ không gửi các Hello Packet ra các interface được cấu hình Passive-interface. Do đó, ngăn chặn thiết lập quan hệ Adjacency trên các interface này.
* Cấu hình.
```
Router(config)#router eigrp [as-number]
Router(config-router)#passive-interface [interface-name]
```

<a name="authentication"></a>
### 8. Authentication trong EIGRP
* EIGRP sử dụng MD5 Authentication.
* Cấu hình Authentication.
	- Tạo **Key chain**. Key chain không cần giống nhau giữa các Router.
	```
	Router(config)#key chain [key-chain-name]
	```
	
	- Tạo **KeyID**. KeyID phải giống nhau giữa các Router.
	```
	 Router(config-keychain)#key [keyID]
	```
	
	- Tạo **Key String**. Key String dùng để xác thực giữa các Router và phải giống nhau giữa các Router.
	```
	Router(config-keychain-key)#key-string [keystring]
	```
	
	- Thiết lập **Time frame** cho Key.
	```
	Router(config-keychain-key)#accept-lifetime start_time {infinite | end_time | duration seconds}
	
	Router(config-keychain-key)#send-lifetime start_time {infinite | end_time | duration seconds}
	```
	
	- Enable trên interface tham gia EIGRP.
	```
	Router(config)#interface [interface-name]
	
	Router(config-if)#ip authentication mode eigrp [as-number] md5
	
	Router(config-if)#ip authentication key-chain eigrp [as-number] [key-chain-name]
	```
---

### Tài liệu tham khảo:

[1] Tìm hiểu về giao thức định tuyến EIGRP. http://svuit.vn/threads/bai-15-tim-hieu-giao-thuc-dinh-tuyen-eigrp-114/

[2] Reliable Transport Protocol. https://geek-university.com/ccna/reliable-transport-protocol-rtp/

[3] RTP in EIGRP. http://packetlife.net/blog/2009/jan/17/rtp-eigrp/

[4] Understanding EIGRP. http://kwallaceccie.com/eigrp-1/

[5] EIGRP Authentication. https://study-ccna.com/eigrp-authentication-load-balancing/

---

### Hết



