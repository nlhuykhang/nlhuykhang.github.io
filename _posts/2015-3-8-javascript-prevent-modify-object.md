---
layout: post
title: Maintainable Javascript - Do Not Modify Object You Do Not Own 
categories: [web, javascript, book]
tags: [javascript, summarize, maintainable javascript]
published: true
fullview: true
---

<h3>Preventing Modification</h3>
ECMAScript 5, giờ thì cứ hiểu Js vì chưa bao giờ thực sự đọc để phân biệt js và ECMAScript chỉ biết hình như js là wrapper của ECMAScript :v, có cung cấp một vài phương thức khóa một đối tượng với mực độ khác nhau ngăn chặn ai đó vô tình hay cố ý chỉnh sửa đối tượng không nên được sửa. <br><br>
Browser hỗ trợ: Chrome, Opera 12+, IE 9+, Firefox 4+, Safari 5+<br><br>
Các phương thức được chia làm 3 cấp với độ "mạnh" tằng dần như sau:

* Prevent Extension: không cho phép add thuộc tính hay phương thức mới vào object, nhưng các phương thức thuộc tính đã tồn tại có thể bị chỉnh sửa hay xóa
* Seal: tương tại Prevent Extension nhưng không cho phép xóa thuộc tính, phương thức đã tồn tại
* Freeze: đẳng cấp cao nhất là thằng này đóng băng tất cả lại, biến object trở thành read-only

Mỗi cấp trên có 2 phương thức, một dùng thể khóa một dùng để kiểm tra đã khóa chưa. Như vậy đây là mèo Tom khóa cửa và nuốt luôn chìa khóa không thể nào mở lại được nữa do đó ta phải cẩn thận khi dùng các phương thức này.<br><br>
Cấp Prevent extension: Object.preventExtension() và Object.isExtensible()
<br>
{% highlight html linenos %}
var person = {
	name: "Khang"
};

Object.preventExtension(person);			//khóa

Object.isExtenalbe(person);		//trả về false

person.age = 22;				//lỗi
{% endhighlight %}
<br>
Khi gán thêm thuộc tính mới cho object person chắc chắn lệnh náy sẽ không thành công do ta đã khóa lại rồi, tuy nhiên nên lưu ý thông thường lệnh này sẽ lỗi một cách lặng lẽ mà không quăng ra bất kì thông báo nào cả. Trường hợp ta đang ở strict mode nó sẽ chửi vào mặt ta. Nguyên tắc này cũng áp dụng cho các cấp độ khóa khác khi ta vi phạm quy ước khóa của cấp đó.<br><br>

Cấp Seal: Object.seal() và Object.isSeal()

{% highlight html linenos %}
...
Object.seal(person);
   
Object.isExtensible(person);		//false
Object.isSeal(person);				//true

delete person.name; 				//lỗi
person.age = 21;					//lỗi
{% endhighlight %}
<br>
Cấp Freeze: Object.freeze() và Object.isFrozen()
<br>
{% highlight html linenos %}
...
Object.freeze(person);

Object.isExtensible(person);		//false
Object.isSeal(person);				//true
Object.isFronzen(person); 			//true

person.name = "Khang Nguyen-Le";	//lỗi	
delete person.name; 				//lỗi
person.age = 21;					//lỗi
{% endhighlight %}
