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
	- **Destination Address** (128 bit): lưu IPv6 Address của host Đích. Hầu hết, Destination Address là Address cuối cùng mà Packet đi đến. Tuy nhiên, nếu có một Routing Extension Header, thi Destination Address có thể là next router interface trong source route list.

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
	
<a name="unicast"></a>
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
##### 4.1.5. Global Unicast Address
* **Global Unicast Address** hay còn được gọi là *Aggregatable Global Unicast Address*, là loại địa chỉ được sử dụng trên toàn cầu, reachable trong IPv6 Internet.
* Nó tương đương với IPv4 Public Address. Hiện tại, IANA chỉ cấp dải **2000::/3** cho Global Unicast Address.
* Cấu trúc của một Global Unicast Address:
![gua](https://github.com/nhuhp/CCNA/blob/master/IPv6/img/gua.png)		

---


### Tài liệu tham khảo:

[1] IPv6 The History and Timeline. https://www.ipv6.com/general/ipv6-the-history-and-timeline/

[2] Header của IPv6. https://vnpro.vn/thu-vien/header-cua-ipv6-2394.html

[3] IPv6 Header. http://euclid.nmu.edu/~rappleto/Classes/CS442/Notes/IPv6_Header.html

[4] Tìm hiểu về IPv6. https://www.vnnic.vn/ipv6/thamkhao/t%C3%ACm-hi%E1%BB%83u-v%E1%BB%81-ipv6-header

[5] http://www.ciscopress.com/articles/article.asp?p=2803866&seqNum=4

[6] https://study-ccna.com/ipv6-global-unicast-addresses/

---

### Hết
