## OSPF

> Tài liệu: OSPF
> 
> Thực hiện: **Phạm Hoàng Nhu**
> 
> Cập nhật lần cuối: **12/12/2018**

### Mục lục
[1. Link-state Protocol](#linkstateprotocol)

[2. OSPF](#ospf)
- [2.1. Tổng quan về OSPF](#tongquanveospf)
- [2.2. Hệ thống phân cấp](#hethongphancap)
	+ [2.2.1. Hierarchical Topology](#hierarchicaltopology)
	+ [2.2.2. Router Roles trong OSPF Hierarchical Topology](#routerroles)
- [2.3. Network Types](#networktypes)
	+ [2.3.1. DR\BDR Roles](#drbdrroles)
		+[2.3.1.1. DR-BDR-DROther](#drbdrdrother)
		+[2.3.1.2. Cách giao tiếp giữa DR-BDR-DROther](#giaotiep)
		+[2.3.1.3. Bầu chọn DR-BDR-DROther](#bauchon)
	+ [2.3.1. Point-to-Point Network](#pointtopoint)
	+ [2.3.2. Multi-access Broadcast Network](#multiaccess)
	+ [2.3.3. Non-broadcast Multi-access Network](#nbma)
- [2.4. Nguyên lý hoạt động của OSPF](#nguyenlyhoatdong)
	+ [2.4.1. Chọn Router ID](#routerid)
	+ [2.4.2. Thiết lập Adjacency](#thietlapddjacency)
	+ [2.4.3. ](#tinhtoanduongdi)
	+ [2.4.4. Xây dựng và duy trì bảng định tuyến](#routingtable)
- [2.5. Cách tính Metric trong OSPF](#metric)
- [2.6. Cấu hình OSPF](#cauhinh)
	+ [2.6.1. Wildcard Mask](#wildcardmask)
	+ [2.6.2. Cấu hình OSPF](#cauhinhospf)
- [2.7. Passive-interface trong OSPF](#passiveinterface)
- [2.8. Authentication trong OSPF](#authentication)

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
	- OSPF lần đầu được chuẩn hóa vào năm 1989, RFC 1131, được xem như là OSPF version 1.
	- OSPF version 2 được thay thế năm 1991 trong RFC 1247.
	- Kể từ đó có nhiều bản sửa đổi OSPF version 2 trong RFC 1583, 2178 và 2328.
	- Bản cập nhật cho IPv6 được xem như là OSPF version 3 trong RFC 5340 năm 2008.
* Tổng quan.
	- Là IGP.
	- Là một Routing Protocol chuẩn mở.
	- Sử dụng thuật toán Dijkstra để tìm đường đi tốt nhất.
	- Sử dụng khái niệm **Area** trong thiết kế và triển khai hệ thống phân cấp.
	- Hỗ trợ mạng Classless, cho phép thiết kế phân cấp với các kỹ thuật VLSM, Route Summarization.
	- Giao tiếp với các peer bằng các bảng tin Link-state Advertisement (LSA).
	- Protocol ID number = 89.
	- AD = 110.
	- Metric phụ thuộc vào Bandwidth.

<a name="hethongphancap"></a>
#### 2.2. Hệ thống phân cấp

<a name="hierarchicaltopology"></a>
##### 2.2.1. Hierarchical Topology
* Như đã biết, OSPF trao đổi với các Peer bằng các bảng tin LSA được gửi liên tục. Một khi có thay đổi, các Router phải trao đổi thông tin định tuyến để đồng bộ và cập nhật LSDB. Đối với vùng AS nhỏ, số lượng Router ít, số lượng trao đổi giữa các Router là không đáng kể.
* Tuy nhiên, đối với những AS lớn hơn, số lượng Router có thể lên đến hàng trăm, số lượng thông tin trao đổi cần để cập nhật LSDB cũng tăng lên. Điều này có thể làm cho các Router chạy OSPF bị suy giảm hiệu suất. Lý do là các Router chạy OSPF vừa phải trao đổi thông tin định tuyến với các Peer, vừa phải LSDB và bảng định tuyến khá lớn.
* Để phục vụ các hệ thống mạng lớn hơn, OSPF hỗ trợ sử dụng cho "**Hierarchical Topology**" hay Mô hình phân cấp. Với kỹ thuật này, AS không còn là một cấu trúc đơn, phẳng (flat structure) của nhiều Router kết nối lại với nhau. Thay vào đó, AS sẽ được chia thành nhiều kiến trúc, gọi là ***Area***. Mỗi Area sẽ chứa một số lượng các Router và Mạng liên tục nhau. Các Area này được đánh số và được quản lý độc lập bởi các Router trong Area. Có thể xem các Area như là các "AS con" (sub-AS), các sub-AS nằm trong AS. 
* Trong chương trình CCNA, người ta phân chia 2 loại Area chính trong mô hình phân cấp của OSPF.
	- **Backbone Area** hay còn gọi là **Area 0**: là vùng "Xương sống" của OSPF. Là vùng kết nối các Area khác với nhau. Hay nói cách khác, các Area muốn trao đổi được với nhau, đều phải thông qua Backbone Area, hoặc dùng kỹ thuật **Virtual Link** (chương trình CCNP). Trong hệ thống sử dụng OSPF, chỉ có một Backbone Area duy nhất.
	- **Non-Backbone Area**: các vùng được khác Backbone Area. Được đánh số. Dựa vào chức năng của các Non-Backbone Area mà người ta cũng chia thành các Area (chương trình CCNP).
![hierarchical_1](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/hierarchical_1.png)

* Các Router nằm trong một Area chỉ trao đổi và duy trì LSDB về các Router và mạng trong Area đó. 
* Các Area có thể liên kết nối với nhau, nên thông tin định tuyến có thể chia sẻ giữa các Area và toàn bộ AS. Các Router thuộc hơn một Area thì sẽ duy trì thông tin LSDB cho mỗi Area mà nó thuộc về, và nó là cũng cầu nối giữa các Area để chia sẻ thông tin định tuyến.
* Benefit.
	- Giảm được nhu cầu sử dụng Memory, CPU và tăng hiệu suất của thiết bị.
		+ Do các Router chỉ duy trì LSDB với các Router trong Area, nên khi có thay đổi xảy ra, lượng thông tin trao đổi mà Router xử lý cũng ít hơn. 
	- Giảm được số lượng Entry trong bảng định tuyến của Router.
		+ Cũng xuất phát từ việc các Router chỉ duy trì với LSDB với các Router trong Area.
		+ Áp dụng các kỹ thuật **Route Summarization**, **Route Filtering** đối với các Router kết nối liên Area, có thể tối giản được các route đưa vào bảng định tuyến, đồng thời ngăn chặn, hoặc lọc đi những route không mong muốn.
	- Hệ thống được phân hóa rõ ràng, dễ dàng quản lý.
		+ Khi có thay đổi hoặc xảy ra xử cố, dễ dàng phân vùng và có thể tránh tác động tới các vùng khác.

<a name="routerroles"></a>
##### 2.2.2. Router Roles trong OSPF Hierarchical Topology
* Trong Mô hình phân cấp, các Router sẽ không còn "peer" với nhau trong một nhóm nữa. Thay vào đó, dựa vào vị trí và dạng kết nối, các Router sẽ đóng vai trò (Role) khác nhau. 
![routerrole_1](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/routerrole_1.png)

* **Internal Router** (IR): Là những Router chỉ connected với các Router khác hoặc các mạng trong một Area. Chúng chỉ duy trì LSDB cho Area đó và không cần biết đến topology của vùng khác.
	- Ví dụ: Router A là Internal Router của Area 0; Router F là Internal Router của Area 1; ...
* **Area Border Router** (ABR): Là những Router connected với các Router khác hoặc các mạng trong nhiều hơn một Area. Chúng duy trì LSDB cho mỗi Area mà nó thuộc về. Các Area muốn liên kết với Area khác phải thông qua ABR.
	- Ví dụ: Router C, D là các ABR.
* **Autonomous System Border Router** (ASBR): Là những Router kết nối *OSPF Domain* và các *Routing Domain* khác như RIP, EIGRP,ERP,...
	- Ví dụ: Router B, H là các ASBR.
* *Lưu ý*: Kỹ thuật **Route Summarization** chỉ có thể cấu hình trên ABR và ASBR. Là nơi các Area hay Domain kết nối với nhau. Có thể kết hợp thêm các kỹ thuật **Route Filtering**, **Path Control** để tối ưu route.
* **Backbone Router**: Là những Router có kết nối trong Backbone Area. Các Router này biết tất cả chi tiết của các AS. Các Backbone Router cũng nắm các thông tin về các Area khác nhưng ở dưới dạng "summary".
	- Dựa vào định nghĩa trên, ta có: Router A, B, C, D, H là những Backbone Router.
	- Tuy nhiên, có ý kiến cho rằng, Backbone Router là những Router nằm trong Backbone Area và chỉ có kết nối đến các Backbone Router khác. Theo quan điểm này thì chỉ có Router A là Backbone Router.
	
<a name="networktypes"></a>	
#### 2.3. Network Types

* Có 3 loại môi trường mạng (**Network Types**) được định nghĩa trong OSPF:
	- Point-to-Point Network
	- Broadcast Multi-access Network
	- Non-Broadcast Multi-access Network
	
<a name="drbdrroles"></a>	
##### 2.3.1. DR\BDR Roles

<a name="drbdrdrother"></a>
###### 2.3.1.1. DR-BDR-DROther
* Giả sử, khi trao đổi LSA, một Router phải nói chuyện với tất cả các Router còn lại. Như vậy tất cả các kết nối là khá lớn. Có thể sẽ gây ảnh hưởng đến traffic của hệ thống.
![multiaccess_2](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/multiaccess_2.png)
	
* Do đó, OSPF đã định nghĩa ra **Designated Router**. Designated Router (DR) sẽ là điểm trung tâm cho việc trao đổi thông tin định tuyến giữa các Router trong miền Broadcast Multi-access Network.
	- DR thiết lập neighbor với các Router còn lại.
	- DR thu thập và gửi đi các thông tin LSA cho các Router.
![multiaccess_3](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/multiaccess_3.png)

* **Backup Designated Router** (BDR) là Router dự phòng cho DR. Nếu trường hợp DR không hoạt động thì BDR sẽ chiếm quyền và trở thành DR.
	- BDR cũng thiết lập neighbor với các Router còn lại.
	- BDR không thu thập và gửi đi các thông tin LSA cho các Router, ngoại trừ BDR.
	- BDR có một bộ timer để theo dõi DR. Nếu bộ timer chạy hết mà DR không trả lời cho BDR thì BDR sẽ xem như DR bị down và tiến hành chiếm quyền trở thành DR.

* **DROther** là các Router còn lại, không phải là DR cũng không phải là BDR.

<a name="giaotiep"></a>
###### 2.3.1.2. Cách giao tiếp giữa DR - BDR - DROther.
	- Các OSPF Router gửi các Hello Packet đến địa chỉ Multcast **224.0.0.5**.
	- Khi có thay đổi, các DROther gửi Update Packet đến địa chỉ Multcast **224.0.0.6**, trong DR và BDR lại gửi Update đến **224.0.0.5**.
	
<a name="bauchon"></a>
###### 2.3.1.3. Bầu chọn DR - BDR
* Việc bầu chọn DR - BDR diễn ra trong 1 segment mạng.
* Việc bầu chọn như sau:
	- Xét **Priority**:
		+ Priority được cấu hình trên Interface tham gia vào OSPF trong Segment.
		```
		Router(config)#interface [interface-name]
		Router(config-if)#ip ospf priority [1-255]
		```
		+ Priority mặc định là **1** trên tất cả các Router.
		+ Router có Priority cao nhất sẽ giữ vai trò làm DR.
		+ Router có Priority cao thứ hai sẽ giữ vai trò làm BDR.
		+ Các Router còn lại có Priority thấp hơn sẽ là DROther. Các Router có Priority = 0 không tham gia bầu chọn, tức cũng trở thành DROther.
	
	- Xét **Router ID**: nếu Priority chưa được cấu hình thì xét đến Router ID.
	
<a name="pointtopoint"></a>	
##### 2.3.1. Point-to-Point Network
* Là một kết nối của 2 Router.
* Sử dụng serial link (HDLC, PPP).
* Không có sự bầu chọn Designated Router (DR) và Backup Designated Router (BDR).
* Các Hello Packet đều được gửi đến địa chỉ Multcast 224.0.0.5.
![pointtopoint_1](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/pointtopoint_1.png)

<a name="multiaccess"></a>
##### 2.3.2. Broadcast Multi-access Network
* Môi trường Ethernet.
* Giả sử, khi trao đổi LSA, một Router phải nói chuyện với tất cả các Router còn lại. Như vậy tất cả các kết nối là khá lớn. Có thể sẽ gây ảnh hưởng đến traffic của hệ thống.
![multiaccess_2](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/multiaccess_2.png)
	
* Do đó, OSPF đã định nghĩa ra **Designated Router**. Designated Router (DR) sẽ là điểm trung tâm cho việc trao đổi thông tin định tuyến giữa các Router trong miền Broadcast Multi-access Network.
	- DR thiết lập neighbor với các Router còn lại.
	- DR thu thập và gửi đi các thông tin LSA cho các Router.
![multiaccess_3](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/multiaccess_3.png)

* **Backup Designated Router** (BDR) là Router dự phòng cho DR. Nếu trường hợp DR không hoạt động thì BDR sẽ chiếm quyền và trở thành DR.
	- BDR cũng thiết lập neighbor với các Router còn lại.
	- BDR không thu thập và gửi đi các thông tin LSA cho các Router, ngoại trừ BDR.
	- BDR có một bộ timer để theo dõi DR. Nếu bộ timer chạy hết mà DR không trả lời cho BDR thì BDR sẽ xem như DR bị down và tiến hành chiếm quyền trở thành DR.

* **DROther** là các Router còn lại, không phải là DR cũng không phải là BDR.
* Cách giao tiếp giữa DR - BDR - DROther.
	- Các OSPF Router gửi các Hello Packet đến địa chỉ Multcast **224.0.0.5**.
	- Khi có thay đổi, các DROther gửi Update Packet đến địa chỉ Multcast **224.0.0.6**, trong DR và BDR lại gửi Update đến **224.0.0.5**.

* Bầu chọn DR - BDR.
	- Việc bầu chọn DR - BDR diễn ra trong 1 segment mạng.
	- Việc bầu chọn như sau:
		+ Xét **Priority**: Router có Priority cao nhất s
		
<a name="nbma"></a>
##### 2.3.3. Non-Broadcast Multi-access Network


<a name="nguyenlyhoatdong"></a>
#### 2.4. Nguyên lý hoạt động của OSPF

<a name="routerid"></a>	
##### 2.4.1. Chọn Router ID
* Trong OSPF, mỗi Router cần phải có một **Router ID**. Router ID được dùng để cung cấp một định danh duy nhất trong môi trường OSPF.
* Router ID có dạng là một **IPv4 Address** A.B.C.D, 32 bit, nhưng không phải là IP.
* Router ID được chọn theo thứ tự như sau: 
	- 1. Cấu hình bằng lệnh.
	```
	Router(config)#router ospf [process-id]
	Router(config-router)#router-id [A.B.C.D]
	```
	
	- Nếu không cấu hình bằng lệnh:
	
		+ 2. IP của Loopback Interface có địa chỉ cao nhất sẽ được chọn là Router ID của Router.
		
		+ 3. Nếu không có Loopback Interface nào được cấu hình, thì IP của Interface đang **Active** có địa chỉ cao nhất sẽ là Router ID của Router.

* Sau khi cấu hình OSPF, Router ID có thể chưa được áp dụng với thứ tự trên. Vì vậy sau khi cấu hình OSPF xong, sử dụng lệnh sau để reset các OSPF Process và Router ID có thể được lựa chọn đúng.
```
Router#clear ip ospf process
```

---

### Tài liệu tham khảo:

[1] Tìm hiểu giao thức định tuyến OSPF. http://svuit.vn/threads/bai-14-tim-hieu-giao-thuc-dinh-tuyen-ospf-101/

[2] Introduction to Link State Routing Protocols. http://www.omnisecu.com/cisco-certified-network-associate-ccna/introduction-to-link-state-routing-protocols.php

[3] OSPF Hierarchical Topology, Areas and Router Roles. http://www.tcpipguide.com/free/t_OSPFHierarchicalTopologyAreasandRouterRoles.htm

[4] What is OSPF Area, OSPF Hierarchical Network Design, and advantages of OSPF Areas. http://www.omnisecu.com/cisco-certified-network-associate-ccna/what-is-ospf-area-hierarchical-network-design-and-advantages-of-ospf-areas.php

[5] OSPF Desginated and Backup Designated Router. http://nguyenvcuong.blogspot.com/2012/05/ospf-desginated-and-backup-designated.html


---

### Hết



