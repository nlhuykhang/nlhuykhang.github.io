---
layout: post
title: Bootstrap - Giao diện với Grid 
categories: [tut, web]
tags: [bootstrap, grid]
fullview: true
---

Updating...

Hôm nay mình vừa học xong khóa học online<a class="btn btn-link btn-sm" href="https://www.codeschool.com/courses/blasting-off-with-bootstrap">Blasting off with Bootstrap</a>về Bootstrap trên<a class="btn btn-link btn-sm" href="https://www.codeschool.com/">Codeschool</a>, có một nhận xét đơn giản phải nói là "công nhận bọn này nó bá thật". Nói thế không bởi vì hàm lượng kiến thức học được về Bootstrap vì tính ra chỉ học được những thứ cơ bản nhất thôi, mà vì cái cách mà học truyền đạt. Cảm giác khi học là có một cái flow rất thanh thoát, rành mạnh sự kết hợp nhuần nhuyễn của người nói và slide được thể hiện trên các clip giúp mình nhớ và hiểu được vài thứ về Bootstrap chỉ sau khoảng 3h bỏ công ngồi học cả khóa của họ. Mà đặc biệt thích nhất là cái video ca nhạn giới thiệu đầu mỗi phần, làm như kiểu phần giới thiệu đầu một phim truyền hình vậy. Nhạc làm hơi bị hay nghĩ dám chừng thằng cha director của Codeschool cũng thích chơi nhạc cụ giống mình :v, nên mới có một cái model như vậy cho tất cả khóa học trên đó :)).
<br><br>
Tâng bóc nhiều thế được rồi, phần chính cho post này là để nói về những gì mình thấy hay học được từ Codeschool về Bootstrap. Blog này sẽ chỉ nói về grid thôi, những phần khác để sau có lẽ sẽ có một series 3 4 bài để nói về Bootstrap.
<br><br>
**Chú ý**: series này được tạo ra chỉ vì tác giả(mình :v) muốn ghi lại một cách chi tiết với hi vọng nhớ được những điều cốt yếu về Bootstrap nên nội dung và resources mình dùng gần như tất cả đều được lấy từ<a class="btn btn-link btn-sm" href="https://www.codeschool.com/courses/blasting-off-with-bootstrap">Blasting off with Bootstrap</a>. Quang minh chính đại là thế nên hi vọng không bị mang danh ăn cắp bản quyền nhé :D(nếu ai mà vẫn nói vậy thì đành chịu ><). 
<br><br>
Update khi đã viết được nữa bài: ngồi chỉnh mấy cái link mới phát hiện chính cái blog này đây cũng dùng bootstrap :))
<br><br>
<h1>Sơ lược về Bootstrap</h1>
Bootstrap là framework nguồn mở dùng để làm front-end cho một website. Nó bao gồm một tập hợp các file css, js, eot, ttf ... được tổ chức và phát triển có hệ thống và chất lượng để giúp các web developer có thể sống nhẹ nhàng hơn trong khi vẫn có thể tạo ra được những giao diện đẹp mà vẫn rất quen thuộc với người dùng(quen thuộc vì khi nhiều người dùng nó làm web nên thành ra tạo cả một thế hệ web sau ngày Bootstrap ra đời có những đặc điểm giống nhau, nhờ đó tạo cho người dùng cảm giác quen thuộc mặc dù đang dùng những trang web không cùng người phát triển).
<br><br>
Khởi nguồn Bootstrap được 2 nhà lập trình Mark Otto và Jacob Thornton phát triển với mục đích tạo thành một framework để dùng nội bộ trong công ty của họ nhằm phát triển Twitter. Đến khi mình vác mông vào đại học thì Twitter làm phước cho mình và các bạn bây giờ bằng cách nguồn mở hóa Bootstrap, từ đó Bootstrap dưới sự hỗ trợ của cộng đồng và với một core team mạnh đã trở thành framework front-end hùng mạnh nhất hiện nay.
<br><br>
<h1>Sử dụng Bootstrap</h1>
Đối với Bootstrap có nhiều cách để chúng ta có thể bắt đầu sử dụng được bạn nào quen dùng với Bower hay npm thì chỉ cần một dòng lệnh là code được ngay, mình thì không quen nên làm theo cách thủ công. Đầu tiêu download Bootstrap về <a class="btn btn-link btn-sm" href="http://getbootstrap.com/getting-started/#download">Download Bootstrap</a>. 
Sau đó ta phải thêm đường dẫn đến các file css và js của Bootstrap vào file html muốn sử dụng Bootstrap. Ví dụ như sau:
<br>
{% highlight html linenos %}
...
<head>
	...
	<link href="css/bootstrap.min.css" ref="stylesheet">
	...
</head>
<body>
	...
	<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.2/jquery.min.js"></script>
	<script src="js/bootstrap.min.js"></script>
	...
