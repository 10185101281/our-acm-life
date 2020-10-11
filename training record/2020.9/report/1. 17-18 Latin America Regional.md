# 17-18 Latin America Regional



### F. Fundraising

有N个人，每个人有三个值: beauty, fortune , donation

选择若干个人，要求donation总和最大

条件是对于选择的任意两个人X,Y。 

若 X.beauty > Y.beauty 则必须 X.fortune > Y.fortune。



注意：完全相等的两个人合并！！

显然二维偏序问题，先排个序，例如按照beauty,fortune。 从前往后处理。

显然对于每一个beauty值，至多只能挑选一个fortune值，只需考虑fortune一维



不妨设 $F_i$表示取到Fortune值为i的时候的最大donation和

每当我们在考虑是否选择一个人$(b,k,donation)$的时候，

我们时间上就是在求考虑在b之前   $Max(F_1....F_{k-1}) + donation$  的最大值



用线段树或者树状数组，从后往前更新或者慢更新就行了。



### Marble coin

题意： K个堆，合并后字典序最小



显然后缀数组，连起来后根据rank顺序使用优先队列，每次弹出一个值后把他后面的位置加进去，如果是休止符就舍弃。注意休止符得搞大点。





### Keep it covered

题意：太麻烦略。

确实没想到是个二分图网络流，不过情理之中，毕竟是网格图。 把能够贡献的度数看成是流量，发现每个普通点都能往四周总共2的度数，特殊点是1，染色后跑最大流。S流出=T流入即可。











