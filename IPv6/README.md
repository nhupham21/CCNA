## IPv6

> Tài liệu: IPv6
> 
> Thực hiện: **Phạm Hoàng Nhu**
> 
> Cập nhật lần cuối: **12/02/2019**

### Mục lục
[1. Tổng quan về IPv6](#tongquan)
- [1.1. Lịch sử](#lichsu)
- [1.2. Không gian IP](#khonggian)
- [1.3. Các tính năng của IPv6](#tinhnang)

[2. IPv6 Header](#header)
- [2.1. IPv6 Header](#ipv6header)
- [2.2. Extension Header](#extensionheader)

[3. Format](#format)

[4. Các loại IPv6](#phanloai)
- [4.1. Unicast Address](#unicastaddress)
	+ [4.1.1. Global Unicast Address](#globalunicast)
	+ [4.1.2. Link-Local Unicast Address](#linklocal)
	+ [4.1.3. Loopback Address](#loopback)
	+ [4.1.4. Unspecified Address](#unspecified)
	+ [4.1.5. Unique Local Address](#uniquelocal)
	+ [4.1.6. IPv4 Embedded Address](#ipv4embedded)
	
- [4.2. Multicast Address](#multicastaddress)
- [4.3. Anycast Address](#anycastaddress)

---

<a name="tongquan"></a>
### 1. Tổng quan về IPv6

<a name="lichsu"></a>
#### 1.1. Lịch sử
* Năm 1994, IETF bắt đầu thiết kế và phát triển chồng giao thức và các tiêu chuẩn mà ngày nay được gọi là **Internet Protocol Version 6** (IPv6).
* Basic Protocol (RFC 2460) được publish năm 1998.
* Basic socket API (RFC 2553) và DHCPv6 (RFC 3315) được publish năm 2003.
* Mobile IPv6 (RFC 3775) được publish năm 2004.
* Flow label Specification (RFC 3697) được thêm vào năm 2004.
* Kiến trúc địa chỉ (RFC 4291) ổn định, sửa đổi nhỏ trong năm 2006.
* Node requirements (RFC 4294) được publish năm 2006.

<a name="khonggianip"></a>
#### 1.2. Không gian địa chỉ IP
* IPv4 được phát triển vào những năm 70, với độ dài 32 bit, IPv4 có thể đáp ứng tới khoảng 4.3 tỉ (2^32) địa chỉ IP.
* Tuy nhiên, với sự phát triển quá nhanh của Internet và các thiết bị di động, tính đến giữa năm 2002, đã có 2/3 tổng số địa chỉ IP đã được sử dụng.
* Khi IPv6 được phát triển, đã giải quyết được vấn đề khoảng gian địa chỉ IP.
![ipv6](https://github.com/nhuhp/CCNA/blob/master/IPv6/img/ipv6.png)

<a name="tinhnang"></a>
#### 1.3. Các tính năng của IPv6
* Không gian địa chỉ lớn hơn.
	- Global reachability and flexibility.
	- Aggregation.
	- Multihoming.
	- Auto-configuration.
	- Plug-and-play.
	- End-to-end without NAT.
	- Renumbering.
* Mobility and Security.
	- Mobile IP RFC-compliant.
	- IPsec mandatory for IPv6.
* Header đơn giản hơn.
	- Routing effciency.
	- Performance and forwarding rate scalability.
	- No broadcasts.
	- No checksums.
	- Extension headers.
	- Flow labels.
* Transition richness.
	- Dual stack.
	- 6to4 and manual tunnels.
	- Translation.

<a name="header"></a>
### 2. IPv6 Header

<a name="ipv6header"></a>
#### 2.1. IPv6 Header
* IPv6 Header là một phiên bản cải tiến của IPv4 Header.  Một số trường không cần thiết hoặc hiếm khi sử dụng sẽ bị loại bỏ, thay vào đó, sẽ thêm một số trường để cung cấp một số tính năng khác.
* Cấu trúc của IPv6 Packet.
![ipv6_packet](https://github.com/nhuhp/CCNA/blob/master/IPv6/img/ipv6_packet.png)

* IPv6 Header có độ dài cố định là **40 byte**.
![ipv6_header](https://github.com/nhuhp/CCNA/blob/master/IPv6/img/ipv6_header.png)

* Các trường trong IPv6 Header.
	- **Version** (4 bit): cho biết Version của IP. Trong trường hợp này là 6.
	- **Traffic Class** (8 bit): cho biết class hoặc priority của IPv6 Packet. Trường này cung cấp chức năng tương tự như trường **Type of Service** trong IPv4. 
	- **Flow Label** (20 bit): cho biết Packet này thuộc về một chuỗi cụ thể các Packet giữa Source và Destination, yêu cầu xử lý đặc biệt bỏi các IPv6 Router trung gian. Flow Label được sử dụng cho non-default quality of service connection, chẳng hạn như các kết nối cần real-time (vocal và video). Để xử lý các Router mặc định, Flow Label được set bằng 0. Có thể có nhiều flow giữa Source và Destination, được phân biệt bằng những Flow Label khác không.
	- **Payload Length** (16 bit): cho biết chiều dài của IPv6 Payload, bao gồm các Extension Header và PDU các tầng trên. Với độ dài 16 bit, trường IPv6 Payload Length có thể lên tới 65535 byte. Nếu vượt quá 65535 byte, trường IPv6 Payload Length được set bằng 0 và **Jumbo Payload option** được sử dụng trong Hop-by-Hop Options Extension Header.
	- **Next Header** (8 bit): cho biết Extension Header tiếp theo hoặc Header của tầng cao hơn.
	![next_header](https://github.com/nhuhp/CCNA/blob/master/IPv6/img/next_header.png)
		
		+ Một số giá trị Next Header (ở dạng thập phân):
	![next_header_code](https://github.com/nhuhp/CCNA/blob/master/IPv6/img/next_header_code.png)
	
	- **Hop Limit** (8 bit): cho biết số lượng Node lớn nhất mà IPv6 Packet có thể đi qua trước khi discard. Trường này giống với trường **TTL** trong IPv4. Khi Hop Limit bằng 0, một **ICMPv6 Time Exceeded message** sẽ được gửi tới Source Address và Packet bị discard.
	- **Source Address** (128 bit): lưu IPv6 Address của host bắt đầu gửi Packet.
	- **Destination Address** (128 bit): lưu IPv6 Address của host Đích. Hầu hết, Destination Address là Address cuối cùng mà Packet đi đến. Tuy nhiên, nếu có một Routing Extension Header, thi Destination Address có thể là next router Interface trong source route list.

* So sánh với IPv4 Header.
![ipv4vsipv6](https://github.com/nhuhp/CCNA/blob/master/IPv6/img/ipv4vsipv6.png)

<a name="extensionheader"></a>
#### 2.2. Extension Header
* IPv4 Header chứa tất cả các option. Vì thế, mỗi Router trung gian phải kiểm tra các trường đó và xử lý chúng. Điều này có thể làm cho Performance bị giảm trong quá trình Forward IPv4 Packet.
* Với IPv6, các Delivery và Forwarding Option được chuyển vào các Extension Header. Chỉ có Hop-by-Hop Option Extension Header là được xử lý tại mỗi Router trung gian. Điều này làm tăng khả năng xử lý IPv6 Header và cải thiện Performance quá trình Forwarding.
* Trong một IPv6 Packet thông thường, sẽ không có Extension Header. Nếu được yêu cầu xử lý đặc biệt bởi các Router trung gian hay Destination, một hoặc nhiều Extension Header sẽ được thêm vào bởi Host gửi Packet.
* Mỗi Extension Header cũng chứa trường Next Header và xác định Extension Header nào sẽ theo sau nó. Các Node khi nhận được Packet chứa các Extension Header, chúng sẽ xử lý các Extension Header này theo thứ tự được sắp xếp của chúng.
![next_header_usage](https://github.com/nhuhp/CCNA/blob/master/IPv6/img/next_header_usage.png)

* Thứ tự của các Extension Header trong IPv6 Packet: việc xử lý các Extension Header diễn ra theo đúng thứ tự mà các Header được sắp xếp trong IPv6 Packet.
![next_header_code](https://github.com/nhuhp/CCNA/blob/master/IPv6/img/next_header_code.png)

<a name="format"></a>
### 3. Format
* IPv6 có độ dài 128 bit.
* IPv6 có Format:
```
x:x:x:x:x:x:x:x
```
	- x là chuỗi hexa 16 bit.
	- Không phân biệt chữ hoa chữ thường cho A, B, C, D, E và F
	- Các số 0 đầu các chuỗi có thể loại bỏ.
	- Các trường 0 liên tiếp có thể thay thế bằng **::**, nhưng chỉ được dùng một lần cho mỗi địa chỉ.
	
* Ví dụ:
	```
	2031:0000:130F:0000:0000:09C0:876A:130B
		
	--> 2031:0:130f::9c0:876a:130b
	```

	```
	FF01:0:0:0:0:0:0:1
	
	--> FF01::1
	```
	
	```
	0:0:0:0:0:0:0:1
	
	--> ::1
	```
	
	```
	0:0:0:0:0:0:0:0
	
	--> ::
	```

<a name="phanloai"></a>
### 4. Các loại IPv6
* Có 3 loại địa chỉ IPv6.
	- **Unicast Address**
	- **Multicast Address**
	- **Anycast Address**
	
<a name="unicastaddress"></a>
#### 4.1. Unicast Address
![unicast](https://github.com/nhuhp/CCNA/blob/master/IPv6/img/unicast.png)

* Unicast Address định danh duy nhất cho một Interface trong một IPv6 device. Một Packet được gửi đến một Unicast Address thì được nhận bởi Interface được gán Address đó.
* Giống như IPv4, một Source IPv6 Address phải là một Unicast Address.
* IPv6 có một loại Unicast Address.
	- **Global Unicast**
	- **Link-Local**
	- **Loopback**
	- **Unspecified Address**
	- **Unique Local**
	- **IPv4 Embedded**

<a name="globalunicast"></a>
##### 4.1.1. Global Unicast Address
* **Global Unicast Address** hay còn được gọi là *Aggregatable Global Unicast Address*, là loại địa chỉ được sử dụng trên toàn cầu, reachable trong IPv6 Internet.
* Nó tương đương với IPv4 Public Address. Hiện tại, IANA chỉ cấp dải **2000::/3** cho Global Unicast Address.
* Cấu trúc của một Global Unicast Address.
![gua](https://github.com/nhuhp/CCNA/blob/master/IPv6/img/gua.png)		

* 3 bit đầu của Global Unicast Address là **001**.
* Một số cách để cấu hình Global Unicast Address.
	- Manually Configured.
	- Stateless Address Autoconfiguration.
	- Stateful DHCPv6.

<a name="linklocalunicast"></a>
##### 4.1.2. Link-Local Unicast Address
* **Link-Local Unicast Address** là IPv6 Unicast Address đại diện cho một host trong một đường Link hay một Segment mà host đó kết nối.
* Link-Local Unicast Address chỉ cần duy nhất trong đường Link hoặc Segment và không nhất thiết phải duy nhất bên ngoài Link.
* Các thiết bị có thể sử dụng DAD (Duplicate Address Detection) để xác định xem Link-Local Address đó có phải là duy nhất hay không.
* Router sẽ không forward Packet có Link-Local Address.   
* Link-local Address được thiết kế để đánh địa chỉ trên một đường Link phục vụ cho mục đích như cấu hình địa chỉ tự động, neighbor discovery hoặc khi không có Router nào.   
* Cấu trúc của Link-Local Address.
![linklocal](https://github.com/nhuhp/CCNA/blob/master/IPv6/img/linklocal.png)

* Các Link-Local Address thuộc dãy **FE:80::/10**, tuy nhiên chúng sẽ thường có prefix **FE:80**. Các địa chỉ có prefix khác nên sử dụng với mục địch thử nghiệm.
* Một số điểm quan trọng của Link-Local Address.
	- Để trở thành một thiết bị sử dụng IPv6, thiết bị đó phải có Link-Local Address, không cần có IPv6 Global Unicast Address cũng được.
	- Router sẽ không forward Packet nào có Link-Local Address.
	- Link-Local chỉ cần duy nhất trong đường Link hoặc Segment. Các Interface khác nhau trên một thiết bị có thể có Link-Local Address giống nhau.

* Một số cách sử dụng Link-local Address trên thiết bị IPv6.
	- Khi một thiết bị khởi động, trước khi nhận được Global Unicast Address, nó sẽ sử dụng IPv6 Link-Local Address làm Source Address để giao tiếp với các thiết bị khác trong mạng, bao gồm Local Router.
	- Các thiết bị sử dụng Link-Local Address của Router làm Default Gateway của chúng.
	- Các thiết bị trao đổi các message IPv6 dynamic Routing Protocol (RIPng, OSPFv3, EIGRP for IPv6) từ Link-Local Address của chúng.
	- Các entry IPv6 Routing Table cho dynamic Routing Protocol sử dụng IPv6 Link-local Address làm Next-hop Address.
	
* Các cách cấu hình Link-Local Address.
	- Thiết bị sẽ tự động tạo IPv6 Link-Local Address của chính nó khi khởi động.
	- Cấu hình bằng tay.

<a name="loopback"></a>
##### 4.1.3. Loopback Address
* **Loopback Address** là Unicast Address **0:0:0:0:0:0:0:1** hay **0::1**.
* Nó thường được một Node sử dụng bởi để gửi IPv6 Packet cho chính nó, cụ thể trong việc kiểm tra chồng giao thức TCP/IP.
* Loopback Address có một số tính chất sau.
	- Loopback Address không thể gán cho Interface vật lý.
	- Một Packet có Loopback Address làm Source Address hay Destination Address sẽ không bao giờ được gửi ra bên ngoài thiết bị.
	- Một Router sẽ không bao giờ forward một Packet có Destination Address là một Loopback Address.
	- Một thiết bị phải drop một Packet nhận được trên một Interface nếu như Destination Address của nó là một Loopback Address.

<a name="unspecified"></a>
##### 4.1.4. Unspecified Address
* **Unspecified Address** có các bit đều bằng 0, **0:0:0:0:0:0:0:0** hay **::**.
* Unspecified Address thường được dùng làm Source Address để thể hiện sự "absence" của một Address.
* Nó không được gán cho Interface nào cả.
* Unspecified Address có một số tính chất sau.
	- Unspecified Address thường được dùng để thể hiện sự "absence" của một Address.
	- Unspecified Address không thể gán cho một Interface vật lý.
	- Unspecified Address không thể làm Destination Address.
	- Một Router không bao giờ forward một Packet có Unspecified Address làm Source Address.

<a name="uniquelocal"></a>
##### 4.1.5. Unique Local Address
* **Unique Local Address** tương đương với IPv4 Private Address.
* Unique Local Address cũng được biết như là IPv6 Private Address hay IPv6 Local Address (tránh nhầm lẫn với Link-Local Address).
* Unique Local Address (ULA) cũng được sử dụng như Global Unicast Address, ngoại trừ nó được sử dụng Private và không được định tuyến ở Internet toàn cầu.
* Unique Local Address chỉ được sử dụng trong một khu vực giới hạn, chẳng hạn như trong phạm vị một Site hay được định tuyến với một lượng giới hạn Administrative Domains.
* Unique Local Address dùng cho những thiết bị không bao giờ cần truy cập Internet.
* Cấu trúc của Unique Local Address.
![uniquelocal](https://github.com/nhuhp/CCNA/blob/master/IPv6/img/uniquelocal.png)

* Unique Local Address có một số tính chất sau.
	- Chúng được sử dụng giống như Global Unicast Address.
	- Chúng được sử dụng cho những thiết bị không cần truy cập đến hoặc từ Internet.
	- Chúng cho phép các Site được kết hợp hoặc liên kết nối một cách Private mà không có sự xung đột địa chỉ hay yêu cầu về việc đánh địa chỉ. (Xung đột địa chỉ hiếm khi xảy ra vì không gian địa chỉ lớn)
	- Chúng độc lập với bất kỳ ISP nào và có thể sử dụng trong một Site ngay cả khi không có kết nối Internet.

<a name="ipv4embedded"></a>
##### 4.1.6. IPv4 Embedded Address
* IPv4 Embedded Address là IPv6 Address được dùng để hỗ trợ viện chuyển dổi IPv4 sang IPv6.
* IPv4 Embedded Address mang một IPv4 Address bên trong một IPv6.
* RFC 4291 định nghĩa 2 loại IPv4 Embedded Address.
	- IPv4-mapped IPv6 Address.
	- IPv4-compatible IPv6 Address.  (không dùng nữa)
	
* IPv4-mapped IPv6 Address có thể được sử dụng bởi một thiết bị Dual-stack để gửi một IPv6 Packet đến một thiết bị chỉ sử dụng IPv4.
* 80 bit đầu được set bằng 0, 16 bit trước phần IPv4 được set bằng 1, 32 bit cuối để chứa IPv4 dưới dạng thập phân có chấm. Như vậy, 96 bit đầu được biểu diễn dưới dạng hexa, 32 bit cuối chứa IPv4 ở dưới dạng thập phân có dấu chấm.
![ipv4mappedipv6](https://github.com/nhuhp/CCNA/blob/master/IPv6/img/ipv4mappedipv6.png)

<a name="multicastaddress"></a>
#### 4.2. Multicast Address
* Multicast là kỹ thuật mà một thiết bị dùng nó để gửi một Packet đến nhiều đích cùng một lúc (one-to-many).
* Đa đích (Multiple Destination) có thể là các Interface trên cùng một thiết bị, nhưng thường là trên những thiết bị khác nhau.
* Một **IPv6 Multicast Address** định danh cho một nhóm các Interface (hoặc các thiết bị), được gọi là **Multicast Group**. IPv6 Multicast Address tương đương với IPv4 Multicast Address 224.0.0.0/4.
* Một Packet gửi đến Multicast Group luôn luôn có Source Address là một Unicast Address. Multicast Address không bao giờ đóng vai trò Source Address.
* IPv6 Multicast Address dùng prefix **ff00::/8**.
* Cấu trúc của IPv6 Multicast Address
![multicastaddress](https://github.com/nhuhp/CCNA/blob/master/IPv6/img/multicastaddress.png)

	- 8 bit đầu được set bằng 1.
	- 4 bit kế tiếp dùng đánh dấu các Flag.
	- 4 bit kế tiếp dùng cho trường **Scope**. Trường Scope định nghĩa phạm vi mà Router được phép forward Multicast Packet.
	- 112 bit còn lại dùng cho **Group ID**.

* Trường Flag có 4 bit.0 R P T
	- 0 : reserved, được khởi tạo bằng 0.
	- R : rendezvous point. RFC3956.
	- P : network prefix. RFC3306. 
	- T : định nghĩa cho 2 loại Multicast Address.
		+ T = 0, Predefined Multicast Address, được cấp bởi IANA.
		+ T = 1, Transient or Dynamically assigned Multicast Address (Multicast Address được cấp động). Chúng thường được cấp bởi các ứng dụng Multicast.

* Có 2 loại Predefined Multicast Address.
	- Well-known Multicast Address
	- Solicited-node Multicast Address
	
* **Well-known Multicast Address** có prefix **ff00::/12**.
* Một số Well-known Multicast Address thường dùng:
	- **ff02::1**: Tất cả các thiết bị IPv6.
	- **ff02::2**: Tất cả các Router IPv6.
	- **ff02::5**: Tất cả các Router chạy OSPFv3.
	- **ff02::a**: Tất cả các Router chạy EIGRP for IPv6.
	
* **Solicited-node Multicast Address** được dùng trong quá trình phân giải địa chỉ Layer3-to-Layer2, dùng giống như ARP trong IPv4.
* Solicited-node Multicast Address được tạo một cách tự động bằng cách sử dụng cơ chế mapping đặc biệt giữa Unicast Address của thiết bị với Solicited-node Multicast prefix **ff02:0:0:0:0:1:ff00::/104**.
* Solicited-node Multicast Address được tạo một cách tự động cho mỗi Unicast Address trên thiết bị.

<a name="anycastaddress">
#### 4.3. Anycast Address
* **Anycast Address là địa chỉ có thể gán có nhiều hơn một Interface (thường là trên những thiết bị khác nhau). Nói cách khác, nhiều thiết bị có thể có cùng một Anycast Address.
* Một Packet được gửi đến một Anycast Address, sẽ được định tuyến tới Interface gần nhất (nearest) mang địa chỉ đó, dựa trên Bảng định tuyến của Router.
![multicastaddress](https://github.com/nhuhp/CCNA/blob/master/IPv6/img/multicastaddress.png)

* Anycast Address có trên IPv4 và IPv6, được định nghĩa trong RFC 1546, Host Anycasting Service. Anycast có ý nghĩa trong việc sử dụng các Service như DNS và HTTP, nhưng không bao giờ được triển khai trong thiết kế.
* Không có một prefix cụ thể nào cho Anycast Address.


---


### Tài liệu tham khảo:

[1] IPv6 The History and Timeline. https://www.ipv6.com/general/ipv6-the-history-and-timeline/

[2] Header của IPv6. https://vnpro.vn/thu-vien/header-cua-ipv6-2394.html

[3] IPv6 Header. http://euclid.nmu.edu/~rappleto/Classes/CS442/Notes/IPv6_Header.html

[4] Tìm hiểu về IPv6. https://www.vnnic.vn/ipv6/thamkhao/t%C3%ACm-hi%E1%BB%83u-v%E1%BB%81-ipv6-header

[5] http://www.ciscopress.com/articles/article.asp?p=2803866&seqNum=4

[6] https://study-ccna.com/ipv6-global-unicast-addresses/

[7] RFC4291. https://tools.ietf.org/html/rfc4291


---

### Hết

