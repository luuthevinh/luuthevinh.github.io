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

Ý tưởng cơ bản là mình vẽ lần lượt hai hình lên canvas, thêm chức năng di chuyển ảnh sau đó mình lưu canvas đó xuống là xong. Rất đơn giản phải không?

Đầu tiên trong file html, các bạn thêm hai thẻ `<canvas>` và `<img>` như sau:

```html
<canvas id="canvas">Xin lỗi, trình duyệt bạn không hỗ trợ.</canvas>
<img style="display: none;" id="source">

<a id="selectImageBtn" onclick="selectFile()" role="button">Chọn hình</a>
<a id="downloadBtn" role="button">Tải về</a></pre>
```

Trong đó,

  * Thẻ canvas để mình sử dụng vẽ hình.
  * Thẻ img dùng để chứa hình mà mình tải lên.
  * 2 nút chọn hình và tải hình về.

Tiếp theo, các bạn tạo file js để code một số xử lý hình ảnh.

```js
// lấy hình, canvas, context của canvas bên html
var img1 = document.getElementById("source");
var canvas = document.getElementById("canvas");
var context = canvas.getContext("2d");

// gán kích thước mặc định cho canvas
var canvasWidth = canvas.width = 500;
var canvasHeight = canvas.height = 500;

// cạnh nhỏ nhất
var minEdge = 0;

// tạo đối tượng khung hình để ghép
var frame = new Image;
frame.src = "images/avatar_frame.png";

// mình gán sự kiện onload cho hình mình tải lên.
img1.onload = function() {
    // trong đây, mình tìm cạnh ngắn của hình
    minEdge = Math.min(img1.width, img1.height);

    // xóa và vẽ lại hình, frame lên canvas, 
    // theo tỉ lệ với cạnh ngắn (cho hình scale vừa với khung canvas)
    context.clearRect(0,0, canvasWidth, canvasHeight);
    context.drawImage(img1, 0, 0, minEdge, minEdge, 0, 0, canvasWidth, canvasHeight);
    context.drawImage(frame, 0, 0, frame.width, frame.height, 0, 0, canvasWidth, canvasHeight);
}
```

Các bạn có thể xem hàm [drawImage](http://www.w3schools.com/tags/canvas_drawimage.asp){:target="_blank"}.

# Load hình

Tiếp theo mình sẽ load hình lên và cập nhật sự kiên di chuyển hình thôi.

Trong file html các bạn thêm **input** để tải hình lên.

```html
<form>
	<input style="display:none;" type="file" id="imageFile" accept="image/*" onchange="updateImage()">
</form>
````

Viết sự kiện **onchange** cho input.

```js
function selectFile() {
  // gọi sự kiện click cho input từ button
  $("#imageFile").click();
};

function updateImage() {
  // lấy dữ liệu từ input
  var input = $("#imageFile")[0];

  if (input.files && input.files[0]) {
      var reader = new FileReader();

      reader.onload = function (e) {
          // cập nhật src cho hình từ input
          img1.src = e.target.result;
      }

      reader.readAsDataURL(input.files[0]);
  }
};
```

Hàm **tải canvas** về thành hình.

```js
function downloadFile(button, canvasId, filename) {
    var dt = document.getElementById(canvasId).toDataURL();
    button.href = dt;
    button.download = filename;
};

document.getElementById('downloadBtn').addEventListener('click', function() {
    downloadFile(this, "canvas", "up_avatar.png");
}, false);
```

# Di chuyển hình

Cuối cùng mình sẽ thêm chức năng **di chuyển hình** nền mình tải lên.

**Ý tưởng:** mỗi khi chuột di chuyển mình sẽ tính toán độ thay đổi của chuột so với lần trước rồi cập nhật lại vị trí của hình.

```js
// offset của canvas
var canvasOffset = $("#canvas").offset();
var offsetX = canvasOffset.left;
var offsetY = canvasOffset.top;
// biến cờ xem có đang kéo ko
var isDragging = false;
// vị trí hình trước đó
var preX, preY;
var imagePosX = 0;
var imagePosY = 0;

// sự kiện mouse down
function handleMouseDown(e){
	// vị trí hiện tại chuột theo canvas = vị trí chuột hiện tại - offset của canvas
	canMouseX = parseInt(e.clientX - offsetX);
	canMouseY = parseInt(e.clientY - offsetY);
	  
	// đang kéo hình
	isDragging = true;

	// lưu lại vị trí cũ
	preX =  canMouseX;
	preY = canMouseY;
}

// sự kiên mouse up
function handleMouseUp(e){
	canMouseX = parseInt(e.clientX - offsetX);
	canMouseY = parseInt(e.clientY - offsetY);
  
	// hết kéo hình
	isDragging = false;

	preX = canMouseX;
	preY = canMouseY;
}

// sự kiện mouse out
function handleMouseOut(e){
	canMouseX = parseInt(e.clientX - offsetX);
	canMouseY = parseInt(e.clientY - offsetY);
  
	// chuột ra khỏi canvas rồi nên hết kéo được
	isDragging = false;

	preX = canMouseX;
	preY = canMouseY;
}

// sự kiên mouse move
function handleMouseMove(e) {
  canMouseX = parseInt(e.clientX - offsetX);
  canMouseY = parseInt(e.clientY - offsetY);

  // nếu đang kéo hình thì mình xóa canvas và vẽ lại vị trí mới
	if(isDragging) {
		context.clearRect(0,0,canvasWidth,canvasHeight);

		// vị trí của hình = độ thay đổi vị trí của chuột (vị trí hiện tại - vị trí trước đó)
		imagePosX -= canMouseX - preX;
		imagePosY -= canMouseY - preY;

		// chặn lại khi ra khỏi biên
		if(imagePosX < 0)
		{
			imagePosX = 0;
		}
		else if(imagePosX > img1.width - minEdge)
		{
			imagePosX = img1.width - minEdge;
		}

		if(imagePosY < 0)
		{
			imagePosY = 0;
		}
		else if(imagePosY > img1.height - minEdge)
		{
			imagePosY = img1.height - minEdge;
		}

		// vẽ lại hình và khung theo vị trí mới
		context.drawImage(img1, imagePosX, imagePosY, minEdge, minEdge, 0, 0, canvasWidth, canvasHeight);
		context.drawImage(frame, 0, 0, frame.width, frame.height, 0, 0, canvasWidth, canvasHeight);
	}

	// lưu vị trí hiện tại lại
	preX = canMouseX;
	preY = canMouseY;
}

// gán sự kiện cho canvas
$("#canvas").mousedown(function(e){handleMouseDown(e);});
$("#canvas").mousemove(function(e){handleMouseMove(e);});
$("#canvas").mouseup(function(e){handleMouseUp(e);});
$("#canvas").mouseout(function(e){handleMouseOut(e);});
```

# Tổng kết

Vậy là xong các chức năng cơ bản của việc ghép khung vào hình, các bạn có thể phát triển thêm như là xoay hình, phóng to thu nhỏ, chọn frame... Còn lại là các thứ như là css lại giao diện theo ý các bạn thôi.

<a href="https://github.com/LuuTheVinh/up-2016" target="_blank">Full Source code của project.</a>

Bài viết này mình cũng đang tìm hiểu về Javascript, nên nếu có gì sai sót các bạn có thể góp ý thêm nha. Bạn nào có sử dụng lại nhớ báo mình cho mình biết nhé.
  
Hẹn gặp lại các bạn.