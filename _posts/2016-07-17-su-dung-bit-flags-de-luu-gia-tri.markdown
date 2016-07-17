---
title: "Sử dụng bit flags để lưu giá trị"
date: 2016-07-17 14:00:00 +0700
categories: bai-viet
tags: [bit, bit-flags]
---

# Bit flags là gì?
Sử dụng bit flags là cách chúng ta sử dụng **thứ tự các bit** của biến được lưu trong bộ nhớ để quy định **một loại giá trị**. Từ đó mình có thể lưu được nhiều giá trị nhưng chỉ cần một biến.

Ví dụ ta có kiểu enum quy định các loại giá trị như thế này để sử dụng bit flags.

```c
enum eDirection
{
	TOP 	= 1,		// 0001
	LEFT 	= 2,		// 0010
	BOTTOM 	= 4,		// 0100
	RIGHT 	= 8		// 1000
}
```

Các bạn thấy giá trị của các enum này nếu là cơ số 2 thì mỗi loại có bit 1 ở vị trí khác nhau.

Bây giờ các bạn có một biến để lưu hướng như theo ví dụ trên:

```c
eDirection _direction;
```

# Thêm một giá trị

Bình thường bạn gán một giá trị cho nó như thế này.

```c
_direction = eDirection::TOP;
```

Vậy khi bạn muốn gán nhiều giá trị cho một biến, bạn chỉ cần sử dụng phép OR để gán thêm cho nó. 

```c
_direction |= eDirection::LEFT;
```

Khi đó giá trị biến **_direction** sẽ trở thành **0011**, có thể hiểu nó được bật 2 cờ tại các giá trị tương ứng là **TOP và LEFT**.

```c
	0001
OR	0010
=	0011
```

# Kiểm tra giá trị

Bạn muốn kiếm tra giá trị có trong biến thì các bạn dùng:

```c
if((_direction & eDirection::TOP) == eDirection::TOP)
{
	// làm gì đó...
}
```

Khi **AND** với biến hiện tại, nếu vị trí của bit ở giá trị cần kiểm tra và **biến cùng là 1**, nó sẽ **giữ lại** còn những vị trí **khác sẽ trở thành 0**. Kết quả là nó sẽ ra là giá trị muốn kiểm tra. Sau đó khi đem so sánh với giá trị đó, nếu bằng đương nhiên là nó có chứa trong đó. Cách này sẽ kiểm tra được giá trị toàn bit 0 hoặc toàn bit 1.

Theo ví dụ trên, biến **_direction** hiện tại là **0011**, khi **AND với 0001** nó sẽ bằng **0001** nghĩa là nó có giá trị **TOP**.

```c
	0011
AND	0001
=	0001
```

Vậy muốn kiểm tra nhiều giá trị thì sao? Tương tự như trên:

```c
if((_direction & (eDirection::TOP | eDirection::RIGHT)) == (eDirection::TOP | eDirection::RIGHT))
{
	// làm gì đó...
}
```

# Xóa giá trị

Bạn muốn xóa giá trị đó ra khỏi biến hiện tại, các bạn **AND cho NOT** của giá trị đó:

```c
_direction &= ~eDirection::TOP;
```

hoặc bạn **XOR** cho giá trị đó:

```c
_direction ^= eDirection::TOP;
```

Theo mình, bạn **nên dùng AND với NOT**, để khi không có giá trị trong đó, nó sẽ **không bị thay đổi biến hiện tại** của mình.

– **NOT của TOP** là **1110**, khi **AND** với _direction là **0011** nó sẽ còn lại **0010** là giá trị **LEFT**.

```c
	0011
AND	1110
=	0010
```

– khi lấy **0001 XOR** với **0011** nó bằng **0010** là giá trị **LEFT**.

```c
	0011
XOR	0010
=	0010
```

Cả 2 **đều đúng nếu đã có giá trị LEFT** trong đó, giả sử mình kiểm tra giá trị RIGHT.

– **NOT của RIGHT** là **0111**, khi **AND** với **0011** nó sẽ ra **0011** là giá trị **TOP LEFT**, do RIGHT không có nên nó không xóa đi.

```c
	0011
AND	0111
=	0011
```

– Còn khi dùng **1000 XOR 0011** nó sẽ ra **1011** là giá trị **TOP LEFT RIGHT**, vậy cách này để dùng cho trường hợp nếu chưa có thì gán thêm còn có rồi thì xóa đi.

```c
	0011
XOR	1000
=	1011
```

Bên trên là cách sử dụng bit flags mà mình tìm được và dùng thấy khá là có ích trong các trường hợp mình gặp phải. 

Ví dụ hồi trước mình làm cho cái font chữ, nó cũng có các trạng thái như là in đậm, nghiêng, gạch dưới… hoặc trạng thái của các nhân vật trong game chẳng hạn.

Các bạn có thắc mắc hay góp ý gì về bài viết sử dụng bit flags này cứ thoải mái comment bên dưới để cùng thảo luận nhé.

Tạm biệt và hẹn gặp lại các bạn trong những bài tiếp theo.