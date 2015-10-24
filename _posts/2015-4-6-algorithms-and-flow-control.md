---
layout: post
title: High Performance Javascript - Algorithms and flow control
categories: [web, javascript, book]
tags: [javascript, summarize, high performance javascript, DOM]
published: false
fullview: true
---


đìu hơi bị hết hồn nha cái này từ nào giờ mới nghe nói à, có thiệt không vậy trời thay đổi thứ tự duyệt khi loop làm giảm đến tận 50% thời gian chạy, thật ra là cho lập từ cuối về nên cứ trừ số thứ tự đi khi số thứ tự bằng 0 thì nó tự out luôn

ủa nhưng mà vậy nếu lập trên mảng thì hỏng rồi chạy từ 0 -> n-1 mà này chạy từ n -> 1

Duff's device
	cái trò này hơi bị ảo à nha :)) chắc chắn mình phải viết một cái post nho nhỏ cho nó mới được hơi bị ảo diệu mặc dù là chắc chẳng mấy khi dùng phải cả vài trăm ngàn dòng code mới thấy có hiệu quả mà
	miêu tả thế nào nhỉ?
	à là cho một vòng lập nhỏ hơn trong vòng lập lớn vòng lập nhỏ lập 8 lần, hoặc gọi hàm cần thực hiện 8 lần, vòng lập lớn bên ngoài có số lần lập bằng n/8
	nhưng mà tại sao lại là 8??

Summary
    • The for, while, and do-while loops all have similar performance characteristics,
    and so no one loop type is significantly faster or slower than the others.
    • Avoid the for-in loop unless you need to iterate over a number of unknown object
    properties.
    • The best ways to improve loop performance are to decrease the amount of work
    done per iteration and decrease the number of loop iterations.
    • Generally speaking, switch is always faster than if-else, but isn’t always the best
    solution.
    • Lookup tables are a faster alternative to multiple condition evaluation using ifelse or switch.
    • Browser call stack size limits the amount of recursion that JavaScript is allowed to
    perform; stack overflow errors prevent the rest of the code from executing.
    • If you run into a stack overflow error, change the method to an iterative algorithm
    or make use of memoization to avoid work repetition.