# 使用Alist挂载云盘到本地ubuntu系统

## 配置Alist

1. 下载安装包[alist-linux-amd64.tar.gz](https://pan.quark.cn/s/6476091a018d)

2. 解压，切换到可执行文件目录，赋予执行权限```bash chmod +x alist```接着以server模式运行该文件```bash ./alist server```，运行结束，确定没有输出错误

3. 第一次运行，会要求创建管理员账号，输入```./alist admin random```生成一个随机密码，然后输入```./alist admin set NEW_PASSWORD```设置一个新密码NEW_PASSWORD，请牢记并保存，后续登陆会用到。

4. 访问```http://pan.quark.cn```，任意方式登陆自己的账号，按下```F12```打开开发者模式，选择```Network(网络)```框，在筛选查看条目那一栏里他他填入```sort=```，选择筛选结果的第一条并点开，找出其```Cookie```值并保存

5. 访问```http://localhost:5244```，默认用户名密码都是```admin```，密码是第3步设置的新密码。

6. 进入Alist管理界面后，点击左边栏的```储存```，驱动选择```夸克```，挂载路径输入```/quark```，Cookie一栏将第4步的```Cookie```值复制粘贴即可，其他选项没有改动的必要，如果只希望展示网盘某个目录（出于隐私原因或者其他原因等）那就点进入这个目录，此时在浏览器上看到类似```https://pan.quark.cn/list#/list/all/cfeac2c04c61490fb3abca846a48c7cd-Film```格式地址，其中的```cfeac2c04c61490fb3abca846a48c7cd```就是这个目录的fid，将这个fid填写到根文件夹ID中，保存

7. 使用任意方式编辑 /usr/lib/systemd/system/alist.service 并添加如下内容，其中 path_alist 为 AList 所在的路径

    ```bash
    [Unit]
    Description=alist
    After=network.target
    
    [Service]
    User=your-user
    Type=simple
    WorkingDirectory=path_alist
    ExecStart=path_alist/alist server
    Restart=on-failure
    
    [Install]
    WantedBy=multi-user.target
    ```

8. 执行 ```bash systemctl daemon-reload``` 重载配置

## 挂载到本地文件系统

1. 在Alist服务所在电脑上执行放开端口命令```bash sudo ufw allow 5244/tcp```

2. 安装davfs```bash sudo apt install davfs2```

3. 配置密码信息

    ```bash
    cat << EOF | sudo tee -a /etc/davfs2/secrets
    # personal webdav, nextcloud application password
    /mnt/dav admin mypassword
    # older versions used URL, it is equivalent for compatibility reasons
    #https://nextcloud.example.com/remote.php/webdav/ admin mypassword
    EOF
    ```

   其中```mypassword```改为上面自己设置的新密码

4. 输入命令

    ```bash
    sudo systemctl daemon-reload 
    mount -a
    ```

    将quark云盘挂载到本地文件系统中

## 注意点以及相关报错解决

1. 挂载期间，请勿关闭alist server运行的终端

2. 为什么没有设置开机自启动的步骤：在ubuntu中，会导致一直卡在检查任务完成的步骤导致无法正常开机，只能进入修复模式停用功能，卸载软件，这样一来，功夫就全白费了

3. 当遇到```user vencent must be member of group davfs2```这个错误时，意味着用户```vencent```不属于```davfs2```组，而这是挂载 webDAV 文件系统所需的权限。为了修复这个问题，添加用户到 davfs2 组： 使用以下命令将用户 vencent 添加到 davfs2 组：

    ```bash
    sudo usermod -a -G davfs2 vencent
    newgrp davfs2
    ```

4. 缺点：太过于依赖网速，经常在打开较大文件夹时出现卡死的情况

## 参考文档以及相关附件

1. 参考博客 [网盘可以当本地硬盘用？没错！使用Alist挂载云盘到本地Linux系统方法](https://blog.csdn.net/dragonballs/article/details/135384413)

脚本下载链接 [mount_quark.sh](https://pan.quark.cn/s/38e4035e3629)
下载脚本至根目录，并将将脚本内容中的```Your_alist_dic```替换为```alist```安装的绝对路径
2. 总结，重启电脑后，想再次挂载，只需进行以下步骤
第一步，运行```./mount_quark.sh```， 第二步，新建终端页面，运行```mount -a```，根据提示，输入账号密码即可完成挂载
