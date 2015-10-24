---
layout: post
title: Javascript - Một vài kĩ thuật nâng cao hiệu năng
categories: [tip, web, javascript]
tags: [javascript, performance]
published: true
fullview: true
---

**Nội dung:**

* <a href="#h1">Dùng cache value khi loop mảng thuộc tính của đối tượng</a>
* <a href="#h2">Đặt script ở đâu là tốt nhất?</a>
* <a href="#h3">Sử dụng Prototype khi khai báo class</a>
* <a href="#h4">Dùng Fragment khi tạo nhiều thẻ HTML</a>

Sau một khoảng thời gian vật lộn với đóng công nghệ liên quan đến Javascript: Nodejs và Angularjs mình đã có ý niệm nhiều hơn về cái nghề nhiệp coder. Xưa nhiều lúc nghĩ chứ code thì thằng quái nào mà học chẳng được, một cái ngôn ngữ mới học 2 ngày xong(trước mình học js cũng chừng đấy thời gian). Rồi cũng có hồi lo lắng chuyện dễ thế mà mình lấy làm nghề nghiệp thế có ổn không? biết đâu rồi thiên hạ thằng nào cũng code được thế hóa ra ăn cám à? :v
<br><br>
Cái thời trẻ trâu(thiệt ra là cách đây chừng mất tháng thôi) lâu lâu lại có suy nghĩ kiểu đó trong đầu, mãi đến thời điểm này mình mới biết cuộc đời coder đâu đơn giản như vậy và cũng biết luôn vì nó không đơn giản nên coder sẽ lắm lúc gian truân. Sự là dạo trước có làm cái final project cho một môn học, nhóm 3 móng mà có mỗi móng này là thực sự code thôi mà lại chỉ code trong chừng có 4 5 ngày gì đó thời gian trước đó ngồi tìm hiểu kĩ thuật mà muốn khùng. Cũng may là đến cuối cùng thì project cũng hoàn thành và ăn đầy điểm(mô phật), cái hay mình ngộ ra được từ cái project đó là khi mình up nó lên cloud. Khi up xong rồi thì mới phát hiện cái performance của trang web thấy mà gớm tệ kinh khủng không thể nào chấp nhận được nếu là một project làm business thật sự có khách hàng người dùng này nọ, làm vậy chắc chúng nó bổ đầu mình ra. Thế là mới ngộ ra là code cho xong code cho thành thì thằng quái nào cũng code được, nhưng mà code cho ra sản phẩm thật sự code có thể kiếm ăn được lại là chuyện khác. Lúc mà ngộ ra cái chân lý này lúc mừng lúc lo, mừng vì "aha không phải thằng nào cũng code được mình không lo thất nghiệp", lo vì "chết bố, khó này có làm được không :v". Nghĩ mà hài, nhìn lại thì cái gì chẳng khó lúc đầu gặp cái gì chẳng điên khùng nhưng mà từ từ rồi cũng ổn :3. 
<br><br>
Trở lại chuyện performance của sản phẩm, dạo gần đây may mắn vớ được vài cái tip hay hay về cải thiện performance cho js, mừng húm học lấy học để thế mà mới sáng nay ngồi ngẫm lại thì chẳng nhớ bao nhiêu. Nên thôi nghĩ là blog lại có vẻ là ổn nhất vừa nhớ được vừa xem như là có cái cheat sheet sau này có khi cần kíp.
<br><br>
<h1><a id="h1">Dùng cache value khi loop mảng thuộc tính của đối tượng</a></h1>
Ta xét ví dụ sau:<br><br>
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

<h1><a id="h2">Đặt script ở đâu là tốt nhất?</a></h1>
Phần này mình nghĩ nhiều bạn đã làm đúng, nhưng mà không chắc các bạn có hiểu được vấn đề không nên tiện thể trình bày luôn :).<br><br>
Câu hỏi đặt script ở đâu là tốt? ý mình là để dùng được file js ta phải đặt nó ở đâu đó trong phần body của trang html. Mặc định thì đặt chỗ nào cũng được, trong điều kiện lý tưởng thì kiểu gì trang web của mình cũng chạy tốt. Mà thường thì có cái gì hoàn toàn lý tưởng đâu, bởi thế ta tốt hơn nên xem trang html sau và rút ra bài học:<br><br>
![Hình 3]({{ site.url }}/assets/media/2015-1-29-javascript-performance/3.png)<br><br>
Trong trang html trên mình đặt dòng lệnh gọi script JQuery từ server Google ở lưng chừng trang html, trang này trong trường hợp ngon lành trời yên bể lặng mình chạy hơi bị mượt, hơi bị ngon. Nhưng mà lỡ đâu hôm nào trái gió trở trời cá mập buồn tình cắn đứt cáp quang thế là mạng chậm rùa bò, trang html của chúng ta load cái JQuery từ server bên US về mãi không xong.<br><br>
Thế là browser cứ đứng đó chờ hoài chờ mãi mà không load được JQuery, thành ra quên load luôn cả những phần khác của trang html ở phía dưới của cái dòng load script đó nên trang html của ta nhìn chẳng ra cái gì. Thằng người dùng ngồi chờ mãi không xem được trang web lê chuột sang trang khác thôi xong thằng coder thế là thất nghiệp vì cá mập buồn tình. :v<br><br>
Vì mặc định của browser là nó sẽ load trang html của ta từ trên xuống dưới tuần tự từng phần một, do đó trường hợp ta thất nghiệp có thể xảy ra. Để phòng xa con cá mập thì các bạn thấy là hầu hết các trang web đều để dòng lệnh load script ở cuối cùng của trang html, cái này như một quy chuẩn khi biết html vậy nhưng mà mình biết không nhiều bạn hiểu bản chất thâm xâu là vì cá mập đâu :)).<br><br>
Ta nên đặt script như hình sau nhé:<br><br>
![Hình 4]({{ site.url }}/assets/media/2015-1-29-javascript-performance/4.png)<br><br>

