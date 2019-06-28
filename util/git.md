##### git 撤销cimmit && 保留代码

```bash
//	查看git历史，如果要撤销最近的两次commit，那么再看前一次commit（也就是最近的一次正确的commit）
git log
//	这个commit_id是最近一次正确commit的id，不是你要撤销的那次commit的id
git reset --soft <commit_id>
```

