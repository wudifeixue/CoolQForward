# 酷Q转发
+ 一个能把酷Q事件进行二次分发和接收外部数据来对酷Q进行操作的插件

------------------------------------
## 设置说明
#### TCP设置
+ enable 设置为0表示不启用，1表示启用
+ port TCP端口
#### Security设置
+ enable 设置为0表示不启用，1表示启用
+ ID 设置允许连接到插件的ID，不在此列的拒绝连接，多个用","隔开
+ permissions 设置允许监听的事件，若连接请求监听不在此列的事件则拒绝连接，多个用","隔开

------------------------------------
## 使用说明
1. 使用TCP协议连接到插件
2. 发送一个标识数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | Packet Length  | Int       |       |
> | ID             | String    |       |
> | Permissions    | String    | 监听事件标识，用','隔开，例如:'21,2,4'表示监听私聊事件、群消息事件、讨论组事件 |
3. 响应事件/对酷Q请求操作

------------------------------------
## 数据包规范
+ 以下所有数据包都应遵守的规范
> | Name          | Type      | Notes |
> |---------------|-----------|-------|
> | Packet Length | Int       |       |
> | Packet        | ByteArray |       |

------------------------------------

+ String类型规范，以下整体封装为String
> | Name          | Type      | Notes |
> |---------------|-----------|-------|
> | String Length | Int       |       |
> | String        | RawString |       |

------------------------------------

+ 应答包规范，以下整体封装在Packet中
> | Name          | Type      | Notes |
> |---------------|-----------|-------|
> | Code          | Int       | 应答码 |
> | Msg           | String    | 应答信息 |
> | Data          | ByteArray | 附加数据 |

### 事件数据包解包规范
+ 以下整体封装在Data中

#### PrivateMsg事件，应答码21，私聊消息
> | Name    | Type   | Notes |
> |---------|--------|-------|
> | subType | Int    | 子类型，11/来自好友 1/来自在线状态 2/来自群 3/来自讨论组 |
> | msgId   | Int    | 消息ID |
> | fromQQ  | Long   | 来源QQ |
> | msg     | String | 消息内容 |
> | font    | Int    | 字体 |
> |         | String | 字体名称 |
> |         | Int    | 字体字号 |
> |         | Int    | 字体颜色 |
> |         | Int    | 字体样式 1:粗体,2:斜体,4:下划线 |
> |         | Int    | 字体气泡 |

#### GroupMsg事件，应答码2，群消息
> | Name          | Type      | Notes |
> |---------------|-----------|-------|
> | subType       | Int       | 子类型，目前固定为1 |
> | msgId         | Int       | 消息ID |
> | fromGroup     | Long      | 来源群号 |
> | fromQQ        | Long      | 来源QQ号 |
> | fromAnonymous | String    | 来源匿名者 |
> |               | Long      | 匿名AID |
> |               | String    | 匿名代号 |
> |               | Int       | 匿名Token长度 |
> |               | ByteArray | 匿名Token |
> | msg           | String    | 消息内容 |
> | font          | Int       | 字体 |
> |               | String    | 字体名称 |
> |               | Int       | 字体字号 |
> |               | Int       | 字体颜色 |
> |               | Int       | 字体样式 1:粗体,2:斜体,4:下划线 |
> |               | Int       | 字体气泡 |

#### DiscussMsg事件，应答码4，讨论组消息
> | Name          | Type      | Notes |
> |---------------|-----------|-------|
> | subType       | Int       | 子类型，目前固定为1 |
> | msgId         | Int       | 消息ID |
> | fromDiscuss   | Long      | 来源讨论组 |
> | fromQQ        | Long      | 来源QQ号 |
> | msg           | String    | 消息内容 |
> | font          | Int       | 字体 |
> |               | String    | 字体名称 |
> |               | Int       | 字体字号 |
> |               | Int       | 字体颜色 |
> |               | Int       | 字体样式 1:粗体,2:斜体,4:下划线 |
> |               | Int       | 字体气泡 |

