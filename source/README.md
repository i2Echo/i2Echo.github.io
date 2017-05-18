# 个人博客（hexo框架）

## 访问地址
[Kujohn's Blog](https://ikuyman.pub/)

## 自动化部署方案
本博客为Git+TravisCI+VPS自动化部署

## 自动化构建流程
* 本地新建一个hexo项目并git初始化
* github建立远程仓库，创建一个hexo分支用来存放源文件，master分支用来存放生成后的静态文件
* github与Travis关联，Travis设置webhook当github的hexo分支收到推送时，自动通过.travis.yml运行配置好hexo环境
* Travis运行结束之后将生成的public目录推送至github的master分支（我这里算是用来备份的，当然也可以作为githubpages临时访问当vps出现问题时）
* vps安装git和nginx并配置好nginx
* vps上运行一个crontab任务，用来定时拉取github上的静态文件并复制到nginx运行的站点目录下
* 然后访问博客域名，就能查看博客了

## features
* 全自动化流程，极大地简化了操作步骤
* 可以随时随地写博客了，再也不用局限于一个设备或者需要重新搭建环境的麻烦
* 使用vps访问速度大大提升（毕竟国内github速度并不理想）
* 全站https，提高安全性
* 采用多重备份机制，减少数据丢失的风险

 