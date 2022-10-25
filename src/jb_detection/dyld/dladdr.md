# `dladdr`

TODO：

* 【部分解决】iOS越狱检测：用dladdr解析函数所属动态库
* 【无需解决】已越狱iOS且已反越狱但dladdr仍是系统库libsystem_kernel.dylib
* 【已解决】iOS反越狱检测：dyld的dladdr

---

```c
- (IBAction)dladdrBtnClicked:(UIButton *)sender {
    _curBtnLbl.text = sender.titleLabel.text;
    NSLog(@"dladdr check");

    const int DLADDR_FAILED = 0;

    const char* curSystemLib = NULL;
    char* curTestFuncName = NULL;
    Dl_info dylib_info;

    const char* SystemLib_kernel = "/usr/lib/system/libsystem_kernel.dylib";
    curSystemLib = SystemLib_kernel;
    curTestFuncName = "stat";
    int (*func_stat)(const char *, struct stat *) = stat;
    int ret = dladdr(func_stat, &dylib_info);

//    const char* SystemLib_c = "/usr/lib/system/libsystem_c.dylib";
//    curSystemLib = SystemLib_c;
//    curTestFuncName = "fopen";
//    FILE* (*func_fopen)(const char *filename, const char *mode) = fopen;
//    int ret = dladdr(func_fopen, &dylib_info);

    NSLog(@"dladdr ret=%d", ret);

    NSString *dladdrResultStr = @"";
    if (DLADDR_FAILED != ret){
        NSString* conclusionStr = @"";
        const char* libName = dylib_info.dli_fname;
        NSLog(@"dladdr dli_fname=%s, dli_fbase=%p, dli_sname=%s, dli_saddr=%p", libName, dylib_info.dli_fbase, dylib_info.dli_sname, dylib_info.dli_saddr);

        if (0 == strcmp(libName, curSystemLib)){
            conclusionStr = @"是系统库 -> 非越狱手机";
        } else {
            conclusionStr = @"不是系统库 -> 越狱手机";
        }

        dladdrResultStr = [NSString stringWithFormat:@"解析成功 -> %s 所属动态库: %s -> %@", curTestFuncName, libName, conclusionStr];
    } else{
        NSLog(@"dladdr failed: ret=%d", ret);
        dladdrResultStr = [NSString stringWithFormat:@"无法解析，返回值=%d", ret];
    }
    NSLog(@"dladdr: %@", dladdrResultStr);

    _detectResultTv.text = dladdrResultStr;
}
```
