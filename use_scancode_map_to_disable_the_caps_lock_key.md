禁用大写锁定键，以及更多

在使用Vim的过程中，我经常会误按到大写锁定(Caps Lock)键。误按后我会下意识的通过u或者Ctrl+R恢复。但由于大写锁定键已经按下，命令并不会像我想的那样去执行，反而会错得更离谱。

因此，禁用大写锁定键就是最好的办法了。Google了一下，发现方法还不少：

**通过软件来禁用**

使用“[键盘屏蔽器](http://blog.sina.com.cn/s/blog_63e7d40a0100hype.html)”这个国产软件，可以轻易屏蔽键盘按键或者切换键位，而且立即生效。唯一的缺点就是必须保持软件运行。这显然是我无法接受的。

**修改注册表**

1. 找到注册表键名`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Keyboard Layout`
2. 在其下新建一个二进制键值“Scancode Map”，注意不要建立在子键中了。
3. 将键值设置为：`00000000 00000000 00000000 0200000000 003A00 00000000`
4. 重启操作系统生效。

**修改注册表的更简单的办法**

<a href="http://www.mympc.org" target="_blank">Silence</a>为我们准备了一个小工具专门做这件事情，于是我们就不用面对注册表了，而且也能很方便的还原它。

该工具的名字叫Keybmap，下载地址：<http://www.mympc.org/down/1/2005-11-26_0111998067.html>

**原理及更多**

作为一个有理想有抱负的程序猿，当然希望知道其中原理。其实“Scancode Map”这个东东，我们可以把它翻译成“扫描码映射”，是从Windows 2000开始提供的一种改变键盘布局的方法。利用它，我们可以禁用某些键，或者让某些键代替另外的某些键的功能（好拗口啊……）

上面的那串很长很长的二进制码，我们可以这么理解它：

>Scancode Map 代码的一般格式是：  
>hex:00,00,00,00,00,00,00,00,|02|,00,00,00,|原键,替代键,原键,替代键,|00,00,00,00  
>其含义为：  
>前8个00(DWord两个0)是版本号，接下来的“02”表示映射数，其最小为值为“02”，表示只映射一组，若要映射多组，只需增加相应的值即可，如映射2组其值应为“03”，3组为“04”，4组为“05”  
>后边代码每4个是一组：前两个是映射后键位的扫描码，后两个是键位原扫描码。如果要交换两个键，则最后四个值的排列形式是：键A，键B，键B，键A——它表示：键A成为键B，键B成为键A  
>最后以“00,00,00,00” 结尾。

如果你想知道更多的Scancode，盖茨先生为我们提供了文档：<http://download.microsoft.com/download/whistler/hwdev3/1.0/WXP/EN-US/scancode.exe>

如果你不喜欢看文档，还可以看这张图：

![Scancode Map分布图](use_scancode_map_to_disable_the_caps_lock_key/scan_codes.png)

**参考文章**

* <http://429006.com/article/technology/1532.htm>
* <http://www.experts-exchange.com/OS/Microsoft_Operating_Systems/Windows/A_2155-Keyboard-Remapping-CAPSLOCK-to-Ctrl-and-Beyond.html>
* <http://hi.baidu.com/dopod100/blog/item/fe23a37e4f4e093f0dd7da62.html>
