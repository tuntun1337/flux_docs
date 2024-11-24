# Gamesense常见问题

## 注入时出现Disable KVA shadowing and try again弹窗

这是由于您的电脑没有关闭KVA Shadowing导致的，您可以通过以下步骤关闭KVA Shadowing：

1. 右键Windows的开始按钮，选择 *终端 (管理员)* 或 *Windows PowerShell (管理员)* 或 *命令提示符 (管理员)*
2. 在弹出的窗口中输入以下命令(您可以复制后在窗口右键粘贴)，然后按回车键：
    ```shell
    reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /v FeatureSettingsOverride /t REG_DWORD /d 3 /f
    reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /v FeatureSettingsOverrideMask /t REG_DWORD /d 3 /f
    ```
3. 当您看到**两个** *操作成功完成* 字样时，重启电脑。
4. 尝试注入Gamesense。
