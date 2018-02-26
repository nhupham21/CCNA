## Routing Overview, Static Route

> Tài liệu: Routing Overview, Static Route
> 
> Thực hiện: **Phạm Hoàng Nhu**
> 
> Cập nhật lần cuối: **20/12/2017**

### Mục lục
[1. Chức năng của Router](#chucnangcuarouter)

[2. Tìm hiểu về Routing Table](#timhieuveroutingtable)

[3. Phân biệt Routing Protocol với Routed Protocol](#phanbietroutingvarouted)

[4. Phân loại Routing Protocol](#phanloairoutingprotocol)

[5. Administrative Distance - AD](#AD)

[6. Metric](#metric)

[7. Static Routing](#staticrouting)

- [7.1. Khái niệm](#khainiemstaticrouting)

- [7.2. Cấu hình](#cauhinh)

- [7.3. Dự phòng đường đi bằng AD](#duphongduongdibangad)

- [7.4. Default Route](#defaultroute)

---

<a name="chucnangcuarouter"></a>
### 1. Chức năng của Router
* Chức năng chính của Router:
	- Switching: quá trình này được sử dụng để switch một packet từ một incoming interface ra một outgoing interface trên cùng một router.
	- Lựa chọn đi đường: lựa chọn đường đi tốt nhất đến mạng đích.
	- Duy trì, sử dụng bảng định tuyến. 
	- Phân loại packet.
	- Quản lý, kiểm tra, thống kê.
	
<a name="timhieuveroutingtable"></a>
### 2. Tìm hiểu về Routing Table
* Một bảng định tuyến (routing table) gồm nhiều entry, mỗi entry chứa thông tin về các tuyến đường đến các đích khác nhau.
* Cấu tạo một entry bao gồm:
	- Route Source: Định nghĩa cách Route được học.
	- Destination network: Địa chỉ này có thể là địa chỉ của một host hoặc là địa chỉ của một mạng.
	- Administrative distance: định danh độ tin cậy cho các giao thức định tuyến.
	- Metric: định nghĩa độ tin cậy giữa các đường đi trong một giao thức định tuyến.
	- Next-hop: Địa chỉ của Router kế tiếp để forward gói tin tới.
	- Route timestamp: Định ra thời điểm cuối cùng route được học.
	- Outgoing interface: Interface đầu ra dùng để forward một gói tin đến mạng đích. 

![entry_routing_table](https://github.com/nhuhp/CCNA/blob/master/Routing_Overview/img/entry_routing_table.jpg)
	
* Bảng định tuyến là một cấu trúc phân cấp, được dùng để tăng tốc quá trình tìm kiếm khi định vị route và forward packet.
* Đối với bảng định tuyến của Cisco, có thể chia route thành một số cấp độ như sau:	
	- **Ultimate route**: là một route chứa IP next-hop hoặc là một outgoing interface.
	
	![ultimate_route](https://github.com/nhuhp/CCNA/blob/master/Routing_Overview/img/ultimate_route.jpg)
	
	- **Level 1 Route**: là một route có subnet mask bằng hoặc nhỏ hơn classful mask của một địa chỉ mạng. Một *level 1 route* có thể là:
		+ **Network route**: có subnet mask bằng classful mask của địa chỉ mạng.
		+ **Supernet route**: là một địa chỉ mạng có subnet mask nhỏ hơn classful mask, ví dụ như, một địa chỉ mạng.
		+ **Default route**: là một static route có địa chỉ 0.0.0.0/0.
		
	![level_1_route](https://github.com/nhuhp/CCNA/blob/master/Routing_Overview/img/level_1_route.jpg)
	
	- **Level 1 Parent Route**: là một *level 1 network route* mà được chia subnet. Một Parent route không phải là một ultimate route. Một level 1 parent route thường chứa địa chỉ mạng classful, số lượng subnet, và số lượng các subnet mask khác nhau mà địa chỉ classful có thể chia nhỏ được.
	
	![level_1_parent_route](https://github.com/nhuhp/CCNA/blob/master/Routing_Overview/img/level_1_parent_route.jpg)
	
	- **Level 2 Child Route**: là một route mà đó là subnet của một mạng classful.
	  Một level 1 parent route chứa nhiều level 2 child route.
	
	![level_2_child_route](https://github.com/nhuhp/CCNA/blob/master/Routing_Overview/img/level_2_child_route.jpg)
	
<a name="phanbietroutingvarouted"></a>
### 3. Phân biệt Routing Protocol với Routed Protocol
* **Routed Protocol** là protocol có thể định tuyến dữ liệu. Ví dụ như IP, IPX and AppleTalk. Yêu cầu đối với loại protocol này là một sơ đồ địa chỉ. Dựa vào sơ đồ địa chỉ, bạn có thể xác định mạng mà host thuộc về, ngoài việc xác định host đó trong mạng đó. Tất cả các host trong internetwork (router, server và máy trạm) có thể sử dụng các dịch vụ của một routed protocol.
* **Routing Protocol** là protocol chỉ được sử dụng giữa các router. Mục đích của nó là giúp các router xây dựng và duy trì các bảng định tuyến. Có một số loại routing protocol: path-vector, distance-vector, link-state,...
 
 <a name="phanloairoutingprotocol"></a>
### 4. Phân loại Routing Protocol

![routing_protocol](https://github.com/nhuhp/CCNA/blob/master/Routing_Overview/img/routing_protocol.jpg)

* Routing được chia thành 2 nhóm: Static và Dynamic.
	- Static Routing: là phương thức định tuyến mà người quản trị trực tiếp cấu hình định tuyến cho hệ thống mạng
	- Dynamic Routing: là phương thức định tuyến sử dụng các giao thức định tuyến để trao đổi thông tin định tuyến cho hệ thống mạng. Dynamic Routing sử dụng 2 loại giao thức:
		+ EGP (exterior gateway protocol): giao thức định tuyến được sử dụng để trao đổi thông tin định tuyến giữa các AS khác nhau. 
			Path-Vector: chứa các thông tin về gói tin đường đi đến một mạng cụ thể. Ví dụ: BGP.
		+ IGP (Interior Gateway Protocol): giao thức định tuyến được sử dụng để trao đổi thông tin định tuyến trong cùng một AS. Trong IGP, được chia thành một số loại:
			+ Distance-Vector: trao đổi thông tin định tuyến bằng cách gửi theo định kỳ, toàn bộ bảng định tuyến sang cho láng giếng kết nối trực tiếp với nó. Ví dụ: RIP.
			+ EIGRP là một advanced Distance-Vector, được cải tiến hơn so với distance-vector điển hình, chẳng hạn như sử dụng cơ chế triggered update, hội tụ nhanh, sử dụng thuật toán DUAL để lựa chọn đường đi,...
			+ Link-state: giao thức định tuyến sử dụng các LSA quảng bá các mạng mà router biết cho nhau, từ đó xây dựng nên được một bản đồ mạng, sử dụng thuật toán Dijkstra để tìm đường đi. Ví dụ như OSPF, IS-IS

* Nếu phân loại theo cách gửi Bảng tin định tuyến, thì:
	- **Classful**: không gửi kèm subnet mask, không hỗ trợ VSLM, discontinuous network.
	- **Classless**: gửi kèm subnet mask, hỗ trợ VSLM, discontinuous network.
	
<a name="AD"></a>	
### 5. Administrative Distance - AD
* **Administrative Distance** - AD: mức độ ưu tiên giữa các giao thức định tuyến.
* AD càng thấp thì độ ưu tiên càng cao.
* Một số giá trị AD của các giao thức định tuyến:

|Route Source|Default Distance Values|
|:----|:----:|
|Connected interface|0|
|Static route|1|
|Enhanced Interior Gateway Routing Protocol (EIGRP) summary route|5|
|External Border Gateway Protocol (BGP)|20|
|Internal EIGRP|90|
|IGRP|100|
|OSPF|110|
|Intermediate System-to-Intermediate System (IS-IS)|115|
|Routing Information Protocol (RIP)|120|
|Exterior Gateway Protocol (EGP)|140|
|On Demand Routing (ODR)|160|
|External EIGRP|170|
|Internal BGP|200|
|Unknown|255|

<a name="metric"></a>
### 6. Metric
* Mức độ ưu tiên giữa các đường đi trong một giao thức định tuyến.
* Cũng giống như AD, giá trị Metric càng thấp thì độ ưu tiên càng cao.

<a name="staticrouting"></a>
### 7. Static Routing

<a name="khainiemstaticrouting"></a>
#### 7.1. Khái niệm
* Static Routing là phương thức định tuyến mà người quản trị sẽ trực tiếp cấu hình thông tin đường đi cho hệ thống mạng.
* Ưu điểm:
	- Cấu hình dễ dàng và nhanh chóng.
	- Hỗ trợ nhiều thiết bị.
	- Phù hợp với hệ thống mạng doanh nghiệp nhỏ, ít sự thay đổi về cấu trúc.
	- Người quản trị có quyền kiểm soát điểu khiển bảng định tuyến.
* Nhược điểm:
	- Kích thước hệ thống mạng càng tăng thì cấu hình càng phức tạp.
	- Không phù hợp vói các hệ thống mạng lớn vì không thích ứng được với sự thay đổi của hệ thống mạng lớn.
	- Khả năng cập nhật đường đi bị hạn chế.
	
<a name="cauhinh"></a>
#### 7.2. Cấu hình

```
R(config)# ip route {network} {mask} {address|interface} [distance] [parmanent]
```

* Trong đó:
	- network: Địa chỉ mạng của đích đến
	- mask: Subnet Mask của địa chỉ mạng
	- address: Địa chỉ IP của Router kế tiếp nối với mạng đích (khuyến cáo là nên sử dụng)
	- interface: Tên interface đi đến mạng đích
	- distance: giá trị AD cho route đi đến mạng đích
	- parmanent: Quy định Router không bị thay đổi khi có 1 interface bị down

<a name="duphongduongdibangad"></a>
#### 7.3. Dự phòng đường đi bằng AD
* Một số trường hợp yêu cầu cấu hình Static Route 2 đường về mạng đích sao cho lưu lượng ưu tiên đi 1 đường, đường còn lại chỉ để dự phòng. Khi đường chính gặp sự cố, lưu  lượng sẽ chuyển sang đi đường dự phòng. 


![du_phong_voi_ad](https://github.com/nhuhp/CCNA/blob/master/Routing_Overview/img/du_phong_voi_ad.jpg)

* Giả sử cấu hình Static Route trên R1 đi đến loopback 0 của R3 với đường **R1 --> R2 --> R3** là đường ưu tiên, đường **R1 --> R4 --> R3** là đường dự phòng. Đối với yêu cầu này, cấu hình Static Route trên R1 2 đường đi đến loopback 0 của R3 và sử dụng thêm thông số **distance**. Cụ thể:
	- Như ta đã biết, đường nào có giá trị AD càng thấp thì càng được ưu tiên. Vậy khi cấu hình Static Route, đường nào ưu tiên thì cấu hình với giá trị distance thấp hơn của đường dự phòng.
	- Trên R1 cấu hình như sau:
	
	![du_phong_voi_ad_2](https://github.com/nhuhp/CCNA/blob/master/Routing_Overview/img/du_phong_voi_ad_2.jpg)
	
	- Cấu hình Static Route 2 đường đi loopback 0 của R3. Mặc định giá trị distance sẽ là **1** nên chỉ cần cấu hình distance cho đường dự phòng cao hơn là được. Ở đây cấu hình distance là **10**.
* Kiểm tra:
	- Nếu không sử dụng distance thì cấu hình 2 đường đi đến loopback 0 của R3, `show ip route` trên R1, trên bảng định tuyến có kết quả như sau:
	![show_ip_route](https://github.com/nhuhp/CCNA/blob/master/Routing_Overview/img/show_ip_route.jpg)
	
	- Nếu sử dụng distance thì `show ip route` trên R1 sẽ được:
	![show_ip_route_2](https://github.com/nhuhp/CCNA/blob/master/Routing_Overview/img/show_ip_route_2.jpg)
	
	- Tiếp theo sử dụng `traceroute` đến mạng đích để kiểm tra đường đi của lưu lượng. Trên R1, vào privileged mode và gõ `traceroute 172.16.3.1`:
	![traceroute](https://github.com/nhuhp/CCNA/blob/master/Routing_Overview/img/traceroute.jpg)
	
	- Sau đó, cũng trên R1, shutdown cổng e0/0:
	![r1_shutdown_e00](https://github.com/nhuhp/CCNA/blob/master/Routing_Overview/img/r1_shutdown_e00.jpg)
	
	- Thực hiện `traceroute` một lần nữa:
	![traceroute_2](https://github.com/nhuhp/CCNA/blob/master/Routing_Overview/img/traceroute_2.jpg)
	
<a name="defaultroute"></a>
#### 7.4. Default Route

---

### Tài liệu tham khảo:

[1] Cisco Router Architectute. https://www.cisco.com/networkers/nw99_pres/601.pdf

[2] Wikipedia. FTP. https://vi.wikipedia.org/wiki/FTP

[3] Routing Table. https://thinhcaominh.wordpress.com/2017/06/06/routing-table-bang-dinh-tuyen/

[4] Cisco Networking Academy's Introduction to Routing Dynamically. The Routing Table (3.5). http://www.ciscopress.com/articles/article.asp?p=2180210&seqNum=12

[5] Static Route là gì?. http://vnpro.vn/thu-vien/static-route-la-gi-2045.html

---

### Hết
