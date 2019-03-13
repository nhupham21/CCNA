## Tổng quan IPv6 

> Tài liệu: IPv6
> 
> Thực hiện: **Phạm Hoàng Nhu**
> 
> Cập nhật lần cuối: **13/03/2019**

### Mục lục
[1. Tổng quan về IPv6](#tongquan)
- [1.1. Lịch sử](#lichsu)
- [1.2. Không gian IP](#khonggian)
- [1.3. Các tính năng của IPv6](#tinhnang)

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

---

### Tài liệu tham khảo:

[1] IPv6 The History and Timeline. https://www.ipv6.com/general/ipv6-the-history-and-timeline/

[2] Tìm hiểu về IPv6. https://www.vnnic.vn/ipv6/thamkhao/t%C3%ACm-hi%E1%BB%83u-v%E1%BB%81-ipv6-header

---

### Hết

