## Phân Loại IPv6

> Tài liệu: IPv6
> 
> Thực hiện: **Phạm Hoàng Nhu**
> 
> Cập nhật lần cuối: **13/03/2019**

### Mục lục
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

* Trường Flag có 4 bit.
```
0 R P T
```
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

<a name="anycastaddress"></a>
#### 4.3. Anycast Address
* **Anycast Address** là địa chỉ có thể gán có nhiều hơn một Interface (thường là trên những thiết bị khác nhau). Nói cách khác, nhiều thiết bị có thể có cùng một Anycast Address.
* Một Packet được gửi đến một Anycast Address, sẽ được định tuyến tới Interface gần nhất (nearest) mang địa chỉ đó, dựa trên Bảng định tuyến của Router.
![multicastaddress](https://github.com/nhuhp/CCNA/blob/master/IPv6/img/multicastaddress.png)

* Anycast Address có trên IPv4 và IPv6, được định nghĩa trong RFC 1546, Host Anycasting Service. Anycast có ý nghĩa trong việc sử dụng các Service như DNS và HTTP, nhưng không bao giờ được triển khai trong thiết kế.
* Không có một prefix cụ thể nào cho Anycast Address.


---


### Tài liệu tham khảo:

[1] http://www.ciscopress.com/articles/article.asp?p=2803866&seqNum=4

[2] https://study-ccna.com/ipv6-global-unicast-addresses/

[3] RFC4291. https://tools.ietf.org/html/rfc4291

---

### Hết

