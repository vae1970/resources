##### git 撤销cimmit && 保留代码

```bash
//	查看git历史，如果要撤销最近的两次commit，那么再看前一次commit（也就是最近的一次正确的commit）
git log
//	这个commit_id是最近一次正确commit的id，不是你要撤销的那次commit的id
git reset --soft <commit_id>
```

##### git 删除commit中的文件

```bash
//	下载用户删除commit记录的工具
wget http://repo1.maven.org/maven2/com/madgag/bfg/1.13.0/bfg-1.13.0.jar
git clone --mirror git://example.com/some-big-repo.git
cd some-big-repo.git
java -jar ../bfg-1.13.0.jar --delete-files "xxx.md"
git reflog expire --expire=now --all && git gc --prune=now --aggressive
git push -f
```

