# 说明

**本人非原作者，已经自行修改了Github Actions的一些逻辑，包括触发时机，以及rclone v1.65.x版本产生的onedrive无法执行ls的bug（暂时使用已经应用修复补丁的beta版）**

**如果需要使用此修改版的项目，你需要注意以下两点**

- **仅fork `source` 分支，这是没有产生垃圾提交的干净分支**
- **我将触发时机为了仅在`main`分支更新时触发，并且只会定时更新此分支。所以你需要在按照下方原作者的配置教程配置完成后，基于`source`分支手动创建`main`分支并推送到你自己的仓库，命令参考如下**

```bash
# 先fork，然后克隆你的仓库
$ git clone <url>(替换为你自己的仓库地址) && cd e5-renew
# 确保你目前正在source分支
$ git checkout source 
# 基于source分支创建main分支
$ git checkout -b main
# 推送main分支
$ git push origin main
```



# E5-Rclone-Actions-Repo（仓库版）

孪生项目：[E5-Rclone-Actions-Gist（Gist版）](https://github.com/ChirmyRam/E5-Rclone-Actions-Gist)，配置繁琐。

> 基本原理：在Actions中每天自动使用Rclone调用OneDrive使Office E5订阅保持活跃，玄学续期，不保证百分百成功。

**由于我自己同时用了多种续订工具，所以并不确定能否只靠本项目续订！**

基于原项目[E5-Rclone-Actions](https://github.com/peng4740/E5-Rclone-Actions)进一步改良，原项目在secret填入rclone配置文件后无法更新配置文件，尽管rclone获取的token有效期挺长的，但重新配置一次还是得花一点时间。

改良思路：
**预先把rclone配置文件压缩加密后放进仓库，用rclone读取配置文件，过一遍自动上传、删除文件（夹），配置文件就会自动刷新，最后将刷新后的配置文件再次打包加密上传到仓库覆盖，实现自动更新配置文件无限循环。**

其他修改：
- 删除执行前的随机延迟函数。
- 修改rclone读取参数缩短执行时间。
- 加入每月自定提交文件的workflow，防止系统休眠。

# 使用步骤
## 1 Fork仓库
点击仓库右上角分叉fork，当然自己新建仓库也行。
## 2 配置rclone
rclone添加OneDrive的教程不再赘述，需注意的是配置名应为**e5**，配置时或配置完成后注意设定配置名，若实在需要改配置名，应同时更改仓库`/.github/workflows/`路径下**E5-Rclone-Gist-Actions.yml**文件中的**e5**相关参数。将名为**rclone.conf**的配置**单文件**加密压缩（即解压后只有这个文件没有目录）为**zip**格式，并将压缩包重命名为**rclone.zip**上传至仓库根目录。
## 3 配置secret

点击仓库顶栏**settings**→**Secrets→New repository secret**：

- Name：**PASSWD**  Value：**压缩包密码**


## 4 启动actions
点击仓库顶栏**Actions**→**I understand my workflows, go ahead and enable them**，手动enable两个workflow。

- **Avoid workflow being suspended**：每月自动提交文件到仓库，防止因长期未提交文件造成actions被系统停止。

- **E5-Rclone-Actions-Repo**：每天自动使用rclone上传文件到OneDrive。

顺手star一下自己的仓库手动触发actions。