</body>
...
{% endhighlight %}
<br>
Khi thêm Bootstrap vào các bạn cần lưu ý: do các file js của Bootstrap dùng JQuery để phát triển thêm nên ta phải thêm cả JQuery(dòng 9) vào và phải thêm trước khi thêm file js của Bootstrap thì mới có thể dùng Bootstrap được. JQuery cũng là nguồn mở các bạn nên down về dùng như ta đang làm với Bootstrap(ví dụ mình để link online cho tiện thôi chứ dùng online nhiều lúc mạng xiđa ức chế lắm)
<br><br>
<h1>Grid trong Bootstrap</h1>
<h2>Grid là gì?</h2>
Grid trong tiếng Anh là lưới, đối với Bootstrap ta có thể hiểu grid là một hệ thống các dòng và cột đan xen nhau tạo thành một lưới, nội dung của trang html sẽ được đặt trên lưới này. Nếu như Microsoft Word cung cấp cho chúng ta các cách thức đa dạng để canh lề và sắp xếp nội dung văn bản thì ở Bootstrap ta có grid. Các bạn xem hình sau:<br>
![Hình 1]({{ site.url }}/assets/media/2015-1-24-bootstrap-grid/1.png)<br>

Hình này có 8 dòng, trên mỗi dòng có các ô được đánh số khác nhau dựa theo độ lớn của chúng. Nếu chúng ta tính tổng các ôn theo từng dòng sẽ được kết quả 12 cho mỗi dòng.
<br>
Grid có 2 đơn vị cơ bản ta cần nắm là row(dòng) và column(cột):

* Row(dòng) là đơn vị tính theo chiều dọc của trang html, một trang html có thể có bao nhiêu row tùy thích, mỗi row có thể kéo dài bao nhiêu tùy thích
* Column(cột) là đơn vị tính theo chiều nganh của trang html. Các column phải nằm trong một row và một row có tổng cộng 12 column có kích thước bằng nhau như ta thấy trong hình trên. 

Dựa vào những quy ước này ta có thể tạo những lưới đa dạng về cấu trúc để đặt nội dung của mình vào đó. Như ta có thể lồng nhiều row vào cùng một row, row vào một cột sau đó từ row này lại khai báo thêm các column khác ... Tùy vào mục đích của mình bạn có thể thiết kế grid thế nào cũng được.
<br><br>
<h2>Sử dụng thế nào?</h2>
Trả lời: thêm class của Bootstrap vào tag html. Như đã nói ở trên thì Bootstrap thực chất là một bộ các file chủ yếu là css và js đi cùng với nhau nên để dùng nó ta chỉ cần thêm class do nó định nghĩa sẳn vào trong trang html của mình là được. Các bạn chú ý ta hoàn toàn có thể sửa mã nguồn của Bootstrap theo mong muốn của mình, nhưng ta không nên làm vậy thì khả năng xảy ra lỗi rất phiền phức bởi thế nếu có ý định như thế thì tốt hơn bạn nên tạo file css của chính mình và khai báo đè lên thuộc tính của Bootstrap, cách này an toàn hơn không lo đụng độ, không lo bug...
<br><br>
Về cách thêm class, Bootstrap có một class tên gọi <span style="font-weight: bold">&nbsp;.container </span> và yêu cầu mặc định tất cả các class khác phải nằm trong class này. Do đó khi sử dụng Bootstrap, đầu tiên ta cứ thêm cái class <strong>&nbsp;.container</strong> này đầu tiên nhé:
<br>
{% highlight html linenos %}
...
<head>
	...
	<link href="css/bootstrap.min.css" ref="stylesheet">
	...
</head>
<body>
	<div class='container'>
		...nội dung ở bên này...
	</div>
	...
	<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.2/jquery.min.js"></script>
	<script src="js/bootstrap.min.js"></script>
	...
</body>
...
{% endhighlight %}
<br>
<strong>&nbsp;.container</strong> giúp cho trang web tự scale khi kích thước của browser thay đổi, như là khi xem trên desktop hay tablet thì kích thước browser sẽ khác nhau container sẽ giúp trang web responsive sao cho phù hợp(mình đỡ phải làm sướng vật :3)
<br><br>
Ta xét qua ví dụ sau về cách dùng grid:
![Hình 2]({{ site.url }}/assets/media/2015-1-24-bootstrap-grid/2.png)<br>
Đây là trang kết quả mà ta muốn tạo thành, mục tiêu là dùng grid để sắp xếp các nội dung trang html được như trên hình
<br><br>
![Hình 3]({{ site.url }}/assets/media/2015-1-24-bootstrap-grid/3.png)<br>
Để làm được như vậy mình chia trang html làm 2 row
<br><br>
![Hình 4]({{ site.url }}/assets/media/2015-1-24-bootstrap-grid/4.png)<br>
Row 1 có 2 cột mỗi cột có kích thước bằng 6, row 2 có 3 cột mỗi cột kích thước bằng 4. Và đây là code:
<br>
{% highlight html linenos %}
...
<div class='container'>

    <div class='row'>				dòng 1
      <div class='col-md-6'>		2 cột có kích thước bằng 6
        ...
      </div>
      <div class='col-md-6'>
        <img src='images/img-header.jpg' alt='Blast off with Bootstrap' />
      </div>
    </div>

    <div class='row'>				dòng 2
      <div class='col-md-4'>
        ...
      </div>
      <div class='col-md-4'>		3 cột có kích thước bằng 4
        ...
      </div>
      <div class='col-md-4'>
        ...
      </div>
    </div>
  </div>
