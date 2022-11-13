## 用于记录项目的相关文档以及阅读项目的笔记

## git 简单的提交流程

跳转到到项目目录下

### 1.新建分支

```
git checkout  -b feature/introduce-dock-lp
```

### 2.提交分支commit

```
git add .
git commit -m "添加规范相关文档" .
```

### 3.推送到远程仓库

```
git push -f origin feature/introduce-dock-lp （分支名）
```

### 4.github界面处理提交的分支

![image.png](https://s2.loli.net/2022/11/12/FbDZhyqeTxoVXvs.png)

### 5.添加描述并确认文件修改没问题后创建pr
![image.png](https://s2.loli.net/2022/11/12/EZRKDalsI3efcdv.png)


### 6.这里设置的是三个人review完后才可以合并，符合后进行合并即可

![image.png](https://s2.loli.net/2022/11/12/FcDJuK4oWebSrmM.png)
