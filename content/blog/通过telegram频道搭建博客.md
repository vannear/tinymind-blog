---
title: 通过Telegram频道搭建博客
date: 2024-12-02T03:58:43.303Z
---

# 感谢活佛

[来源](https://yinji.org/5334.html)

# 基本流程示意图
![image.png](https://cloudflare-imgbed-p1r.pages.dev/file/1731463910830_image.png)

# 步骤
## 1. Fork BroadcastChannel项目
[项目地址](https://github.com/ccbikai/BroadcastChannel)


## 2. Cloudflare创建pages
### 2.1 创建telegram频道
telegram中选择添加频道(channel)而不是群组(group)，将频道的属性设置为公开，创建完成之后，频道的代码——t.me/ **之后的那一部分**就是频道的代码——下一步要用到，例如t.me/swiftgram swiftgram即为这一频道的**频道代码**。
### 2.2 创建page
到Cloudflare的控制面板当中选择Workers and Pages，创建新的pages。如果已经链接过github账户的话并设置了cloudflare可以访问全部仓库的话，则直接勾选BroadCastChannel仓库项目即可，如果只添加了账户没有选择CF可以访问全部仓库，则需要点击添加账户按钮，在新的弹出窗口中重新认证一次，并将新的项目仓库授权给Cloudflare；如果没有Cloudflare链接过github账户则需要链接并进行认证，勾选BroadCastChannel的项目。

### 2.3 设置pages的变量
进入pages的创建页面之后，为项目添加一个名字，cloudflare会自动为这个项目生成一个工作域名。框架选择Astro，添加自定义变量CHANNEL，值为2.1中提到的**频道代码**。还可以添加其他的变量：
```
## Telegram 频道用户名，必须配置。 t.me/ 后面那串字符
CHANNEL=swiftgram

## 语言和时区设置，语言选项见[dayjs](https://github.com/iamkun/dayjs/tree/dev/src/locale)
LOCALE=zh-cn
TIMEZONE=Asia/Shanghai

## 社交媒体用户名
TELEGRAM=telegramUsername
TWITTER=twitterUsername
GITHUB=githubUsername

## 下面两个社交媒体需要为 URL
DISCORD=https://DISCORD.com/...
PODCASRT=https://PODCASRT.com/...

## 头部尾部代码注入，支持 HTML，可以用于添加谷歌分析或者广告等
FOOTER_INJECT=FOOTER_INJECT
HEADER_INJECT=HEADER_INJECT

## SEO 配置项，可不让搜索引擎索引内容
NO_FOLLOW=false
NO_INDEX=false

## Sentry 配置项，收集服务端报错
SENTRY_AUTH_TOKEN=SENTRY_AUTH_TOKEN
SENTRY_DSN=SENTRY_DSN
SENTRY_PROJECT=SENTRY_PROJECT

## Telegram 主机名称和静态资源代理，不建议修改
HOST=telegram.dog
STATIC_PROXY=
```
之后点击下一步进行部署即可，此外可以在设置当中添加自定义的域名，如果域名属于cloudflare的账户之下，cloudflare会直接添加相应的cname解析。

之后如果需要修改，再次回到cloudflare的workers and pages页面，选择对应项目，修改变量等信息之后保存，回到部署页面，点击最新一次的部署提交，再点击重试部署即可。
