# 1.Git 配置

## Git的首次配置
- 配置文件
	- /etc/gitconfig文件  系统配置
	- ~/.gitconfig或~/.config/git/config文件，针对用户
	- 当前仓库的Git目录（即.git/config）文件，针对单个仓库
每一级都会覆盖上一级的设置，因此.git/config中的值要优于/etc/gitconfig中的值。

- 用户身份
```
$ git config --global user.name "John Doe"
$ git config --blobal user.email johndoe@example.com
```

- 个人编辑器
	设置个人编辑器：
` $ git config --global core.editor emas`

- 检查个人设置
` $ git config --list`
可以读取单独的值，通过键入`git config <key>`，如：`git config user.name`

- 获取帮助
` git help <verb>`
如： `git help config`


# 2.Git 基础

## 2.1 获取Git仓库

- 在现有目录中初始化Git仓库
`$ git init`
这会创建一个名为.git的子目录。这个子目录包含了构成Git仓库骨架的所有必需文件。

- 克隆现有仓库
checkout : 只是检出当前版本的所有内容。
clone : 会将服务器仓库几乎所有数据完整复制，包括项目历史中的每个版本。
克隆仓库需要使用`git clone [url]`命令，如：
`$ git clone https://github.com/libgit2/libgit2`
若需要项目克隆到其他名字的目录中，可以把目录名作为命令行选项传入：
`$ git clone https://github.com/libgit2/libgit2 mylibgit`

## 2.2 在Git仓库中记录变更

### 2.2.1 查看当前文件状态 : `$ git status`
`$ git status`
更简洁的显示命令为：`$ git status -s`或`$ git status --short`

### 2.2.2 跟踪新文件 : `$ git add (files)`
`$ git add (files)`

### 2.2.3 忽略文件 : `.gitignore`
创建.gitignore文件，文件匹配规则如下：
- 空行或者以#开始的行会被忽略
- 支持标准的glob模式，类似于shell所使用的的简化版正则表达式。
	- `*` : 匹配零个或更多字符
	- `[abc]` 或者`[0-9]` : 匹配方括号内的任意单个字符
	- `?` : 匹配任意单个字符
	- `a/**/z` : 用于匹配目录，此例可匹配`a/z 或 a/b/z 或 a/b/c/z`等
- 以斜杠`/`开头的模式可用于禁止递归匹配
- 以斜杠`/`结尾的模式表示目录
- 以感叹号`!`开始的模式表示取反

例：
```
*.a				# 忽略.a类型的文件
!lib.a 			# 仍然跟踪lib.a，即使上一行要忽略.a类型的文件
/TODO 			# 只忽略当前目录的TODO文件，而不忽略子目录下的TODO
build/ 			# 忽略build/目录下的所有文件
doc/*.txt 		# 忽略doc/notes.txt, 而不忽略doc/server/arch.txt
doc/**/*.pdf 	# 忽略doc/目录下的所有.pdf文件
```

### 2.2.4 查看patch : `$ git diff`
- `$ git diff`
显示具体添加和删除的内容，即补丁patch。
单独此命令，只是显示没有进入暂存区的那些变更；若所有变更都已添加到暂存区，则`git diff`不会有任何输出。
**工作区** 与 **暂存区** 进行比较。

- `git diff --staged 或 git diff --cached`
将暂存的变更与上一次提交的内容相比较，并输出差异。
**暂存区** 与 **库** 进行比较。

### 2.2.5 提交变更 : `$ git commit -m "msg"`
若跳过暂存区，直接提交使用以下命令：
`$ git commit -a -m "msg"`

### 2.2.6 移除文件 : `$ git rm (files)`
- `git rm (files)`
将文件从已跟踪文件列表中移除，并从工作目录中删除，下一次提交，文件即不存在。
	- 若要删除某个目录下的某类文件，需要注意对`*`进行转义，格式为`\*`，例：`$ git rm log/\*.log`
- `git rm --cached (files)`
文件保留在本地当前工作目录，只将其从暂存区移除，提交后，库中也将不存在此文件。

### 2.2.7 移动文件 : `$ git mov file_from file_to`

## 2.3 查看提交历史 : `$ git log`
### 2.3.1 `git log`的常用选项：
```
	-p : 按补丁格式显示每个提交引入的更改
	--stat : 显示每个提交中被更改的文件的统计信息
	--shortstat : 只显示上述--stat输出中包含"已更改/新增/删除"行的统计信息
	--name-only : 在每个提交信息后显示被更改的文件列表
	--name-status : 在上一个选项输出基础上还显示出"已更改/新增/删除"统计信息
	--abbrev-commit : 只显示完整的SHA-1 40位校验和字符串中的前几个字符
	--relative-date : 显示相对日期（例如“两周前”），而不是完整日期
	--graph : 在提交历史旁边显示ASCII图表，用于展示分支和合并的历史信息
	--pretty : 用于一种可选格式显示提交。选项有oneline、short、full和format（用于指定自定义格式）
```
- `$ git log -p -Num`
显示每次提交所引入的差异， -Num表示需要显示最近几次提交

