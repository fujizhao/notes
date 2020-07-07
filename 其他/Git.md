# github上传或修改文件

## 上传

首先在github上创建仓库,并且在本地新建同名仓库。

```git
git init
git add -A
git commit -m "提交信息"
git remote rm origin
git remote add origin https://github.com/fujizhao/仓库.git
git push -u origin master
```

## 修改

如果已经建立过项目，每次修改之后进行文件上传

```git
git init
git add -A
git commit -m "提交信息"
git push -u origin master
```



-   git add相关

    ```
    git add -A  提交所有变化
    git add -u  提交被修改(modified)和被删除(deleted)文件，不包括新文件(new)
    git add .  提交新文件(new)和被修改(modified)文件，不包括被删除(deleted)文件
    ```

    

