# 1、nodejs

- **[Node 24（推荐）](https://docs.openclaw.ai/install/node)**（出于兼容性考虑，仍支持 Node 22 LTS，目前为 `22.16+`；如果缺失，[安装脚本](https://docs.openclaw.ai/zh-CN/install?f_link_type=f_linkinlinenote&flow_extra=eyJpbmxpbmVfZGlzcGxheV9wb3NpdGlvbiI6MCwiZG9jX3Bvc2l0aW9uIjowLCJkb2NfaWQiOiI0ZDA3NGMxOGYxODY0NDkwLTdkYzgwNzA1MWIzOTEyMDAifQ%3D%3D#install-methods) 会安装 Node 24）
- 下载地址：https://nodejs.org/zh-cn/download（选择安装位置，下一步就完了）
- 验证cmd
  - node -v
  - 显示 v24.x.x 就对了



# 2、git

- 下载地址：https://git-scm.com/install/windows（下一步就行了）
- 验证cmd
  - git -v
  - 显示版本号就行了



# 3、openclaw

- 打开管理员 PowerShell
  - Win + X
  - 弹出的菜单里点击 **"终端管理员（管理员）"**
- 修改执行策略
  - Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
  - 提示时输入 `Y` 再回车
- 配置镜像源
  - npm config set registry https://registry.npmmirror.com
  - 验证 npm config get registry
    - 看到 `https://registry.npmmirror.com` 就对了
- 安装：npm i -g openclaw，等待两三分钟左右
- 验证：openclaw --version，看到版本号就成功啦！

![安装1](图片/安装1.png)





# 4、配置模型

- 输入命令：openclaw onboard --flow quickstart

- 屏幕会显示很多选项，按下面操作：

  - 安全提示 → 输入 `Y` 回车

  ![配置1](图片/配置1.png)

  - 选择模型 → 方向键 ↓ 选中 `MiniMax M2.5`，回车

  ![配置2](图片/配置2.png)

  ![配置3](图片/配置3.png)

  - 认证方式 → 选择 `Paste API key now（现在粘贴）`，回车

  ![配置4](图片/配置4.png)

  - 输入 API Key → 粘贴你的 MiniMax Key，回车
  - 输入 Group ID → 粘贴你的 MiniMax Group ID，回车
  - 选择模型 → 直接回车（用默认的）

  ![配置5](图片/配置5.png)

  - 问是否配置飞书 → 选择最后一个暂时跳过

  ![配置6](图片/配置6.png)

  - 最后一个跳过

  ![配置7](图片/配置7.png)

  - 问是否配置技能 → 输入 `N` 回车

  ![配置8](图片/配置8.png)

  - 问是否配置 Hooks → 方向键移动到 `Skip for now`，空格选中，回车

  ![配置8](图片/配置8.png)

- **MiniMax API Key 怎么获取？**

  1. 浏览器打开 https://platform.minimaxi.com/
  2. 手机号注册 / 登录
  3. 点击左侧 "控制台" → "应用管理" → "创建应用"
  4. 点击 "API 密钥" → 复制 Key 和 Group ID
  5. 粘贴到上面第 6 步



# 5、启动网关

- cmd启动：openclaw gateway run
- 看到Gateway running on http://127.0.0.1:18789就成功了

![网关启动1](图片/网关启动1.png)

- 浏览器打开 `http://127.0.0.1:18789`

![网关配置2](图片/网关配置2.png)



# 6、创建飞书应用

- 浏览器打开 https://open.feishu.cn/app
- 点击 "创建应用" → "企业自建应用"

- 填写名称（如 "Nini"），点击创建

![飞书1](图片/飞书1.png)

- 添加机器人应用能力

![飞书2](图片/飞书2.png)

- 左侧点击 "权限管理" ，开通权限，选择：im:，所有的

![飞书3](图片/飞书3.png)

- 左侧点击 "版本管理与发布" → "创建版本" → 填写 `1.0.0` → 发布

![飞书4](图片/飞书4.png)



# 7、安装飞书插件

- PowerShell运行：openclaw plugins install @openclaw/feishu
- 运行添加通讯渠道：openclaw channels add

![飞书5](图片/飞书5.png)

- 输入app screct

![飞书6](图片/飞书6.png)

- app secret就是飞书页面上：凭证与基础信息里面的

![飞书7](图片/飞书7.png)

- 选择长连接，即时通信

![飞书8](图片/飞书8.png)

- 国内飞书

![飞书9](图片/飞书9.png)

- 群聊也可以使用飞书

![飞书10](图片/飞书10.png)

- 完成

![飞书11](图片/飞书11.png)

- 设置名字

![飞书12](图片/飞书12.png)

- openclaw gateway run重启网关
- 回到飞书页面上，事件配置选择长连接

![飞书13](图片/飞书13.png)

- 添加事件，接收消息

![飞书14](图片/飞书14.png)



# 8、关闭开机自启

- win+r: **taskschd.msc**
- 找到openclaw禁用

![禁用1](图片/禁用1.png)



# 9、卸载

打开终端（Terminal）

Windows用户：按键盘上的Win+R键，在弹出的框中输入cmd，然后按回车；或者直接在开始菜单里搜索“命令提示符“或“PowerShell”。

Mac用户：按键盘上的Command+Space（空格键），在搜索框输入Terminal或终端，然后按回车。

输入下面这行命令并回车

代码块：openclaw uninstall --all --yes，其中：

uninstall：告诉程序我要卸载。

--all：彻底删除，包括网关服务、本地[数据库](https://cloud.tencent.com/product/tencentdb-catalog?from=20067&from_column=20067)、配置文件等所有数据。

--yes：全程自动确认，不需要你手动按 Y 确认。

删除[命令行工具](https://cloud.tencent.com/product/cli?from=20067&from_column=20067)

以上指令跑完后，电脑里就只剩下OpenClaw的外壳（CLI工具）了。如果想把它彻底清除，再执行一行——代码块：npm uninstall -g openclaw。至此，电脑就彻底干净了。

注意事项

执行完上述操作后，建议重启一次电脑，确保所有后台进程彻底关闭。如果曾在OpenClaw里绑定过API Key（如OpenAI、Claude的密钥），建议去对应的官网废弃旧密钥，生成新密钥，以防万一。