---
title: Windows配置
slug: Windows配置
date: 2024-12-30 13:00:00+0800
tags:
    - Windows
weight: 1
---
- [显示`netplwiz`中的自动登录](#显示netplwiz中的自动登录)
- [自带输入法增加小鹤双拼](#自带输入法增加小鹤双拼)
- [RDP开启60帧](#rdp开启60帧)
- [删除Program启动项](#删除program启动项)
- [删除链接不同网络时提示的网络12345...](#删除链接不同网络时提示的网络12345)
- [通过vbs脚本自启程序](#通过vbs脚本自启程序)
- [允许挂载http的WebDAV](#允许挂载http的webdav)

# 显示`netplwiz`中的自动登录
```regedit
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\PasswordLess\Device]
"DevicePasswordLessBuildVersion"=dword:00000000
```

# 自带输入法增加小鹤双拼
```regedit
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Software\Microsoft\InputMethod\Settings\CHS]
"UserDefinedDoublePinyinScheme0"="小鹤双拼*2*^*iuvdjhcwfg^xmlnpbksqszxkrltvyovt"
```

# RDP开启60帧
```regedit
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations]
"DWMFRAMEINTERVAL"=dword:0000000f
```

# 删除Program启动项
```regedit
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run]
"TeamsMachineInstaller" = -
```

# 删除链接不同网络时提示的网络12345...
```regedit
Windows Registry Editor Version 5.00

[-HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkList\Profiles]
```

# 通过vbs脚本自启程序
```vbs
' 放在%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup

' 声明并初始化 WMI 服务对象和 Shell 对象
Dim objWMIService, objShell
Set objWMIService = GetObject("winmgmts:\\.\root\cimv2")
Set objShell = CreateObject("WScript.Shell")

' 无限循环，检查并启动进程
Do
    CheckAndStartProcess "aria2c.exe", "D:\aria2\aria2c.exe --conf-path=D:\aria2\aria2.conf"
    ' 等待一段时间后再次检查（这里设置为60秒）
    WScript.Sleep 60000
Loop

' 检查并启动进程的函数
Function CheckAndStartProcess(processName, command)
    Dim colProcesses
    Set colProcesses = objWMIService.ExecQuery("Select * from Win32_Process Where Name = '" & processName & "'")
    If colProcesses.Count = 0 Then
        objShell.Run command, 1, False ' 0不显示cmd窗口
    End If
    WScript.Sleep 5000
End Function
```

# 允许挂载http的WebDAV
```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WebClient\Parameters]
"BasicAuthLevel"=dword:00000002
```
