# 配置chatgpt QQ机器人 踩坑记录

原项目地址https://github.com/lss233/chatgpt-mirai-qq-bot

1. 先用一键脚本启动docker-compose

![img](.\Figure1.png)

2. 启动过程中会让你输bot的QQ号密码，管理员QQ号等，还有api-key，输入即可，运行完之后会生成一个`./chatgpt-qq/`文件夹

3. 一键运行完成之后大概率还有问题，后续配置如下

4. 主要结构是两个容器gocqhttp+chatgpt-qq，其中gocqhttp是QQ代理，chatgpt-qq是会话逻辑的机器人

5. 配置gocqhttp，文档：[gocqhttp配置](https://docs.go-cqhttp.org/guide/config.html)，配置信息基本不用改，主要是修改设备信息`./gocqhttp/device.json`文件，将其中的`protocol`字段修改成能使用的类型，有些类型无法使用，会被QQ拒绝。

6. 因为第一次登陆可能会需要验证码，用`sudo docker-compose run --rm gocqhttp`以交互模式单独进入gocqhttp容器，中间会有拖动验证码，需要打开链接地址，F12抓包，拖动完毕后复制union开头的包里的ticket字段回来粘贴。测试gocqhttp能正常接收消息之后退出容器。（只有第一次需要这么麻烦）

7. 配置chatgpt-qq

   文档：[chatgpt-mirai-qq-bot配置文件](https://chatgpt-qq.lss233.com/pei-zhi-wen-jian-jiao-cheng/wan-zheng-pei-zhi-wen-jian-yang-li)

   修改完配置文件后

   在`./chatgpt-qq`目录下`sudo docker-compose up` 看看有没有报错，测试一下，没问题再`sudo docker-compose up -d`后台运行，可以用`sudo docker-compose logs`看日志

配置文件：

`./chatgpt-qq/config.cfg`

```pyhton
[onebot]
qq=10*****219
manager_qq=11*****213
# 此处保持默认设置，无需修改
reverse_ws_port = 8554
[openai]
[[openai.accounts]]
# openAI api-key
api_key = ""  
# 代理站点
api_endpoint = "https://proxy.com/v1/"

[text_to_speech]

# 引擎名填写这一个
# 设置后可以使用 Edge 的语音转文字功能
engine = "edge"
# 音色名
default = "zh-CN-XiaoyiNeural"

# [trigger]

# 全局聊天前缀，在群聊和私聊中，符合下面的前缀【才】会响应，可以自己增减
# prefix = [".",]

# 私聊聊天前缀，在私聊中，符合下面的前缀【也】会响应，可以自己增减
# prefix_friend = [ "ask",]

# 群聊聊天前缀，在群聊中，符合下面的前缀【也】会响应，可以自己增减
# prefix_group = [ "!",]
# require_mention = "none"

# 满足以下正则表达式则忽略此条消息
# ignore_regex = ["^/draw.+$"]
```
