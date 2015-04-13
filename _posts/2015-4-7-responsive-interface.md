---
layout: post
title: High Performance Javascript - Responsive Interface
categories: [web, javascript, book]
tags: [javascript, summarize, high performance javascript, DOM]
published: false
fullview: true
---

updating...

một hành động javascript khi thực thi không được vượt quá 100ms

Yielding with timers
    yielding control có nghĩa là dừng js đang thực thi lại và để cho UI thực thi trước để đáp trả người dùng sau đó mới cho js thực thi


Timer precision

Array processing with timers
        • Does the processing have to be done synchronously?
        • Does the data have to be processed sequentially?

    function processArray(items, process, callback){
     var todo = items.concat(); //create a clone of the original
     setTimeout(function(){
         process(todo.shift());
         if (todo.length > 0){
             setTimeout(arguments.callee, 25);
         } else {
             callback(items);
         }
     }, 25);
    }    

Splitting Up Tasks
    
    function multistep(steps, args, callback){
     var tasks = steps.concat(); //clone the array
     setTimeout(function(){
        //execute the next task
         var task = tasks.shift();
         task.apply(null, args || []);
         //determine if there's more
         if (tasks.length > 0){
            setTimeout(arguments.callee, 25);
         } else {
            callback();
         }
     }, 25);
    }    

Timed code

    function timedProcessArray(items, process, callback){
     var todo = items.concat(); //create a clone of the original
     setTimeout(function(){
        var start = +new Date();
        do {
            process(todo.shift());
        } while (todo.length > 0 && (+new Date() - start < 50));
        
        if (todo.length > 0){
            setTimeout(arguments.callee, 25);
        } else {
            callback(items);
        }
     }, 25);
    }

Web worker
    
    var worker = new Worker("jsonparser.js");
    
    //when the data is available, this event handler is called
    worker.onmessage = function(event){
         
         //the JSON structure is passed back
         var jsonData = event.data;
         
         //the JSON structure is used
         evaluateData(jsonData);
    };
    //pass in the large JSON string to parse
    worker.postMessage(jsonText);

    //inside of jsonparser.js
    //this event handler is called when JSON data is available
    self.onmessage = function(event){
         //the JSON string comes in as event.data
         var jsonText = event.data;
         
         //parse the structure
         var jsonData = JSON.parse(jsonText);
         
         //send back to the results
         self.postMessage(jsonData);
    };

    dùng khi:
        • Encoding/decoding a large string
        • Complex mathematical calculations (including image or video processing)
        • Sorting a large array


Summary
    JavaScript and user interface updates operate within the same process, so only one can be done at a time.
    Managing the UI thread effectively means ensuring that JavaScript isn’t allowed to run so long that the user experience is affected.

    • No JavaScript task should take longer than 100 milliseconds to execute. Longer execution times cause a noticeable delay in updates to the UI and negatively impact the overall user experience.
    • Browsers behave differently in response to user interaction during JavaScript execution. Regardless of the behavior, the user experience becomes confusing and disjointed when JavaScript takes a long time to execute.
    • Timers can be used to schedule code for later execution, which allows you to split up long-running scripts into a series of smaller tasks.
    • Web workers are a feature in newer browsers that allow you to execute JavaScript code outside of the UI thread, thus preventing UI locking.

    