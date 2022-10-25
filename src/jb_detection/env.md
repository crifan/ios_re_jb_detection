# 环境变量

TODO

* 【已解决】iOS越狱测试：getenv获取其他DYLD的环境变量值
* 【未解决】越狱iPhone中getenv获取DYLD_INSERT_LIBRARIES返回为空
* 【已解决】iOS反越狱检测：dyld的getenv获取环境变量DYLD_INSERT_LIBRARIES

---

```c
- (IBAction)getenvDyInsLibBtnClicked:(UIButton *)sender {
    _curBtnLbl.text = sender.titleLabel.text;
    NSLog(@"getenv(DYLD_INSERT_LIBRARIES) check");

    char* dyldPrintEnv = getenv("DYLD_PRINT_ENV");
    NSLog(@"dyldPrintEnv=%s", dyldPrintEnv);

    char* insertLibs = getenv("DYLD_INSERT_LIBRARIES");
    NSLog(@"insertLibs=%s", insertLibs);
    
    const char* dyldEnvList[] = {
        "DYLD_FRAMEWORK_PATH",
        "DYLD_FALLBACK_FRAMEWORK_PATH",
        "DYLD_VERSIONED_FRAMEWORK_PATH",
        "DYLD_LIBRARY_PATH",
        "DYLD_FALLBACK_LIBRARY_PATH",
        "DYLD_VERSIONED_LIBRARY_PATH",
        "DYLD_ROOT_PATH",
        "DYLD_SHARED_REGION",
        "DYLD_INSERT_LIBRARIES",
        "DYLD_FORCE_FLAT_NAMESPACE",
        "DYLD_IMAGE_SUFFIX",
        "DYLD_PRINT_OPTS",
        "DYLD_PRINT_ENV",
        "DYLD_PRINT_LIBRARIES",
        "DYLD_PRINT_LIBRARIES_POST_LAUNCH",
        "DYLD_BIND_AT_LAUNCH",
        "DYLD_NO_FIX_PREBINDING",
        "DYLD_DISABLE_DOFS",
        "DYLD_PRINT_APIS",
        "DYLD_PRINT_BINDINGS",
        "DYLD_PRINT_INITIALIZERS",
        "DYLD_PRINT_REBASINGS",
        "DYLD_PRINT_SEGMENTS",
        "DYLD_PRINT_STATISTICS",
        "DYLD_PRINT_DOFS",
        "DYLD_PRINT_RPATHS",
        "DYLD_SHARED_CACHE_DIR",
        "DYLD_SHARED_CACHE_DONT_VALIDATE",
    };
    const int dyldEnvListLen = sizeof(dyldEnvList)/sizeof(const char *);

    for(int curIdx = 0; curIdx < dyldEnvListLen; curIdx++){
        const char* curDyldEnv = dyldEnvList[curIdx];
        char* curEnvRet = getenv(curDyldEnv);
        NSLog(@"dyld: [%d] %s -> %s", curIdx, curDyldEnv, curEnvRet);
    }

    NSString* insertLibResultStr = @"";
    
    if (NULL != insertLibs){
        insertLibResultStr = [NSString stringWithFormat: @"检测出DYLD_INSERT_LIBRARIES -> 越狱手机; DYLD_INSERT_LIBRARIES=%s", insertLibs];
    } else{
        insertLibResultStr = @"未检测出DYLD_INSERT_LIBRARIES -> 非越狱手机";
    }
    NSLog(@"dyld: insertLibResultStr=%@", insertLibResultStr);

    _detectResultTv.text = insertLibResultStr;
}
```
