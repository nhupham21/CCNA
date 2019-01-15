## OSPF

> Tài liệu: OSPF
> 
> Thực hiện: **Phạm Hoàng Nhu**
> 
> Cập nhật lần cuối: **02/01/2019**

### Mục lục
[1. Link-state Protocol](#linkstateprotocol)

[2. OSPF](#ospf)
- [2.1. Tổng quan về OSPF](#tongquanveospf)
- [2.2. Hệ thống phân cấp](#hethongphancap)
	+ [2.2.1. Hierarchical Topology](#hierarchicaltopology)
	+ [2.2.2. Router Roles trong OSPF Hierarchical Topology](#routerroles)
- [2.3. Network Types](#networktypes)
	+ [2.3.1. DR-BDR Roles](#drbdrroles)
		+ [2.3.1.1. DR-BDR-DROther](#drbdrdrother)
		+ [2.3.1.2. Cách giao tiếp giữa DR-BDR-DROther](#giaotiep)
		+ [2.3.1.3. Bầu chọn DR-BDR-DROther](#bauchon)
	+ [2.3.1. Point-to-Point Network](#pointtopoint)
	+ [2.3.2. Multi-access Broadcast Network](#multiaccess)
	+ [2.3.3. Non-broadcast Multi-access Network](#nbma)
- [2.4. Nguyên lý hoạt động của OSPF](#nguyenlyhoatdong)
	+ [2.4.1. Chọn Router ID](#routerid)
	+ [2.4.2. Thiết lập Adjacency](#thietlapddjacency)
	+ [2.4.3. Trao đổi LSA](#traodoilsa)
	+ [2.4.4. Tìm đường đi](#timduongdi)
- [2.5. Cách tính Cost trong OSPF](#Cost)
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
	- Mỗi Router chạy Link-state Protocol hình thành thông tin về Router, các link kết nối trực tiếp với nó và trạng thái của các link này. Thông tin này được gửi đến tất cả các Router trong network bằng **Multicast Message**. Mỗi Router giữ một bản copy các thông tin này, nhưng không thay đổi nó. Từ đó mỗi Router sẽ có được toàn bộ được Bức tranh Network Topology. Mỗi router sẽ tự tính toán độc lập đường đi tốt nhất đến một mạng nào đó.
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
	- **Non-Backbone Area**: các vùng được khác Backbone Area. Được đánh số. Dựa vào chức năng của các Non-Backbone Area mà người ta cũng chia thành các Area khác nhau (chương trình CCNP).
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
##### 2.3.1. DR-BDR Roles

<a name="drbdrdrother"></a>
###### 2.3.1.1. DR - BDR - DROther
* Giả sử, khi trao đổi LSA, một Router phải nói chuyện với tất cả các Router còn lại. Như vậy tất cả các kết nối là khá lớn. Có thể sẽ gây ảnh hưởng đến traffic của hệ thống.

![drbdrdrother_1](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/drbdrdrother_1.png)

* Do đó, OSPF đã định nghĩa ra **Designated Router**. Designated Router (DR) sẽ là điểm trung tâm cho việc trao đổi thông tin định tuyến giữa các Router.
	- DR thiết lập neighbor với các Router còn lại.
	- DR thu thập và gửi đi các thông tin LSA cho các Router.
	![dr](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/dr.png)

* **Backup Designated Router** (BDR) là Router dự phòng cho DR. Nếu trường hợp DR không hoạt động thì BDR sẽ chiếm quyền và trở thành DR.
	- BDR cũng thiết lập neighbor với các Router còn lại.
	- BDR không thu thập và gửi đi các thông tin LSA cho các Router, ngoại trừ BDR.
	- BDR có một bộ timer để theo dõi DR. Nếu bộ timer chạy hết mà DR không trả lời cho BDR thì BDR sẽ xem như DR bị down và tiến hành chiếm quyền trở thành DR.
	
* **DROther** là các Router còn lại, không phải là DR cũng không phải là BDR.
![drbdrdrother_2](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/drbdrdrother_2.png)

* Vai trò DR, BDR hay DROther chỉ tồn tại trong 1 Segment mạng. Do vậy 1 Router có thể là DR trong Segment này, nhưng lại đóng vai trò DROther trong Segment khác.

![segment](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/segment.png)

* Các DR và BDR có tính chất **Non-preempt**.

<a name="giaotiep"></a>
###### 2.3.1.2. Cách giao tiếp giữa DR - BDR - DROther.
* Các OSPF Router gửi các Hello Packet đến địa chỉ Multicast **224.0.0.5**.
* Khi có thay đổi, các DROther gửi Update Packet đến địa chỉ Multcast **224.0.0.6**, trong DR và BDR lại gửi Update đến **224.0.0.5**.
	
<a name="bauchon"></a>
###### 2.3.1.3. Bầu chọn DR - BDR - DROther
* Việc bầu chọn DR - BDR diễn ra trong 1 segment mạng.
* Khi khởi động một tiến trình OSPF, Router sẽ lắng nghe các Hello Packet từ các Router khác. Nếu hết **Dead Interval Timer** mà không nhận được Hello Packet nào, nó sẽ tự nâng mình là DR.
* Việc bầu chọn như sau:
	- Xét **Priority**:
		+ Priority được cấu hình trên Interface tham gia vào OSPF trong Segment.
		```
		Router(config)#interface [interface-name]
		Router(config-if)#ip ospf priority [0-255]
		```
		+ Priority mặc định là **1** trên tất cả các Router.
		+ Router có Priority cao nhất sẽ giữ vai trò làm DR.
		+ Router có Priority cao thứ hai sẽ giữ vai trò làm BDR.
		+ Các Router còn lại có Priority thấp hơn sẽ là DROther. Các Router có Priority = 0, không tham gia bầu chọn, tức là cũng trở thành DROther.
	
	- Xét **Router ID**: nếu Priority chưa được cấu hình thì xét đến Router ID. 
		+ Lưu ý: **Khi dùng Router ID để bầu chọn DR,BDR, các Router ID được chọn từ Loopback sẽ không có hiệu lực**. Như vậy, lức đó, các interface đang active hoặc Router ID cấu hình bằng tay sẽ có hiệu lực.
		+ Router có Router ID cao nhất sẽ là DR.
		+ Router có Router ID cao thứ hai sẽ là BDR.
		+ Các Router còn lại sẽ là DROther.
		
* Các DR và BDR có tính chất **Non-preempt**. Nghĩa là các Router tham gia vào OSPF sau, mặc dù có Priority hay Router ID cao hơn vẫn không được làm DR.
		
<a name="pointtopoint"></a>	
##### 2.3.2. Point-to-Point Network
* Là một kết nối của 2 Router.
* Sử dụng serial link (HDLC, PPP).
* Không có sự bầu chọn DR và BDR.
* Các Hello Packet đều được gửi đến địa chỉ Multcast 224.0.0.5.
![pointtopoint_1](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/pointtopoint_1.png)

<a name="multiaccess"></a>
##### 2.3.3. Broadcast Multi-access Network
* Môi trường Ethernet.
* Có sự bầu chọn DR,BDR giữa các Router.
* Các chỉ thiết lập Adjacency với DR và BDR.
* Các OSPF Router gửi các Hello Packet đến địa chỉ Multcast 224.0.0.5.
* Khi có thay đổi, các DROther gửi Update Packet đến địa chỉ Multcast 224.0.0.6, trong khi DR và BDR lại gửi Update đến 224.0.0.5.
![multiaccess_1](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/multiaccess_1.png)

<a name="nbma"></a>
##### 2.3.4. Non-Broadcast Multi-access Network
* Mô hình mạng kết nối nhiều hơn 2 Router nhưng không có khả năng broadcast.
* Frame Relay, ATM, X.25,...

![nbma](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/nbma.png)

* Có 5 mode network OSPF trong NBMA.

<a name="nguyenlyhoatdong"></a>
#### 2.4. Nguyên lý hoạt động của OSPF
* OSPF hoạt động qua 4 bước chính:
	- Chọn Router ID
	- Thiết lập Adjacency
	- Trao đổi LSA
	- Tìm đường đi
	
<a name="routerid"></a>	
##### 2.4.1. Chọn Router ID
* Trong OSPF, mỗi Router cần phải có một **Router ID**. Router ID được dùng để cung cấp một định danh duy nhất trong môi trường OSPF.
* Router ID có dạng là một **IPv4 Address** A.B.C.D, 32 bit, nhưng không phải là IP.
* Router ID được chọn theo thứ tự như sau: 
	- Cấu hình bằng lệnh.
	```
	Router(config)#router ospf [process-id]
	Router(config-router)#router-id [A.B.C.D]
	```
	
	- Nếu không cấu hình bằng lệnh:
	
		+ IP của Loopback Interface có địa chỉ cao nhất sẽ được chọn là Router ID của Router.
		
		+ Nếu không có Loopback Interface nào được cấu hình, thì IP của Interface đang **Active** có địa chỉ cao nhất sẽ là Router ID của Router.

* Sau khi cấu hình OSPF, Router ID có thể chưa được áp dụng với thứ tự trên. Vì vậy sau khi cấu hình OSPF xong, sử dụng lệnh sau để reset các OSPF Process và Router ID có thể được lựa chọn đúng.
```
Router#clear ip ospf process
```

<a name="thietlapddjacency"></a>
##### 2.4.2. Thiết lập Adjacency
* Các Router sẽ gửi các **Hello Packet** đến địa chỉ Multcast 224.0.0.5 liên tục theo chu kỳ mặc định là 10s/lần.
![hellopacket_1](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/hellopacket_1.png)

* Các Router sẽ kiểm tra các thông số và một số option trong các Hello Packet. Nếu các thông số match với nhau, các Router đó sẽ thiết lập quan hệ Adjacency.
	- Router ID
	- Hello and Dead Interval Timer
	- Area ID
	- Router Priority
	- DR IP Address
	- BDR IP Address
	- Authentication Password
	- Stub area flag
	
![hellopacket_2](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/hellopacket_2.png)

* Nếu chạy hết Dead Interval Timer (Mặc định, **Dead Interval Timer = 4 * Hello Interval Timer**) mà không nhận được Hello Packet nào cả, thì Router thì tự nâng quyền thành DR.
![hellopacket_3](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/hellopacket_3.png)

* Khi lên Adjacency với nhau, các Router sẽ ở trạng thái **2-WAY**.
* Khi đó, nếu các Router trong môi trường Broadcast Multi-access Network, việc bầu chọn DR và BDR sẽ diễn ra. Các DROther sẽ thiết lập quan hệ Adjacency trực với DR và BDR.
![adjacency_1](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/adjacency_1.png)
* Nếu trong Point-to-Point Network thì không có sự bầu chọn DR và DBR.
![adjacency_2](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/adjacency_2.png)
		
<a name="traodoilsa"></a>
##### 2.4.3. Trao đổi LSA 
* Sau khi thiết lập quan hệ Adjacency, các Router trao đổi các bảng tin LSA để trao đổi và duy trì các thông tin định tuyến. Mỗi Router trong OSPF sử dụng LSA để chia sẻ và học thông tin định tuyến.
* **Link** được định nghĩa trong OSPF là một interface chạy giao tuyến định tuyến OSPF.
* **State** có nghĩa là thông tin ứng với link. Một link có một số thông tin như địa chỉ IP, trạng thái up/down, subnet mask, loại interface, loại network, bandwidth và delay. 
* Ta có thể hiểu, **Link-state** có thể nghĩa là trạng thái của một interface tham gia vào OSPF.
* **Link-state Advertisement** (LSA) là một data packet chứa thông tin link-state và thông tin định tuyến.
* Một Router chạy OSPF duy trì một Link-state Database. **Link-state Database** (LSDB) là tập hợp các LSA của một Router. Router trao đổi LSA để đồng bộ LSDB.
* Quá trình trao đổi LSA diễn ra như sau:
	- Router sẽ gửi ra các gói **DB Description** để giới thiệu các LSA của Router đó cho các Neighbor của mình.
	- Các Router nhận được gói **DB Description** sẽ so sánh với LSDB của mình. Những LSA nào không có trong LSDB của mình sẽ được Router yêu cầu bằng cách gửi ra gói tin **LS Request**.
	- Sau khi nhận lại **LS Request**, Router sẽ gửi LSA được yêu cầu trong gói **LS Update**.
	- Cuối cùng, Router nhận được các LSA từ **LS Update**, cập nhật LSDB của mình và gửi lại thông báo đã nhận được **LS Acknowledge**.
	
* Quá trình trao đổi LSA như vậy diễn ra trong một Area và các Router sẽ có được một bức tranh Mạng của cả hệ thống.

<a name="timduongdi"></a>
###### 2.4.4. Tìm đường đi
* Router sẽ tính toán đường đi tốt nhất đến Mạng đích bằng cách áp dụng thuật toán Dijkstra vào LSDB đã xây dựng và duy trì thông qua việc trao đổi LSA.
![bestroute](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/bestroute.png)

* Đường đi tốt nhất được chọn sẽ là đường đi có **tổng Cost các Link tới Mạng đích là thất nhất**.

* Sau đó, đường đi tốt nhất sẽ được đưa vào bảng định tuyến.

<a name="cost"></a>
##### 2.5. Cách tính Cost trong OSPF

* Cost của mỗi Link sẽ được tính bằng công thức.
```
Cost = (100 Mbps) / (Bandwidth in Mbps)
```

* Như vậy, mỗi loại Link (hay Interface) khác nhau sẽ có một giá trị Cost khác nhau.

|Loại Interface|Bandwidth|Cost|
|:----|----:|----:|
|Serial|1.544|64|
|Ethernet|10|10|
|Fast Ethernet|100|1|
|Giga Ethernet|1000|1|

* Tổng Cost các Link tới Mạng đích sẽ được **tính từ Mạng đích trở về, đi vào Interface thì cộng Cost của Interface đó, đi ra khỏi Interface thì không cộng**.
* Và đường đi tốt nhất được chọn sẽ là đường đi có **tổng Cost các Link tới Mạng đích là thất nhất**.
* Ví dụ:
![tinhcost](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/tinhcost.png)

* Có thể điều chỉnh giá trị cost của một interface bằng một số cách:
	- Thay đổi Bandwidth trên interface đó.
	```
	Router(config)#interface [interface-name]
	Router(config-if)#bandwidth [bandwidth-in-kilobits]
	```
	
	- Thay đổi giá trị Cost của OSPF trên interface đó.
	```
	Router(config)#interface [interface-name]
	Router(config-if)#ip ospf cost [<1-65535>]
	```
* Kiểm tra giá trị Cost của một interface bằng lệnh.
```
Router#show ip ospf interface [interface-name]
```	
![showcost](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/showcost.png)

* Trong OSPF, nếu 2 đường route cùng đi đến một đích, có metric bằng bằng nhau thì 2 đường đó có thể cân bằng tải với nhau (**Load-balancing**).
* Mặc định thì Router Cisco hỗ trợ cân bằng tải 4 đường có metric bằng nhau.
* Có thể thay đổi số đường có thể cân bằng tải (tối đa là 16 đường) bằng lệnh:
```
Router(config)#router ospf [process-id]
Router(config-router)#maximum-paths [<1-16>]
```

<a name="cauhinh"></a>
#### 2.6. Cấu hình OSPF

<a name="wildcardmask"></a>
##### 2.6.1 Wildcard Mask
* **Wildcard Mask** được dùng để định danh một dải địa chỉ mạng. Chúng thường dùng với các giao thức định tuyến (OSPF, EIGRP) và access list.
* Cũng giống như Subnet Mask, Wildcard Mask dài 32 bit. Wildcard Mask thường đi kèm với một địa chỉ IP tham chiếu. IP tham chiếu thường sẽ là IP nằm trong dải địa chỉ mà Wildcard Mask mà thể hiện.
* Cách xác định: Trong dải địa chỉ cần lấy:
	- Các bit không thay đổi tương ứng **bit 0** trên Wildcard Mask.
	- Các bit thay đổi (di động) tương ứng **bit 1** trên Wildcard Mask.
* Ví dụ:
	- Lấy ra dải mạng 192.168.1.8/24 - 192.168.1.11/24
	```
	192.168.1.8/24   =  192.168.  1. 0000 10 00
	192.168.1.9/24   =  192.168.  1. 0000 10 01
	192.168.1.10/24  =  192.168.  1. 0000 10 10
	192.168.1.11/24  =  192.168.  1. 0000 10 11
	```
	
	Ta thấy 2 bit cuối cùng của octet thứ 4 thì liên tục thay đổi, các bit còn lại thì vẫn giữ nguyên. Nên:
	
	```
	Wildcard Mask    =    0.  0.  0. 0000 00 11   =   0.0.0.3
	IP reference     =  192.168.1.8  
	```
			
* Wildcard Mask có thể chọn ra:
	- Địa chỉ của một host hoặc một địa chỉ IP duy nhất.
		+ 192.168.1.212	0.0.0.0
		
	- Toàn bộ một mạng (Class A, B, C, ...)
		+ 172.16.0.1 0.0.255.255
		
	- Toàn bộ Subnet.
		+ 10.0.0.0	0.0.0.128
		
	- Một dải địa chỉ mạng.
		+ 192.168.1.8 0.0.0.3
		
##### 2.6.2 Cấu hình OSPF
* Bật OSPF.
```
Router(config)#router ospf [process-id]
```
* Lưu ý: Process ID chỉ có giá trị local. Do đó, Mỗi Router không cần phải giống nhau về Process ID.

* Cấu hình Router ID.
```
Router(config-router)#router-id [A.B.C.D]
```

* Cấu hình dải mạng tham gia vào OSPF.
```
Router(config-router)#network [reference-ip] [wildcard-mask] area [area-id]
```

* Kiểm tra.
	- Kiểm tra các thông số của OSPF.
	```
	Router#show ip ospf
	```
	
	```
	Router#show ip protocols
	```
	
	- Kiểm tra các Entry trên bảng định tuyến.
	```
	Router#show ip route ospf
	```
	
	- Kiểm tra thông số của các Adjacency.
	```
	Router#show ip ospf neighbor
	```
	
	- Kiểm tra các Event của OSPF.
	```
	Router#show ip ospf events
	```

<a name="passiveinterface"></a>
#### 2.7. Passive-interface trong OSPF
* Trong OSPF, Router sẽ không gửi các bản tin OSPF (Hello, LSA, LSR,...) ra các **Passive-interface**. Do vậy, Router sẽ không thiết lập được Adjacency và không thể trao đổi được thông tin định tuyến với nhau.
![passive_interface_1](https://github.com/nhuhp/CCNA/blob/master/OSPF/img/passive_interface_1.png)

* Cấu hình Passive interface.
```
Router(config)#router ospf [process-id]

Router(config-router)#passive-interface [interface-name]
```

<a name="authentication"></a>
#### 2.8. Authentication trong OSPF
* OSPF có thể được cấu hình để có thể xác thực mỗi OSPF message.
* Trong quá trình thiết lập Adjacency, các Router phải pass qua bước xác thực thì mới trở thành Adjacency.
* Có 2 loại xác thực:
	- Clear text Authentication.
	- MD5 Authentication.
* Cấu hình Clear text Authentication
```
Router(config)#interface [interface-name]

Router(config-if)#ip ospf authentication 

Router(config-if)#ip ospf authentication-key [password]
```

* Cấu hình MD5 Authentication
```
Router(config)#interface [interface-name]

Router(config-if)#ip ospf authentication message-digest

Router(config-if)#ip ospf message-digest-key [key-ID] md5 [encryption-type] [password]
```

* Có thể chọn loại Authentication trên cả Area.
```
Router(config)#router ospf [process-id]

Router(config-router)#area [area-id] authentication
```

hoặc

```
Router(config)#router ospf [process-id]

Router(config-router)#area [area-id] authentication message-digest
```

* Kiểm tra.
```
Router#show ip ospf interface [interface-name]
```

---

### Tài liệu tham khảo:

[1] Tìm hiểu giao thức định tuyến OSPF. http://svuit.vn/threads/bai-14-tim-hieu-giao-thuc-dinh-tuyen-ospf-101/

[2] Introduction to Link State Routing Protocols. http://www.omnisecu.com/cisco-certified-network-associate-ccna/introduction-to-link-state-routing-protocols.php

[3] OSPF Hierarchical Topology, Areas and Router Roles. http://www.tcpipguide.com/free/t_OSPFHierarchicalTopologyAreasandRouterRoles.htm

[4] What is OSPF Area, OSPF Hierarchical Network Design, and advantages of OSPF Areas. http://www.omnisecu.com/cisco-certified-network-associate-ccna/what-is-ospf-area-hierarchical-network-design-and-advantages-of-ospf-areas.php

[5] OSPF Desginated and Backup Designated Router. http://nguyenvcuong.blogspot.com/2012/05/ospf-desginated-and-backup-designated.html

[6] OSPF - The DR and BDR Roles. https://www.fir3net.com/Networking/Protocols/ospf-the-dr-and-bdr-roles.html

[7] DR BDR Communication. https://community.cisco.com/t5/routing/dr-bdr-communication/td-p/1142718

[8] OSPF Neighbor States Explained with Example. https://www.computernetworkingnotes.com/ccna-study-guide/ospf-neighbor-states-explained-with-example.html

[9] Network Types trong OSPF. https://tailamblog.wordpress.com/2017/08/11/network-types-trong-ospf/

[10] OSPF Authentication. https://study-ccna.com/ospf-authentication/

[11] http://www.vnpro.org/forum/forum/ccna%C2%AE/icnd-2-routing-access-list/31418-ph%C3%A2n-bi%E1%BB%87t-passive-interface-gi%E1%BB%AFa-ospf-rip

---

### Hết



