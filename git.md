#### git的工作区和暂存区

1. 工作区 ：执行$ git init 的目录即为工作区

2. 版本库 ：隐藏目录.git 称为git的版本库

3. index文件 ：位于版本库下，称为暂存区

4. 分支：Git自动创建的第一个分支 master

5. 指向master的指针：HEAD，表示最新版本

6. 文件添加到Git的两步：

   第一步是用 git add 把文件添加进去，实际上就是把文件修改添加到暂存区。

   第二步是用 git commit 提交更改，实际上就是把暂存区的所有内容提交到当前分支。（我们现在只有唯一一个分支 master，所以现在就是往 master 分支上提交更改）



#### Git工作区、暂存区、版本库

![关系图](https://www.runoob.com/wp-content/uploads/2015/02/1352126739_7909.jpg)



#### Git创建仓库

1. 初始化仓库

   ```shell
   $ git init 
   $ git init newRepo
   ```

2. 克隆仓库

   ```shell
   $ git clone <repo> <directory>
   $ git clone git@github.com:quinCode/MyRepository
   ```
   
3. 配置

   ```shell
   $ git config --global user.name "runoob"
   $ git config --global user.email test@runoob.com
   
   --global : 对系统上所有仓库都有效
   ```



#### Git基本操作

![关系图](https://www.runoob.com/wp-content/uploads/2015/02/git-command.jpg)

```shell
$ git add	
$ git add [file1] [file2] ...		
$ git add [dir]
$ git add .					#添加当前目录下的所有文件到暂存区
```



```shell
$ git status
$ git status -s				#获得简短的输出结果
```



```shell
$ git diff [file]			#显示暂存区和工作区的差异
$ git diff --cached [file]/$ git diff --staged [file]	 #显示暂存区和上一次提交的差异
$ git diff [first-branch]...[second-branch] #显示两次提交之间差异
```



```shell
$ git commit -m [message]	#提交暂存区到本地仓库
$ git commit [file1] [file2] ... -m [message]	#提交暂存区的指定文件到仓库区
$ git commit -am		   #-am 用于提交跟踪过(add过)的文件，不需要再次add
```

**忘记编写提交信息-m了怎么办**：按i进入编辑模式，输入文本后按esc推出编辑模式，再输入:wq，回车，提交成功！

```shell
$ git reset [--soft | --mixed | --hard] [HEAD]
#--mixed 为默认，可以不用带该参数，用于重置暂存区的文件与上一次的提交(commit)保持一致，工作区文件内容保持不变。
$ git reset --soft HEAD
#--soft 用于回退到某个版本
$ git reset --hard HEAD
$ git reset --hard origin/master	#回退到远程库版本
#--hard 将暂存区和工作区都回到上一个版本

HAED	HEAD~0
HEAD^	HEAD~1
HEAD^^	HEAD~2
...	#HEAD各参数说明

$ git reset HEAD [file]	#用于取消放入暂存区的内容
$ git reset --hard [commit_id] #用退到某一commit
```



```shell
$ git rm [file]		#将文件从工作区和暂存区删除
$ git rm --cached [file]	#从暂存区移除，但保留在工作区
$ git rm -f [file]	#如果删除之前修改过并且已经放到暂存区域的话，则必须要用强制删除选项-f。
$ git rm –r * 		#递归删除整个目录的所有子目录和文件

```



```shell
$ git mv [file] [newfile]	#用于移动或重命名一个文件、目录
$ git mv -f [file] [newfile]	#新文件名已存在时，使用-f参数
```



#### Git分支管理

```shell
$ git branch [branch_name]	#创建分支
# git init Git会默认创建master分支
$ git checkout [branch_name]	#切换分支
$ git checkout -b [branch_name]	#创建新分支并立即切换到该分支
$ git branch	#列出本地分支
$ git branch -d [branch_name]	#删除分支命令

$ git merge [branch_name] -	#将某分支合并到主分支
$ git merge [branch_name] -m
```

```shell
#合并冲突出现
$ git merge origin/master
CONFLICT (add/add): Merge conflict in test.txt
Auto-merging test.txt
Automatic merge failed; fix conflicts and then commit the result.

#可手动修改 合并后的内容
$ git add  [file_conflict]	#add表示冲突已解决
$ git commit			   #提交完成合并
```



#### Git远程操作

```shell
$ git remote -v		#显示所有已经添加的远程仓库
$ git remote show [remote]	#显示某一远程仓库的信息

$ git remote add [shortname] [url]	#添加远程版本库
$ git remote add origin git@github.com:quinCode/MyRepository

$ git remote rm [shortname]		#删除版本库
$ git remote old_name new_name	#重命名版本库
```



```shell
$ git fetch #用于从远程获取代码库
$ git merge #fetch获取后需要执行merge

$ git fetch [alias]
$ git merge [alias]/[branch]


$ git push <远程主机名> <本地分支名>:<远程分支名>	#将本地分支版本上传到远程并合并
$ git push <远程主机名> <本地分支名>	#本地分支和远程分支名相同时

$ git pull <远程主机名> <远程分支名>:<本地分支名> #用于从远程获取代码并合并本地的版本
```







#### git的各种命令

1. **$ git config --global user.name "Your Name"**

2. **$ git config --global user.email "email@example.com"**

3. **$ mkdir xxx  $ cd xxx  $ pwd**

4. **$ git init**

5. **$ git add readme.txt**

6. **$git commit -m "wrote a readme file"** (-m : comment)

7. **$ git status**

8. **$ git diff** : 查看文件是如何修改的

9. $ git log : 查看往届各版本

   **$ git log --pretty=oneline** : 可以查看到commit id

10. **$git reset  --hard HEAD^**: 回退到上一版本

    HEAD表示当前版本 HEAD^表示上一版本 

    HEAD^^表示上上版本

    **$ git reset --hard {commit id}** : 可以回到最新版本

11. **$ cat readme.txt** : 查看 readme.txt文件的内容

12. **$ git diff HEAD -- readme.txt** : 查看工作区和版本库内最新版本的对比

13. **$ git checkout -- readme.txt** : 丢弃工作区的修改,用版本库中的版本替换工作区的版本

14. **$ git reset HEAD readme.txt** : 撤销暂存区的修改，重新放回工作区

15. **$ rm readme.txt** : 删除文件

    1. 从版本库中删除：**$ git rm readme.txt**

       ​							 **$ git commit -m "remove** readme.txt"

    2. 恢复文件 ：**$ git checkout -- readme.txt**

16. **$ git push** : 将本地库所有内容推送到远程库

    初次执行时加上参数：**$ git push -u origin master**

17. 查看最新的commit

    **git show**

    查看指定commit hashID的所有修改：

    **git show commitId**

    查看某次commit中具体某个文件的修改：

    **git show commitId fileName**

18. $ git mv {old_filename} {new_filename}

    使用mv命令进行文件的重命名

    

#### 远程仓库和本地仓库的关联

关联本地库和远程库

```shell
git remote add origin 
git@github.com/quinCode/MyRepository
```

取消关联

```shell
git remote remove origin
```

把本地库的所有内容推送到远程库上

```shell
git push -u origin master
```

push命令

```shell
git push <远程主机名> <本地分支名>:<远程分支名>
<!--分支名相同时-->
git push <远程主机名> <本地分支名>

```



#### gitignore文件

1. 格式规范

   所有空行或者以注释符号 ＃ 开头的行都会被 Git 忽略

   可以使用标准的 glob 模式匹配

   匹配模式最后跟斜杠(/)说明要忽略的是目录

   要忽略指定模式以外的文件或目录，可以在模式前加上感叹号(!)进行取反

2. glob模式

   所谓的 glob 模式是指 shell 所使用的简化了的正则表达式，匹配规则如下：
    `"*"`：星号匹配零个或多个任意字符
    `[]`：匹配任何一个列在方括号中的字符，如[ab]匹配a或者匹配b
    `"?"`：问号匹配一个任意字符
    `[n-m]`：匹配所有在这两个字符范围内的字符，如[0-9]表示匹配所有0到9的数字

   ```shell
   logs/：忽略当前路径下的logs目录，包含logs下的所有子目录和文件
   
   /logs.txt：忽略根目录下的logs.txt文件
   
   *.class：忽略所有后缀为.class的文件
   
   !/classes/a.class：不忽略classes目录下的a.class文件
   
   tmp/*.txt：只忽略tmp目录下的.txt文件
   
   **/foo：可以忽略/foo, a/foo, a/b/foo等
   ```

3. 定义全局的.gitignore文件

   ```shell
   $ git config --global core.excludesfile ~/.gitignore
   ```

4. java开发模板

   ```shell
   #java
   *.class
   
   #package file
   *.war
   *.ear
   *.zip
   *.tar.gz
   *.rar
   #maven ignore
   target/
   build/
   
   #eclipse ignore
   .settings/
   .project
   .classpatch
   
   #Intellij idea
   .idea/
   /idea/
   *.ipr
   *.iml
   *.iws
   
   # temp file
   *.log
   *.cache
   *.diff
   *.patch
   *.tmp
   
   # system ignore
   .DS_Store
   Thumbs.db
   ```

   
