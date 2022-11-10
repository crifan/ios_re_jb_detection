# app本身

TODO：

* 【未解决】iOS逆向：绕过重签名检测embedded.mobileprovision

---

## NSBundle

```c
/*==============================================================================
 Hook: debugging embedded.mobileprovision
==============================================================================*/

// NSString *embeddedPath = [[NSBundle mainBundle] pathForResource:@"embedded" ofType:@"mobileprovision"];
%hook NSBundle

- (NSString *)pathForResource:(NSString *)name ofType:(NSString *)ext {
    NSString* resPath = %orig(name, ext);

    if (cfgHookEnable_aweme) {
        if ([ext isEqualToString: @"mobileprovision"]){
            iosLogInfo("name=%{public}@, ext=%{public}@ -> resPath=%{public}@", name, ext, resPath);
            if ([name isEqualToString: @"embedded"]){
                resPath = NULL;
            }
        }
    }

    return resPath;
}

// https://developer.apple.com/documentation/foundation/nsbundle/1407973-bundlepath
// @property(readonly, copy) NSString *bundlePath;

- (NSString *)bundlePath {
    NSString* origBundlePath = %orig;
    BOOL shouldOmit = [origBundlePath containsString: @"Aweme"] || [origBundlePath containsString: @"/System/Library"];
    if (!shouldOmit){
        iosLogInfo("origBundlePath=%{public}@", origBundlePath);
    }
    return origBundlePath;
}

%end
```
