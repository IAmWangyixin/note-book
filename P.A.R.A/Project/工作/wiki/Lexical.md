# 编辑器回格行为

## 文章结构
节点A
段落
列表

鼠标从段落末尾开始回格，则节点A无法被删除；如果鼠标聚焦在段落的开始进行回格，则节点A被删除了。期望节点A无论如何都不会被删除。

## 思考
为什么从段落末尾开始进行回格，节点A不会被删除，但鼠标从段落起始位置直接回格，节点A会被删除？

## AI 回答（通义灵码）
![TONGYI](/img/lexical-backspace.png)

待验证，回答不太能理解

## 分析
行为：在敲击回车时如果将要被删除的节点是节点A，则阻止删除行为。否则正常删除。

## 实现
通过注册 `KEY_BACKSPACE_COMMAND` 监听回格行为，并在回调函数中执行判断并控制是否阻止回格行为。

主要判断逻辑：
如果鼠标在节点A后紧邻的位置进行删除，对于上述文章结构来说就是段落的起始位置进行回格，此时selection的anchor.offset === focus.offset === 0;

```
useEffect(() => {
    // 单个
    return editor.registerCommand<KeyboardEvent>(
        KEY_BACKSPACE_COMMAND,
        (event) => {
            const selection = $getSelection();
            
            if (!$isRangeSelection(selection)) {
                return false;
            }

            const anchor = selection.anchor;
            if (anchor.offset !== 0) {
                return false;
            }

            // 最近的块节点元素(对应该处的ParagraphNode)
            const ancestorNode = $getNearestBlockElementAncestorOrThrow(anchor.getNode());
            const ancestorNodeFirstChild = ancestorNode.getFirstChild();

            if (anchor.offset === 0 && focus.offset === 0 && anchor.key === ancestorNodeFirstChild?.getKey() && $isNodeA(ancestorNode.getPreviusSibling())) {
                return true; // 阻止删除操作
            }
        }
    )

    // 多个
    // const removeListener = mergeRegister(
    //     editor.registerCommand(...)
    // );
    // return removeListener;
}, [])
return false
```

调试发现：
当在段落进行回格时，段落P的文本节点W和节点A被合并到段落P了。导致上述判断 `anchor.key === ancestorNodeFirstChild?.getKey()` 失效。

