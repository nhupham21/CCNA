# NETWORKING FUNDEMENTALS

## 1. What is a Network?
* **Network** là hệ thống bao gồm các *end devices* (PC, Laptop, Smartphone, ...), và *network devices*  (Router, Switch, Hub, Bridge, Firewall,...), được kết nối vật lý thông qua cables, telephone lines, radio waves, satellites,... ; giao tiếp với nhau dựa trên các ***protocols*** ; mục đích để trao đổi thông tin, giao tiếp, chia sẻ tài nguyên,...
 
 ![networking-fundementals-1](https://github.com/nhuhp/CCNA/blob/master/Networking-Fundementals/img/networking-fundementals-1.png)
 
 * Nhiều network có thể liên kết với nhau tạo thành một network lớn hơn.
 * Các thành phần vật lý của một network:
	- End devices : PC, Laptop, Smartphone, Printer,...
	- Network devices : Router, Switch, Hub, Bridge, Firewall,...
	- Cables
	
## 2. Lợi ích của Network:
* Kết nối và Giao tiếp
* Chia sẻ dữ liệu và tài nguyên
* Chia sẻ phần cứng
* Truy cập internet
* Quản lý và Bảo mật dữ liệu

## 3. Các đặc trưng của Network:
* Speed (Tốc độ)
* Cost (Giá thành)
* Security (Bảo mật)
* Availability (Khả dụng)
* Scalability (Khả năng mở rộng)
* Reliability (Độ tin cậy)
* Topology (Tính mô hình)

## 4. Một số loại topology:
* *Logical topology*: thể hiện cách sắp xếp của các thiết bị trên hệ thống mạng và cách mà chúng giao tiếp với nhau.
* *Physical topology*: mô tả sơ đồ kết nối vật lý của các thiết bị mạng, các thiết bị kết nối thực tế với nhau như thế nào, ...
* Một số loại *Physical topology* thông dụng:
	- *Bus topology*: 
		![bus-topology](https://github.com/nhuhp/CCNA/blob/master/Networking-Fundementals/img/bus-topology.png)
		+ Các node được kết nối đến một đường dây dẫn duy nhất, nhờ các connector. Đường dây này là xương sống (backbone) của cả hệ thống mạng.
		+ Tín hiệu từ một điểm sẽ được truyền theo các hướng đi đến toàn bộ mọi điểm cho đến khi nó đến được điểm muốn đến. Dữ liệu sẽ được nhận chỉ khi địa chỉ của máy khớp với địa chỉ mà dữ liệu muốn đến.
		+ Ưu điểm: Do chỉ sử dụng một đường dây nên chi phí triển khai không cao.
		+ Nhược điểm: Dễ gây ra Single Point of Failure (SPOF); Dữ liệu đến đích chậm; Tồn tại đường dữ liệu không mong muốn.
	- *Star topology*:
		![star-topology](https://github.com/nhuhp/CCNA/blob/master/Networking-Fundementals/img/star-topology.png)
		+ Các node sẽ kết nối với một node trung tâm, kết nối giữa node trung tâm với một node khác là kết nối point-to-point.
		+ Mọi đường dữ liệu trên hệ thống đều phải thông qua node trung tâm. Node trung tâm đóng vai trò như server, các node còn lại là client.
		+ Ưu điểm: Dễ thiết kế và triển khai; Dễ mở rộng.
		+ Nhược điểm: Do mọi dữ liệu đều đi qua node trung tâm nên dễ gây ra Single Point of Failure.
		+ Mở rộng: Extended-Star Topology.
		![extended-star-topology](https://github.com/nhuhp/CCNA/blob/master/Networking-Fundementals/img/extended-star-topology.png)
	- *Ring topology*: 
		![ring-topology](https://github.com/nhuhp/CCNA/blob/master/Networking-Fundementals/img/ring-topology.png)
		+ Các node kết nối liên tiếp nhau tạo thành một vòng tròn khép kín. Các node có vai trò ngang nhau, không tồn tại quan hệ server-client.
		+ Dữ liệu đi theo một hướng nhất định, và sẽ đi qua các node trong vòng cho đến khi đến đích.
		+ Ưu điểm: Năng suất cao hơn bus topology; Không cần server điều khiển kết nối giữa các node.
		+ Nhược điểm: Dữ liệu bị lặp vòng, gây ra Single Point of Failure.
		+ Mở rộng: Dual-Ring Topology.
		![dual-ring-topology](https://github.com/nhuhp/CCNA/blob/master/Networking-Fundementals/img/dual-ring-topology.png)
	- *Ful-Mesh topology*:
		![full-mesh-topology](https://github.com/nhuhp/CCNA/blob/master/Networking-Fundementals/img/full-mesh-topology.png)
		+ Các node trong hệ thống đều có kết nối vật lý với nhau.
		+ Không cần packet switching hoặc broadcast.
		+ Số lượng kết nối tăng đáng kể.
		+ Ưu điểm: Khả năng chịu lỗi cao.
		+ Nhược điểm: Hệ thống phức tạp; Chi phí triển khai cao.
	- *Partial-Mesh topology*:
		![partial-mesh-topology](https://github.com/nhuhp/CCNA/blob/master/Networking-Fundementals/img/partial-mesh-topology.png)
		+ Một số node nhất định sẽ được kết nối đến một node khác, nhưng có một node được kết nối với 2 hoặc nhiều node khác với một liên kết point-to-point.
		+ Giảm thiểu được kết nối thừa trong full-mesh topology, ít phức tạp hơn full-mesh topology.
		+ Đảm bảo được khả năng chịu lỗi và chi phí triển khai.
		