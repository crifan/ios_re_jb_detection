# iOS函数

TODO：

【已解决】iOS中NSURL的checkResourceIsReachableAndReturnError底层调用lstat返回文件结果异常

---

## NSFileManager

```objc
    } else if (FUNC_NSFILEMANAGER == funcType) {
        NSFileManager *defaultManager = [NSFileManager defaultManager];
        NSString* filePathNsStr = [NSString stringWithFormat:@"%s", filePathStr];

        BOOL isExisted = [defaultManager fileExistsAtPath: filePathNsStr];
        NSLog(@"isExisted=%s", boolToStr(isExisted));

        BOOL isDir = FALSE;
        BOOL isExistedWithDir = [defaultManager fileExistsAtPath:filePathNsStr isDirectory: &isDir];
        NSLog(@"isExistedWithDir=%s, isDir=%s", boolToStr(isExistedWithDir), boolToStr(isDir));

        isOpenOk = isExisted || isExistedWithDir;

        NSString* curResultStr = @"";

        if(isExisted){
            curResultStr = [NSString stringWithFormat:@"%@ 是否是目录：%@", @"路径存在", isDir ? @"是":@"否"];
        } else{
            curResultStr = [NSString stringWithFormat:@"%@", @"路径不存在"];
        }

        NSLog(@"fileExistsAtPath %@ -> %@", filePathNsStr, curResultStr);
```

## NSURL

```objc
    } else if (FUNC_NSURL == funcType) {
        NSString* fileStr = [NSString stringWithUTF8String:filePathStr];
        NSString* fileWithFilePrefix = [NSString stringWithFormat:@"file://%@", fileStr];
        NSURL* fileUrl = [NSURL URLWithString:fileWithFilePrefix];
        NSError* error = NULL;
        BOOL isReachable = [fileUrl checkResourceIsReachableAndReturnError:&error];
        NSLog(@"isReachable=%s, error=%@", boolToStr(isReachable), (error != NULL) ? error : @"");

        // for debug
        if (isReachable){
            NSLog(@"fileStr=%@", fileStr);
        }

        isOpenOk = isReachable;
        NSLog(@"NSURL checkResourceIsReachableAndReturnError %@ -> %s", fileStr, boolToStr(isReachable));
```
