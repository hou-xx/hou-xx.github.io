## git 合并两个远程分支
#### 场景：
最初只有一个分支，随着业务发展，分化出不同的分支，分化出的分支又要合并最初分支的修改。

#### 实例：
github 上 fork 别人的项目，别人的项目有更新，自己 fork 的分支也有修改（不能删除后重新fork），需要合并原分支的修改（可能也是 merge 其他人的修改）。

#### 实验准备：
本机已安装好 git 命令行，且配置好 ssh 密钥，可以向自己的 gitbub 提交。

#### 实验材料：
分支地址：       

- `git@github.com:tianqing2117/flexbox-layout.git`  
自己的仓库地址，有 pull、push 的权限，最终也是更新到这个仓库；
- `https://github.com/google/flexbox-layout.git`        
fork 的原项目地址，有 pull 的权限；        

> 两年前 fork 了 google 的项目，google 这两年持续在更新这个项目，一直没有合并 google 的更新。          

![更新前，自己仓库的状态][1]
<br/>
![更新前，google 仓库的状态][2]

### 简单版
1. 本地 clone 自己的远程仓库
```
git clone git@github.com:tianqing2117/flexbox-layout.git
```
clone 后需要 cd 到 flexbox-layout 文件夹里才进入 git 项目目录。             
2. 添加原项目地址为远程仓库地址
```
git remote add google git@github.com:google/flexbox-layout.git
```
这里给这个远程仓库地址起名叫 google       
3. 从步骤 2 添加的远程仓库 merge 代码
```
git merge google/master
```
添加后可以通过 `git remote -v` 查看一下    
4. 提交变更到自己的远程仓库
```
git push origin master
```

### 复杂版
#### 操作步骤
1. 本地 clone 自己的远程仓库
```
git clone git@github.com:tianqing2117/flexbox-layout.git
```
clone 后需要 cd 到 flexbox-layout 文件夹里才进入 git 项目目录。             
2. 添加原项目地址为远程仓库地址
```
git remote add google git@github.com:google/flexbox-layout.git
```
这里给这个远程仓库地址起名叫 google           
3. 新建本地分支接受 google 仓库地址的内容
```
git checkout -b tmp
```
这里创建了一个本地分支叫 tmp ，并切换过去了            
4. 从 google 远程分支更新修改到 本地 tmp 分支 
```
git pull google master:tmp
```
这里把 google 远程仓库的修改同步到了本地 tmp 分支         
5. 切换回本地 master 分支 
```
git checkout master
```
因为这个分支的内容是和自己的远程仓库保持同步的         
6. 从本地 tmp 分支合并修改到 master 分支
```
git merge tmp
```
相当于把 google 远程仓库的变更合并到本地 master 分支          
7. 提交变更到自己的远程仓库
```
git push origin master
```

#### 实验结果

![更新后自己远程仓库][3]

### 相关命令详解
- **git clone**     
```
git clone <版本库的网址> 
git clone <版本库的网址> <本地目录名> // 指定本地目录名
git clone -o romoteBranchName <版本库的网址> //指定远程分支名称
```
clone 命令会创建指定本地目录名的文件夹把版本库的内容更新下来，默认创建本地分支 master 和远程分支 origin 并绑定；     
`-o` 可以指定远程分支名称；         
不指定本地目录名则创建与版本库目录名一样的目录。       

- **git remote**        
```
git remote //命令列出所有远程主机
git remote -v //列出所有远程主机并展示远程主机的网址
git remote show <主机名> //查看远程分支的详细状况
git remote add <主机名> <网址> //添加远程主机名
git remote rm <主机名> // 删除远程主机
git remote rename <原主机名> <新主机名> // 修改远程主机名
```

- **git branch**
```
git branch //查看本地分支 现在所在的分支会有 * 号标注
git branch -r //查看远程分支
git branch -a //查看所有分支（本地+远程）
```
- **git checkout**
```
git checkout 分支名 //切换到指定分支
//指定本地分支切出新分支并切换。不指定分支时根据当前分支切新分支
git checkout -b newBrach 远程分支/本地已有分支 
```
- **git merge**
```
git merge 本地分支名 //合并本地分支
//合并本地分支，用于取 fetch 后的内容
git merge 远程分支/本地分支 
```
- **git pull**
```
//把指定远程主机名远程分支的内容拉取到指定的本地分支
git pull <远程主机名> <远程分支名>:<本地分支名>
//把指定分支内容拉取到当前本地分支，相当于先 fetch 再 merge
git pull <远程主机名> <远程分支名> 
```
- **git push**
```
//把指定本地分支的 commit 推到指定的远程主机远程分支上
git push <远程主机名> <本地分支名>:<远程分支名>
//把本地分支推送与之存在"追踪关系"的远程分支（通常两者同名），如果该远程分支不存在，则会被新建。
git push <远程主机名> <远程分支名> 
//删除指定的远程分支，等同于推送一个空的本地分支到远程分支
git push <远程主机名>:<远程分支名>
//指定默认主机，下次直接 git push 即可
git push -u <远程主机名> <本地分支名>
```
    
- **git archive**        
**导出 commit 的差异文件，且按目录生成，方便提交差分代码**   
> git diff 取出两个版本之间的差异文件            
> git archive 打包差异文件(只能在Bash下执行)        

```
// 命令格式
 git archive -o 导出文件名.zip 导出版本号 $(git diff --name-only 旧版本号..新版本号)
```

- **本地分支与远程分支绑定**    
> 本地分支切换至待绑定分支后 执行 `git branch --set-upstream-to=origin/远程分支名` 即可与远程分支绑定        

```
//命令格式
git branch --set-upstream-to=origin/远程分支名（可通过 `git branch -a` 查看）
```
示例：
```
git branch --set-upstream-to=origin/1.0.1
```

- **合并单个 commit 到另一个分支**    
> 本地分支切换至待合并分支后 执行 `git cherry-pick commit版本号` 即可        

```
//命令格式
git cherry-pick commit版本号
```
示例：
```
git cherry-pick 62ecb3
```

- **git 储藏（暂存）**    
> “储藏”可以获取你工作目录的中间状态——也就是你修改过的被追踪的文件和暂存的变更——并将它保存到一个未完结变更的堆栈中，随时可以重新应用。常发生在分支切换过程。        

```
//暂存
git stash
//查看暂存列表
git stash list
//取出暂存的修改（仅应用，不删除）
git stash apply --index
//删除暂存的内容 （名字可由 git stash list 查询）
git stash drop 暂存名
// 应用最后一个储藏，并从堆栈中移除（最常用）
git stash pop
```
示例：
```
git cherry-pick 62ecb3
```

## git 库迁移

> 时常需要把 git 项目迁移到新的 git 服务上
> 要保留完整的 commit 记录及 branches

#### 1. 在新的 git 服务上创建项目（空项目）
#### 2. clone 裸库
```
git clone --bare 原 git 项目地址
```
#### 3. 推送镜像到新地址
```
git push --mirror 新 git 项目地址
```

## git status 中文文件名乱码 
修改配置即可
```
git config --global core.quotepath false
```



[1]:https://raw.githubusercontent.com/tianqing2117/DailyProgress/master/image/git/mine-before.png
[2]: https://raw.githubusercontent.com/tianqing2117/DailyProgress/master/image/git/google.png
[3]: https://raw.githubusercontent.com/tianqing2117/DailyProgress/master/image/git/mine-after.png