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

![entry_routing_table]()
	
* Bảng định tuyến là một cấu trúc phân cấp, được dùng để tăng tốc quá trình tìm kiếm khi định vị route và forward packet.
* Đối với bảng định tuyến của Cisco, có thể chia route thành một số cấp độ như sau:	
	- **Ultimate route**: là một route chứa IP next-hop hoặc là một outgoing interface.
	
	![ultimate_route]()
	
	- **Level 1 Route**: là một route có subnet mask bằng hoặc nhỏ hơn classful mask của một địa chỉ mạng. Một *level 1 route* có thể là:
		+ **Network route**: có subnet mask bằng classful mask của địa chỉ mạng.
		+ **Supernet route**: là một địa chỉ mạng có subnet mask nhỏ hơn classful mask, ví dụ như, một địa chỉ mạng.
		+ **Default route**: là một static route có địa chỉ 0.0.0.0/0.
		
	![level_1_route]()
	
	- **Level 1 Parent Route**: là một *level 1 network route* mà được chia subnet. Một Parent route không phải là một ultimate route. Một level 1 parent route thường chứa địa chỉ mạng classful, số lượng subnet, và số lượng các subnet mask khác nhau mà địa chỉ classful có thể chia nhỏ được.
	
	![level_1_parent_route]()
	
	- **Level 2 Child Route**: là một route mà đó là subnet của một mạng classful.
	  Một level 1 parent route chứa nhiều level 2 child route.
	
	![level_2_child_route]()
	
<a name="phanbietroutingvarouted"></a>
### 3. Phân biệt Routing Protocol với Routed Protocol
* **Routed Protocol** là protocol có thể định tuyến dữ liệu. Ví dụ như IP, IPX and AppleTalk. Yêu cầu đối với loại protocol này là một sơ đồ địa chỉ. Dựa vào sơ đồ địa chỉ, bạn có thể xác định mạng mà host thuộc về, ngoài việc xác định host đó trong mạng đó. Tất cả các host trong internetwork (router, server và máy trạm) có thể sử dụng các dịch vụ của một routed protocol.
* **Routing Protocol** là protocol chỉ được sử dụng giữa các router. Mục đích của nó là giúp các router xây dựng và duy trì các bảng định tuyến. Có một số loại routing protocol: path-vector, distance-vector, link-state,...
 
[4. Phân loại Routing Protocol](#phanloairoutingprotocol)
* Routing được chia thành 2 nhóm: Static và Dynamic.
	- Static Routing: là kỹ thuật định tuyến mà người quản trị trực tiếp cấu hình định tuyến cho hệ thống mạng
	- Dynamic Routing: là kỹ thuật định tuyến sử dụng các giao thức định tuyến để trao đổi thông tin định tuyến cho hệ thống mạng. Dynamic được chia thành 2 loại:
		+ EGP (exterior gateway protocol): giao thức định tuyến được sử dụng để trao đổi thông tin định tuyến giữa các AS khác nhau. 
			Path-Vector: chứa các thông tin về gói tin đường đi đến một mạng cụ thể. Ví dụ: BGP.
		+ IGP (Interior Gateway Protocol): giao thức định tuyến được sử dụng để trao đổi thông tin định tuyến trong cùng một AS. Trong IGP, được chia thành một số loại:
			+ Distance-Vector: trao đổi thông tin định tuyến bằng cách gửi theo định kỳ, toàn bộ bảng định tuyến sang cho láng giếng kết nối trực tiếp với nó. Ví dụ: RIP.
			+ EIGRP là một advanced Distance-Vector, được cải tiến hơn so với distance-vector điển hình, chẳng hạn như sử dụng cơ chế triggered update, hội tụ nhanh, sử dụng thuật toán DUAL để lựa chọn đường đi,...
			+ Link-state: giao thức định tuyến sử dụng các LSA quảng bá các mạng mà router biết cho nhau, từ đó xây dựng nên được một bản đồ mạng, sử dụng thuật toán Dijkstra để tìm đường đi. Ví dụ như OSPF, IS-IS

![routing_protocol]()

* Nếu phân loại theo cách gửi Bảng tin định tuyến, thì:
	- **Classful**: không gửi kèm subnet mask, không hỗ trợ VSLM, discontinuous network.
	- **Classless**: gửi kèm subnet mask, hỗ trợ VSLM, discontinuous network.
[5. Administrative Distance - AD](#AD)
* **Administrative Distance** - AD: mức độ ưu tiên giữa các giao thức định tuyến.
* AD càng thấp thì độ ưu tiên càng cao.
* Một số giá trị AD của các giao thức định tuyến:

Route Source Default|Distance Values
Connected interface|0
Static route|1
Enhanced Interior Gateway Routing Protocol (EIGRP) summary route|5
External Border Gateway Protocol (BGP)|20
Internal EIGRP|90
IGRP|100
OSPF|110
Intermediate System-to-Intermediate System (IS-IS)|115
Routing Information Protocol (RIP)|120
Exterior Gateway Protocol (EGP)|140
On Demand Routing (ODR)|160
External EIGRP|170
Internal BGP|200
Unknown|255

[6. Metric](#metric)

[7. Static Routing](#staticrouting)

- [7.1. Khái niệm](#khainiemstaticrouting)

- [7.2. Cấu hình](#cauhinh)

- [7.3. Dự phòng đường đi bằng AD](#duphongduongdibangad)

- [7.4. Default Route](#defaultroute)


<a name="caidatvmware"></a>
### 1. Cài đặt VMware Workstation

**Các bước cài đặt:** ...

...

...

...

**Lỗi xảy ra:** ...

...

**Xử lý:** ...

...

<a name="caidatubuntu"></a>
### 2. Cài đặt hệ điều hành Ubuntu

<a name="chuanbi"></a>
#### 2.1. Chuẩn bị

...

...

...

<a name="cacbuoccaidat"></a>
#### 2.2. Các bước cài đặt

**Bước 1:** ...

...

**Bước 2:** ...

...

**Bước 3:** ...

...

**Bước 4:** ...

...

...

---

### Tài liệu tham khảo:

[1] Cisco Router Architectute. https://www.cisco.com/networkers/nw99_pres/601.pdf

[2] Wikipedia. FTP. https://vi.wikipedia.org/wiki/FTP

[3] Routing Table. https://thinhcaominh.wordpress.com/2017/06/06/routing-table-bang-dinh-tuyen/

[4] Cisco Networking Academy's Introduction to Routing Dynamically. The Routing Table (3.5). http://www.ciscopress.com/articles/article.asp?p=2180210&seqNum=12

[5] 

---

### Hết

* Chức năng của Router.
* Tìm hiểu về Routing Table.
* Phân biệt Routing Protocol với Routed Protocol.
* Phân loại Routing Protocol. Cho ví dụ với từng loại.
* AD (Administrative Distance) là gì? Nêu các giá trị AD của một số Routing Protocol.
* Metric là gì?
* Static routing? Cách cấu hình? Dự phòng đường đi bằng AD?
* Default route?

