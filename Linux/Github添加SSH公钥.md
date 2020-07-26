# GitHub添加SSH公钥

```zsh
ssh-keygen -t rsa -C "email@email.com" # email随意设置
# 一路回车
cat ~/.ssh/id_rsa.pub # 复制这个内容到GitHub即可
```

