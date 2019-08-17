
# 关于webhook踩坑
废话不多说，先粘贴代码，这个是网上随便找都能找到的，而且也很简单，不解释了（基础教程在网上，都有，原理很简单，就是提交的时候，触发了webhook的钩子，然后调用你服务器的php文件，自动拉取代码）。

```php
@requires_authorization
$local = '/xxx/www/lte'; 
//获取请求参数
$request = file_get_contents('php://input');
if (empty($request)) {
    die('request is empty');
}
echo shell_exec("cd {$local} && git pull 2>&1");
die('done ' . date('Y-m-d H:i:s', time()));
```
场景：开始不熟悉linux,所有操作都用了root进行了。但是这时候，我又不想再切换成www从新搞一遍了。
注意,在开始之前要知道，你的脚本是以什么用户去执行的。
代码：
```php
system("whoami"); // 查看是哪个用户执行该命令
```
### 1. 这时候你测试，可能会报如下错误
> * Permission denied ...
> * could not read Username ...
> * Enter file in which to save the key (/usr/share/httpd/.ssh/id_rsa)...

### 2. 我的处理方式
> * chown -R apache:apache 项目地址  给执行用户权限
> * chown -R apache:apache /usr/share/httpd  再给这个地方权限（因为linux不熟，后续了解再补全，先解决问题）
> * sudo -u apache git config --global credential.helper store (以apache用户去执行git保存密码)
> * sudo -u apache git pull (以apache用户去执行git拉取)

经过这一顿的操作，webhook上终于可以正常拉取代码了，但是其实有些操作，我还是不理解到底是什么意思，欢迎大家给我指点一下，我自己也会不断学习。
第一次，用markdown写东西，不太习惯...
