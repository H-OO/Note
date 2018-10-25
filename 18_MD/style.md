## **flex**

- flex-direction: row; // 默认主轴为水平方向 [ row-reverse | column | column-reverse ]
- flex-wrap: nowrap; // 默认不换 [ wrap | wrap-reverse ]
- flex-flow: row nowrap; // 默认主轴为水平方向，不换行

- justify-content: flex-start; // 默认左对齐 [ flex-end | center | space-between | space-around ]

- align-items: stretch; // 默认高度占满交叉轴 [ flex-start | flex-end | center | baseline ]

- align-content: stretch; // 默认多根轴线占满交叉轴 [ flex-start | flex-end | center | space-between | space-around ]

- order: 0; // 排列顺序 数值越小越靠前

- flex-grow: 0; // 默认不自动填充剩余空间
- flex-shrink: 1; // 默认空间不足就压缩大小
- flex-basis: auto; // 占据的固定空间
- flex: 0 1 auto; // 简写默认不自动填充，不压缩，占据空间自动设置