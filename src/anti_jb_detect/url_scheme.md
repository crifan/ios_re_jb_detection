# URL Scheme

TODO：

* 【已解决】iOS反越狱检测：绕过cydia://的url scheme
* 【未解决】iOS反越狱检测：能否打开特定开头的URL scheme

---

```c
const char* CydiaPrefix = "cydia://";

/*==============================================================================
 Hook: UIApplication canOpenURL:
==============================================================================*/

/*
 hook url scheme, eg: cydia://
 */

%hook UIApplication

const char* CydiaPrefix = "cydia://";

- (BOOL)canOpenURL:(NSURL *)url
{
    iosLogDebug("url=%{public}@", url);
    bool couldOpen = false;
    bool isCydia = false;

    if (cfgHookEnable_misc) {
        NSString *urlNSStr = [url absoluteString];
        const char* urlStr = [urlNSStr UTF8String];
        char* urlStrLower = strToLowercase(urlStr);
        iosLogDebug("urlStrLower=%s", urlStrLower);
        isCydia = strStartsWith(urlStrLower, CydiaPrefix);
        free(urlStrLower);
        iosLogDebug("isCydia=%{public}s", boolToStr(isCydia));

        if(isCydia){
            couldOpen = false;
        } else{
    //        couldOpen = %orig(url);
            couldOpen = %orig;
        }
    } else {
        couldOpen = %orig;
    }

    // for debug
//    if (isCydia) {
        iosLogInfo("url=%{public}@ -> isCydia=%{public}s -> couldOpen=%{public}s", url, boolToStr(isCydia), boolToStr(couldOpen));
//    }
    return couldOpen;
}

%end

```