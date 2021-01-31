## 请仔细阅读 [WIKI](https://github.com/EvineDeng/jd-base/wiki) 和各文件注释，95%的问题都能找到答案

## 如有二次使用，请注明来源

本脚本是以下两个仓库的shell套壳工具：

[LXK9301/jd_scripts](https://github.com/LXK9301/jd_scripts)：主要是长期任务。

[shylocks/Loon](https://github.com/shylocks/Loon)：主要是短期任务、一次性任务，正因为是短期的和一次性的，所以经常会有报错，报错就报错了，不要催我也不要去催[shylocks](https://github.com/shylocks)大佬。

## 适用于以下系统

- ArmBian/Debian/Ubuntu/OpenMediaVault/CentOS/Fedora/RHEL等Linux系统

- OpenWRT

- Android

- MacOS

- Docker

## 说明

1. 宠汪汪赛跑助力先让用户提供的各个账号之间相互助力，助力完成你提供的所有账号以后，再给我和lxk0301大佬助力，每个账号助力后可得30g狗粮。

2. 将部分临时活动修改为了我的邀请码，已取得lxk0301大佬的同意。

## 如有帮助你薅到羊毛，请不吝赏作者一杯茶水费

![thanks](https://github.com/EvineDeng/jd-base/wiki/Picture/thanks.png)

## 更新日志

> 只记录大的更新，小修小改不记录。

2021-01-23，控制面板增加日志查看功能，Docker重启容器后可以使用`docker restart jd`，非Docker如果是pm2方式的请重启pm2进程`pm2 resatrt server.js`。

2020-01-21，增加shylocks/Loon脚本。

2021-01-15，如果本机上安装了pm2，则挂机程序以pm2启动，否则以nohup启动。

# 原作者Linux部署教程

## 脚本可以干什么
- 自动更新lxk0301的京东薅羊毛脚本，自动更新我的shell脚本（git_pull）。

- 自动按设定天数删除旧日志（rm_log）。

- 自动添加新的定时任务（git_pull）。

- 自动删除失效的定时任务（git_pull）。

- 一键导出所有互助码（export_sharecodes）

- 自动按crontab.list设定的时间去跑各个薅羊毛脚本。

## 准备环境
debian/ubuntu/armbian/OpenMediaVault，以及其他debian系：
```sh
apt update && apt install -y git wget curl nodejs npm perl moreutils
```

CentOS/RedHat/Fedora等红帽系
```sh
yum update && yum install git wget curl perl moreutils
```

如果安装源中有nodejs和npm，也安装好，否则请访问 Node.js官网 或者 nodesource@github 查看如何安装。

OpenWrt， 需要添加官方软件源， 如果某个软件包已集成在固件中，则可跳过安装。如果你会编译，可以把下面这些包直接编译在固件中。
```sh
opkg update && opkg install git git-http wget curl node node-npm perl moreutils
```
声明：OpenWrt环境千差万别，不保证一定可用，需要根据自己的环境来配置，如果OpenWrt安装了Docker，也可以使用Docker的方法。Rom小于256M就不要安装了，你空间不够。

注：不同系统的包名不一定一样，需保证 node 大版本 >=10，安装好后使用node -v或nodejs -v命令可查看版本。

## 流程
以下全文均以此路径/home/myid/jd进行举例，请自行修改为你自己的路径！

以下全文均以此路径/home/myid/jd进行举例，请自行修改为你自己的路径！

以下全文均以此路径/home/myid/jd进行举例，请自行修改为你自己的路径！

注意：需要多账号并发的，请建立多个账户，每个账户各自使用一套脚本。看不懂这句话的小白别碰多账号并发。想要方便简单使用多账号并发的，请使用Docker的方式。

### 克隆本仓库

如需以后从Github更新我的和lxk0301大佬的脚本：

cd ~ && git clone -b v3 https://github.com/EvineDeng/jd-base jd （原作者的库被删了，这里请替换成fork库的地址）
如需以后从Gitee更新我的和lxk0301大佬的脚本：

cd ~ && git clone -b v3 https://gitee.com/evine/jd-base jd

复制并编辑自己的配置文件

```sh

cd ~/jd
#创建一个配置文件保存目录
mkdir config

#复制仓库下sample/config.sh.sample到config目录中，并命名为config.sh
cp sample/config.sh.sample config/config.sh

#复制仓库下sample/computer.list.sample到config目录中，并命名为crontab.list
cp sample/computer.list.sample config/crontab.list

#如果本地用户名不是myid，而是linuxuser 可以使用下面命令
cat  sample/computer.list.sample |  sed  's/myid/linuxuser/g' > ./config/crontab.list 
```



然后编辑这两个文件：
可以通过控制面板编辑，详见 控制面板使用教程 ，也可以自行通过SFTP工具连接编辑。

其中config.sh是配置文件，crontab.list是定时任务清单，如何编辑请查看两个文件内的注释，请务必仔细阅读！请务必仔细阅读！请务必仔细阅读！

关于crontab.list，这里说明一下，除了那些本来就会准时运行的脚本外，如果还有一些脚本你不想随机延迟，要么在config.sh中RandomDelay不要赋值(所有任务都将不延迟执行)，要么参考下文 如何手动运行脚本 部分，在crontab.list中不想被随机延迟运行的任务后面，添加上 now，比如：

20 * * * * bash /home/myid/jd/jd.sh jd_dreamFactory now
初始化

在首次编辑好config.sh和crontab.list后，请务必手动运行一次git_pull.sh，不仅是为检查错误，也是为了运行一次npm install用以安装js指定的依赖。

bash git_pull.sh
针对首次运行git_pull.sh，出现类似以下字样才表示npm install运行成功：
```sh
audited 205 packages in 3.784s

11 packages are looking for funding
run `npm fund` for details

found 0 vulnerabilities
```

如果npm install失败，请尝试手动运行，可按如下操作，如果失败，可运行多次：
```sh
cd ~/jd/scripts

# 如果只安装了npm
npm install || npm install --registry=https://registry.npm.taobao.org

# 如果安装了yarn
yarn install
```

### 添加定时任务

请注意：以下命令会完整覆盖你当前用户的crontab清单，请务必按照crontab.list中的注释操作！！！

请注意：以下命令会完整覆盖你当前用户的crontab清单，请务必按照crontab.list中的注释操作！！！

请注意：以下命令会完整覆盖你当前用户的crontab清单，请务必按照crontab.list中的注释操作！！！
```sh
cd ~/jd
crontab config/crontab.list
```
如果以后你还要增加其他定时任务，也请加在这个文件中以后，再运行上述命令。如果不添加在这个文件中，那么脚本会以crontab.list中的清单覆盖掉你通过其他方式添加的定时任务。

部署完成。

### 如何更新配置文件
config.sh和crontab.list两个文件都一样，在任何时候改完保存好就行，其他啥也不用干，改完以后，新的任务就以新配置运行了。其中config.sh改完立即生效，crontab.list会在下一次任何定时薅羊毛任务启动时更新。

如需要在线比对编辑，请参考：控制面板使用教程

### 如何添加除lxk0301大佬以外的脚本
本环境基于node，所以也只能跑js脚本。你可以把你的后缀为.js的脚本放在/home/myid/jd/scripts下。比如你放了个test.js，可以在你的crontab.list中添加如下的定时任务：
```sh
15 10 * * * bash /home/myid/jd/jd.sh test     # 如果不需要准时运行或RandemDelay未设置
15 10 * * * bash /home/myid/jd/jd.sh test now # 如果设置了RandemDelay但又需要它准时运行
```
然后运行一下crontab /home/myid/jd/config/crontab.list更新定时任务即可。

>注意：你额外添加的脚本不能以“jd_”、“jr_”、“jx_”开头，以“jd_”、“jr_”、“jx_”开头的任务如果不在lxk0301大佬仓库中，会被删除。

如果你额外加的脚本要用到环境变量，直接在你的config.sh文件最下方按以下形式添加好变量即可（单引号或双引号均可）：
```sh
export 变量名1="变量值1"
export 变量名2="变量值2"
export 变量名3="变量值3"
```

### 如何手动运行脚本
手动 git pull 更新脚本
```sh
cd ~/jd
bash git_pull.sh
```

手动删除指定时间以前的旧日志
```sh
cd ~/jd
bash rm_log.sh
```

手动导出所有互助码
```sh
cd ~/jd
bash export_sharecodes.sh
```

手动启动挂机程序

cd到脚本目录后输入bash jd.sh hangup即可，然后挂机脚本就会一直运行。如果你希望每天终止旧的挂机进程，然后启动新的挂机进程，请参考sample/termux.list.sample中的挂机定时任务，添加到自己的crontab.list中。目前仅一个jd_crazy_joy_coin.js为挂机脚本。

手动执行薅羊毛脚本，用法如下(其中xxx为lxk0301大佬的脚本名称)，不支持直接以node xxx.js命令运行：
```sh
cd ~/jd
bash jd.sh xxx      # 如果设置了随机延迟并且当时时间不在0-2、30-31、59分内，将随机延迟一定秒数
bash jd.sh xxx now  # 无论是否设置了随机延迟，均立即运行
```


