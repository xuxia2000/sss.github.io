---
title: 建立自己的博客
category: hexo
---

总体来说，不是很难

*第一步，创建github仓库，注意名字一定要是  github用户名.github.io  (大坑，浪费我很多时间)
>使用hexo init 本地初始化时，可能会因为git 通过https克隆仓库失败，导致初始化不了，直接使用ssl克隆快速开始项目
```
	hexo init  //初始化项目
	hexo generate   //生成public静态文件
	hexo server   //运行，可在localhost:4040访问
```
>先本地运行起hexo博客，即达到本地访问无问题，主要修改主题比较麻烦，在theme中放一个主题，修改_config.yml中的theme的指向，完成主题的更改
>本地访问没有问题后使用如下指令部署到github服务器上

```
	hexo clean
	hexo deploy  //会将本地的public文件夹覆盖到仓库里面
```

>值得说的是仓库名起错了，就太折腾了  [配置域名博客](https://blog.csdn.net/weixin_29092579/article/details/112100678)
>[hexo中文网](https://hexo.io/zh-cn/docs/)
> 
> 
> 
 >如上教程绑定域名，在此提交文件后，会被取消掉，在source文件夹下创建CNAME文件，里面写入顶层域名（即服务器给你提供的那个），才不会被刷新掉