##git教程
####git配置
1. 设置Git的user name和email
    git config --global user.name "username"
    git config --global user.email "email@server"
2. 创建ssh密钥
    ssh-keygen -t rsa -C "email@server"
3. 添加密钥到ssh-agent
    eval "$(ssh-agent -s)"
    ssh-add ~/.ssh/id_rsa
4. 把id_rsa.pub文件里的key添加到github
5. 测试
    ssh -T git@github.com
6. 修改git项目下的.git文件夹
修改前
    [remote "origin"]
    url = https://github.com/username/project_name.git
    fetch = +refs/heads/*:refs/remotes/origin/*
修改后
    [remote "origin"]
    url = git@github.com:username/project_name.git
    fetch = +refs/heads/*:refs/remotes/origin/*

####git使用
    git add .
    git status -s
    git commit -m "注释"
    git push