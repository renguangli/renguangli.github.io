今天提交代码时，git 报了如下错误

```bash
fatal: Unable to create ‘project_path/.git/index.lock’: File exists.
```

日志已经很明显了，index.lock 文件已存在，删除即可

```bash
git rm -f ./.git/index.lock
```
