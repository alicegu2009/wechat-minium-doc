# `Class` minium.LivePusherElement :id=minium-live-pusher-element
> `LivePusherElement` 继承于  `BaseElement`, 提供了对 live-pusher 组件的操作 <br />
元素标签包含`live-pusher`

!> 示例小程序没有直播权限，需要测试请使用有直播权限的appid替换

通过调用[`Page.get_element`](minium/Python/api/Page?id=get_element)/[`Page.get_elements`](minium/Python/api/Page?id=get_elements)/[`Element.get_element`](minium/Python/api/Element?id=get_element)/[`Element.get_elements`](minium/Python/api/Element?id=get_elements)方法获取`Element`实例

---

## start() :id=start
> 开始推流

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

## switch_camera() :id=switch_camera
> 切换摄像头

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

## toggle_torch() :id=toggle_torch
> 切换手电筒

**Parameters:**
- `None`

**Returns:**
- `None`

---

## play_bgm() :id=play_bgm
>  播放背景音

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|  url | str | Not None | 背景音乐链接 |

**Returns:**
- `None`

---

## stop_bgm() :id=stop_bgm
> 停止背景音乐

**Parameters:**
- `None`

**Returns:**
- `None`

---

## pause_bgm() :id=pause_bgm
> 暂停背景音乐

**Parameters:**
- `None`

**Returns:**
- `None`

---

## resume_bgm() :id=resume_bgm
> 恢复背景音乐

**Parameters:**
- `None`

**Returns:**
- `None`


---

## set_bgm_volume() :id=set_bgm_volume
> 设置背景音乐音量

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|  volume | float | Not None | 0-1的浮点数 |

**Returns:**
- `None`

---

## set_mic_volume() :id=set_mic_volume
> 设置麦克风音量

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|  volume | float | Not None | 0-1的浮点数 |

**Returns:**
- `None`

---

## start_preview() :id=start_preview
> 开启摄像头预览

**Parameters:**

- `None`

**Returns:**
- `None`

---

## stop_preview() :id=stop_preview
> 关闭摄像头预览

**Parameters:**

- `None`

**Returns:**
- `None`

---

## send_message() :id=send_message
> 发送SEI消息

**Parameters:**

|名称| 类型| 默认值| 说明|
| :----- | :-----: | :-----: | :----- |
|  msg | str | Not None | SEI消息 |

**Returns:**
- `None`

---

**代码示例:** 


```python
import minium, time
class LivePusherTest(minium.MiniTest):
    def test_live_pusher(self):
        if self.mini.app.app_id == "wx3eb9cfc5787d5458":
            self.skipTest("appid[wx3eb9cfc5787d5458]没有推流权限，请更换appid")
        page = self.app.redirect_to("/pages/testlive/testpusher")
        self.native.allow_authorize()
        time.sleep(1)
        self.native.allow_authorize()
        el = page.get_element("live-pusher")
        time.sleep(1)
        el.switch_camera()
        time.sleep(1)
        el.start()
        time.sleep(3)
        el.pause()
        time.sleep(3)
        el.resume()
        time.sleep(3)
        el.snapshot()
        time.sleep(3)
        el.toggle_torch()
        time.sleep(3)
        el.play_bgm("https://dldir1.qq.com/music/release/upload/t_mm_file_publish/2339610.m4a")
        time.sleep(3)
        el.pause_bgm()
        time.sleep(3)
        el.resume_bgm()
        time.sleep(3)
        el.set_bgm_volume(0.5)
        time.sleep(3)
        el.set_mic_volume(0.5)
        time.sleep(3)
        el.stop_bgm()
        time.sleep(3)    
        el.start_preview()
        time.sleep(3)    
        el.stop_preview()
        time.sleep(3)    
        el.send_message("123")
        time.sleep(3)    
        el.stop()    
```