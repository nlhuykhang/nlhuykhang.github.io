---
layout: post
title: High Performance Javascript - Responsive Interface
categories: [web, javascript, book]
tags: [javascript, summarize, high performance javascript, DOM]
published: false
fullview: true
---

updating...

Avoid double evaluation
Use object/array literals
    function addHandler(target, eventType, handler){
         if (target.addEventListener){ //DOM2 Events
            target.addEventListener(eventType, handler, false);
         } else { //IE
            target.attachEvent("on" + eventType, handler);
         }
    }
    
    function removeHandler(target, eventType, handler){
         if (target.removeEventListener){ //DOM2 Events
            target.removeEventListener(eventType, handler, false);
         } else { //IE
            target.detachEvent("on" + eventType, handler);
         }
    }


Lazy loading

function addHandler(target, eventType, handler){
     //overwrite the existing function
     if (target.addEventListener){ //DOM2 Events
        addHandler = function(target, eventType, handler){
             target.addEventListener(eventType, handler, false);
        };
     } else { //IE
         addHandler = function(target, eventType, handler){
            target.attachEvent("on" + eventType, handler);
         };
     }
     //call the new function
     addHandler(target, eventType, handler);
}
function removeHandler(target, eventType, handler){
     //overwrite the existing function
     if (target.removeEventListener){ //DOM2 Events
         removeHandler = function(target, eventType, handler){
            target.addEventListener(eventType, handler, false);
         };
     } else { //IE
         removeHandler = function(target, eventType, handler){
            target.detachEvent("on" + eventType, handler);
         };
     }
     //call the new function
     removeHandler(target, eventType, handler);
}

Conditional advance loading
    var addHandler = document.body.addEventListener ?
                     function(target, eventType, handler){
                        target.addEventListener(eventType, handler, false);
                     }:
                     function(target, eventType, handler){
                        target.attachEvent("on" + eventType, handler);
                     };
    var removeHandler = document.body.removeEventListener ?
                     function(target, eventType, handler){
                        target.removeEventListener(eventType, handler, false);
                     }:
                     function(target, eventType, handler){
                        target.detachEvent("on" + eventType, handler);
                     };


Use the fast parts
    Bitwise operators
        