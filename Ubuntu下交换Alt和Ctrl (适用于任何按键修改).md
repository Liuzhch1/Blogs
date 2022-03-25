在 Ubuntu 下交换`Alt`和`Ctrl`键：

```
sudo vim /usr/share/X11/xkb/keycodes/evdev
```
或者用系统默认编辑器打开：
```
sudo xdg-open /usr/share/X11/xkb/keycodes/evdev
```

然后找到`LALT`和`LCTL`所在的行，它们的默认值应该为`<LALT>=64`, `<LCTL>=37`。把它们的值互换即可交换这两个键。

其他的按键映射同理。

最后重启使更改生效
```
reboot
```

---
此外还可以用 Ubuntu 自带的软件进行更改。按下`Super`键(也就是`Win`键)，输入`Tweaks`，一个开关图标的软件就会跳出来(中文名叫`优化`)。打开它，在左边栏选择`键盘和鼠标`，在`键盘`里最后`其他布局选项`。在`Alt/Win键行为`或`Ctrl键位置`里自行修改。
但是我自己使用`优化`进行更改有时候会失效，比如在挂起之后。更改keycodes暂时没有遇到失效的情况。


Based On: Ubuntu 20.04.4 LTS
Reference: [ubuntu 下 ctrl 和 alt 互换](https://gist.github.com/VetaZhang/4d262e4ca6e553c63be253de84a08cab "ubuntu 下 ctrl 和 alt 互换")