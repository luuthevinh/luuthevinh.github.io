---
title:  "Xét và xử lý va chạm bằng Swept AABB"
date:   2016-11-28 08:50:14 +0700
categories: bai-viet
tags: [ aabb, swept-aabb, collision, game ]
description: "Xin chào các bạn, bài viết này mình sẽ giới thiệu cho các bạn về xử lý va chạm trong game bằng Swept AABB"
image: /assets/images/swept-aabb-03.png
---

Xin chào các bạn, bài viết hôm nay mình sẽ giới thiệu cho các bạn về xử lý va chạm trong game bằng **Swept AABB**. Khi các bạn tự viết framework có phần xử lý va chạm thì có thể sử dụng phương pháp này.

## AABB là gì?
AABB là viết tắt của Axis-Aligned Bounding Box, nó là thuật toán xét va chạm giữa các cạnh của **hình chữ nhật** mà ở đây các cạnh này nó **song song** với cùng **hệ trục tọa độ**. Vậy cơ bản là xét xem 2 hình chữ nhật nó có chồng lên nhau hay không.

## Swept AABB?
Với Swept AABB, chúng ta sẽ xem trước ở frame kế tiếp hình chữ nhật mình có va chạm hay không. Có thì mình xử lý nó theo ý mình muốn.

# Ví dụ:
Ở **frame đầu tiên**, hình chữ nhật chưa có va chạm, **frame tiếp theo** hình chữ nhật di chuyển 1 đoạn và va chạm với thanh màu xanh. Mình sẽ dự đoán trước và quyết định vị trí kế tiếp của nó, như ở đây nó sẽ đi đoạn ngắn hơn thay vì như bình thường sẽ là ở vị trí nét đứt.

![ví dụ swept aabb][example-01]

## Xét va chạm AABB

Để xem 2 hình chữ nhật có va chạm hay không thì mình sẽ xét từng cặp cạnh của 2 hình như sau:

![ví dụ 2 hình chữ nhật va chạm với nhau][example-03]

Các bạn để ý sẽ thấy, khi 2 hình đè lên nhau thì mình luôn có **ĐỒNG THỜI**:

* other.left <= object.right
* other.right >= object.left
* other.top  >= object.bottom
* other.bottom <= object.top

Như vậy nếu **1 trong 4** điều kiện trên **sai** thì 2 hình **không có va chạm** với nhau.

![ví dụ 2 hình chữ nhật chưa va chạm][example-02]

Ví dụ ở đây ta có **left** của **other** lớn hơn **right** của **object**, nên không có va chạm.

**Tất cả ví dụ mình lấy gốc tọa độ là bottom-left**

Hàm xét va chạm có thể viết đơn giản như sau:

<script src="https://gist.github.com/luuthevinh/68db8232903c82ea4256242242d7d391.js"></script>

Ở trên, mình có hàm kiểm tra hai Rect có va chạm với nhau hay không, mình lấy điều kiện ngược lại cho gọn hơn.

## Xét va chạm với Swept AABB

Đầu tiên, mình tìm khoảng cách giữa các cạnh của hai hình

![ví dụ 2 hình chữ nhật chưa va chạm][example-04]

Trong đó,

* **dxEntry**, **dyEntry**: là khoảng cách cần đi để các bắt đầu va chạm.
* **dxExit**, **dyExit**: là khoảng cách cần đi kể từ lúc này để khi hết va chạm.

Nó cũng như top, left, right, bottom của AABB cơ bản phía trên. Mình xét thêm hướng của vận tốc, để nó đồng bộ với dấu lúc sau tính thời gian không ngược khi có va chạm.

<script src="https://gist.github.com/luuthevinh/5a4c3e6aae21f7fd67b6e9caee30bc7e.js"></script>

Tiếp theo, từ khoảng cách và vận tốc, mình tìm thời gian để bắt đầu và kết thúc va chạm:

> thời gian = quãng đường / vận tốc

<script src="https://gist.github.com/luuthevinh/112130880384f8e5a11207d7ab403cfd.js"></script>

Để xảy ra va chạm, cả hai trục x và y phải **ĐỒNG THỜI XẢY RA VA CHẠM**, vậy mình lấy thời gian bắt để đầu va chạm **lớn nhất**.

