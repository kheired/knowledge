# Git心得

### 基本操作
1. 未提交的内容清空、恢复

    ```bash
    git reset --hard HEAD
    ```
2. 拉取远程并修剪本地

    ```bash
    git fetch -fp
    ```
3. 新建远程分支

    ```bash
    # 新建本地分支（不需要提交commit即可创建远程分支）
    git push origin 分支名
    ```
4. tag

    ```bash
    git tag

    git tag 名字
    git push --tags

    git tag -d 名字 # 删除本地tag
    ```

### 如何在一台电脑中使用2（多个）个Github账号的SSH keys

>在github网站中：不同账户无法使用相同的**SSH key**。

1. 生产多对的**SSH keys**，并放入 **.ssh文件夹**：

    ```bash
    ssh-keygen -f '地址/名字'
    ```
2. 为不同账户地址设置对应的SSH key路径：

    **~/.ssh/config**文件添加
    ```bash
    Host 账户1.github.com
    	HostName github.com
    	User git
    	IdentityFile ~/.ssh/键1

    Host 账户2.github.com
    	HostName github.com
    	User git
    	IdentityFile ~/.ssh/键2
    ```
3. 克隆仓库时修改**仓库地址**：

    `git@github.com:账户/仓库.git` -> `git@账户.github.com:账户/仓库.git`
    ```bash
    #进入文件夹1
    git clone git@账户1.github.com:账户1/仓库.git

    #进入文件夹2
    git clone git@账户2.github.com:账户2/仓库.git
    ```
    >如果已经克隆过的仓库，仅需要修改`.git/config`文件夹内的`url`仓库地址即可。

### 设置gitconfig
1. 用户名和邮箱

    1. 全局设置

        ```bash
        git config --global user.email 邮箱
        git config --global user.name 用户名
        ```
    2. 为具体项目设置

        ```bash
        cd 进入某个git仓库
        git config user.email 邮箱
        git config user.name 用户名
        ```
2. 全局忽略文件

    1. 添加忽略文件

        ```bash
        git config --global core.excludesfile ~/.gitignoreglobal
        ```
    2. 打开添加的文件.gitignoreglobal，填写要全局忽略的文件（夹）

        e.g.
        ```bash
        .idea
        ```

