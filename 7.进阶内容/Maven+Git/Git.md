# Git

## 一、概述

Git 是一个免费的、开源的 **分布式版本控制系统 **，可以快速高效地处理从小型到大型的各种项目。

- 什么是版本控制？	

  答：是一种记录文件内容变化的系统，从而让用户能够查看历史版本，方便版本切换。

- 为什么需要版本控制?    

  答：个人开发过渡到团队协作。

**两种版本控制工具：**

1. 集中式版本控制工具

   SVN、CVS 、VSS

2.  分布式版本控制工具

​		**Git**、Mercurial、Bazaar、Darcs……

**Git工作机制：**

工作区(代码文件目录)  -------git add------->  暂存区(临时存储) 

​									   ----git commit----->  本地库(历史版本v1、v2……)

​									   ---------push--------->  远程库(如：GitHub、Gitee)

**Git** **和代码托管中心**：

局域网（私人，不开源）： GitLab

互联网： GitHub、Gitee 码云

**安装Git**：

官网



## 二、常用命令

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207091707631.png" alt="image-20220709165841315" style="zoom: 67%;"/>

1. 设置用户签名。
2. 初始化本地库 / 查看本地库状态 
3. 添加暂存区
4. 提交本地库
5. 查看历史版本 / 版本穿梭



## 三、Git分支操作

同时并行推进多个功能开发，提高开发效率。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207091736825.png" alt="image-20220709173605763" style="zoom:50%;" />

各个分支在开发过程中，如果某一个分支开发失败，不会对其他分支有任何影响。失败的分支删除重新开始即可。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207091735676.png" alt="image-20220709173519610" style="zoom:80%;" />

1. 查看分支
2. 创建分支
3. 切换分支（如：切换到hot-fix分支进行修复bug）
4. 合并分支 （需要在master分支上进行操作，把需要合并的分支名输入）



## 四、Github操作

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207091828837.png" alt="image-20220709182803746" style="zoom:80%;" />

1. 创建远程仓库别名
2. 推送(push)本地分支到远程仓库。
3. 拉取(pull)远程仓库到本地库。【拉取后会自动提交，更新版本。版本信息在GitHub获取】
4. 克隆远程仓库到本地。（自动初始化）



### 团队协作

管理员，用户1，用户2。

管理员创建了一个GitHub项目。

管理员：用push更新本地库项目到GitHub。用pull拉取项目更新到本地库。可以邀请成员加入该项目。

用户：用clone该项目到本地。用push更新项目（前提需要是该项目的成员/创始人）。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207092016468.png" alt="image-20220709201653246" style="zoom:67%;" />

### 跨团队协助

其他团队需要协助该Github项目时：

先fork一份该Github项目，这样在其他管理员GitHub网站也会有一个该项目。

修改开发完成后，通过pull request进行更新。该项目管理员可通过查看所有的pull request请求，同意合并。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207092017012.png" alt="image-20220709201714905" style="zoom: 67%;" />



## 五、idea集成

### idea集成Git

准备工作：

1. 配置Git忽略文件
2. idea中配置Git.exe程序，集成到idea开发工具中。
3. 初始化本地库（VCS）

操作：

1. 添加暂存区
2. 提交本地库
3. 切换版本
4. 创建分支 / 切换分支
5. 合并分支 / 冲突合并 

### idea集成Github

1. 设置**Github账号**（还可以通过token）
2. **分享工程**到Github，无需到Github手动创建仓库。
3. **push**项目到Github。
4. 从Github上**pull**项目更新到本地idea.
5. **克隆**到本地idea。



## 六、国内平台Gitee

## 七、自建代码平台GitLab



**常用命令：**

git status																											状态查看，红色表示未加入缓冲区

① git add *.md	

② git commit -m "版本信息"

git remote -v 																										查看版本信息

git remote add study_note https://github.com/LifeSum12/study_note	添加GitHub路径

③ git push study_note master																		master表示当前分支