## IPv6 Header

> Tài liệu: IPv6
> 
> Thực hiện: **Phạm Hoàng Nhu**
> 
> Cập nhật lần cuối: **13/03/2019**

### Mục lục
[2. IPv6 Header](#header)
- [2.1. IPv6 Header](#ipv6header)
- [2.2. Extension Header](#extensionheader)

---

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


---


### Tài liệu tham khảo:

[1] Header của IPv6. https://vnpro.vn/thu-vien/header-cua-ipv6-2394.html

[2] IPv6 Header. http://euclid.nmu.edu/~rappleto/Classes/CS442/Notes/IPv6_Header.html


---

### Hết

