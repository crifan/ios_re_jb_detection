# 已安装app

```c
- (IBAction)lsapplicationBtnClicked:(UIButton *)sender {
    _curBtnLbl.text = sender.titleLabel.text;
    NSLog(@"LSApplication check");
    NSString* resultStr = @"TODO";

    Class LSApplicationWorkspace_class = objc_getClass("LSApplicationWorkspace");
    NSObject* workspace = [LSApplicationWorkspace_class performSelector:@selector(defaultWorkspace)];
    NSArray *allAppList = [workspace performSelector:@selector(allApplications)]; //这样就能获取到手机中安装的所有App

    resultStr = [NSString stringWithFormat: @"已安装app总数: %d", [allAppList count]];
    resultStr = [NSString stringWithFormat: @"%@\n非系统app列表：", resultStr];

    for (int i=0; i<[allAppList count]; i++) {
//        LSApplicationProxy *appProxy = [allAppList objectAtIndex:i];
//        LSApplicationProxy_class *appProxy = [allAppList objectAtIndex:i];
//        NSString* bundleId =[appProxy applicationIdentifier];
//        NSString* name = [appProxy localizedName];

        id appProxy = [allAppList objectAtIndex:i];
        NSString* bundleId =[appProxy performSelector:@selector(applicationIdentifier)];
        NSString* name = [appProxy performSelector:@selector(localizedName)];
        NSString* version = [appProxy performSelector:@selector(bundleVersion)];
        NSObject *description = [appProxy performSelector:@selector(description)];
        NSArray *plugInKitPlugins = [appProxy performSelector:@selector(plugInKitPlugins)];
        if(![bundleId hasPrefix: @"com.apple."]) {
            resultStr = [NSString stringWithFormat: @"%@\n[%d] bundleId=%@, name=%@, version=%@, description=%@, plugInKitPlugins=%@", resultStr, i, bundleId, name, version, description, plugInKitPlugins];
        }
    }

//    Class LSApplicationProxy_class = object_getClass(@"LSApplicationProxy");
//
//    for (LSApplicationProxy_class in allAppList) {
//        NSString *bundleId = [LSApplicationProxy_class performSelector:@selector(applicationIdentifier)];
//        NSString *version = [LSApplicationProxy_class performSelector:@selector(bundleVersion)];
//    }

    NSLog(@"resultStr=%@", resultStr);
    _detectResultTv.text = resultStr;
}
```
