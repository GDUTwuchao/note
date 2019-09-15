**windows下github的使用**

- 先下载git工具

- 配置打开`git bash`配置信息

    ```bash
    git config --global user.name "your name" #输入你github的名字
    git config --global user.email "your email" #输入你github的邮箱
    在根据提示输入密码等操作。
    git config --list #查看配置信息
    ```
- 在github上新建一个仓库
- 在**本地要上传的文件夹**上右键`git bash here`
- 然后初始化，使用`git init`。会在文件夹里面生成`.git`的隐藏刚文件。
- 添加你要上传的文件
  - 如果是整个文件夹都上传。`git add .`
  - 如果是上传一个文件夹下的文件。`git add filename`
- 提交，添加信息。`git commit -m "message of this commit"` 输入提交的信息，例如做了什么改动之类的。
- 远程上传到`github`。
  ```bash
 git remote add origin https://github.com/yourname/yourrepository.git
 后面的地址就是你的github新建的仓库的名字的git
  ```
- 远程上传。 `git push -u origin master`
- 刷新`github`网页就可以看见全部文件都上传好。