# ObjC运行时

## objc_copyImageNames

```c
/*==============================================================================
 Hook: objc_copyImageNames
==============================================================================*/

//const char * _Nonnull * objc_copyImageNames(unsigned int *outCount);
const char ** objc_copyImageNames(unsigned int *outCount);

%hookf(const char **, objc_copyImageNames, unsigned int *outCount){
    iosLogInfo("outCount=%p", outCount);
    const char** imageList = %orig(outCount);
    iosLogInfo("*outCount=%d, imageList=%p", *outCount, imageList);
    if (cfgHookEnable_aweme) {
        // TODO: add support

        if ((*outCount > 0) && (imageList != NULL)) {
            for (int i = 0; i < *outCount; i++) {
                const char* curImagePath = imageList[i];
                bool isJbPath = isJailbreakPath(curImagePath);
                if (isJbPath) {
                    iosLogInfo("[%d] %s -> isJbPath=%s", i, curImagePath, boolToStr(isJbPath));
                }
            }
        }
    }
    return imageList;
}
```
