git/github/gitlab

[TOC]

# 1. git

- [Git Reference](https://git-scm.com/docs)
- [Git Book 中文](https://git-scm.com/book/zh/v2)

# 2. github

# 3. gitlab

- [https://gitlab.com/](https://gitlab.com/)

## 3.1 ssh 免密

[GitLab and SSH keys](https://docs.gitlab.com/ee/ssh/README.html)

GitLab uses the SSH protocol to securely communicate with Git. When you use SSH keys to authenticate to the GitLab remote server, you don’t need to supply your username and password each time.

```shell
MacBook:workspace ld$ ssh-keygen -t rsa -b 2048 -C "rsa ssh key"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/liudong/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /Users/liudong/.ssh/id_rsa.
Your public key has been saved in /Users/liu:q/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:cFYd6P3WpWS/PKwZ0u557NhoMypFayn/Jdvr5ieO/rU rsa ssh key
The key's randomart image is:
+---ÆRSA 2048Å----+
ø          .o..   ø
ø         .. .    ø
ø      . o. .     ø
ø       +  ... o .ø
ø        S . o+ +.ø
ø         . =. + oø
ø          =. =+.oø
ø         . .o=&Xoø
ø          .oB&%E+ø
+----ÆSHA256Å-----+
MacBook:workspace ld$ 
```

## 3.2 Merge Request

> #### Merge requests are a place to propose changes you've made to a project and discuss those changes with others.

项目下有`Merge Request`接口，可用于分支合并，同时，也可用于其他同事进行代码审核。

## 3.3 git tag

