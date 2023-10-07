---
title: Logic and Computer Design 笔记
date: 2023-10-07 12:56:17
tags: [技术,数电,课程,编程,硬件]
index_img: /img/dvimg2.jpg
categories: 课程
---
# 电路逻辑优化 Circuit Optimization
For one same boolean function, we usually have more than one different implementations of it which costs different numbers of wires and logic gates.   

In order to simplify the circuit, we shall obtain the simplest implementation of a given boolean function.  
## 电路复杂度 Cost Criteria
Before we make attempts to optimize the circuit, we must set up a certain criteria to measure costs of different implementations of a boolean function. We can define:  
+ `Literal cost (L)`  the count of boolean variable in the implementation.
+ `Gate input cost (G)`  the sum of gate inputs excluding inverters.
+ `Gate input cost with NOTs (GN)`  the sum of gate inputs including inverters.  
  
E.G.   
$$
F=BD+A\overline BC+A\overline C\overline D
$$
We can easily obtain that, 
$$
L = 8,
G = 11,
GN = 14,

$$
We found an efficient criteria to measure the cost, then what we need is a effective method to obtain the simplest implementation.
## 卡诺图 Karnaugh Map

Karnaugh map (K-map) is a graphical representation of a Boolean function, which is widely-used to obtain the simplest implementation of a boolean function.   
E.G, we can represent the boolean function  
$$
F=BD+A\overline BC+A\overline C\overline D
$$
in the k-map  
|  AB\CD   | 00  | 01 | 11 | 10 |
|  :----:  | :----:  | :----: | :----:| :----:|
| 00  |0|0|0|0|
| 01  |0|1|1|0|
| 11  |1|1|1|0|
| 10  |1|1|1|0|
  
note that the enumerate of AB and CD must be arranged in Gray-code order.  

In the K-map, we can define:  
+ 项 `Term` 
  + An "1" in the K-map, corresponding to one minterm in the DNF.
+ 蕴含项 `Implicant`
  +  Rectangles produced by combining adjacent squares with the number of a power of 2.
+ 质蕴含项 `Prime Implicant` 
  + `Implicants` that can't be merged into other `Implicants`
+ 实质本源蕴含项 `Essential Prime Implicants` 
  + `Prime Implicants` which have at least one `term` not included by other `Implicants`

To find the simplest implementation of a boolean function via K-map, we can follow the following steps:
+ Find minterms of a function (via DNF)
+ Illustrate the K-map
+ Find all Essential Prime Implicants
+ Eliminate the redundant (冗余的) variables
  
E.G.  
$$
F(X,Y,Z)= \Sigma (0, 1, 2,4,6,7)
$$


