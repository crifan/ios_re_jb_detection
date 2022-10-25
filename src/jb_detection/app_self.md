# app本身

## 重签名

TODO：

* 【已解决】iOS防护：签名校验重签名检测

```c

- (IBAction)reCodeSignBtnClicked:(UIButton *)sender {
    _curBtnLbl.text = sender.titleLabel.text;
    NSLog(@"re-CodeSign check");
    NSString* resultStr = @"TODO";
    
//    NSString *embeddedPath = [[NSBundle mainBundle] pathForResource:@"embedded" ofType:@"mobileprovision"]; // embeddedPath    __NSCFString *    @"/private/var/containers/Bundle/Application/4366136E-242E-4C5D-9CC8-CF100A0B6FB2/ShowSysInfo.app/embedded.mobileprovision"    0x0000000282c11830
//    if (![[NSFileManager defaultManager] fileExistsAtPath:embeddedPath]) {
//        return;
//    }

//    // 读取application-identifier  注意描述文件的编码要使用:NSASCIIStringEncoding
//    NSStringEncoding fileEncoding = NSASCIIStringEncoding;
////    NSStringEncoding fileEncoding = NSUTF8StringEncoding;
//    NSString *embeddedProvisioning = [NSString stringWithContentsOfFile:embeddedPath encoding:fileEncoding error:nil];
//    NSArray<NSString *> *embeddedProvisioningLines = [embeddedProvisioning componentsSeparatedByCharactersInSet:[NSCharacterSet newlineCharacterSet]];
//    for (int i = 0; i < embeddedProvisioningLines.count; i++) {
//        if ([embeddedProvisioningLines[i] rangeOfString:@"application-identifier"].location != NSNotFound) {
//            NSString *identifierString = embeddedProvisioningLines[i + 1]; // 类似：<string>L2ZY2L7GYS.com.xx.xxx</string>
//            NSRange fromRange = [identifierString rangeOfString:@"<string>"];
//            NSInteger fromPosition = fromRange.location + fromRange.length;
//            NSInteger toPosition = [identifierString rangeOfString:@"</string>"].location;
//            NSRange range;
//            range.location = fromPosition;
//            range.length = toPosition - fromPosition;
//            NSString *fullIdentifier = [identifierString substringWithRange:range];
//            NSScanner *scanner = [NSScanner scannerWithString:fullIdentifier];
//            NSString *teamIdString;
//            [scanner scanUpToString:@"." intoString:&teamIdString];
//            NSRange teamIdRange = [fullIdentifier rangeOfString:teamIdString];
//            NSString *appIdentifier = [fullIdentifier substringFromIndex:teamIdRange.length + 1];
//            // 对比签名teamID或者identifier信息
// //           if (![appIdentifier isEqualToString:identifier] || ![teamId isEqualToString:teamIdString]) {
//            
//            if (![appIdentifier isEqualToString: curAppId]) {
//                // exit(0)
//                asm(
//                    "mov X0,#0\n"
//                    "mov w16,#1\n"
//                    "svc #0x80"
//                    );
//            }
//            break;
//        }
//    }
    
    BOOL isExistCodesign = [CrifanLibiOS isCodeSignExist];
    
    if (isExistCodesign) {
//        NSString* curAppId = @"com.crifan.ShowSystemInfo";
        NSString* selfAppId = @"3WRHBBSBW4.*";
        NSString* gotAddId = [CrifanLibiOS getAppId];
//        BOOL isSelfId = [CrifanLibiOS isSelfAppId: curAppId];
//        BOOL isSelfId = FALSE;
        if ([gotAddId isEqualToString: selfAppId]) {
//            isSelfId = TRUE;
            resultStr = [NSString stringWithFormat: @"embedded.mobileprovision中是自己app的ID：%@ -> 合法app", selfAppId];
        } else {
//            isSelfId = FALSE;
            resultStr = [NSString stringWithFormat: @"embedded.mobileprovision中的app的ID是%@ != 自己的AppId %@ -> 非法app", gotAddId, selfAppId];
        }
    } else {
        resultStr = @"不存在embedded.mobileprovision";
    }

    NSLog(@"resultStr=%@", resultStr);
    _detectResultTv.text = resultStr;
}
```

## __RESTRICT

TODO：

* 【未解决】iOS越狱检测手段：__RESTRICT
