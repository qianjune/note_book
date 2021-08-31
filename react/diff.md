## [diff算法](https://segmentfault.com/a/1190000039021724)

得到新的state之后进行对比

对比要素：

1. key
2. tag



### 节点类型：

#### 单节点

* 单个旧节点 -》 单个旧节点
* 多个旧节点 -〉单个旧节点
* 没有旧节点 -》 单个旧节点

##### oldFiber链不为空

##### oldFiber链为空



#### 多节点

将newChildren和oldFiber里的节点逐个对比，通过tag和key

* 节点更新

  没变：从oldFiber节点clone一个fiber节点，将来自newChildren的props，赋予clone的fiber

  变化：立即中断便利

  ```js
  let newIdx = 0;
  for (; oldFiber !== null && newIdx < newChildren.length; newIdx++) {
     ...
     // 更新节点，对于DOM节点来说，updateSlot内部会判断
     // key 和 tag。任意一个不同，则返回null
     const newFiber = updateSlot( returnFiber,
       oldFiber,
       newChildren[newIdx],
       lanes,
     );
     // newFiber为null则说明当前的节点不是更新的场景，中止这一轮循环
     if (newFiber === null) {
       if (oldFiber === null) {
          oldFiber = nextOldFiber;
       }
       break;
     }
      ...
   }
  ```

  

* 节点增加

  oldFiber循环完，newChildren剩下的都新建fiber节点，并用链表形式链接到fiber链上

* 节点删除

  当newChildren遍历完，oldFiber还没有，剩下的节点就标记deletion的effectTag

* 节点移动

  如果位置相同，就不需要移动，并且把lastPlacedIndex设为最右位置的index；

  继续遍历newChildren，将位置和之前不同的节点座位固定节点，更新lastPlacedIndex，并从existingChildren中删除该元素

