# system

TODO：

* 【已解决】iOS越狱检测：system(NULL)
* 【已解决】iOS代码XCode中报错：system is unavailable not available on iOS
* 【已解决】iOS中传入fork调用system的返回值的含义和逻辑

---

```c
- (IBAction)systemBtnClicked:(UIButton *)sender {
    _curBtnLbl.text = sender.titleLabel.text;
    NSLog(@"system() check");

//    int systemRet = system(NULL);
    const char* command = NULL;
//    command = "ls -lh";
//    command = "fork";
    int systemRet = iOS_system(command);

    const int SYSTEM_RET_SHELL_EXEC_CMD_FAIL = 32512; // == 0x7F00 -> bit 15-8 is 0x7F = 127
    const int SYSTEM_RET_FORK_FAIL = -1;

    NSString* conclusionStr = @"未知结果";
    if (NULL == command){
//        if (0 == systemRet){
        if (systemRet > 0){
            conclusionStr = @"sh存在 -> 越狱手机";
        } else {
            conclusionStr = @"sh不存在 -> 非越狱手机";
        }
    } else {
        if (SYSTEM_RET_SHELL_EXEC_CMD_FAIL == systemRet){
            conclusionStr = @"shell执行命令失败 -> 可能是非越狱手机";
        } else if (SYSTEM_RET_FORK_FAIL == systemRet){
            conclusionStr = @"fork或waitpid失败 -> 可能是非越狱手机";
        } else {
            conclusionStr = [NSString stringWithFormat: @"shell退出状态值为%d -> 无法判断", systemRet];
        }
    }
    NSString* systemResultStr = [NSString stringWithFormat: @"system(%s)返回: %d -> %@", command, systemRet, conclusionStr];
    NSLog(@"%@", systemResultStr);
    _detectResultTv.text = systemResultStr;
}
```
