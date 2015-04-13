---
layout: post
title: High Performance Javascript - DOM Scripting
categories: [web, javascript, book]
tags: [javascript, summarize, high performance javascript, DOM]
published: false
fullview: true
---

updating...

DOM: document object model

dom và js bị phân tách, cái này là do thiết kế, nên một cách tự nhiên luôn luôn có một độ trễ không thể tránh đối với web browser

truy xuất và chỉnh sửa các đối tượng của DOM có chi phí rất cao nên lời khuyên là hãy đụng đến DOM càng ít càng tốt

innerHTML nhanh hơn so với createElement() đây là đánh giá hồi tận năm 2010 chẳng biết bây giờ thế nào, lười làm lại cái test tương tự quá đang buồn ngủ mà

html collection - nothing to write or can not remember anythings

html reflow, repaint
	khi browser đã có đủ các thành phần cần có của trang web html css js... nó sẽ tạo 2 thứ một là DOM tree hai là render tree
	DOM tree: thể hiện cấu trúc trang web
	render tree: thể hiển cái gì sẽ được hiển thị lên màn hình, trong render tree chỉ có các DOM node sẽ được hiển thị lên màn hình các node đang ẩn sẽ không hiện diện trong render tree

	reflow là gì: khi một DOM node thay đổi và sự thay đổi này liên quan đến kích thước như thay đổi kích thước cạnh, thay đổi text mới hay thêm text, khi đó browser sẽ cần phải tính lại các kích thước hình học và vị trí của node đó và cả những node sẽ bị ảnh hưởng bởi thay đổi đó. Tiếp theo browser xác định phần bị ảnh hưởng của render tree và cấu trúc lại phần bị ảnh hưởng đó.

	repaint là gì: đây là bước kế sau của reflow, sau khi render tree đã được thay đổi browser sẽ tiến hành vẽ lại lên màn hình 

	chú ý: không phải tất cả những thay đổi của DOM đều khiến browser reflow và repaint lại trang web, như nếu chỉ thay đổi màu sác của node thì chẳng làm gì mà ảnh hưởng đến kích thước của node đó cả nên sẽ không có reflow, mà chỉ có repaint thôi

	do reflow và repaint thuộc dạng tốn ngốn nhiều tài nguyên khi thực thi thế nên dù biết thay đổi là cần thiết, ta cần phải tìm cách để thay đổi có thể xảy ra ít nhất như có thể
	
	khi nào reflow xảy ra:
		một element được thêm vào hoặc hủy đi
		element thay đổi vị trí
		element thay đổi kích thước
		nội dung element bị thay đổi
		load trang lần đầu tiên
		kích thước cửa sổ thay đổi


queuing and flushing render tree
	trong quá trình thay đổi style của một trang web không nên truy xuất các thông tin về layout, làm thế sẽ làm chậm performance của trang web

minimizing repaints and reflows
	nếu một element bị thay đổi nhiều thuộc tính nên cố gắng thay đổi các thuộc tính đó cùng một lần(một dòng code)
	một cách khác là thay đổi class name chứ không thay đổi thuộc tính cụ thể, tuy nhiên cách này chỉ có thể dùng nếu, thay đổi cần thực hiện không phụ thuộc vào logic hay tính toán lúc runtime

ba phương pháp đúng chuẩn để điều chỉnh element style với chi phí ít nhất:
	- ẩn element đi, điều chỉnh, hiện lại
	- tạo ra một cái fragment rồi thao tác trên thằng này sau đó thêm vào trong element(cái này tính ra chỉ add được thôi chứ đâu có modify được)
	- clone ra một cái element tương tự trong code, điều chỉnh, thay thế element cũ bằng thằng mới này(mình thích cách này nhất)

Caching layout information
	dùng cache thôi cái này quen quá rồi

Take elements out of the flow for animations
	1. Use absolute positioning for the element you want to animate on the page, taking
		it out of the layout flow of the page.
	2. Animate the element. When it expands, it will temporarily cover part of the page.
		This is a repaint, but only of a small part of the page instead of a reflow and repaint
		of a big page chunk.
	3. When the animation is done, restore the positioning, thereby pushing down the
		rest of the document only once.
	không hiểu cứ để đây đi đã, lấy ra khởi flow nhớ lúc trước anh Lên có nói mình về vụ này top left j đó hừm cái này về html mà mình làm cái nào học nó đây ta?

IE and :hover
	từ IE7 là cho dùng :hover để highlight dòng của bảng. Nhưng mà nếu số dòng lớn chừng 500 đổ lên thì đây là ác mộng mà trong đó sự rùa bò lên ngôi do đó nếu dữ liệu lớn thì k chơi hover hết.

Event delegation
	nếu mỗi element cùng loại, cùng một thao tác cần thể hiện mà cho mỗi cái element đó một cái event handler thì rất tốn kém chi phi nến dùng event delegation thì ngon lành hơn
	event delegation làm được nhờ vào bubble up 
	Các giai đoạn của event:
		- capturing
		- at target
		- bubbling

Summary:
	• Minimize DOM access, and try to work as much as possible in JavaScript.
	• Use local variables to store DOM references you’ll access repeatedly.
	• Be careful when dealing with HTML collections because they represent the live,
	underlying document. Cache the collection length into a variable and use it when
	iterating, and make a copy of the collection into an array for heavy work on
	collections.
	• Use faster APIs when available, such as querySelectorAll() and
	firstElementChild.
	• Be mindful of repaints and reflows; batch style changes, manipulate the DOM tree
	“offline,” and cache and minimize access to layout information.
	• Position absolutely during animations, and use drag and drop proxies.
	• Use event delegation to minimize the number of event handlers.

