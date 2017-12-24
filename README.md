# public-docs

听云产品文档，使用gitbook发布

## 开始使用

```bash
git clone git@github.com:Datafruit/public-docs.git
cd public-docs

# 先安装nodejs和npm（略）
# 安装gitbook
npm i -g gitbook-cli

# 安装gitbook插件
gitbook install

# 预览
gitbook serve 或者 npm start
# 然后浏览器访问http://localhost:4000

```

## 编辑并提交内容

```bash
# 从master创建分支再编辑
git checkout master
git pull
git checkout -b your-branch

# 编辑 book-index.md 以修改首页
# 编辑 SUMMARY.md 以编辑左侧菜单
# 编辑其他文件夹的.md文件以修改内容

#提交
git add --all
git commit -m '编辑了什么内容的描述'
git push

#然后创建pull request, 并通知管理员review 你编辑的内容，合并到master分支并且更新到服务器
```

## 更新到docs.sugo.io

```bash
# ssh SkfodExwx:***************************@120.76.247.214:22 
cd ~/dev/public-docs
# update and reload
./update

# 或者手动
git pull
gitbook build
```


## 更新到docs.tsa.sugo.io

```bash
# ssh SkfodExwx:***************************@120.76.247.214:22 
cd ~/dev/tsa-docs
# update and reload
./update-tsa
```

