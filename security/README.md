# 安全

## 关于

安全，不管怎么强调都不过分。

下面的每一项实践，都来自血淋淋的教训。

## 个人密码

* 针对每一个账号使用不同的密码 [1]
* 使用密码管理工具生成随机密码 [2]
  * [1Password](https://agilebits.com/)
  * [LastPass](https://www.lastpass.com/)
* 如果需要传输密码，务必使用 GnuPG 等工具对密码加密

## 服务中的密码

* 不能明文存储密码 [3], [4]
* 不能使用可逆算法生成密码
* 不能通过 HTTP 传输密码或令牌
* 不应使用 broken ciphers 生成密码，MD5 和 SHA1 都在此列

## HTTPS

* 全站使用 HTTPS
* 最开始就使用 HTTPS
* 对 HTTP 进行 HTTPS 重定向
* 使用 HSTS 首部强制使用 HTTPS [5]
* 使用 secure cookies [6]

## 项目

* 不论是闭源项目还是开源项目，一律不能在仓库中存储任何敏感数据 [7]
* 如果不幸暴露敏感数据，必须更改或失活相关数据，仅从仓库中删除文件是不够的 [8]

## 参考

1. [Why Is It So Important to Use a Different Password on Every Site?](https://askleo.com/why-is-it-so-important-to-use-a-different-password-on-every-site/)　
2. [Lesson #1 from the Hacking Team hack: Choose strong passwords](https://www.grahamcluley.com/hacking-team-strong-passwords/)
3. [Reddit's database was stolen](http://web.archive.org/web/20070311083012/http://reddit.com/blog/theft)
4. [CSDN明文口令泄露的启示](http://coolshell.cn/articles/6193.html)
5. [HTTP Strict Transport Security, HSTS](https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security?oldformat=true)
6. [Secure cookies](https://tools.ietf.org/html/rfc6265#section-4.1.2.5)
7. [In major goof, Uber stored sensitive database key on public GitHub page](http://arstechnica.com/security/2015/03/in-major-goof-uber-stored-sensitive-database-key-on-public-github-page/)
8. [Why Deleting Sensitive Information from GitHub Doesn't Save You](https://jordan-wright.github.io/blog/2014/12/30/why-deleting-sensitive-information-from-github-doesnt-save-you/)