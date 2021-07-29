#  GIT 

## git 六行配置


```shell
# 配置用户名
git config --global user.name 你的英文名
# 配置邮箱
git config --global user.email 你的邮箱
# 配置git的默认行为，默认是Matching,此行为会push所有分支，simple只会push当前分支
git config --global push.default simple
# 解决提交文件名如果是中文名会出现乱码的问题 
git config --global core.quotepath false
# 配置commit的时候以哪种编辑器打开填写commit信息
git config --global core.editor "code --wait"
# 这一个配置是解决不同系统提交代码换行的问题,具体不太了解，windows平台下配就完了
git config --global core.autocrlf input
```
