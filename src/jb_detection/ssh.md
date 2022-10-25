# SSH相关

TODO：

* 【未解决】iOS越狱检测之ssh相关
* 【未解决】iOS中如何用C语言代码实现ssh调用

---

```c
- (IBAction)sshBtnClicked:(UIButton *)sender {
    _curBtnLbl.text = sender.titleLabel.text;
    NSLog(@"ssh check");
    const char* sshCmd = "ssh root@127.0.0.1";
//    int systemRet = system(sshCmd);
    int systemRet = iOS_system(sshCmd);
    NSLog(@"sshCmd=%s -> systemRet=%d", sshCmd, systemRet);
    
    _detectResultTv.text = @"TODO";
}
```

* 调用的函数：`iOS_system`
  * https://github.com/crifan/crifanLib/blob/master/c/crifanLib.c

```c
/*==============================================================================
 iOS: implement deprecated system()
==============================================================================*/

int iOS_system(const char* command){
    const int SYSTEM_FAIL = -1;
    int systemRet = SYSTEM_FAIL;

//    if (NULL == command) {
//        return systemRet;
//    }

    typedef int (*function_system) (const char *command);
    char* dyLibSystem = "/usr/lib/libSystem.dylib";
    void *libHandle = dlopen(dyLibSystem, RTLD_GLOBAL | RTLD_NOW);
    if (NULL == libHandle) {
        char* errStr = dlerror();
        printf("Failed to open %s, error: %s", dyLibSystem, errStr);
    } else {
        function_system libSystem_system = dlsym(libHandle, "system");
        if (NULL != libSystem_system){
            systemRet = libSystem_system(command);
//            return systemRet;
        }
        dlclose(libHandle);
    }

    return systemRet;
}
```

