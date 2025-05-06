# AI 写作

## 绘制选区

## 流式请求

接口流式输出结果，前端显示流式结果，类似打字机，接口返回的结果，一个字一个字的显示到界面。

```
import { fetchEventSource } from '@microsoft/fetch-event-source';

let ctrl: AbortController;

function fetchContent() {
  ctrl = new AbortController();

  fetchEventSource(url, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      Authorization: `Bearer ${token}`,
    },
    body: JSON.stringify(fetchParams),
    signal: ctrl.signal,
    openWhenHidden: true, // 切换浏览器流式是否暂停
    async onopen(response) {
      // 处理请求正常、401、异常等情况
      if (response.ok && response.headers.get('content-type').indexOf(EventStreamContentType) > -1) {// ?
        return;
      } else if (response.status === 401) {
        // refreshToken
        // fetchContent()
      } else {
        // handleError
      }
    },
    onmessage(msg) {
      // 处理流式结果
      const {data} = msg;
      const result = JSON.parse(data).results;
    },
    onclose() {
      // 请求结束
    },
    onerror(err) {
      // 请求失败
    }
  });
}
```

## 实时设置 AI 会话框位置信息

## 在会话期间用户做其他操作时做提示

捕获鼠标点击事件，AI 写作区域为 areaActive **鼠标点击区域 A 时进行提示，点击区域 B 时不做提示，并阻止鼠标点击行为（既点击事件无效，例如点击区域 B 的按钮，不触发按钮对应的事件）**

在编辑器里面监听 `mousedown` 和 `click` 事件；

- 通过监听 `mousedown` 事件，判断是否点击了 AI 会话框外的区域，如果是，判断是否是需要做提示的区域，如果是则做出相应的提示，否则阻止默认行为。但是若鼠标点击了按钮，且按钮上绑定了 `onclick` 方法，那么此时该方法仍然会被触发
- 通过监听 `click` 事件， 判断是否点击了 AI 会话框外的区域，若是，判断点击的元素是否在需要阻止事件的区域，若在则阻止默认行为、阻止事件传播、阻止事件立即传播

```
useEffect(() => {
  const cancelTips = (event, triggerBtn?: HTMLElement) => {

  }

  function handleClickOutside (event) {
    // event: MouseEvent ——> UIEvent ———> Event
    const areaActive = areaActiveRef.current;
    const areaA = document.getElementById('areaA')
    const areaB = document.getElementById('areaB')
    if (areaActive.contains(areaA)) {
      
    }
  }

  document.addEventListener('mousedown', handleClickOutside, true);
  document.addEventListener('click', handleClickOutside, {capture: true});

  return () => {
    // 卸载组件时，移除事件监听器
    document.removeEventListener('mousedown', handleClickOutside, true);
    document.removeEventListener('click', handleClickOutside, {capture: true});
  }
}, [])
```

`click` 事件和 `mousedown` 事件的 event 都是 MouseEvent， 继承自 Event。
