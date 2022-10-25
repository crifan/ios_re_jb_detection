# dlopen+dlsym

TODO：

* 【已解决】iOS越狱检测：正向的dlopen+dlsym
* 【记录】iOS中尝试dlopen和dlsym的system函数传入其他参数确保运行正常
  * 【已解决】iOS中调用system返回值始终是32512
* 【已解决】iOS反越狱检测：dyld的dlopen和dlsym

---

```c
- (IBAction)dlopenDlsymBtnClicked:(UIButton *)sender {
    _curBtnLbl.text = sender.titleLabel.text;
    NSLog(@"dlopen + dlsym check");

    typedef void (*function_common) (void *para);
//    typedef void (*lib_MSHookFunction)(void *symbol, void *hook, void **old);

    char* dylibPathList[] = {
//        // for debug
//        "/usr/lib/libstdc++.dylib",
//        "/usr/lib/libstdc++.6.dylib",
//        "/usr/lib/libstdc++.6.0.9.dylib",

        // common: tweak plugin libs
        "/usr/lib/libsubstrate.dylib",

        // Cydia Substrate libs
        "/Library/MobileSubstrate/MobileSubstrate.dylib",
        "/usr/lib/substrate/SubstrateInserter.dylib",
        "/usr/lib/substrate/SubstrateLoader.dylib",
        "/usr/lib/substrate/SubstrateBootstrap.dylib",

        // Substitute libs
        "/usr/lib/libsubstitute.dylib",
        "/usr/lib/substitute-inserter.dylib",
        "/usr/lib/substitute-loader.dylib",

        // Other libs
        "/usr/lib/tweakloader.dylib",
    };
    const int StrSize = sizeof(const char *);
    const int DylibLen = sizeof(dylibPathList) / StrSize;
    
    char* libFuncNameList[] = {
        "MSGetImageByName",
        "MSFindSymbol",
        "MSHookFunction",
        "MSHookMessageEx",
        
        "SubGetImageByName",
        "SubFindSymbol",
        "SubHookFunction",
        "SubHookMessageEx",
    };
    const int LibFuncLen = sizeof(libFuncNameList) / StrSize;

//    NSMutableArray *detectedJbDylibList = [NSMutableArray array];
//    NSMutableArray *detectedJbFuncNameList = [NSMutableArray array];
    NSMutableArray *detectedJbLibAndFuncList = [NSMutableArray array];

    for(int libIdx = 0; libIdx < DylibLen; libIdx++) {
        char* curDylib = dylibPathList[libIdx];
        void *curLibHandle = dlopen(curDylib, RTLD_GLOBAL | RTLD_NOW);
        if (NULL == curLibHandle) {
            char* errStr = dlerror();
            NSLog(@"Failed to open dylib %s, error: %s", curDylib, errStr);
        } else {
//            NSString* curDylibNs = [NSString stringWithFormat:@"%s", curDylib];
//            [detectedJbDylibList addObject:curDylibNs];

            for(int funcIdx = 0; funcIdx < LibFuncLen; funcIdx++) {
                char* curFuncName = libFuncNameList[funcIdx];
                function_common funcInLib = dlsym(curLibHandle, curFuncName);
                if (NULL != funcInLib){
                    NSLog(@"Found func %s=%p in dylib %s\n", curFuncName, funcInLib, curDylib);

//                    NSString* curFuncNameNs = [NSString stringWithFormat:@"%s", curFuncName];
//                    [detectedJbFuncNameList addObject:curFuncNameNs];
                    NSString* curLibAndFuncNs = [NSString stringWithFormat:@"%s -> %s", curDylib, curFuncName];
                    [detectedJbLibAndFuncList addObject:curLibAndFuncNs];
                }
            }

            dlclose(curLibHandle);
        }
    }

    NSString* finalResult =  @"";
//    BOOL isJb = (detectedJbDylibList.count > 0) || (detectedJbFuncNameList.count > 0);
//    NSString *detectedJbDylibListStr = [CrifanLibiOS nsStrListToStr:detectedJbDylibList isSortList:FALSE isAddIndexPrefix:TRUE];
//    NSString *detectedJbFuncNameListStr = [CrifanLibiOS nsStrListToStr:detectedJbFuncNameList isSortList:FALSE isAddIndexPrefix:TRUE];
//    NSString* detectedLibAndFuncNameStr = [NSString stringWithFormat:@"越狱库=%@\n库函数=%@", detectedJbDylibListStr, detectedJbFuncNameListStr] ;
    
    BOOL isJb = (detectedJbLibAndFuncList.count > 0);
    NSString *detectedJbLibAndFuncListStr = [CrifanLibiOS nsStrListToStr:detectedJbLibAndFuncList isSortList:FALSE isAddIndexPrefix:TRUE];
    NSString* detectedLibAndFuncNameStr = [NSString stringWithFormat:@"越狱库和库函数=%@", detectedJbLibAndFuncListStr];

    if (isJb){
        finalResult = [NSString stringWithFormat:@"检测出越狱库或库函数 -> 越狱手机\n%@", detectedLibAndFuncNameStr] ;
    } else {
        finalResult = @"未检测出越狱库和库函数 -> 非越狱手机";
    }
    NSLog(@"finalResult=%@", finalResult);
    _detectResultTv.text = finalResult;
}
```