#### GroupUpload事件，应答码11，群文件上传事件
> | Name          | Type      | Notes |
> |---------------|-----------|-------|
> | subType       | Int       | 子类型，目前固定为1 |
> | sendTime      | Int       | 发送时间(时间戳) |
> | fromGroup     | Long      | 来源群号 |
> | fromQQ        | Long      | 来源QQ号 |
> | file          | String    | 上传文件信息 |
> |               | Long      | 文件大小 |
> |               | Int       | 文件busid |
> |               | String    | 文件名 |
> |               | String    | 文件ID |

#### System_GroupAdmin事件，应答码101，群事件-管理员变动
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | subType        | Int       | 子类型，1/被取消管理员 2/被设置管理员 |
> | sendTime       | Int       | 发送时间(时间戳) |
> | fromGroup      | Long      | 来源群号 |
> | beingOperateQQ | Long      | 被操作QQ |

#### System_GroupMemberDecrease事件，应答码102，群事件-群成员减少
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | subType        | Int       | 子类型，1/群员离开 2/群员被踢 |
> | sendTime       | Int       | 发送时间(时间戳) |
> | fromGroup      | Long      | 来源群号 |
> | fromQQ         | Long      | 操作者QQ(子类型为2时为管理员QQ) |
> | beingOperateQQ | Long      | 被操作QQ |

#### System_GroupMemberIncrease事件，应答码103，群事件-群成员增加
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | subType        | Int       | 子类型，1/管理员已同意 2/管理员邀请 |
> | sendTime       | Int       | 发送时间(时间戳) |
> | fromGroup      | Long      | 来源群号 |
> | fromQQ         | Long      | 操作者QQ(管理员QQ) |
> | beingOperateQQ | Long      | 被操作QQ(加群的QQ) |

#### Friend_Add事件，应答码201，好友事件-好友已添加
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | subType        | Int       | 子类型，目前固定为1 |
> | sendTime       | Int       | 发送时间(时间戳) |
> | fromQQ         | Long      | 来源QQ |

#### Request_AddFriend事件，应答码301，请求-好友添加
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | subType        | Int       | 子类型，目前固定为1 |
> | sendTime       | Int       | 发送时间(时间戳) |
> | fromQQ         | Long      | 来源QQ |
> | msg            | String    | 附言 |
> | responseFlag   | String    | 反馈标识(处理请求用) |

#### Request_AddGroup事件，应答码302，请求-群添加
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | subType        | Int       | 子类型，1/他人申请入群，2/自己受邀入群 |
> | sendTime       | Int       | 发送时间(时间戳) |
> | fromGroup      | Long      | 来源群号 |
> | fromQQ         | Long      | 来源QQ |
> | msg            | String    | 附言 |
> | responseFlag   | String    | 反馈标识(处理请求用) |

------------------------------------

### 操作数据包封包规范
+ 以下整体封装在Packet中
+ 操作请求数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | operate        | Int       | 请求的操作 |
> | arguments      | ByteArray | 请求时的参数 |
+ 操作应答数据包
> | Name          | Type      | Notes |
> |---------------|-----------|-------|
> | Code          | Int       | 应答码 |
> | Msg           | String    | 应答信息 |
> | Data          | ByteArray | 附加数据 |

#### 操作码1，获取登录QQ
+ 无请求参数
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | QQ             | Long      |       |

#### 操作码2，获取登录昵称
+ 无请求参数
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | 昵称           | String    |       |

#### 操作码20，获取Cookies
+ 无请求参数
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | Cookies        | String    |       |

#### 操作码30，接收语音
+ 请求参数包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | 文件名         | String    | 收到消息中的语音文件名 |
> | 指定格式       | String    | 应用所需的语音文件格式，目前支持mp3,amr,wma,m4a,spx,ogg,wav,flac |
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | ReturnValue    | String    | 返回保存在\data\record\目录下的文件名 |

#### 操作码101，发送群消息
+ 请求参数包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | 群号           | Long      |  |
> | msg            | String    | 消息 |
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | msgId          | Int      | 返回消息ID |

