---
layout: post
title: Javascript - Một vài kĩ thuật nâng cao hiệu năng
categories: [tip, web, javascript]
tags: [javascript, performance]
published: true
fullview: true
---

updaing...

**Nội dung:**

* loop optimization
* script execution
	* where to put load script on html file
	* async load 

* some tips to improve performance
	* use prototype
	* use fragment to touch html
	* reduce amount of declaration times
	* array join

Sau một khoảng thời gian vật lộn với đóng công nghệ liên quan đến Javascript: Nodejs và Angularjs mình đã có ý niệm nhiều hơn về cái nghề nhiệp coder. Xưa nhiều lúc nghĩ chứ code thì thằng quái nào mà học chẳng được, một cái ngôn ngữ mới học 2 ngày xong(trước mình học js cũng chừng đấy thời gian). Rồi cũng có hồi lo lắng chuyện dễ thế mà mình lấy làm nghề nghiệp thế có ổn không? biết đâu rồi thiên hạ thằng nào cũng code được thế hóa ra ăn cám à? :v
<br><br>
Cái thời trẻ trâu(thiệt ra là cách đây chừng mất tháng thôi) lâu lâu lại có suy nghĩ kiểu đó trong đầu, mãi đến thời điểm này mình mới biết cuộc đời coder đâu đơn giản như vậy và cũng biết luôn vì nó không đơn giản nên coder sẽ lắm lúc gian truân. Sự là dạo trước có làm cái final project cho một môn học, nhóm 3 móng mà có mỗi móng này là thực sự code thôi mà lại chỉ code trong chừng có 4 5 ngày gì đó thời gian trước đó ngồi tìm hiểu kĩ thuật mà muốn khùng. Cũng may là đến cuối cùng thì project cũng hoàn thành và ăn điểm(mô phật), cái hay mình ngộ ra được từ cái project đó là khi mình up nó lên cloud. Khi up xong rồi thì mới phát hiện cái performance của trang web thấy mà gớm tệ kinh khủng không thể nào chấp nhận được nếu là một project làm business thật sự có khách hàng người dùng này nọ, làm vậy chắc chúng nó bổ đầu mình ra. Thế là mới ngộ ra là code cho xong code cho thành thì thằng quái nào cũng code được, nhưng mà code cho ra sản phẩm thật sự code có thể kiếm ăn được lại là chuyện khác. Lúc mà ngộ ra cái chân lý này lúc mừng lúc lo, mừng vì "aha không phải thằng nào cũng code được mình không lo thất nghiệp", lo vì "chết bố, khó này có làm được không :v". Nghĩ mà hài, nhìn lại thì cái gì chẳng khó lúc đầu gặp cái gì chẳng điên khùng nhưng mà từ từ rồi cũng ổn :3. 
<br><br>
Trở lại chuyện performance của sản phẩm, dạo gần đây may mắn vớ được vài cái tip hay hay về cải thiện performance cho js, mừng húm học lấy học để thế mà mới sáng nay ngồi ngẫm lại thì chẳng nhớ bao nhiêu. Nên thôi nghĩ là blog lại có vẻ là ổn nhất vừa nhớ được vừa xem như là có cái cheat sheet sau này có khi cần kíp.
<br><br>
<h1>Dùng cache value khi loop mảng thuộc tính của đối tượng</h1>
Ta xét ví dụ sau:
![Hình 1]({{ site.url }}/assets/media/2015-1-29-javascript-performance/1.png)<br><br>
Trên đây là một thủ tục thường gặp trong cuộc đời hằng ngày của coder, mình thì không chắc lắm coder lâu năm đã đi làm rồi thì viết thế nào(vì mình đã đi làm đâu mà biết) nhưng đối với những coder tương lai đang chà mông trên ghế nhà trường thì mình chắc chắn là kiểu code trên là cực kì phổ biến. Chính bản thân mình cũng thường viết thế trước khi biết cách giải quyết tốt hơn.<br><br>
Giải quyết tốt hơn? chắc nhiều bạn sẽ hỏi câu này. Đúng đây bạn có cách tốt hơn thật, chúng ta nhìn vào đoạn code thấy quá là đơn giản kiểu gì mà có thể viết khác đi mà tốt hơn nhiều được có chăng chỉ là tí ít thôi. Vậy bây giờ hãy phân tích thử đối với vòng lập trên với dữ liệu mình cho, **&nbsp;sẽ có tổng cộng bao nhiêu lệnh gọi truy xuất bộ nhớ?**<br><br>
Đối với mỗi vòng lặp, ta sẽ cần truy xuất bộ nhớ và lấy được giá trị của:

* biến i
* exampleObj
* thuộc tính exArray
* giá trị của thuộc tính exArray
* thuộc tính ***&nbsp;length*** của mảng exArray

Suy ra với mỗi vòng lặp ta cần 5 thao tác truy xuất bộ nhớ, ví dụ này mảng có 4 phần tử nên lặp cả thảy 5 lần thành ra tổng cộng **&nbsp;25 lệnh truy xuất bộ nhớ**. Như phân tích trên ta thấy, thực chất mỗi lần lặp chỉ cần giá trị biến i và giá trị lengh của mảng là đủ vậy tại sao phải xét đến cả đóng kia chi cho phiền phức làm sao để cải tiến đây.<br><br>
Đơn giản và hiệu quả nhất, ta dùng một biến tạm để lưu giá trị length của mảng:<br><br>
![Hình 2]({{ site.url }}/assets/media/2015-1-29-javascript-performance/2.png)<br><br>
Đoạn code trên đây thì cần bao nhiêu lệnh truy xuất bộ nhớ?<br><br> 
Trước khi bắt đầu lặp ta cần 1 lệnh tạo biến ***&nbsp;temp*** trong bộ nhớ, 4 lệnh khác để truy xuất giá trị biến ***&nbsp;length***<br><br>
Đối với mỗi vòng lặp ta cần 2 lệnh tìm kiếm giá trị biến i và x, vậy cần 10 lệnh cho vòng lặp<br><br>
Kết lại đối với đoạn code 2 khi chạy chương trình sẽ thực hiện tổng cộng **&nbsp;15 lệnh truy xuất bộ nhớ**. So sánh với đoạn code 1 thì ta đã tiếm kiệm được 10 lệnh truy xuất bộ nhớ đối với mảng 4 phần tử. "Bỏ bèn có mỗi 10 lệnh :v", vậy nếu mảng có kích thước 1000 thì sao? Tính thử nào:

* code 1: 5 lệnh * (1000 lần lặp + 1 lần stop) = 5005 lệnh
* code 2: 2 lệnh * (1000 lần lặp + 1 lần stop) + 5 lệnh tạo biến temp = 2007
* => tiết kiệm được: 5005 - 2007 = 2998 ~ 3000 lênh. cũng đáng để thêm 1 dòng code đấy nhỉ :3

<h1>Script execution</h1>



<h1>Some tips to improve performance</h1>



