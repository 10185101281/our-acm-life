## 差分约束 ##
1.约束求的是$X_i-X_a$的最大值，取决于$SPFA(X_a)$的参数
2.方向要统一
3.极值方向也要统一，求$|X_j-X_i|$,如果$i<j$正常做，$i>j$则交换位置，条件等价

## 弦图/团 ##
团：是完全图的子图
极大团：不能被任何一个团包含
最大团：点数最多的极大团
诱导子图？
单纯点？

$$团数 \leq色数\\最大独立集\leq最小团覆盖数$$
