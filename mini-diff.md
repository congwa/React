# mini-diff

Fiber是React 16.8开始中引入的一种新的渲染架构，为了提高渲染效率和优化用户体验，React使用了一种基于diff算法的渲染策略，可以避免不必要的DOM操作和渲染，提高渲染效率和性能。

在React Fiber中，diff算法是在Reconciler阶段使用的一种算法，用于比较和更新前后两个虚拟DOM树之间的差异，并生成一组操作序列，用于更新对应的真实DOM节点。

下面是基于Fiber架构的diff算法的详细源码：
```javascript
function reconcileChildren(current, workInProgress, nextChildren) {
  if (current === null) {
    // 如果当前节点没有孩子节点，则创建新的节点
    workInProgress.child = mountChildFibers(
      workInProgress,
      null,
      nextChildren,
      expirationTime,
    );
  } else if (current.child === null) {
    // 如果当前节点已经有孩子节点了，则直接进行更新
    workInProgress.child = reconcileChildFibers(
      workInProgress,
      null,
      nextChildren,
      expirationTime,
    );
  } else {
    // 通过diff算法比较前后两个树之间的差异
    workInProgress.child = reconcileChildFibers(
      workInProgress,
      current.child,
      nextChildren,
      expirationTime,
    );
  }
}

function reconcileChildFibers(
  returnFiber,
  currentFirstChild,
  newChild,
  expirationTime,
) {
  // 使用单链表来存储节点，比较节点树的过程中会用到这个链表
  let currentChild = currentFirstChild;
  let newChildIndex = 0;

  // 遍历链表，依次比较前后两个树之间的差异
  while (
    currentChild !== null &&
    newChildIndex < newChild.length
  ) {
    if (currentChild.key === newChild[newChildIndex].key) {
      // 如果节点key相同，则进行更新
      const newChild = newChild[newChildIndex];
      const newExpirationTime = expirationTime;

      if (shouldClone) {
        cloneChildIndex += 1;
        currentChild = currentChild.sibling;
      } else {
        if (
          currentChild.expirationTime <= newExpirationTime ||
          currentChild.childExpirationTime <= newExpirationTime
        ) {
          // 如果需要更新，则进入update阶段
          const clone = cloneChildFiber(
            currentChild,
            returnFiber,
            newExpirationTime,
          );
          clone.child = cloneChildFiber(
            currentChild.child,
            clone,
            newExpirationTime,
          );
          currentChild = currentChild.sibling;
          workInProgress = push(clone, workInProgress);
        } else {
          // 如果不需要更新，则直接复用
          newChild[newChildIndex].stateNode =
            currentChild.stateNode;
          workInProgress = appendAllChildren(
            returnFiber,
            currentChild,
            newChild[newChildIndex],
            expirationTime,
          );
        }
      }
      newChildIndex += 1;
    } else {
      if (shouldClone) {
        // 如果需要clone，则复制节点，进入mount阶段
        const created = createChild(returnFiber, newChild[newChildIndex], expirationTime);
        if (created !== null) {
          const next = created.sibling;
          created.sibling = null;
          workInProgress = appendChildToContainerChildFiber(
            workInProgress,
            created,
          );
        }
        newChildIndex += 1;
      } else {
        // 如果当前节点不需要更新，则遍历下一个节点
        currentChild = deleteChild(returnFiber, currentChild);
      }
    }
  }

  // 遍历完成之后，如果还有未处理完的新节点，则依次进行创建
  if (newChildIndex < newChildren.length) {
    workInProgress = placeNewChildren(
      returnFiber,
      workInProgress,
      newChildren,
      newChildIndex,
      expirationTime,
    );
  }

  return workInProgress.child;
}
```

以上是基于Fiber架构的diff算法的实现代码，简单来说，就是对新的虚拟DOM树和旧的虚拟DOM树进行比较，通过对每个节点的key值和type值的比较，找到需要更新的节点，并生成一组操作序列，用于对应更新真实DOM节点。整个过程参考了React官方的源码实现，只是进行了简化和去除不必要的细节。