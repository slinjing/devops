第一次开机来到联网界面后,不要联网：
1.Shift+F10调出命令提示符窗口
2.输入oobe\bypassnro
3.自动重启后来到联网界面选择我没有Internet连接

如果已经联网，输入：netsh wlan delete profile name=* i=*  删除联网记录再执行前面的操作