- `$ git log --stat`
查看每个提交的简要统计信息

- `$ git log --pretty=oneline`
更改日志输出的默认格式，oneline代表每行只显示一个提交

- `$ git log --pretty=format:"%h - %an, %ar : %s"`
允许指定自己的输出格式，常用格式如下：
	- %H ： 提交对象的散列值
	- %h ： 提交对象的简短散列值
	- %T ： 树对象的散列值
	- %t ： 树对象的简短散列值
	- %P ： 父对象的散列值
	- %p ： 父对象的简短散列值
	- %an： 作者的名字
	- %ae： 作者的电子邮箱地址
	- %ad： 创作日志（可使用-date=选项指定日期格式）
	- %cn： 提交者的名字
	- %ce： 提交者的电子邮箱地址
	- %cd： 提交日期
	- %cr： 相对于当前日期的提交日期
	- %s ： 提交信息的主题
- `% git log --pretty=format:"%h %s" --graph`
`--graph`选项会用ASCII字符的简单图表来显示Git分支和合并历史。

### 2.3.2 限制提交历史的输出范围
用于限制git log输出范围的选项
```
	-(n) : 只显示最新的n次提交
	--since, --after : 只输出指定日期之后的提交
	--until, --before : 只输出指定日期之前的提交
	--author : 只输出作者与指定字符串匹配的提交
	--committer : 只输出提交者与指定字符串匹配的提交
	--grep : 只输出提交信息包含指定字符串的提交
	--S : 只输出包含“添加或删除指定字符串”的更改的提交
```

### 2.3.3 撤销操作 ： `$ git commit --amend`
如下：
```
$ git commit - m 'initial commit'
$ git add forgotten_file
$ git commit --amend
```
最终只是产生了一个提交，因为第二个提交命令修正了第一个提交的结果。

- 撤销已暂存的文件 ： `$ git reset HEAD file`

- 撤销对文件的修改 : `$ git checkout -- file`

## 2.4 远程仓库的使用 : `$ git remote`

### 2.4.1 显示远程仓库 : `$ git remote`
在克隆某个仓库之后，Git给克隆源服务器取的默认名称为origin。
- `$ git remote -v`
此命令会显示出Git存储的每个远程仓库对应的URL

### 2.4.2 添加远程仓库 : `$ git remote add [shortname] [url]`

### 2.4.3 从远程仓库获取和拉取数据 : `$ git fetch [remote-name]`

### 2.4.4 将数据推送到远程仓库 : `$ git push [remote-name] [branch-name]`

### 2.4.5 检查远程仓库 : `$ git remote show [remote-name]`

### 2.4.6 删除和重命名远程仓库 : `$ git remote rename [name_from] [name_to]` `$ git remote rm [remote-name]`

## 2.5 标记 : `$ git tag`

### 2.5.1 列举标签 : `$ git tag`
查看指定标签：
`$ git tag -l "[tag_name]"`
### 2.5.2 创建标签 

### 2.5.3 注释标签 : `$ git tag -a [tag_name] -m "msg"`
可以使用以下命令，显示对应标签的信息：
`$ git show [tag_name]`

### 2.5.4 轻量标签 : `$ git tag [tag_name]`

### 2.5.5 补加标签 : `$ git tag -a [tag_name] [sha-1(或部分)]`

### 2.5.6 共享标签 : `$ git push origin [tag_name]`
如果有很多标签需要一次性推送，使用如下命令：
`$ git push origin --tags`

### 2.5.7 检出标签 : `$ git checkout -b [branchname] [tagname]`

## 2.6 Git别名 : `$ git config --global alias.[name_to] [name_from]`
如：
```
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status
$ git config --global alias.unstage 'reset HEAD --'
```

# 3. Git 分支机制 : `$ git branch`

## 3.1 基本操作
- `$ git branch [branch_name]`
创建新分支
查看各个分支当前所指向的对象：
`$ git log --oneline --decorate`

- `$ git checkout [branch_name]`
切换分支

- `$ git checkout -b [branch_name]`
创建并切换到新分支

- `$ git merge [branch_name]`
将branch_name合并到当前HEAD指向的分支

- `$ git branch -d [branch_name]`
删除分支

- `$ git mergetool`
使用图形化工具解决冲突

- `$ git branch -v`
查看每个分支上的最新提交

- `$ git branch --merged`
查看有哪些分支已经并入当前分支

- `$ git branch --no-merged`
查看包含尚未合并的工作的所有分支


