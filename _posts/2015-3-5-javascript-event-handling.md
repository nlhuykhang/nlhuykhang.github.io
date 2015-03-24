---
layout: post
title: Maintainable Javascript - Event handling
categories: [web, javascript, book]
tags: [javascript, summarize, maintainable javascript]
published: true
fullview: true
---

<h2>Event Handling</h2>
Này thì quen quá rồi ai đã từng đụng vào js bất kể nền tảng là gì hay không có nền tảng gì ráo mà chỉ có js thuần thì chắc chắn đã từng xử lý biến sự kiện event. Nói cho rõ thì đối tượng event, mà trong mỗi hàm bắt sự kiện của js, thường là tham số đầu tiên bao gồm tất cả các thông tin liên quan đến sự kiện đó và cả các thông tin được người lập trình thêm vào.<br><br>
Quen thuộc là thế nhưng thật ra rất nhiều coder đang có cách đối xử với đối tượng event này không ổn cho lắm, thật ra thì code vẫn chạy tốt thôi, nhưng khi đặt vào môi trường chuyên nghiệp đặc biệt là đối với các project lớn được phát triển bới nhiều coder hoặc thậm chí là nhiều công ty khác nhau, thì cách đối xử này có thể là ác mộng của coder.<br><br>
Ví dụ đoạn code sau ta hãy cùng xem và nghĩ thử vấn đề gì có thể xảy ra với đoạn code này, không phải vấn đề về chức năng nhé đoạn code này hoàn toàn ổn khi làm công việc của nó:
<br>
<!-- {% highlight html linenos %}
...
function handleClick(event){
	var popup = document.getElementById('popup');
	popup.style.left = event.clientX + 'px';
	popup.style.top = event.clientY + 'px';
	popup.className = 'reveal';
}

addListener(element, 'click', handleClick);
...
{% endhighlight %} 
 -->
<br>
Đoạn code trên đơn giản là show một cái popup tại vị trí nhập chuột của người dùng, vậy vấn đề gì có thể với một đoạn code đơn giản như này?<br>

<h3>Seperate application logic</h3>
Nếu bạn muốn show popup tương tự nhưng ở vị trí bất kì không phải ở chỗ nhất chuột thì sao, hoặc giả bạn muốn show popup tại ví trí chuột nhưng mà là sau khi enter thì sao? viết thêm một đoạn code tương tự trong hàm khác à?<br>
Vấn đề đoạn code trên đang mắc phải là nó xử lý logic của sự kiện trong hàm bắt sự kiện, cách xử lý này có thể tạo ra một vài các phiền toái như việc lập code vừa nêu ở trên. Vấn đề nữa chắc chắn sẽ xảy ra khi bạn cần test thử logic ứng dụng của mình đã chuẩn chưa trong trường hợp đó do code xử lý logic đang nằm trong hàm xử lý sự kiện vậy để test ta phải trigger được sự kiện bằng cách trực tiếp hay gián tiếp nào đó cụ thể ở đây là click chuột. Như thế rất phiền toát chẳng dư hơi nhiều đến mức chạy server lên chỉ để test một đoạn code logic có 3 dòng.<br>
Vậy nên để giải quyết vấn đề này ta luôn luôn nên tách biệt code xử lý logic của sự kiện và code bắt sự kiện, làm thế ta sẽ cơ bản giải quyết được các vấn đề trên từ lập code đến kiểm thử. Đoạn code sẽ tiến hóa thành:

<!-- {% highlight html linenos %}
...
var showPopup = function(event) {
	var popup = document.getElementById('popup');
	popup.style.left = event.clientX + 'px';
	popup.style.top = event.clientY + 'px';
	popup.className = 'reveal';
};

var handleClick = function(event) {
	showPopup(event);
}

addListener(element, 'click', handleClick);
...
{% endhighlight %} 
 -->
Với đoạn code này ta có thể dễ dàng thực hiện việc show popup một lần nữa ở đâu mà ta thích ta cũng có thể kiểm thử tính đúng đắn của hàm showPopup mà không cần phải tạo ra sự kiện click chuột. 

<h3>Do not pass the event object around</h3>

 Sau khi tách hàm, đoạn code trở nên đẹp hơn và dễ phát triển hơn trong quá trình sống của ứng dụng như thế là chưa đủ, ta vẫn đang có vấn đề với đối tượng sự kiện event. Vấn đề ở đây là hàm xử lý logic nhận vào biến event, việc này tạo ra những khó khăn trong việc nhận biết chức năng của hàm lẫn việc test chức năng của hàm. <br><br>
 Bây giờ ta thử đổi tên hàm showPopup thành abcPopup, hàm này vẫn nhận tham số duy nhất là event bạn nghĩ nếu người khác nhìn vào có chắc sẽ biết được chức năng của hàm này là gì không. Đây chỉ là trong trường hợp đoạn code bé xí thôi nếu trong trong một project thực sự lớn cho dù tên hàm của bạn có đặt cho cố sát nghĩa thì vẫn rất khó cho người khác có thể đoán được chức năng của hàm này là gì. <br><br>
 Tóm lại cách truyền tham số thế này tạo ra sự nhập nhằng, mù mờ về chức năng của hàm, đồng ý là cho dù đặt tên đúng và truyền tham số chuẩn hơn như là chỉ truyền toạn độ x và y cần dùng thôi thì chưa chắc người khác đã hiểu chức năng hàm của bạn nhưng mà nó vẫn tốt hơn chứ nhỉ.<br><br>
 Một vấn đề khác là truyền biến event vào hàm thì khi bạn cần kiếm tra tính đúng đắn của hàm thì phải tạo ra một biến event khác để truyền vào, thành ra là phải hiểu hàm cần những thuộc tính nào để tạo ra một đối tượng bao gồm các thuộc tính đó rồi truyền vào còn không thì phải trigger sự kiện bằng cách nào đó -> lại phiền. Nếu hàm chỉ cần có 2 tham số x và y có phải dễ hơn không, ta chỉ cần truyền 2 số tọa độ x và y vào là có thể kiểm thử hàm ngay ngon lành cành đào.<br><br>
 Nguyên tác cuối cùng khi xử lý sự kiện là hàm xử lý sự kiện nên thực hiện tất cả lệnh liên quan đến biến event trước khi gọi hàm để xử lý logic. Nên các hành động như là preventDefault hay stopPropagation nên được gọi trong hàm xử lý sự kiện nếu cần thiết trước khi logic sự kiện xảy ra. Việc làm này giúp ta quản lý sự kiện tốt hơn, dễ debug hơn, dễ test hơn, dễ phát triển ứng dụng hơn, nói chung theo chiều hướng tốt thì cái gì cũng dễ hơn :3<br><br>
 Do đó đoạn code tiếp tục tiến hóa thêm 1 bước nữa, có thể gọi là siêu tiến hóa và trở thành thế này:
 <br>
<!-- 
{% highlight html linenos %}
...
var showPopup = function(x, y) {
	var popup = document.getElementById('popup');
	popup.style.left = x + 'px';
	popup.style.top = y + 'px';
	popup.className = 'reveal';
};

var handleClick = function(event) {
	event.preventDefault();
	event.stopPropagation();
	showPopup(event.clientX, event.clientY);
}
  
addListener(element, 'click', handleClick);
...
{% endhighlight %}
 -->
what is the problem here? post can not be end with a block code?
