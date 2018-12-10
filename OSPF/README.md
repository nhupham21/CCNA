## OSPF

> Tài liệu: OSPF
> 
> Thực hiện: **Phạm Hoàng Nhu**
> 
> Cập nhật lần cuối: **09/12/2018**

### Mục lục
[1. Link-state Protocol](#linkstateprotocol)



[2. OSPF](#ospf)
- [2.1. Tổng quan về OSPF](#tongquanveospf)
- [2.2. Hệ thống phân cấp](#hethongphancap)
- [2.3. Nguyên lý hoạt động của OSPF](#nguyenlyhoatdong)
- [2.4. Cách tính Cost trong OSPF](#cost)
- [2.5. Cấu hình OSPF](#cauhinh)
	+ [2.5.1. Wildcard Mask](#wildcardmask)
	+ [2.5.2. Cấu hình OSPF](#cauhinhospf)
- [2.6. Passive-interface trong OSPF](#passiveinterface)
- [2.7. Authentication trong OSPF](#authentication)

---

<a name="linkstateprotocol"></a>
### 1. Link-state Protocol
* Lịch sử.
	- Một số báo cáo cho rằng Link-state Routing đã được thiết kế và triển khai từ năm 1976-77 bởi một nhóm đến từ Plessey Radar, đứng đầu là Bernard J Harris.
	- Khái niệm **Link-state Routing** lần đầu được công bố vào 1979 bởi John M. McQuillan như là một cơ chế có thể tính toán đường đi nhanh hơn khi các điều kiện trong mạng thay đổi, làm ổn định định tuyến hơn.
	- Sau đó, nghiên cứu tại BBN Technologies đã chỉ ra cách sử dụng kỹ thuật Link-state trong hierarchical system (hệ thống phân cấp - hệ thống mạng được chia thành nhiều khu vực), trong đó các node không cần biết toàn bộ bản đồ mạng, mà chỉ còn biết khu vực mà nó nằm trong đó.
* Một số Link-state Protocol.
	- OSPF (Open Shortest Path First).
	- IS-IS (Intermediate System to Intermediate System)
* Tổng quan.
	- Mỗi Router chạy Link-state Protocol hình thành thông tin về Router, các link kết nối trực tiếp với nó và trạng thái của các link này. Thông tin này được gửi đến tất cả các Router trong network bằng **Mutlicast Message**. Mỗi Router giữ một bản copy các thông tin này, nhưng không thay đổi nó. Từ đó mỗi Router sẽ có được toàn bộ được Bức tranh Network Topology. Mỗi router sẽ tự tính toán độc lập đường đi tốt nhất đến một mạng nào đó.
	- Link-state Protocol dựa trên thuật toán Dijkstra để tìm đường đi tốt nhất.
* Benefit.
	- Hội tụ nhanh.
	- Ít bị **Routing Loop**.
		+ Mỗi Router đều nắm được topology.
		+ Các Link-state Packet được gửi liên tục và có thông báo thừa nhận Ack.
	- Dùng trong thiết kế hệ thống phân cấp có thể tối ưu được tài nguyên.

* Hạn chế.
	- Yêu cầu tài nguyên đáng kể.
		+ CPU (thuật toán Dijkstra tính toán nhiều, đặc biệt khi có nhiều thay đổi).
		+ Memory (Link-state hình thành nhiều bảng).
	- Yêu cầu việc thiết kế phải chặt chẽ.
	- Cấu hình khá phức tạp với nhiều tham số.
	
<a name="ospf"></a>
### 2. OSPF

<a name="tongquanveospf"></a>
#### 2.1. Tổng quan về OSPF
* Lịch sử.
	- OSPF lần đầu được chuẩn hóa vào năm 1989, RFC 1131, được biết là OSPF version 1.
	- Được định nghĩa OSPF version 2 trong RFC 2328 năm 1998 cho IPv4.
	- Bản cập nhật cho IPv6 được xem như là OSPF version 3 trong RFC 5340 năm 2008.
* Tổng quan.
	- Là IGP.
	- Là một Routing Protocol chuẩn mở.
	- Sử dụng thuật toán Dijkstra để tìm đường đi tốt nhất.
	- Sử dụng khái niệm **Area** trong thiết kế và triển khai hệ thống phân cấp.
	- Hỗ trợ mạng Classless, cho phép thiết kế phân cấp với các kỹ thuật VLSM, Route Summarization.
	- Protocol number = 89.
	- AD = 110.
	- Metric phụ thuộc vào Bandwidth.

<a name="hethongphancap"></a>
#### 2.2. Hệ thống phân cấp
![hierarchical_1](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/hierarchical_1.png)

<a name="nguyenlyhoatdong"></a>
#### 2.2. Nguyên lý hoạt động của OSPF


---

### Tài liệu tham khảo:

[1] Tìm hiểu giao thức định tuyến OSPF. http://svuit.vn/threads/bai-14-tim-hieu-giao-thuc-dinh-tuyen-ospf-101/

[2] Introduction to Link State Routing Protocols. http://www.omnisecu.com/cisco-certified-network-associate-ccna/introduction-to-link-state-routing-protocols.php

---

### Hết