...
{% endhighlight %}
Như các bạn thấy ở trên để thêm row ta dùng class <strong>&nbsp;row</strong>. Đối với column ta dùng class <strong>&nbsp;col-md-4 và col-md-6</strong> tương ứng với cột có kích thước 4 và 6. Bootstrap còn có các lớp khác tương ứng với kích thước của các cột chạy từ 1 đến 12, các class này được đặt tên rất dễ nhớ với định dạng như sau: <strong>&nbsp;col-[loại khung hình]-[kích thước column 1->12]</strong>.<br>
Trong ví dụ col-md-4: kích thước column là 4, md là loại khung hình medium. Bootstrap có tổng cộng 4 loại khung hình theo thức tự từ nhỏ đến lớn: xs <=> extra small, sm <=> small, md <=> medium, lg <=> large. Mục đích của các loại khung hình này nhằm giúp ta có thể khiến trang web responsive sao cho thích hợp với các thiết bị có kích thước khác nhau, phần này mình không biết viết thế nào -> để sau tính :v.
<br><br>
<h2>Một vài ví dụ</h2>
![Hình 5]({{ site.url }}/assets/media/2015-1-24-bootstrap-grid/5.png)<br>
Trong ví dụ này ta có thêm một class mới để tạo ra cột rỗng là col-md-offset-1 và col-md-offset-2. Tên 2 class này khá tương tự tên class column bình thường khác mỗi chữ offset, cú pháp tổng quát như sau <strong>&nbsp;col-[loại khung hình]-offset-[kích thước cột rỗng 1->12]</strong>. Nhìn các quy ước đặt tên này cảm thấy rất sướng người vì nhìn là hiểu chẳng cần động tí neuron nào, mình thấy đây là lí do vì sao mà Bootstrap rất thông dụng: đơn giản dễ dùng. Code:
{% highlight html linenos %}
...
<div class='container'>
    <div class='row'>
      <div class='col-md-6'>...</div>
      <div class='col-md-5 col-md-offset-1'>...</div>
    </div>
    <div class='row'>
      <div class='col-md-5'>...</div>
      <div class='col-md-offset-2  col-md-5'>...</div>
    </div>
  </div>
...
{% endhighlight %}
Các bạn có thể thấy, khi thêm các class các class css ta không cần chú ý đến thứ tự của chúng cứ thêm thế nào tùy thích miễn thế nào đảm bảo không vượt quá 12 cột là được. Vậy nếu hôm nào đẹp trời phởn phơ run tay mắt kém mà thêm dư thì sao? Như đoạn code sau chẳng hạn:
{% highlight html linenos %}
...
<div class='container'>
    <div class='row'>
      <div class='col-md-5'>...</div>
      <div class='col-md-offset-2  col-md-6'>...</div>
    </div>
  </div>
...
{% endhighlight %}
Đoạn code trên chỉ có một row, trong row này cộng cả thảy 5+2+6 ta thấy tổng bằng 13 cột -> vi phạm quy ước và giao diện nó thành thế này:
![Hình 6]({{ site.url }}/assets/media/2015-1-24-bootstrap-grid/6.png)<br>
Do tổng kích thước vượt quá 12 thành ra cột thứ 2 nó tự nhảy xuống thêm 1 dòng, cá nhân mình thấy mặc dù lỗi nhưng cách thằng Bootstrap xử lý rất là ổn ít nhất vẫn còn ra cái gì đó xem được chứ không mất luôn tất cả :). Nói chung bug là bug nhìn xấu hẳn đi các bạn cẩn thận nhé.
<br><br>

**Tham khảo**

* <a href="https://www.codeschool.com/courses/blasting-off-with-bootstrap">**Blasting off with Bootstrap**</a>
* <a href="http://getbootstrap.com/">**Bootstrap**</a>	
* <a href="https://github.com/codeschool/BlastingOffWithBootstrapDemo">**Code of Blasting off with Bootstrap**</a>