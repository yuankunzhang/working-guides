# 安全

## 个人密码

* 针对每一个账号使用不同的密码
* 使用密码管理工具生成随机密码
  * [1Password](https://agilebits.com/)
  * [LastPass](https://www.lastpass.com/)
* 如果需要传输密码，务必使用 GnuPG 等工具对密码加密

## 服务中的密码

* 不能明文存储密码
* 不能使用可逆算法生成密码
* 不能使用 broken ciphers 生成密码，MD5 和 SHA1 都不行
* 不能通过 HTTP 传输密码或令牌

## HTTPS

* 全站使用 HTTPS
* 最开始就使用 HTTPS
* 对 HTTP 进行 HTTPS 重定向
* 使用 HSTS 首部强制使用 HTTPS
* 使用 secure cookies

## 开源项目

* 不能在开源项目中存储任何敏感数据
* 如果不幸暴露敏感数据，必须更改或失活相关数据