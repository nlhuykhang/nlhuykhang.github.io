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
<h2>**Browser detection**</h2>
**User-Agent detection**
Mọi browser đều có user-agent string để xác định cho thằng khác biết, cụ thể là server và client, bản thân là tên chi cha mẹ là ai nhà ở đâu để cho người ta còn biết mà chửi, như mình thì thích chủ anh M$ lắm :v.<br>
Cú pháp: không biết server thế nào, mà hình như backend chẳng thằng nào thèm đếm xỉa chỉ client frontend là mình lo thôi. Frontend thì detect thế này: <strong>&nbsp;navigator.userAgent</strong>. Nhưng mà cách này có nhiều nhược điểm vì kết quả trả về là string nên trước hết phải parse rồi kiểm tra rồi còn ba thứ hầm bà lằng về version của browser hay nếu browser update có code mới thì code cũng phải update lại mà update vì code của mình còn mệt huống chi vì code của thằng khác => đây là cách cuối cùng nên dùng.<br>
**Feature detection**
