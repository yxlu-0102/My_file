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

