# 沙箱完整性校验

TODO：

* 【已解决】iOS反越狱检测：fork()进程即沙箱完整性检测
* 【已解决】iOS反越狱检测：fork()的hook绕过

----

```c
/*==============================================================================
 Hook: fork()
==============================================================================*/

pid_t fork(void);

%hookf(int, fork, void){
    int retForkValue = FORK_FAILED;
    if (cfgHookEnable_misc) {
        retForkValue = FORK_FAILED;
    } else {
        retForkValue = %orig;
    }
    iosLogInfo("retForkValue=%d", retForkValue);
    return retForkValue;
}
```
