# Sublime

# 安装`Package Control`

+ [下载](https://github.com/wbond/package_control/releases) 
+ 打开`sublime` -> `Preferences` -> `Browse Packages...`
+ 复制下载的`JAR`包到当前文件夹并解压,修改文件名为`Package Control`
+ 重新打开`sublime` -> `Ctrl + Shift + P` -> `pcip`可以看到`Package Control`已经安装

# 添加`channel`

+ 这里使用`https://raw.githubusercontent.com/SuCicada/channel_v3.json/master/channel_v3.json`
+ 打开`sublime` -> `Preferences` -> `Package Settings` -> `Package Control` -> `Settings User`增加`channel`
```
{
    "bootstrapped": true,
    "channels":
    [
        "https://raw.githubusercontent.com/SuCicada/channel_v3.json/master/channel_v3.json",
        "https://packagecontrol.io/channel_v3.json"
    ],
    "in_process_packages":
    [
    ],
    "installed_packages":
    [
        "Emmet",
        "Package Control"
    ]
}

```
+ 重新打开`sublime`可以快乐的安装插件