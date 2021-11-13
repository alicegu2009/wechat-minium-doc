# `Class` minium.LivePlayerElement :id=minium-live-player-element
> `LivePlayerElement` 继承于  `BaseElement`, 提供了对 live-player 组件的操作 <br />
元素标签包含`live-player`

!> 示例小程序没有直播权限，需要测试请使用有直播权限的appid替换

通过调用[`Page.get_element`](minium/Python/api/Page?id=get_element)/[`Page.get_elements`](minium/Python/api/Page?id=get_elements)/[`Element.get_element`](minium/Python/api/Element?id=get_element)/[`Element.get_elements`](minium/Python/api/Element?id=get_elements)方法获取`Element`实例

---

## play() :id=play
> 播放

**Parameters:**
- `None`

**Returns:**
- `None`

---

## pause() :id=pause
> 暂停

**Parameters:**
- `None`

**Returns:**
- `None`

---

## resume() :id=resume
> 恢复播放

**Parameters:**
- `None`

**Returns:**
- `None`

---

## stop() :id=stop
> 停止

**Parameters:**
- `None`

**Returns:**
- `None`

---

## mute() :id=mute
> 静音

**Parameters:**
- `None`

**Returns:**
- `None`

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

---

## exit_full_screen() :id=exit_full_screen
> 退出全屏

**Parameters:**
- `None`

**Returns:**
- `None`

---

## snapshot() :id=snapshot
> 截屏

**Parameters:**
- `None`

**Returns:**
- `None`

---

## request_picture_in_picture() :id=request_picture_in_picture
> 画中画播放。基础库2.15.0后支持

**Parameters:**
- `None`

**Returns:**
- `None`

---

## exit_picture_in_picture() :id=exit_picture_in_picture
> 退出画中画

**Parameters:**
- `None`

**Returns:**
- `None`

---

**代码示例:** 

```python
import minium, time
class LivePlayerTest(minium.MiniTest):
    def test_live_player(self):
        if self.mini.app.app_id == "wx3eb9cfc5787d5458":
            self.skipTest("appid[wx3eb9cfc5787d5458]没有拉流权限，请更换appid")
        page = self.app.redirect_to("/pages/testlive/testplayer")
        page.data = {
            "videoSrc": "https://sf1-hscdn-tos.pstatp.com/obj/media-fe/xgplayer_doc_video/flv/xgplayer-demo-360p.flv"
        }
        el = page.get_element("live-player")
        time.sleep(1)
        el.play()
        time.sleep(3)
        el.pause()
        time.sleep(3)
        el.play()
        time.sleep(3)
        el.mute()
        time.sleep(3)
        el.resume()
        time.sleep(3)
        el.request_full_screen()
        time.sleep(3)
        el.exit_full_screen()
        time.sleep(3)
        el.request_picture_in_picture()
        time.sleep(3)
        el.exit_picture_in_picture()
        time.sleep(3)
        el.snapshot()
        time.sleep(3)
        el.stop()
```

