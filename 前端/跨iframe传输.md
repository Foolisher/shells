![](/assets/前端/.png)

对于Iframe，必须等待Iframe渲染完成才能发送消息，否则获取到的window是空的，而且也不能发送消息；

一个特别注意的点事addTarget里面的对方名称定义一定要与iframe里面的Messenger的定义一致，否则找不到对象

```js
Parent Window

var msger = new Messenger('parent');
msger.addTarget(iframe1.contentWindow, 'iframe1') // 一定要和 iframe 的名称(如iframe1)一致
msger.targets['iframe1'].send('hello')

Iframe1

var msger = new Messenger('iframe1'); 
msger.addTarget(window.parent, 'parent');  // 一定要和父window的信使名称(如parent)一致
msg.listen((msg)=> {})
```



