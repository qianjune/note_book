## BFC

**具有 BFC 特性的元素可以看作是隔离了的独立容器，容器里面的元素不会在布局上影响到外面的元素，并且 BFC 具有普通容器所没有的一些特性。**

### 触发条件

1. float（非none）
2. overflow（非visible）
3. display: inline-block、table-cells、flex
4. position: absolute、fixed
5. 根元素



### 特点

#### 1.问题：margin重叠

原因： 同一个BFC相邻的容器上下margin重叠

解决方式： 给其中一个容器套父元素，并触发起BFC

#### 2.问题：浮动元素没有正确参与高度计算

原因：浮动元素脱离文本流，父元素没设置高度，导致高度塌陷

解决方式：触发父元素BFC，让浮动元素也参与高度计算

#### 3.问题：浮动元素与普通元素发生重叠

原因：浮动元素脱离文本流

解决方式：触发普通元素BFC，BFC不会和浮动元素重叠

#### 4.BFC内元素垂直排列

#### 5.BFC内元素的margin-left会和BFC的border-left相接触

#### 6.BFC内的元素不会影响到BFC外的元素，是独立容器