#### 操作码103，发送讨论组消息
+ 请求参数包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | 讨论组号        | Long      |  |
> | msg            | String    | 消息 |
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | msgId          | Int      | 返回消息ID |

#### 操作码106，发送私聊消息
+ 请求参数包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | QQ             | Long      |  |
> | msg            | String    | 消息 |
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | msgId          | Int      | 返回消息ID |

#### 操作码110，发送赞
+ 请求参数包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | QQ             | Long      |  |
> | 次数            | Int     | 赞的次数，最多10次 |
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | ReturnValue    | Int      |   |

#### 操作码120，置群员移除
+ 请求参数包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | 群号             | Long      |  |
> | QQ             | Long      |  |
> | 拒绝再加群      | Boolean     |  |
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | ReturnValue    | Int      |   |

#### 操作码121，置群员禁言
+ 请求参数包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | 群号             | Long      |  |
> | QQ             | Long      |  |
> | 禁言时间         | Long      | 单位为秒。如果要解禁，此项应为0 |
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | ReturnValue    | Int      |   |

#### 操作码122，置群管理员
+ 请求参数包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | 群号             | Long      |  |
> | QQ             | Long      |  |
> | 成为管理员      | Boolean     | true:设置管理员,false:取消管理员 |
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | ReturnValue    | Int      |   |

#### 操作码123，置全群禁言
+ 请求参数包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | 群号             | Long      |  |
> | 开启禁言      | Boolean     | true:开启,false:关闭 |
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | ReturnValue    | Int      |   |

#### 操作码124，置匿名群员禁言
+ 请求参数包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | 群号            | Long      |  |
> | fromAnonymous  | String      | 群消息事件收到的fromAnonymous参数 |
> | 禁言时间        | Long     | 单位为秒，不支持解禁 |
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | ReturnValue    | Int      |   |

#### 操作码125，置群匿名设置
+ 请求参数包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | 群号            | Long      |  |
> | 开启匿名        | Boolean    |  |
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | ReturnValue    | Int      |   |

#### 操作码126，置群成员名片
+ 请求参数包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | 群号            | Long      |  |
> | QQ             | Long      |  |
> | 名片            | String    |  |
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | ReturnValue    | Int      |   |

#### 操作码127，置群退出
+ 请求参数包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | 群号            | Long      |  |
> | 是否解散        | Boolean    | true:解散本群(群主),false:退出本群(管理员、群成员) |
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | ReturnValue    | Int      |  |

#### 操作码128，置群成员专属头衔
+ 请求参数包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | 群号            | Long      |  |
> | QQ             | Long      |  |
> | 头衔            | String    | 如果要删除，这里留空 |
> | 过期时间        | Long    | 专属头衔过期时间，单位为秒。如果永久有效，此处为-1 |
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | ReturnValue    | Int      |   |

#### 操作码130，取群成员信息
+ 请求参数包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | 群号            | Long      |  |
> | QQ             | Long      |  |
> | 不使用缓存      | Boolean    |  |
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | ReturnValue    | Int      |  |
> | 群号            | Long      |  |
> | QQ             | Long      |  |
> | 昵称            | String    |  |
> | 名片            | String    |  |
> | 性别            | Int      | 0/男性 1/女性 |
> | 年龄            | Int      |  |
> | 地区            | String    |  |
> | 加群时间        | Int      |   |
> | 最后发言        | Int      |   |
> | 管理权限        | Int      | 1/成员 2/管理员 3/群主 |
> | 专属头衔        | String    |  |
> | 等级名称        | String    |  |
> | 不良记录成员    | Boolean    |  |
> | 允许修改名片    | Boolean    |  |
> | 专属头衔过期时间 | Int      | -1代表不过期 |

#### 操作码131，取陌生人信息
+ 请求参数包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | QQ             | Long      |  |
> | 不使用缓存      | Boolean   |  |
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | ReturnValue    | Int      |  |
> | QQ             | Long      |  |
> | 性别            | Int      | 0/男性 1/女性 255/未知|
> | 年龄            | Int      |  |
> | 昵称            | String    |  |

