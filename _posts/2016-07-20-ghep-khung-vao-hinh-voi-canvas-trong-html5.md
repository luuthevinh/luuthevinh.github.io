---
title: Ghép khung vào hình với Canvas trong HTML5
date: 2016-07-20
author: Vinh
layout: post
categories: bai-viet
tags: [ canvas, ghep-hinh, html, html5, javascript ]
image: /assets/images/thumbnail-ghep-khung-vao-hinh-voi-canvas-trong-html5.png
---
Xin chào các bạn,
  
Trong bài này mình sẽ hướng dẫn các bạn cách ghép hình bằng Canvas trong HTML5, cụ thể ở đây mình ghép khung vào hình mà mình chọn từ máy lên. Các bạn có thể xem <a href="http://luuthevinh.me/up-2016/" target="_blank">demo</a> ở đây.

# Ý tưởng ghép khung vào hình

Ý tưởng cơ bản là mình vẽ lần lượt hai hình lên canvas, sau đó di chuyển, phóng to thu nhỏ hay xoay hình rồi cho tải về là xong. Rất đơn giản phải không?

# Vẽ khung hình

Trong file html, các bạn thêm các thẻ `<canvas>`, `<input>` và `<img>` như sau:

<script src="https://gist.github.com/luuthevinh/d964d1aa6c4d41f7379e93c036cb3de3.js"></script>

Trong đó,

  * Thẻ `canvas` để mình sử dụng vẽ hình.
  * Thẻ `img` dùng để chứa hình mà mình tải lên.

Tiếp theo, các bạn tạo file js để code một số xử lý hình ảnh.

<script src="https://gist.github.com/luuthevinh/a9df7f342273e3597764778bc44389b4.js"></script>

Mình lấy các đối tượng trong HTML như `image`, `canvas` và `context` và tạo các biến cơ bản. Sau đó tạo đối tượng **frame** là khung hình muốn ghép.

Ở hàm `onload` của **frame**, mình vẽ khung hình lên context.

<script src="https://gist.github.com/luuthevinh/4c65e552d45f7afa44b223852691b5aa.js"></script>

