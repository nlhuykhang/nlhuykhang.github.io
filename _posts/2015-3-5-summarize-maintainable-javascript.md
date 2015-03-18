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

<h2>Don't modify object you don't own</h2>
**Preventing Modification**<br>
ECMAScript 5, giờ thì cứ hiểu Js vì chưa bao giờ thực sự đọc để phân biệt js và ECMAScript chỉ biết hình như js là wrapper của ECMAScript :v, có cung cấp một vài phương thức khóa một đối tượng với mực độ khác nhau ngăn chặn ai đó vô tình hay cố ý chỉnh sửa đối tượng không nên được sửa. <br>
Browser hỗ trợ: Chrome, Opera 12+, IE 9+, Firefox 4+, Safari 5+<br>
Các phương thức được chia làm 3 cấp với độ "mạnh" tằng dần như sau:

* Prevent Extension: không cho phép add thuộc tính hay phương thức mới vào object, nhưng các phương thức thuộc tính đã tồn tại có thể bị chỉnh sửa hay xóa
* Seal: tương tại Prevent Extension nhưng không cho phép xóa thuộc tính, phương thức đã tồn tại
* Freeze: đẳng cấp cao nhất là thằng này đóng băng tất cả lại, biến object trở thành read-only

Mỗi cấp trên có 2 phương thức, một dùng thể khóa một dùng để kiểm tra đã khóa chưa. Như vậy đây là mèo Tom khóa cửa và nuốt luôn chìa khóa không thể nào mở lại được nữa do đó ta phải cẩn thận khi dùng các phương thức này.<br>
Cấp Prevent extension: Object.preventExtension() và Object.isExtensible()

{% highlight html linenos %}
var person = {
	name: "Khang"
};

Object.preventExtension(person);			//khóa

Object.isExtenalbe(person);		//trả về false

person.age = 22;				//lỗi
{% endhighlight %}

Khi gán thêm thuộc tính mới cho object person chắc chắn lệnh náy sẽ không thành công do ta đã khóa lại rồi, tuy nhiên nên lưu ý thông thường lệnh này sẽ lỗi một cách lặng lẽ mà không quăng ra bất kì thông báo nào cả. Trường hợp ta đang ở strict mode nó sẽ chửi vào mặt ta. Nguyên tắc này cũng áp dụng cho các cấp độ khóa khác khi ta vi phạm quy ước khóa của cấp đó.<br>
Cấp Seal: Object.seal()

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

