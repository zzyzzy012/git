# Git

## 1.客户端操作

创建分支

![](D:\学习\program\前端学习\Git\git_img\branch_1.jpg)

合并分支：基于主分支main的合并，选中main。

![](D:\学习\program\前端学习\Git\git_img\branch_2.jpg)

分支若有冲突文件，需要手动解决，git自动提交，没有description，可以create tag。也可以删除标签delete tag。

![](D:\学习\program\前端学习\Git\git_img\branch_3.jpg)

![](D:\学习\program\前端学习\Git\git_img\branch_4.jpg)

忽略文件

![](D:\学习\program\前端学习\Git\git_img\branch_5.jpg)

## 2.版本号

git会生成40位的版本号，前2位表示分支文件，后38位表示文件。

git-bash命令中输入

```
git cat-file - p 版本号
```

提交信息——版本状态(tree)——版本内容。

提交和修改都会生成新的版本内容，同时指向原始文件，而删除则不再生成新的版本内容。

parent:表示上一次提交文件。

![](D:\学习\program\前端学习\Git\git_img\版本号.jpg)