# Git 笔记

==目前世界上最先进的分布式版本控制系统==

## 创建版本库

1. 先创建文件夹，进入后

   ```
   $ git init
   Initialized empty Git repository in /目录
   ```

   当前目录下会多处一个 `.git` 的目录，但是是隐藏的，用 `ls-ah` 可以看见

2. 编写一个 `readme.txt` 文件

3. 用命令 `git add` 告诉 Git，把文件添加到仓库

   ```
   $ git add readme.txt
   ```

4. 用命令 `git commit` 告诉 Git，把文件提交到仓库

   ```
   $ git commit -m "Wrote a readme file"
   ```

   `-m` 后面输入的是本次提交的说明，`commit` 一次可以提交很多文件，因此可以 `add` 很多次。

## 时光机穿梭

修改 `readme.txt` 之后，使用 `git status` 命令，可以看到 `readme.txt` 被修改过了，但没有准备提交的修改。

使用 `git diff` 命令可以看到具体修改的内容。

再使用  `git add` 命令，并使用 `git status` 命令查看当前仓库的状态可以发现将要被提交的修改包括 `readme.txt` ，最后用 `git commit` 提交，使用 `git status` 命令查看当前仓库的状态可以发现目前没有需要提交的修改，并且工作目录是干净的。

### 版本回退

- 用 `git log` 命令查看从最近到最远的日志，可以添加参数 `--pretty=online` 查看精简信息，前面的字符串是 `commit id` 版本号。

- 启动时光穿梭机，让 `readme.txt` 回退到上一个版本，可以使用 `git reset` 命令，在 Git 中，`HEAD` 表示当前版本，`HEAD^` 表示上一个版本，`HEAD^^` 表示上上一个版本，也可以写成诸如`HEAD~100` 。

  ```
  $ git reset --hard HEAD^
  ```

- 但此时使用 `git log` 查看版本库状态会发现没有最新的版本了，但是可以用指定版本号回到原来的版本。

  ```
  $ git reset --hard 1094a
  ```

  版本号不需要写全，Git 会去自动寻找。

- Git 还提供了一个 `git reflog` 命令用来记录每一次命令，可以通过这个找到版本号。 

### 工作区与暂存区

- 工作区（Working Directory）就是在电脑中能看到的目录。

- 版本库（Repository）,比如工作区的一个隐藏目录 `.git` 是 Git 的版本库。

  ![image-20210902094953630](https://yxlu-0102-1305682038.cos.ap-nanjing.myqcloud.com/uPic/image-20210902094953630.png)

  版本库里有名为 index 的暂存区，还有 Git 自动创建的第一个分支 `master` ，以及指向 `master` 的一个指针 `HEAD`。

- `git add` 实际上就是把文件修改添加到 `index`，而 `git commit` 是往 `master` 分支上提交修改。 

### 管理修改

每次修改，如果不用 `git add` 添加到暂存区，那么就不会加入到 `commit` 中。

### 撤销修改

- 当改乱了工作区的某个文件的内容，想直接丢弃工作区的修改时，可以用命令 `git checkout --file` 命令。
  - 如果文件修改后还没有放到暂存区，撤销修改后就回到和版本库一样的状态；
  - 如果已经添加到暂存区后，又作了修改，撤销修改就回到添加到暂存区后的状态。
- 当不仅改乱了工作区某个文件的内容，还添加到了暂存区，此时想丢弃修改需要分两步：
  - 用命令 `git reset HEAD <file>` 回到上一个场景；
  - 按照上一个场景操作。

### 删除文件

- 一般情况下，在文件管理器中删除文件或者用 `rm` 命令删除

- 如果确实要在 Git 版本库中删除文件，那就用命令 `git rm` 删除掉，并且 `git commit`。

  ```
  $ git rm test.txt
  $ git commit -m "remove test.txt"
  ```

  

- 如果删错了，但是版本库里还有，可以

  ```
  git checkout -- test.txt
  ```

  

## 远程仓库

Git 是分布式版本控制系统，同一个 Git 仓库，可以分不到不同的机器上，有一台机器是原始的版本库，其他的机器可以克隆这个版本库。

1. 创建 SSH Key，查看用户主目录下有没有 `.ssh` 目录，再看看这个目录下有无 `id_rsa` 和 `id_rsa.pub` 这两个文件，这是 SSH Key 密钥对，一个是私钥一个是公钥。如果没有，可以在 Shell 里（Win 在 Git bash里）创建。

   ```
   $ ssh-keygen -t -rsa -C "youremail@example.com"
   ```

2. 登陆 Git-hub，打开"Account settings"，"SSH Keys" 页面，点击"Add SSH Keys"，在 Key 文本框里粘贴  `id_rsa.pub` 文件的内容。

### 添加远程库

已经在本地创建了一个 Git 仓库，并希望在 Git-hub 创建一个 Git 仓库，并使两者进行远程同步。

1. 在 Git-hub 上新建一个 repository 名为 learngit，在本地的 learngit 仓库下运行命令：

   ```
   $ git remote add origin git@github.com:yourname/learngit.git
   ```

   Git 默认的远程库的名字就是  `origin`。

2. 将本地库上的内容推送到远程库上：

   ```
   $ git push -u origin master
   ```

   就是用 `git push` 命令将当前分支 `master` 推送到远程。远程库是空的，在第一次推送的时候加上了 `-u` 参数，Git 不但会把本地的 `master` 分支内容推送到远程新的 `master` 分支，而且会把这两个分支关联起来，在以后的推送或拉取时可以简化命令：

   ```
   $ git push origin master
   ```

### 删除远程库

先用 `git remote -v` 命令查看远程库信息，再用 `git remote rm <name> ` 命令根据名字删除。

```
$ git remote rm origin
```

这里只是解除了本地库和远程库的绑定关系，远程库本身没有任何改变。

### 从远程库克隆

```
$ git clone git@github.com:yourname/your_repository.git
```

## 分支管理

### 创建与合并分支

在 Git 里，主分支是 `master` 分支，`HEAD` 严格来说不是指向提交，而是指向 `master` ， `master` 指向提交。

创建一个新的分支，比如 `dev` 时，Git 新建了一个指针叫 `dev` ，指向 `master` 相同的提交，再把`HEAD` 指向 

 `dev` ，就表示当前分支在 `dev` 上。

从现在开始，对工作区的修改和提交就是针对 `dev` 分支了。当在 `dev` 上的工作完成了，就把 `master` 指针指向 `dev` ，就完成了合并。

- 首先创建 `dev` 分支：

  ```
  $ git checkout -b dev
  相当于
  $ git branch dev
  $ git checkout dev 
  ```

- 用 `git branch` 命令查看当前分支

  ```
  $ git branch
  *dev
   master
  ```

-  `dev` 的工作完成后，切换回 `master` 分支：

  ```
  git checkout master
  ```

  此时 `readme.txt` 不见了。

- 把 `dev` 的工作合并到 `master` 分支上：

  ```
  git merge dev
  ```

- 合并完成后，可以删除 `dev` 分支：

  ```
  git branch -d dev 
  ```

#### SWITCH

最新版本的 Git 提供了新的 `git switch` 命令来切换分支

创建并切换到新的 `dev` 分支，可以使用：

```
$ git switch -c dev
```

直接切换到已有的 `master` 分支，可以使用：

```
$ git switch master
```

### 解决冲突

使用 `git log --graph` 命令可以看到分支合并图

