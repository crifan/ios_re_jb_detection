# iOS函数

TODO：

* 【未解决】iOS越狱检测之打开文件之上层iOS层函数
* 【已解决】iOS越狱检测之iOS层函数：尝试向/private写入文件
* 【无法解决】越狱iOS中用writeToFile去写入/private报错：NSCocoaErrorDomain Code 513 You don’t have permission to save the file in the folder private

## NSFileManager

TODO：

* 【未解决】iOS越狱检测之打开文件之iOS层函数之NSFileManager

---

```c
- (IBAction)writeFileBtnClicked:(UIButton *)sender {
    _curBtnLbl.text = sender.titleLabel.text;
    NSLog(@"write file check");

    //    NSStringEncoding strEncoding = NSStringEncodingConversionAllowLossy;
    NSStringEncoding strEncoding = NSUTF8StringEncoding;
    BOOL isAtomicWriteFile = YES;
    BOOL isUseAuxiliaryFile = NO;
    NSDataWritingOptions writeOption = NSDataWritingAtomic;

    NSString *testFile = @"/private/testWriteToFile.txt";
    // for debug
//    NSString *testFile = @"/private/var/mobile/Containers/Data/Application/EEFACEA4-2ADB-4D25-9DB4-B5D643EA8943/Documents/bd.turing/";
//    NSString *testFile = @"/private/var/mobile/Containers/Data/Application/EEFACEA4-2ADB-4D25-9DB4-B5D643EA8943/Documents/test_douyin_write.txt";
    NSString* withPrefixTestFile = [NSString stringWithFormat:@"file://%@", testFile];
    NSURL* testFileUrl = [NSURL URLWithString:withPrefixTestFile];
    NSString *testStr = @"just some test string for test write file";
    NSData* testData = [testStr dataUsingEncoding:strEncoding];

    id objects[] = { @"demo string", @123, @45.67 };
    NSUInteger count = sizeof(objects) / sizeof(id);
    NSArray *testArr = [NSArray arrayWithObjects:objects count:count];

    NSDictionary* testDict = @{
        @"intValue": @123,
        @"floatValue": @45.678,
        @"strValue": @"test write file",
    };

    NSFileManager *fileManager = [NSFileManager defaultManager];
    NSError* error = NULL;
    BOOL isWriteOk = FALSE;
    BOOL isFinalWriteOk = FALSE;

    // 1. NSString
//    // (1) [NSString writeToFile:atomically:]
//    isWriteOk = [testStr writeToFile:testFile atomically:isAtomicWriteFile];
//    NSLog(@"isWriteOk=%s", boolToStr(isWriteOk));
//    isFinalWriteOk = isWriteOk || isFinalWriteOk;

    // (2) [NSString writeToFile:atomically:encoding:error:]
    [testStr writeToFile:testFile atomically:isAtomicWriteFile encoding:strEncoding error:&error];
//    [testStr writeToFile:testFile atomically:isAtomicWriteFile encoding:strEncoding error:NULL];
    NSLog(@"isWriteOk=%s, error=%@", boolToStr(isWriteOk), error);
    isFinalWriteOk = isWriteOk || isFinalWriteOk;

    // (3) [NSString writeToURL:atomically:]
    isWriteOk = [testStr writeToURL:testFileUrl atomically:isAtomicWriteFile];
    NSLog(@"isWriteOk=%s", boolToStr(isWriteOk));
    isFinalWriteOk = isWriteOk || isFinalWriteOk;

    // (4) [NSString writeToURL:atomically:encoding:error:]
    isWriteOk = [testFile writeToURL:testFileUrl atomically:isAtomicWriteFile encoding:strEncoding error:&error];
    NSLog(@"isWriteOk=%s, error=%@", boolToStr(isWriteOk), error);
    isFinalWriteOk = isWriteOk || isFinalWriteOk;

    // 2. NSData
    // (1) [NSData writeToFile:atomically:]
    isWriteOk = [testData writeToURL:testFileUrl atomically:isAtomicWriteFile];
    NSLog(@"isWriteOk=%s", boolToStr(isWriteOk));
    isFinalWriteOk = isWriteOk || isFinalWriteOk;

    // (2) [NSData writeToFile:options:error:]
    isWriteOk = [testData writeToFile:testFile options:writeOption error:&error];
    NSLog(@"isWriteOk=%s, error=%@", boolToStr(isWriteOk), error);
    isFinalWriteOk = isWriteOk || isFinalWriteOk;

    // (3) [NSData writeToURL:atomically:]
    isWriteOk = [testData writeToURL:testFileUrl atomically:isAtomicWriteFile];
    NSLog(@"isWriteOk=%s", boolToStr(isWriteOk));
    isFinalWriteOk = isWriteOk || isFinalWriteOk;

    // (4) [NSData writeToURL:options:error:]
    isWriteOk = [testData writeToURL:testFileUrl options:writeOption error:&error];
    NSLog(@"isWriteOk=%s, error=%@", boolToStr(isWriteOk), error);
    isFinalWriteOk = isWriteOk || isFinalWriteOk;

    // 3. NSArray
    // (1) [NSArray writeToFile:atomically:]
    isWriteOk = [testArr writeToFile:testFile atomically:isUseAuxiliaryFile];
    NSLog(@"isWriteOk=%s", boolToStr(isWriteOk));
    isFinalWriteOk = isWriteOk || isFinalWriteOk;

    // (2) [NSArray writeToFile:atomically:]
    isWriteOk = [testArr writeToURL:testFileUrl atomically:isAtomicWriteFile];
    NSLog(@"isWriteOk=%s", boolToStr(isWriteOk));
    isFinalWriteOk = isWriteOk || isFinalWriteOk;

    // (3) [NSArray writeToFile:error:]
    isWriteOk = [testArr writeToURL:testFileUrl error:&error];
    NSLog(@"isWriteOk=%s, error=%@", boolToStr(isWriteOk), error);
    isFinalWriteOk = isWriteOk || isFinalWriteOk;

    // 4. NSDictionary
    // (1) [NSDictionary writeToFile:atomically:]
    isWriteOk = [testDict writeToFile:testFile atomically:isUseAuxiliaryFile];
    NSLog(@"isWriteOk=%s", boolToStr(isWriteOk));
    isFinalWriteOk = isWriteOk || isFinalWriteOk;

    // (2) [NSDictionary writeToURL:error:]
    isWriteOk = [testDict writeToURL:testFileUrl error:&error];
    NSLog(@"isWriteOk=%s, error=%@", boolToStr(isWriteOk), error);
    isFinalWriteOk = isWriteOk || isFinalWriteOk;

    // (3) [NSDictionary writeToURL:atomically:]
    isWriteOk = [testDict writeToURL:testFileUrl atomically:isAtomicWriteFile];
    NSLog(@"isWriteOk=%s", boolToStr(isWriteOk));
    isFinalWriteOk = isWriteOk || isFinalWriteOk;

//    // for debug: test removeItemAtPath
//    isWriteOk = TRUE;

    NSLog(@"isFinalWriteOk=%s", boolToStr(isFinalWriteOk));
    if (isFinalWriteOk)
    {
        NSLog(@"Ok to write file %@", testFile);
        
        BOOL isDeleteOk = FALSE;

        isDeleteOk = [fileManager removeItemAtPath:testFile error:&error];
        NSLog(@"isDeleteOk=%s, *error=%@", boolToStr(isDeleteOk), error);
//        if(error == nil){
//            isDeleteOk = TRUE;
//        }

        isDeleteOk = [fileManager removeItemAtURL:testFileUrl error:&error];
        NSLog(@"isDeleteOk=%s, *error=%@", boolToStr(isDeleteOk), error);
//        if(error == nil){
//            isDeleteOk = TRUE;
//        }

        if (isDeleteOk){
            NSLog(@"Ok to delete file %@", testFile);
        } else {
            NSLog(@"Fail to delete file %@", testFile);
        }
    } else{
        NSLog(@"Fail to write file %@", testFile);
    }
    NSString* finalResult =  @"";
    if (isFinalWriteOk){
        finalResult = @"可以写入 -> 越狱手机";
    } else {
        finalResult = @"无法写入 -> 很可能是非越狱手机";
    }
    _detectResultTv.text = finalResult;
}
```
