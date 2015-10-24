---
layout: post
title: Scheme language
categories: [scheme, language]
tags: [study, thesis]
published: false
fullview: true
---

<h2>Numbers and arithmetic</h2>

(+ 5 5)      (+ -5 5)     (+ 5 -5)     (- 5 5)     (* 3 4)      (/ 8 12)

  (* (+ 2 2) (/ (* (+ 3 5) (/ 30 10)) 2))
= (* 4 (/ (* 8 3) 2))
= (* 4 (/ 24 2))
= (* 4 12)
= 48

(sqrt A) computes (A)1/2;

(expt A B) computes AB;

(remainder A B) computes the remainder of the integer division A/B;

(log A) computes the natural logarithm of A; and

(sin A) computes the sine of A radians.

<h2>Variables and programs</h2>

(define (area-of-disk r) 
  (* 3.14 (* r r)))

  (area-of-disk 5) 
= (* 3.14 (* 5 5))
= (* 3.14 25)
= 78.5

(define (area-of-ring outer inner) 
  (- (area-of-disk outer)
     (area-of-disk inner)))

Fahrenheit to Celcius
(define (F2C f)
   (/ (- f 32) 1.8))

(define (r n)
    (n))


;; Contract: area-of-ring : number number  ->  number

;; Purpose: to compute the area of a ring whose radius is
;; outer and whose hole has a radius of inner

;; Example: (area-of-ring 5 3) should produce 50.24

;; Definition: [refines the header]
(define (area-of-ring outer inner)
  (- (area-of-disk outer)
     (area-of-disk inner)))
  
;; Tests:
(area-of-ring 5 3) 
;; expected value
50.24


;; condition
(cond
  [(< n 10) 20]
  [(> n 20) 0]
  [else 1])


(define-struct posn (x y))

1. make-posn, the CONSTRUCTOR, which creates posn structures;
2. posn-x, a SELECTOR, which extracts the x coordinate;
3. posn-y, also a selector, which extracts the y coordinate