# URL Scheme

TODO：

* 【已解决】iOS的canOpenURL报错：failed for URL OSStatus error -10814
* 【已解决】iOS越狱检测：cydia://开头的URL scheme
* 【已解决】iOS中canOpenURL测试普通可以打开的app的url scheme
* 【已解决】iOS的canOpenURL报错：This app is not allowed to query for scheme weixin
* 

---

```c

- (IBAction)detectCydiaBtnClicked:(UIButton *)sender {
    _curBtnLbl.text = sender.titleLabel.text;
    NSLog(@"Clicked detect cydia://");
    BOOL canOpen = FALSE;

//    NSString *fakeCydiaStr = @"cydia://package/com.fake.packagename";
//    NSString *fakeCydiaStr = @"CYDIA://package/com.fake.packagename";
//    NSString *fakeCydiaStr = @"Cydia://package/xxx";
//    NSString *openPrefAbout = @"Prefs:root=General&path=About";
//    NSString *openPrefAbout = @"prefs:root=General&path=About";

    NSString *curToOpenStr = NULL;

//    curToOpenStr = @"weixin://";
    curToOpenStr = @"cydia://";

    NSURL *curToOpenUrl = [NSURL URLWithString:curToOpenStr];
    canOpen = [[UIApplication sharedApplication] canOpenURL:curToOpenUrl];
    NSString *canOpenStr = canOpen ? @"可以打开": @"无法打开";
    NSString *conclusionStr = canOpen ? @"可能是越狱手机": @"很可能不是越狱手机";
    NSString *resultStr = [NSString stringWithFormat:@"%@: %@\n-> %@", canOpenStr, curToOpenUrl, conclusionStr];
    NSLog(@"resultStr=%@", resultStr);
    _detectResultTv.text = resultStr;
}
```
