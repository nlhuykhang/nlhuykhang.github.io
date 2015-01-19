---
layout: post
title: Tut - Deploy web Nodejs dùng MongoDb với host Openshift
categories: [tut, web]
tags: [nodejs, mongodb, openshift]
fullview: true
---

Tháng trước để thỏa mãn giáo viên lý thuyết mình phải vật vã cả ngày để up một trang web dùng Nodejs và MongoDb lên Openshift. Nay có lòng tốt muốn giúp các bạn tiết kiệm được một ngày nên sẽ làm tut hướng dẫn chi tiết cách làm thế nào (và cũng để chính mình nhớ luôn). Ai làm theo mà không được dám chừng ăn ở đừng trách mình nhé :3.

Tut gồm có 3 phần chính: <br>

* Cài đặt mọi thứ râu ria cần thiết <br>
* Tạo app trên cloud <br>
* Cấu hình trang web để chạy được trên host và deploy<br>

Các bạn cần gì thì xem đó không cần phải step-by-step <br>

<h1>I. Phần cài đặt</h1>
Để up được host Openshift cơ bản chúng ta bắt buộc có các thứ sau đây:<br>

* Tài khoản Openshift (hiển nhiên rồi), này tự đăng kí nhé.<br>
* Openshift client tools, cái này mình sẽ hướng dẫn gồm có tất cả 3 bước<br>

**Chú ý**: đối với các bạn ở Việt Nam khi làm 3 bước sau đây thường sẽ bị dính lỗi do setting mặc định trong máy Windows, mình không nhớ rỏ bước nào(làm cả tháng rồi :v). Nên để tránh ức chế đầu tiên các bạn vào **Controll Panel -> Region -> Administrative -> Change system locate...** và chọn **English(United States)**, sau đó reset máy(nếu nó yêu cầu)<br>
<br>
![Hình 1]({{ site.url }}/assets/media/1.png)<br>

![Hình 2]({{ site.url }}/assets/media/2.png)<br>
<br>
Giải thích thêm: do client tools của Openshift(và đa số các cloud khác Heroku chẳng hạn) không có hỗ trợ Unicode nên đối với các máy ở Việt Nam dùng Unicode thì chắc chắn bị lỗi không thể cài được(nôm na là thế tại mình cũng không hiểu rỏ :v) <br>

Giờ mình vào phần chính:<br>

* **Bước 1**: Cài đặt Ruby<br>
Tải phiên bản mới nhất của <a class="btn btn-default" href="http://rubyinstaller.org/">**RubyInstaller**</a> và cài đặt. 
Khi cài đặt các bạn nhớ tick chọn **Add Ruby executables to your PATH** để có thể chạy Ruby bằng Command Line.
<br>
![Hình 3]({{ site.url }}/assets/media/3.png)<br>

* Bước 2: Cài đặt Git<br>
Bạn nào có rồi thì thôi nhé, cài để quản lý code của mình trên server ấy mà.
Tương tự Ruby, các bạn cũng download <a class="btn btn-default" href="http://msysgit.github.io/">**Git for Windows**</a> và cài đặt.
Tick chọn **Run Git from the Windows Command Link Prompt** khi cài.

* Bước 3: Cài đặt rhc<br>
Đây chính là client của Openshift, các bạn mở command line và nhập dòng lệnh sau:<br>
{% highlight ruby %}
C:\> gem install rhc
{% endhighlight %}

Đến đây, nếu tất cả thành công chúng ta đã hoàn thành phần cài đặt. 

<h1>II. Phần tạo app trên cloud</h1>
Phần này có 2 cách làm một là tạo bằng command line hai là tạo trực tiếp trên web của Openshift, mình không biết dùng command line(gà ><) nên sẽ hướng dẫn tạo trực tiếp.
Bước này thực chất rất đơn giản các bạn hoàn toàn có thể tự làm(mình mò đại cũng ra không cần phải xem theo tut :D)<br>

* Bước 1: đăng nhập vào <a class="btn btn-default" href="http://msysgit.github.io/">**Openshift**</a><br>
* Bước 2 -> n: tuần tự theo hình<br>
![Hình 4]({{ site.url }}/assets/media/4.png)<br>
<br>
![Hình 5]({{ site.url }}/assets/media/5.png)<br>
<br>
![Hình 6]({{ site.url }}/assets/media/6.png)<br><br>
Hình này có 2 chỗ cần chú ý: tên web của bạn thì không bàn rồi, còn phần Git URL nếu bạn có up source code mình lên git thì có thể dán link vào đây, không có vẫn ổn vì làm sau cũng được.  
<br>
![Hình 7]({{ site.url }}/assets/media/7.png)<br>
Hoàn thành tạo app<br>

Để tạo cơ sở dữ liệu MongoDb cho trang web các bạn chỉ cần chọn như hình sau đó next next là được.<br>
![Hình 8]({{ site.url }}/assets/media/8.png)<br>

<h1>III. Phần cấu hình và deploy</h1>
<h2>Cấu hình</h2>
Phần này mình sẽ update sau giờ bận xem đá banh rồi :3<br>
<h2>Deploy</h2>

* Bước 1: clone source code của trang web<br>
Các bạn đăng nhập vào Openshift và ứng dụng của mình và copy đoạn code trong ô **Source Code**, gọi đoạn code này là SOURCECODE<br>
![Hình 9]({{ site.url }}/assets/media/9.png)<br>
Sau đó mở command line, nhập lệnh sau để clone source code của trang web<br>
{% highlight ruby %}
C:\> git clone SOURCECODE<br>
{% endhighlight %}
* Bước 2: <br>
Giờ bạn chỉ cần copy tất cả code của trang web bạn vào thư mục vừa clone về được. Sau đó dùng các lệnh sau đây đủ deploy lên server<br>
{% highlight ruby %}
git add
git commit -m "whatever"
git push
{% endhighlight %}

Mỗi lần bạn cần thay đổi về source code của trang web chỉ cần sửa code ở local, sau đó dùng các lệnh trên đây để push lên server.

Vậy là đã xong web của bạn giờ đã được chạy trên cloud Openshift.
Enjoy it.
fuck ><