Ban đầu đơn giản chỉ xóa canvas rồi vẽ frame hình lên trước, các bạn có thể xem thêm hàm [drawImage()](http://www.w3schools.com/tags/canvas_drawimage.asp){:target="_blank"}, [clearRect()](http://www.w3schools.com/tags/canvas_clearrect.asp){:target="_blank"}

# Load hình

Tiếp theo mình sẽ load hình lên.

Trong file HTML các bạn thêm thẻ **input** để up hình.

<script src="https://gist.github.com/luuthevinh/d625116940380a89ccbb30b295fe5045.js"></script>

Viết sự kiện **change** cho input.

<script src="https://gist.github.com/luuthevinh/7ab371366a07171c034fbbdff515fc07.js"></script>

Khi hình được load lên, mình sử dụng `FileReader` để đọc dữ liệu từ `input` và cập nhật lại source hình từ dữ liệu đó cho đối tượng **userImage**.

<script src="https://gist.github.com/luuthevinh/7c04012eb70ea135c5177af3851bfba6.js"></script>

Ở hàm `onload` của **userImage**, mình sẽ tìm cạnh nhỏ nhất để scale hình cho vừa với khung. Và lưu giá trị vào **minScale** để giới hạn giá trị scale. (mình thích không cho người ta scale nhỏ hơn canvas thôi). 

Giờ mình có hình rồi nên ta cập nhật thêm cho hàm `drawCurrentImage()`

<script src="https://gist.github.com/luuthevinh/91d6452a1eb970fdd8414f356a095e57.js"></script>

Các bạn `save()` context hiện tại trước khi thay đổi để vẽ `userImage`, rồi nhớ `restore()` để trả về mặc định cho context trước khi vẽ cái khác.

Để vẽ theo tâm của hình (origin là tâm của hình), mình dùng cách [translate()](http://www.w3schools.com/TAgs/canvas_translate.asp){:target="_blank"} chuyển hình đến tọa độ mong muốn. Sau đó khi vẽ bằng [drawImage()](http://www.w3schools.com/tags/canvas_drawimage.asp){:target="_blank"}, ta vẽ lùi về một nữa kích thước ngang, dọc của hình.

Và mình dùng [rotate()](http://www.w3schools.com/tags/canvas_rotate.asp){:target="_blank"}, [scale()](http://www.w3schools.com/tags/canvas_scale.asp){:target="_blank"} để gán các giá trị tương ứng cho context.

# Di chuyển

**Ý tưởng:** mỗi khi chuột di chuyển mình sẽ tính toán độ thay đổi của chuột so với lần trước rồi cập nhật lại vị trí của hình.

Các bạn thêm các biến như bên dưới và các sự kiện `mousedown`, `mousemove`, `mouseup`, `mouseout` cho canvas.

<script src="https://gist.github.com/luuthevinh/2c09826a28f759c769fb3885c3a1bfa7.js"></script>

Ở sự kiện cho `mousemove`, ta cập nhật vị trí hiện tại của hình với độ thay đổi của chuột. Các sự kiện `mouseup`, `mousedown` và `mouseout` để xác định trạng thái kéo thả hình.

Mình thêm giới hạn cho tọa đổ để không kéo hình lệch xa quá, chỉ cho nó vừa khung và di chuyển hết hình thôi.

<script src="https://gist.github.com/luuthevinh/425df3941532cbc426ad8a63ed483bd8.js"></script>

Các bạn thêm vào hàm `drawCurrentImage()`, mình tính phần dư của hình so với canvas, phần này sẽ là giới hạn di chuyển của tọa độ. Nếu tọa độ thấp hơn hoặc lớn hơn thì mình không cho phép vượt qua.

# Thay đổi giá trị scale

Với giá trị scale, ta thêm thanh `input` `range` để kéo thay đổi giá trị vào HTML như sau

<script src="https://gist.github.com/luuthevinh/3417b5ea8e127b78bf2e1fa429729bec.js"></script>

Mỗi lần thay đổi giá trị, mình cập nhật lại **imageScale** với giá trị của **scaleBar**.

<script src="https://gist.github.com/luuthevinh/c0ddc61608e6c66b0d0346e083b3189d.js"></script>

# Thay đổi góc xoay

Để thay đổi góc xoay, các bạn thêm vào file HTML hai nút xoay trái/phải.

<script src="https://gist.github.com/luuthevinh/f330a5f1a452f05db37e5894b513eef4.js"></script>

<script src="https://gist.github.com/luuthevinh/8dbd97397b6eb60a91e6c8f38818159d.js"></script>

Mỗi lần bấm nút xoay hình, mình cộng / trừ góc xoay cho 90 độ, và đổi width, height của hình cho nhau. Sau đó là vẽ lại hình.

# Tải hình

Để tải hình, mình gán sự kiện `click` cho nút download, dùng hàm `toDataURL()` để lấy dữ liệu của canvas và tải xuống.

<script src="https://gist.github.com/luuthevinh/c656454778d77648b8600c9a6bf2f093.js"></script>

# Tổng kết

Vậy là xong các chức năng cơ bản của việc ghép khung vào hình, các bạn có thể phát triển thêm như là xoay hình, phóng to thu nhỏ, chọn frame... Còn lại là các thứ như là css lại giao diện theo ý các bạn thôi.

<a href="https://github.com/luuthevinh/up-2016" target="_blank">Full Source code của project.</a>

Bài viết này mình cũng đang tìm hiểu về Javascript, nên nếu có gì sai sót các bạn có thể góp ý thêm nha. Bạn nào có sử dụng lại nhớ báo mình cho mình biết nhé.
  
Hẹn gặp lại các bạn.

# Cập nhật

* 3/10/2016: cập nhật scale hình ảnh 
* 11/2/2017: cập nhật xoay hình ảnh