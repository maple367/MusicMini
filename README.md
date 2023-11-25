## 概述
HTML5前端网易云播放器，支持哔哩哔哩直播点歌    
可用于OBS浏览器源作为点歌机使用   
也可经过electron包装后作为桌面悬浮播放器

## 注意事项
需要先行配置nodejs网易云API和nodejs哔哩哔哩直播弹幕跨域转发API          
没有登录功能，所以版权歌听不了   
## 使用方法
### 1. 安装API
nodejs: <https://nodejs.org>    
网易云音乐 API: <https://github.com/Binaryify/NeteaseCloudMusicApi>    
哔哩哔哩直播弹幕跨域转发API: <https://github.com/starkoi-nya/biliDanMu>   
详细安装方法请参照上方链接内说明，此处不再赘述   

### 2. 下载本项目的releases    
.exe可执行文件是已经打包好的electron安装包，可安装后直接使用    
前端文件在`/dist`目录下，可自行部署，也可直接打开`/dist/index.html`使用    

### 3. OBS浏览器源及浏览器注意事项    
#### 注意事项   
由于浏览器安全设置，每次载入浏览器后，需与UI上的按钮等元素发生一次交互（最简单就是点击一下歌曲封面，当然戳任意按钮也可以）才可播放音乐。   
本播放器整体通过窗口宽度自动调整高度，请将窗口高度至少设为宽度数值的35%，避免窗口溢出。(实际宽高比1:0.3316931982633864)    
推荐窗口宽高（300:105,400:140）   
不建议通过obs二次调整宽高，以免频谱出现摩尔纹    
#### 地址栏参数    
可以通过设置地址栏参数来导入歌单和绑定直播间    
URL参数:SongListID<网易云音乐歌单ID>，RoomID<B站直播间ID> ,PlayMode<数值>（0=列表循环，1=单曲循环，2随机播放）  
例子:`http://127.0.0.1?SongListID=959808737&RoomID=935815&PlayMode=2`    

### 4. election桌面端注意事项及快捷键    
#### 注意事项:   
桌面端可以通过拖动专辑封面上半部分以及灰色圆角部分移动浮窗，右键该部分可以弹出菜单以关闭程序（当然你也可以暴力alt+F4）    
菜单及歌单将会在无操作后5秒自动内收回   
#### 桌面端快捷键:   
Ctrl+↑:音量加    
Ctrl+↓:音量减    
Ctrl+←:上一曲    
Ctrl+→:下一曲    
上一曲（Fn快捷键）:上一曲    
下一曲（Fn快捷键）:下一曲    
暂停/播放（Fn快捷键）:暂停/播放    

### 5. 弹幕控制指令   
#### 注意事项   
弹幕获取频率为10秒1次，频繁发送弹幕指令会让通知弹出框鬼畜    
指令前需要`#`号，且需要参数的指令需要在指令和参数中间添加半角空格，例如：`#点歌 紅い瞳に映るセカイ`   
房主需要给自己房管身份才能使用管理员指令(因为在API返回的json里房主不属于admin组)    
#### 弹幕指令:   
##### 管理员指令:
`#歌单 <网易云音乐歌单ID>`   
切换至指定歌单，不清除点歌列表    
     
`#播放 <网易云音乐歌曲ID或歌名>`    
打断当前播放的歌曲，强行播放指定歌曲，强制播放的歌曲不计入历史记录，即不受上一曲功能记录。若打断了点歌列表中的歌曲，被打断的歌曲将暂时保留在点歌列表中，待强制播放的歌曲播放完毕，将会被删除。但被删除前可通过点击点歌列表播放被打断的歌曲。或者使用上一曲功能播放该歌曲。

`#ID播放 <网易云音乐歌曲ID>`    
同上，有些歌曲通过ID搜索不到，可能是网易云音乐搜索引擎的问题，此时可以使用该指令来播放。    
    
`#点歌 <网易云音乐歌曲ID或歌名>`    
将歌曲加入点歌列表，管理员不受歌曲上限和点歌频率限制，但需要开启弹幕点歌开关，该功能才有效   
    
`#切歌`   
切换下一首歌，优先播放点歌列表，歌单模式则根据播放模式切换下一首，管理员不受歌曲上限和点歌频率限制。      
    
`#歌词`   
切换歌词显示，请不要在10秒内多次使用该指令，否则歌词将鬼畜   
    
`#提示 <信息>`    
在播放器上显示一个消息弹窗，用于测试弹幕读取功能，例如`#提示 弹幕测试`   
##### 观众指令:
注意，若弹幕点歌功能开启时，无播放列表且点歌列表为空，此时若有观众点歌，将会自动播放观众所点的歌曲!  
    
`#点歌 <网易云音乐歌曲ID或歌名>`
    
将歌曲加入点歌列表，点歌列表长度为5，点歌冷却时间5分钟。观众无论点歌成功与否，5分钟内都只能点歌一次。需要开启弹幕点歌开关，该功能才有效

`#切歌`   
切换下一首歌，优先播放点歌列表，歌单模式则根据播放模式切换下一首，管理员不受歌曲上限和点歌频率限制。   

## 自定义服务器注意事项
不建议将API公开，高频的弹幕获取可能导致服务器IP被B站和网易ban     
    
若你想使用远程的网易云和弹幕API，不仅需要在服务器上部署相应的API，也需要修改`/dist/js/setting.js`文件中相应的部分来让客户端访问正确的服务器地址:    
```javascript
//API地址   
var ColudMusicAPI = "http://127.0.0.1:3000";    
var BiliDanMuAPI = "http://127.0.0.1:8514";   
```   
若要生成使用自己接口的election桌面应用，需要安装election和electron-builder来对项目重新打包。        

## Fork后修改
- 改为自定义服务器
- 修改部分GET请求
- 修改弹幕处理逻辑，观众也可以切歌

## 开源协议
MIT
