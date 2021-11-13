# `Class` minium.AudioElement :id=minium-audio-element
> `AudioElement` 继承于  `BaseElement`, 提供了对 audio 组件的操作 <br />
元素标签包含`audio`

> `audio`标签在`1.6.0`版本的基础库之后不再维护，不建议继续使用该标签

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
class AudioElementTest(minium.MiniTest):
	def test_play(self):
		page = self.app.redirect_to("/pages/testelement/testelement")
        element_audio = page.get_element("audio")
        element_audio.play()
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
class AudioElementTest(minium.MiniTest):
	def test_pause(self):
		page = self.app.redirect_to("/pages/testelement/testelement")
        element_audio = page.get_element("audio")
        element_audio.pause()
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
class AudioElementTest(minium.MiniTest):
	def test_seek(self):
		page = self.app.redirect_to("/pages/testelement/testelement")
        element_audio = page.get_element("audio")
        element_audio.seek(10)
```

---

## set_src() :id=set_src
> 设置音频地址

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
| src |str|None|音频地址|

**Returns:**
- `None`

**代码示例:** 

```python
import minium
class AudioElementTest(minium.MiniTest):
	def test_set_src(self):
		page = self.app.redirect_to("/pages/testelement/testelement")
        element_audio = page.get_element("audio")
        element_audio.set_src(“http://ws.stream.qqmusic.qq.com/M500001VfvsJ21xFqb.mp3?guid=ffffffff82def4af4b12b3cd9337d5e7&uin=346897220&vkey=6292F51E1E384E06DCBDC9AB7C49FD713D632D313AC4858BACB8DDD29067D3C601481D36E62053BF8DFEAF74C0A5CCFADD6471160CAF3E6A&fromtag=46”)
```

---

