## IPv6 Format

> Tài liệu: IPv6
> 
> Thực hiện: **Phạm Hoàng Nhu**
> 
> Cập nhật lần cuối: **13/03/2019**

### Mục lục

[3. Format](#format)

---

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

---


### Tài liệu tham khảo:

[1] IPv6 Address Format. https://study-ccna.com/ipv6-address-format/


---

### Hết

