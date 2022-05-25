# git

``` bash
################################################
## 密钥对配置
################################################
# 生成密钥对。   man ssh-keygen   -C 是comment，没实际用途
ssh-keygen -t rsa -C "xxxx@gmail.com"
# 添加私钥
ssh-add ~/.ssh/id_rsa
# 查看私钥列表
ssh-add -l
# 清空私钥列表
ssh-add -D

# 测试是否正常连接。
ssh -T git@github.com
ssh -T git@gitlab.alibaba-inc.com

# 同时配置 github 和 gitlab  （需要将密钥对中的 公钥 添加到github/gitlab的setting中）
➜ cat ~/.ssh/config
# github
Host github
        HostName github.com
        IdentityFile ~/.ssh/id_rsa

# gitlab
Host gitlab
        HostName gitlab.alibaba-inc.com
        IdentityFile ~/.ssh/id_rsa_ali
        
        

```

