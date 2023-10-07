---
title: Logic and Computer Design 笔记
subtitile: Karnaugh Map
date: 2023-10-07 12:56:17
tags: [技术,数电,课程,编程,硬件]
index_img: /img/dvimg2.jpg
categories: 课程
---
# 电路逻辑优化 Circuit Optimization
For one same boolean function, we usually have more than one different implementations of it which costs different numbers of wires and logic gates.   

In order to simplify the circuit, we shall obtain the simplest implementation of a given boolean function.  

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
\left\{
\begin{aligned}
L = 8,\\
G = 11,\\
GN = 14,
\end{aligned}
\right.
$$