<h1><a id="h3">Sử dụng Prototype khi khai báo class</a></h1>
Phần này đơn giản là mình khuyên các bạn khi khai báo một class nên dùng prototype để khai báo các hàm cho class đó, không nên khai báo cái hàm trong constructer của class.<br><br>
![Hình 5]({{ site.url }}/assets/media/2015-1-29-javascript-performance/5.png)<br><br>
Như các bạn xem hình trên, nếu ta khai báo constructor như vậy thì cũng ổn thôi tạo ra đối tượng mới thì vẫn chạy tốt không bị lỗi gì cả. Nhưng mà hiệu năng của chương trình sẽ bị ảnh hưởng do phải **&nbsp;tốn thêm thời gian để tạo các phương thức và tốn thêm bộ nhớ để lưu trữ chúng**, đặc biệt là khi chúng ta có nhiều đối tượng thuộc cùng một class. Để cải tiến thì chúng ta nên làm như sau:<br><br>
![Hình 6]({{ site.url }}/assets/media/2015-1-29-javascript-performance/6.png)<br><br>
Kiểu khai báo class như trên sẽ giúp chúng ta tiết kiệm được tài nguyên làm chương trình chạy nhanh hơn. Các phương thức sẽ chỉ được khởi tạo một lần duy nhất trong prototype, sau đó sẽ được gọi bởi các đối tượng chứ không phải mỗi đối tượng sẽ có cùng các phương thức giống nhau.<br><br>
Thế rồi giờ đặt ra câu hỏi: vậy thế vẹo nào mà tạo ra cách khai báo hàm trong constructor làm gì cho thiên hạ phải mắc lỗi? cách khai báo tệ như vậy thì sao phải tạo ra?<br><br>
Câu trả lời là có mục đích bạn à. Và đó là khi đối tượng của bạn có thuộc tính private, thì chỉ các hàm khai báo trong constructor mới có thể truy xuất các thuộc tính đó được thôi. Ví dụ như vầy:<br><br>
![Hình 7]({{ site.url }}/assets/media/2015-1-29-javascript-performance/7.png)<br><br>
Nói chung người ta đã tạo ra cái gì thì đều có lý do, cả hợp lí và ngược lại vậy nên mới có lỗi lầm, trường hợp này thì là hợp lý. Chúng ta nên tìm hiểu khi có cái gì đó bất hợp lý hay có nhiều hơn 1 cách thực hiện cho 1 việc thì chắc là phải có gì đó mà ta chưa biết, đó là cách ổn nhất khi học cái mới nhưng mà tất nhiên là phải đảm bảo thời gian nhé không có thời gian thì thôi cứ quơ lại có demo là được rồi như lúc code final project nè :v. 

<h1><a id="h4">Dùng Fragment khi tạo nhiều thẻ HTML</a></h1>
![Hình 8]({{ site.url }}/assets/media/2015-1-29-javascript-performance/8.png)<br><br>
Hình trên ta có một đoạn code có khả năng thêm các phần tử trong mảng newItems vào trang html tạo tag có id listExample. Đoạn code trên hình mình code đúng có thể chạy tốt thực hiện được chức năng đặt ra, nhưng mà chưa có chuẩn bởi vì trong vòng for mỗi lần ta gọi hàm appendChild để thêm một tag mới trang html sẽ "reflow" - mình hiểu đây là quá trình nó xác định vị trí các tag, rồi sau đó thêm tag mới vào, render lại ... - đây là quá trình khá tốn kém tài nguyên, nó làm chậm trang web của chúng ta. Bởi thế người ta tạo ra một giải phát mới là dùng một mảnh fragment, đây có thể xem là một DOM ảo để ta thao tác tạo ra những gì cần thiết rồi mới truy xuất trang html và thêm vào, như vậy trang html sẽ chỉ phải "reflow" một lần duy nhất thôi rất là tiết kiệm. Cách dùng như ví dụ dưới đây:<br><br>
![Hình 9]({{ site.url }}/assets/media/2015-1-29-javascript-performance/9.png)<br><br>
À các bạn để ý đoạn code trên mình không chỉ dùng fragment để nâng cao hiệu năng mà còn một vài tip như mình **&nbsp;giảm tối đa số lần dùng var để khai báo biến**, dùng var quá nhiều cũng sẽ làm chậm trang web. Và cũng **&nbsp;không nên khai báo biến trong vòng for**, vì mỗi lần lặp lại khai báo biến mới cũng sẽ làm chậm trang web.

**Tham khảo:**

* <a href="https://www.codeschool.com/courses/javascript-best-practices">**Javascript Best Practices**</a>
* <a href="http://stackoverflow.com/questions/8433459/js-why-use-prototype">**Why use Prototype?**</a>
