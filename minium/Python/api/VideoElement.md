# `Class` minium.VideoElement :id=minium-video-element
> `VideoElement` 继承于  `BaseElement`, 提供了对 video 组件的操作 <br />
元素标签包含`video`

> 接口通过[`VideoContext`](https://developers.weixin.qq.com/miniprogram/dev/api/media/video/VideoContext.html)实现，部分安卓真机有问题，建议更换其他机器试试

通过调用[`Page.get_element`](minium/Python/api/Page?id=get_element)/[`Page.get_elements`](minium/Python/api/Page?id=get_elements)/[`Element.get_element`](minium/Python/api/Element?id=get_element)/[`Element.get_elements`](minium/Python/api/Element?id=get_elements)方法获取`Element`实例

---

## play() :id=play
> 播放

**Parameters:**
- `None`

**Returns:**
- `None`

**代码示例:** 

```python
import minium
class VideoElementTest(minium.MiniTest):
	def test_play(self):
		page = self.app.redirect_to("/pages/testelement/testelement")
        element_video = page.get_element("video")
        element_video.play()
```

---

## pause() :id=pause
> 暂停

**Parameters:**
- `None`

**Returns:**
- `None`

**代码示例:** 

```python
import minium
class VideoElementTest(minium.MiniTest):
	def test_pause(self):
		page = self.app.redirect_to("/pages/testelement/testelement")
        element_video = page.get_element("video")
        element_video.pause()
```

---

## stop() :id=stop
> 停止

**Parameters:**
- `None`

**Returns:**
- `None`

**代码示例:** 

```python
import minium
class VideoElementTest(minium.MiniTest):
	def test_stop(self):
		page = self.app.redirect_to("/pages/testelement/testelement")
        element_video = page.get_element("video")
        element_video.stop()
```

---

## seek() :id=seek
> 跳转到指定位置

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
| position |int|None|跳转到的位置，单位 s|

**Returns:**
- `None`

**代码示例:** 

```python
import minium
class VideoElementTest(minium.MiniTest):
	def test_seek(self):
		page = self.app.redirect_to("/pages/testelement/testelement")
        element_video = page.get_element("video")
        element_video.seek(10)
```

---

## send_danmu() :id=send_danmu
>  发弹幕

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
| text |str|None| 文本|
| color |str|#000000| 颜色|

**Returns:**
- `None`

**代码示例:** 

```python
import minium
class VideoElementTest(minium.MiniTest):
	def test_send_danmuself):
		page = self.app.redirect_to("/pages/testelement/testelement")
        element_video = page.get_element("video")
        element_video.send_danmu("say something")
```

---

## playback_rate() :id=playback_rate
>  设置播放倍速

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
| rate | float | None | 倍率，支持 0.5/0.8/1.0/1.25/1.5，2.6.3 起支持 2.0 倍速 |

**Returns:**
- `None`

**代码示例:** 

```python
import minium
class VideoElementTest(minium.MiniTest):
	def test_playback_rate(self):
		page = self.app.redirect_to("/pages/testelement/testelement")
        element_video = page.get_element("video")
        element_video.playback_rate(1.5)
```

---

## request_full_screen() :id=request_full_screen
>  进入全屏

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|  direction | enum | 0 | 设置全屏时视频的方向，不指定则根据宽高比自动判断 |

- 0	正常竖向
- 90	屏幕逆时针90度
- -90	屏幕顺时针90度

**Returns:**
- `None`

**代码示例:** 

```python
import minium
class VideoElementTest(minium.MiniTest):
	def test_request_full_screen(self):
		page = self.app.redirect_to("/pages/testelement/testelement")
        element_video = page.get_element("video")
        element_video.request_full_screen(90)
```

---

## exit_full_screen() :id=exit_full_screen
> 退出全屏

**Parameters:**
- `None`

**Returns:**
- `None`

**代码示例:** 

```python
import minium
class VideoElementTest(minium.MiniTest):
	def test_exit_full_screen(self):
		page = self.app.redirect_to("/pages/testelement/testelement")
        element_video = page.get_element("video")
        element_video.exit_full_screen()
```

---

## show_status_bar() :id=show_status_bar
> 显示状态栏

!> 仅在iOS全屏下有效

**Parameters:**
- `None`

**Returns:**
- `None`

**代码示例:** 

```python
import minium
class VideoElementTest(minium.MiniTest):
	def test_show_status_bar(self):
		page = self.app.redirect_to("/pages/testelement/testelement")
        element_video = page.get_element("video")
        element_video.request_full_screen()
        element_video.show_status_bar()
```

---

## hide_status_bar() :id=hide_status_bar
> 隐藏状态栏

!> 仅在iOS全屏下有效

**Parameters:**
- `None`

**Returns:**
- `None`

**代码示例:** 

```python
import minium
class VideoElementTest(minium.MiniTest):
	def test_hide_status_bar(self):
		page = self.app.redirect_to("/pages/testelement/testelement")
        element_video = page.get_element("video")
        element_video.request_full_screen()
        element_video.hide_status_bar()
```

---