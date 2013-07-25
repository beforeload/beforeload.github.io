title: The Master Method
date: 2013-07-21 10:53:44
categories: Algorithm
tags: 
- Algorithm
- Math
---

T(n) <= aT(n/b) + O(n<sup>d</sup>)

T(n) = O(n<sup>d</sup>logn) if a = b<sup>d</sup> (Case 1)

T(n) = O(n<sup>d</sup>) if a < b<sup>d</sup> (Case 2)

T(n) = O(n<sup>log<sub>b</sub>a</sup>) if a > b<sup>d</sup> (Case 3)