Còn khi hết va chạm, chỉ cần 1 trong 2 trục thoát khỏi là được, nên mình lấy thời gian kết thúc va chạm **nhỏ nhất** giữa 2 trục x, y.

<div align="center" style="margin-bottom: 20px;"><img src="http://i.giphy.com/3oz8xAmf8Tsid9dCqk.gif" alt="ví dụ 2 hình chữ nhật bắt đầu xảy ra va chạm" title="Ví dụ hai hình chữ nhật bắt đầu xảy ra va chạm"></div>

<script src="https://gist.github.com/luuthevinh/023f87bcf897174ca61ad4a13147d572.js"></script>

Có được thời gian rồi thì mình bắt đầu xét va chạm:

* **Lớn hơn 1.0f**: frame tiếp theo nó vẫn chưa thể va chạm.
* **Thời gian để kết thúc va chạm nhỏ hơn 0.0f**: 2 hình chữ nhật đang đi ra xa nhau.
* **Thời gian để kết thúc va chạm phải lớn hơn thời gian để va chạm** (va chạm xong rồi thì sau đó mới hết chứ đúng không).

Khi có thể va chạm mình sẽ trả về thời gian va chạm đó, còn không trả về 1.0f

# Xử lý va chạm

Dựa vào thời gian trả về, mình tính quãng đường đi tiếp theo

<script src="https://gist.github.com/luuthevinh/333075430d03eed557da61e274d63f1f.js"></script>

# Broad-Phasing

Tối ưu một tí, mình dùng **Broad-Phasing** để xét xem nó có thể va chạm trong frame tiếp theo không, không thì mình return luôn cho nhanh.

![ví dụ Broad-Phasing][example-05]

Mình tạo 1 hình chữ nhật dựa trên **vị trí ban đầu** và **kế tiếp**, sau đó lấy hình chữ nhật đó xét xem có chồng lên với hình kia không. Nếu có thì va chạm, còn không thì chắc chắn không thể nên không cần xét tiếp.

<script src="https://gist.github.com/luuthevinh/ebf18994458598428835c8d095767356.js"></script>

Cuối cùng là thêm vào phần đầu của hàm xét va chạm

<script src="https://gist.github.com/luuthevinh/c0de3dc33d96267537d5ecc8f28175a4.js"></script>

# Kiểm tra hướng va chạm

Mình dựa vô vị trí của object để suy ra hướng

<script src="https://gist.github.com/luuthevinh/6d9422f2f885b7f248725921b2fdef2d.js"></script>

Cơ bản xét va chạm chỉ cần thế thôi, từ đây thì các bạn có thể dùng cho các trường hợp cụ thể mà chỉnh sửa thêm cho phù hợp với nó.

> Trường hợp 2 vật di chuyển, các bạn tính lại vận tốc của **object** so với **other** là được, xem object di chuyển còn other đứng yên.

Full code các bạn có thể xem ở đây: [Swept AABB]

Bài viết tham khảo: [Swept AABB Collision Detection and Response]

Các bạn có thắc mắc hay phát hiện sai sót gì thì comment bên dưới nhé. 

Cám ơn và hẹn gặp lại.

[example-01]: /assets/images/swept-aabb-01.png "Ví dụ Swept AABB"
[example-02]: /assets/images/swept-aabb-02.png "2 hình chữ nhật không chồng lấp lên nhau"
[example-03]: /assets/images/swept-aabb-03.png "2 hình chữ nhật chồng lấp lên nhau"
[example-04]: /assets/images/swept-aabb-04.png "Khoảng cách giữa các cạnh để tính thời gian với vận tốc cho Swept AABB"
[example-05]: /assets/images/swept-aabb-05.png "Ví dụ Broad-Phasing"
[example-06]: http://i.giphy.com/3oz8xAmf8Tsid9dCqk.gif "Ví dụ hai hình chữ nhật bắt đầu xảy ra va chạm"

[Swept AABB]: https://gist.github.com/luuthevinh/42227ad9712e86ab9d5c3ab37a56936c
[Swept AABB Collision Detection and Response]: http://www.gamedev.net/page/resources/_/technical/game-programming/swept-aabb-collision-detection-and-response-r3084