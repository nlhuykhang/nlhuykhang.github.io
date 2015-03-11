---
layout: post
title: Javascript - Summarizing "Maintainable Javascript"
categories: [web, javascript, book]
tags: [javascript, summarize]
published: false
fullview: true
---

updaing...
Ngắn gọn thôi nào, chưa hiểu nhiều thì không nên viết nhiều

**Loose coupling of UI layers**
**Avoid globals**
**Event Handling**
**Avoid null comparisons**
**Separate configuration data for code**
**Throw your own errors**<br>
Thật là có cần không, từ lúc học C# đã chẳng mấy quan tâm đến try catch error này nọ vì có debug dòng lệnh rồi mà try catch chỉ tổ tốn thời gian thôi. Js không thể debug ngon lành như C# vậy đây có thể là lý do thuyết phục?<br>

**Don't modify object you don't own**
<h2>Browser detection</h2>
**User-Agent detection**
Mọi browser đều có user-agent string để xác định cho thằng khác biết, cụ thể là server và client, bản thân là tên chi cha mẹ là ai nhà ở đâu để cho người ta còn biết mà chửi, như mình thì thích chủ anh M$ lắm :v.<br>
Cú pháp: không biết server thế nào, mà hình như backend chẳng thằng nào thèm đếm xỉa chỉ client frontend là mình lo thôi. Frontend thì detect thế này: <strong>&nbsp;navigator.userAgent</strong>. Nhưng mà cách này có nhiều nhược điểm vì kết quả trả về là string nên trước hết phải parse rồi kiểm tra rồi còn ba thứ hầm bà lằng về version của browser hay nếu browser update có code mới thì code cũng phải update lại mà update vì code của mình còn mệt huống chi vì code của thằng khác => đây là cách cuối cùng nên dùng.<br>
**Feature detection**
Này là gì quên rồi ta, hình như là cái nào có thì dùng. Là trước khi dùng bất kì thuộc tính hay hàm nào thì kiểm tra nếu nó có tồn tại thì dùng không thì thôi. Kết quả không bị lỗi khi dùng cách này, tức là code sẽ chạy được nhưng mà cũng có nhược điểm vì khi cần qua mặt PM nếu mà PM test browser cũ hơn mà mình chưa cover đến nó thì vỡ mồm. Phải biết các hàm cho từng browser cụ thể thì mới sống qua ngày được, phiền thật.<br>
Ví dụ:<br>
{% highlight html linenos %}
function getById (id) {
	var element = null;
	if (document.getElementById) { // DOM
		element = document.getElementById(id);
	} else if (document.all) { // IE
		element = document.all[id];
	} else if (document.layers) { // Netscape <= 4
		element = document.layers[id];
	} else {
		//...
	}
	return element;
}
{% endhighlight %}
Chú ý: tránh mọi hình thức kiểm tra một hàm để dùng làm cơ sở xác định hàm khác hay browser khác. Như kiểu nếu hàm A tồn tại thì hàm B tồn tại hay browser A có hàm B nếu nếu browser nào có hàm B tức là browser A. Chưa chắc là đúng nha. Kiểu kiểm tra feature này chỉ một chiều thôi chiều ngược lại không có chuẩn đâu. Lí do thì nhiêu lười kể quá mà cũng chẳng biết hết mà kể, ai biết đại số bool thì áp dụng 2 mệnh đề vừa nếu trên thì rõ. :v<br>
**Vậy rốt cuộc thì dùng cái gì???**
Theo thứ tự:

* Feature detection first: dùng cái gì kiểm tra cái đó, nhớ cover hết mọi browser
* User-agent nếu cùng đường và deadline thì kề cổ
* Suy luận dựa vào feature thì biết để KHÔNG BAO GIỜ ĐƯỢC PHÉP DÙNG 

