## emo

风和日丽的早上，准备把仓库的东西 push 到 github 仓库，结果

![](https://resource.lzyan.fun/PigGo/20220424211255.png)

然后 pull、clone 都试了还是不行，都是这样子的错误。害，然后就心想着把错误信息往百度一贴，总能找到方法。好的，不行。那去 stackoverflow 看看，好吧也没找到可行的解决方案（应该搜索姿势错了）。但是看到好像有人说是因为开了个代理 clash 的原因，然而我是有开的，但是别人关了就可以，我关了还是不行。

咦，我开着代理的时候能上 github 的话，据它错误描述好像也不应该 push 不上去代码啊。关了代理后 ping 了好多遍是 ping 不通的，此时 ping 不通的话肯定 push 也不行的了。

所以讲到底应该就是上不了 github 的问题引发的吧。所以就去网上找了找，不挂代理快速访问 github 的办法吧（平时一般都直接挂代理懒得去整别的花里胡哨的）

## 解决 github 打不开的问题

> Reference：[如何解决Github网站打不开的问题](https://www.jianshu.com/p/bf37776b4bb8)

在右侧地址查看 `github.com` 的 `ip` 地址 ：https://github.com.ipaddress.com

![](https://resource.lzyan.fun/PigGo/20220424213019.png)

>其实 git clone 或者 git push 特别慢，并不是因为 http://github.com 的这个域名被限制了。而是 http://github.global.ssl.fastly.Net 这个域名被限制了。

在右侧地址查看 `github.global.ssl.fastly.net` 的 `ip` 地址 : https://fastly.net.ipaddress.com/github.global.ssl.fastly.net

![](https://resource.lzyan.fun/PigGo/20220424213245.png)

> hosts文件是一个用于储存计算机网络中各节点信息的计算机文件；作用是将一些常用的网址域名与其对应的IP地址建立一个关联“数据库”，当用户在浏览器中输入一个需要登录的网址时，系统会首先自动从Hosts文件中寻找对应的IP地址。

此时，只需要在 `C:\Windows\System32\drivers\etc` 目录下的 `hosts` 文件中把这两添加上去就可以了（提示没修改权限记得在属性里面改一下访问权限），上面的问题就解决了，同时打开 `github` 的速度也快多了 

```
140.82.112.3 github.com
199.232.5.194 github.global.ssl.fastly.net
```

除此之外，还可以把 `cdn` 的域名 `ip` 给加进去，在右侧的地址查看 `assets-cdn.github.com` 的 `ip` 地址 ：https://github.com.ipaddress.com/assets-cdn.github.com

![](https://resource.lzyan.fun/PigGo/20220424214125.png)

最后修改一下 `hosts` 文件

![](https://resource.lzyan.fun/PigGo/20220424214217.png)