### 减少Git项目下载大小
1. 处理版本中已push的commits

    >慎重，无法恢复。

    1. 取消**至**第一个commit之后的任意commit：

        ```bash
        git reset --hard HEAD~数字      # 取消当前版本之前的N次提交
        git push origin HEAD --force    # 强制提交到远程版本库，从而删除之前的N次提交数据
        ```
        >最多能取消至第二条commit；要删除第一条commit，不如先删除仓库再创建仓库。
    2. 操作第一个commit之后的任意commit：

        ```bash
        git rebase -i --root master     # 选择commit处理状态，用s或f向上合并
        # git rebase --abort    # 取消所有rebase操作
        # git rebase --continue    # 出现冲突时候能够合并继续处理
        # git rebase --skip    # （当无法使用--continue）出现冲突时丢弃commit，会造成内容丢失（慎重使用）
        # 编辑commit信息
        git push origin HEAD --force    # 强制提交到远程版本库

        # 其他用户需要 git remote 然后 git pull --rebase
        ```
        >当处理太多commits时候容易造成冲突。

    >1. [GitLab](https://about.gitlab.com/)默认设置**master**分支是**protected**状态，无法`git push --force`。
    >
    >    可以在Gitlab设置里面通过：*project* > *Settings* > *Protected branches* > *Developers can push*或*UNPROTECT*，打开权限（强烈不建议长期开启）。
    >2. Github默认允许`git push --force`。
2. 仅在Git项目中选择下载某些文件夹或文件

    >Git1.7.0以后加入了**Sparse Checkout模式**，允许Check Out指定文件或文件夹。但是只能选择一次（？），如果要更改选择的文件夹或文件，必须全部重新操作。

    1. 在空白文件夹内，创建空的本地仓库，然后将远程仓库地址加入到项目的**.git/config**文件中：

        ```bash
        git init
        git remote add -f origin 仓库地址
        ```
    2. 设置git允许使用**Sparse Checkout模式**：

        ```bash
        git config core.sparsecheckout true
        ```
    3. 选择需要单独克隆的文件或文件夹，写入 **.git/info/sparse-checkout**文件：

        ```bash
        echo 'images' >> .git/info/sparse-checkout # 所有包括有 images 的文件夹或文件（如/xxx/xxx/images/*、/images/*、images）
        echo 'js/release' >> .git/info/sparse-checkout
        ```
    4. 仅对设置过的内容进行所有git操作：

        ```bash
        git pull origin master
        ```
3. 减少克隆深度

    `git clone 仓库地址 --depth 数字`

### [git-flow](https://github.com/nvie/gitflow)使用
1. 初始化：

    ```bash
    git flow init -fd
    ```
2. 开发新需求：

    ```bash
    git flow feature start “需求名”
    # 从develop分支本地创建并切换至“feature/需求名”分支


    推送具体需求的commits到远程“feature/需求名”


    git flow feature finish “需求名”
    # “feature/需求名”合并至本地develop分支
    # 删除本地“feature/需求名”分支，切换至develop分支
    # 可能删除远程的“feature/需求名”分支（根据git-flow版本不同）


    git checkout develop
    git push origin develop
    # 推送至远程develop分支
    ```

    >可以分别开发多个需求，再一起发布（release）。
3. 发布版本：

    ```bash
    git flow release start “版本号” [“develop的SHA”]
    # 基于远程“develop的SHA”或最新内容，在本地创建并切换至“release/版本号”分支


    推送需要改动的commits到远程“release/版本号”
    # 更新package.json版本号
    # 更新CHANGELOG.md
    # 修复发版前临时发现的问题


    git flow release finish “版本号”
    #tag描述：
    2017-07-18

    - 重构 某功能 by @wushi
    - 修复 某功能 by @yangjiu
    - 更新 某功能 by @sunba
    - 修改 某功能 by @qianqi
    - 优化 某功能 by @zhaoliu
    - 新增 某功能 by @wangwu
    - 下线 某功能 by @lisi
    - 移除 某功能 by @zhangsan
    - 上线 某功能 by @名字
    # “release/版本号”合并至本地master分支、本地develop分支
    # 新建本地“版本号”tag
    # 删除本地“release/版本号”分支，切换至develop分支
    # 可能删除远程的“release/版本号”分支（根据git-flow版本不同）


    git checkout master
    git push origin master
    # 推送至远程master分支


    git checkout develop
    git push origin develop
    # 推送至远程develop分支


    git push --tags
    # 推送至远程tag
    ```
4. 线上bug修复：

    >类似于release。

    ```bash
    git flow hotfix start “版本号” [“release的版本号”]
    # 从“release的版本号”或最新内容的master分支本地创建并切换至“hotfix/版本号”分支


    推送具体需求的commits到远程“hotfix/版本号”


    git flow hotfix finish “版本号”
    #tag描述：
    2017-07-18

    - 修复 某功能 by @名字
    # “hotfix/版本号”合并至本地master分支、本地develop分支
    # 新建本地“版本号”tag
    # 删除本地“release/版本号”分支，切换至develop分支
    # 可能删除远程的“release/版本号”分支（根据git-flow版本不同）


    git checkout master
    git push origin master
    # 推送至远程master分支


    git checkout develop
    git push origin develop
    # 推送至远程develop分支


    git push --tags
    # 推送至远程tag
    ```

>e.g. CHANGELOG.md
>
>```text
># Change Log
>
>## [1.0.1] - 2017-07-16
>
>- 重构 某功能 by @wushi
>- 修复 某功能 by @yangjiu
>- 更新 某功能 by @sunba
>- 修改 某功能 by @qianqi
>- 优化 某功能 by @zhaoliu
>- 新增 某功能 by @wangwu
>- 下线 某功能 by @lisi
>- 移除 某功能 by @zhangsan
>
>## [1.0.0] - 2017-06-08
>
>- 上线 某功能 by @zhengfeijie
>```

### commit message格式
>仅限于用在commit message，不得用在change log。

```text
<type>: <subject>

<description>   # 可选

<extra>         # 可选
```

>任何一行都不得超过72个字符（或100个字符），避免自动换行影响美观。

1. **\<type\>**

    用于说明commit的类别。

    1. `feat`：新功能（feature）。
    2. `fix`：修补bug。
    3. `docs`：文档（documentation）。
    4. `style`： 格式（不影响代码运行的变动）。
    5. `refactor`：重构（即不是新增功能，也不是修改bug的代码变动）。
    6. `test`：增加测试。
    7. `chore`：构建过程或辅助工具的变动。
    8. `revert`：撤销之前的commit。

        >e.g.
        >
        >```text
        >revert: feat: add 'graphiteWidth' option
        >
        >This reverts commit 667ecc1654a317a13331b17617d973392f415f02.
        >```
2. **\<subject\>**

    commit的简短描述。

    - 以动词开头，使用第一人称现在时。

        >比如change，而不是~~changed~~或~~changes~~。
    - 第一个字母小写。
    - 结尾不加句号。
3. **\<description\>**

    commit的详细描述。

    - 使用第一人称现在时。
4. **\<extra\>**

    1. 不兼容变动

        以`BREAKING CHANGE`开头的内容。
    2. 关闭issue

        `Closes #1, #2`。

>e.g.
>
>```text
>feat: 添加了分享功能
>
>给页面添加了分享功能
>
>- 添加分享到微博的功能
>- 添加分享到微信的功能
>```