#### 操作码140，置讨论组退出
+ 请求参数包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | 讨论组号        | Long      |  |
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | ReturnValue    | Int      |  |

#### 操作码150，置好友添加请求
+ 请求参数包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | responseFlag   | String      | 请求事件收到的responseFlag参数 |
> | 反馈类型        | Int      | 1/通过 2/拒绝 |
> | 备注           | String      | 添加后的备注 |
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | ReturnValue    | Int      |  |

#### 操作码151，置群添加请求
+ 请求参数包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | responseFlag   | String      | 请求事件收到的responseFlag参数 |
> | 请求类型        | Int      | 1/群添加 2/群邀请 |
> | 反馈类型        | Int      | 1/通过 2/拒绝 |
> | 理由           | String      | 操作理由，仅在 请求类型=1且反馈类型=2 时有效 |
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | ReturnValue    | Int      |  |

#### 操作码160，取群成员列表
+ 请求参数包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | 群号            | Long      |  |
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | ReturnValue    | Int      |  |
> | 群成员数        | Int      |  |
> | 同'操作码130'应答 | 同'操作码130'应答 | 同'操作码130'应答且重复群成员数次 |

#### 操作码161，取群列表
+ 无请求参数
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | ReturnValue    | Int      |  |
> | 群数           | Int      |  |
> | 群号           | Long |  |
> | 名称           | Long |  |
> | 同上，重复群号与名称 |  |  |

#### 操作码180，撤回消息
+ 请求参数包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | msgId          | Long      | 消息ID |
+ 应答数据包
> | Name           | Type      | Notes |
> |----------------|-----------|-------|
> | ReturnValue    | Int      |  |

------------------------------------

### CQ码
> 所有的CQ码实际上是由字符串拼接而来，以下为拼接方式
#### @
> @指定QQ  
> `[CQ:at,qq=指定QQ]`  
> @全体成员  
> `[CQ:at,qq=all]`
#### emoji
> `[CQ:emoji,id=表情ID]`
#### QQ表情
> `[CQ:face,id=表情ID]`
#### 窗口抖动
> `[CQ:shake]`
#### 链接分享
> 除分享链接外均可空  
> `[CQ:share,url=分享链接,title=标题,content=内容,image=图片链接]`  
> `[CQ:share,url=分享链接,content=内容,image=图片链接]`  
> `[CQ:share,url=分享链接,title=标题,image=图片链接]`  
> `[CQ:share,url=分享链接,title=标题,content=内容]`
#### 名片分享
> 除分享链接外均可空  
> `[CQ:contact,type=类型,title=标题,id=分享账号]`  
> 类型目前支持: qq/好友分享 group/群分享  
> 类型为qq，则分享账号为QQ；类型为group，则为群号
#### 匿名
> `[CQ:anonymous,ignore=true]` 匿名失败时会转为普通消息发送  
> `[CQ:anonymous]` 匿名失败时不发送
#### 图片
> `[CQ:image,file=图片路径]` 将图片放在data\image下，此处填写相对路径
#### 位置分享
> `[CQ:location,lat=纬度,lon=经度,zoom=放大倍数,title=地点名称,content=地址]`
#### 音乐
> `[CQ:music,id=歌曲ID,type=音乐网站类型]`  
> 音乐网站类型目前支持 qq/QQ音乐 163/网易云音乐 xiami/虾米音乐  
> `[CQ:music,id=歌曲ID,type=qq,style=1]` 新版样式，目前仅QQ音乐支持
#### 音乐自定义分享
> 除分享链接、音频链接外均可空  
> `[CQ:music,type=custom,url=分享链接,audio=音频链接,title=标题,content=内容,image=封面图片链接]`  
> `[CQ:music,type=custom,url=分享链接,audio=音频链接,content=内容,image=封面图片链接]`  
> `[CQ:music,type=custom,url=分享链接,audio=音频链接,title=标题,image=封面图片链接]`  
> `[CQ:music,type=custom,url=分享链接,audio=音频链接,title=标题,content=内容]`
#### 语音
> `[CQ:record,file=语音路径]` 将语音放在data\record下，此处填写相对路径
