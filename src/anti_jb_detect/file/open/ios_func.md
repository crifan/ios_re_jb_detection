# iOS函数

## NSFileManager

TODO：

* 【已解决】iOS反越狱检测：NSFileManager的fileExistsAtPath

```c
/*==============================================================================
 Hook: NSFileManager
==============================================================================*/

//@interface NSFileManager (TweakMethods)
//+ (BOOL) isJailbreakPath_iOS: (NSString*)curPath;
//@end

%hook NSFileManager

///*  Common Util Function */
//
//%new
//+ (BOOL) isJailbreakPath_iOS: (NSString*)curPath{
////- (BOOL) isJailbreakPath_iOS: (NSString*)curPath{
//    BOOL isJbPath = FALSE;
//
//    if (NULL != curPath){
//        const char* curPathStr = [curPath UTF8String];
////        isJbPath = isJailbreakPath(curPathStr);
//        const char* FILE_PREFIX = "file://";
//
////        const char* pathNoFilePrefix = removeHead(curPathStr, FILE_PREFIX);
//        char* toFreePtr = NULL;
//        const char* pathNoFilePrefix = removeHead(curPathStr, FILE_PREFIX, &toFreePtr);
//
//        isJbPath = isJailbreakPath(pathNoFilePrefix);
//
////        free(pathNoFilePrefix);
////        if (NULL != toFreePtr) {
//        iosLogDebug("now to free: toFreePtr=%p", toFreePtr);
//        free(toFreePtr);
////        }
//    }
//    iosLogDebug("curPath=%{public}@ -> isJbPath=%s", curPath, boolToStr(isJbPath));
//    return isJbPath;
//}

- (NSArray<NSString *> *)contentsOfDirectoryAtPath:(NSString *)path error:(NSError * _Nullable *)error
{
    iosLogDebug("path=%{public}@, *error=%@", path, ERROR_STR(error));
    NSArray<NSString *> * retContentList = NULL;
    BOOL isJbPath = FALSE;

    if (cfgHookEnable_openFileiOS) {
        if (NULL != path) {
            isJbPath = [JailbreakiOS isJailbreakPath_iOS: path];
            if (isJbPath){
                retContentList = NULL;
            } else {
//                retContentList = %orig(path, error);
                retContentList = %orig;
            }
        }
    } else {
        retContentList = %orig;
    }

    // for debug
   if (isJbPath){
        iosLogInfo("path=%{public}@, *error=%@ -> isJbPath=%{bool}d -> retContentList=%p", path, ERROR_STR(error), isJbPath, retContentList);
   }
    return retContentList;
}

- (BOOL)fileExistsAtPath:(NSString *)path
{
    iosLogDebug("path=%{public}@", path);
    bool isExists = FALSE;
    BOOL isJbPath = FALSE;

    if (cfgHookEnable_openFileiOS) {
        if (NULL != path){
            isJbPath = [JailbreakiOS isJailbreakPath_iOS: path];
            if(isJbPath){
                isExists = FALSE;
            } else{
//                isExists = %orig(path);
                isExists = %orig;
            }
        }
    } else {
        isExists = %orig;
    }

    // for debug
   if (isJbPath){
        iosLogInfo("path=%{public}@ -> isJbPath=%s -> isExists=%s", path, boolToStr(isJbPath), boolToStr(isExists));
   }

    return isExists;
}

- (BOOL)fileExistsAtPath:(NSString *)path isDirectory:(BOOL *)isDirectory
{
    iosLogDebug("path=%{public}@, isDirectory=%p", path, isDirectory);
    BOOL isJbPath = FALSE;
    BOOL isExists = FALSE;

    if (cfgHookEnable_openFileiOS) {
        if (NULL != path) {
            isJbPath = [JailbreakiOS isJailbreakPath_iOS: path];
            if(isJbPath){
                isExists = FALSE;
            } else{
//                isExists = %orig(path, isDirectory);
                isExists = %orig;
            }
        }
    } else {
        isExists = %orig;
    }

    // for debug
   if (isJbPath){
        iosLogInfo("path=%{public}@, isDirectory=%p -> isJbPath=%s -> isExists=%s", path, isDirectory, boolToStr(isJbPath), boolToStr(isExists));
   }

	return isExists;
}

%end
```

## NSURL

```c
/*==============================================================================
 Hook: NSURL
==============================================================================*/

%hook NSURL

- (BOOL)checkResourceIsReachableAndReturnError:(NSError * _Nullable *)error{
    NSString* curUrlStr = [self absoluteString];
    iosLogDebug("curUrlStr=%{public}@, error=%p", curUrlStr, error);
    BOOL isJbPath = FALSE;
    BOOL isReachable = FALSE;

    if (cfgHookEnable_openFileiOS) {
        isJbPath = [JailbreakiOS isJailbreakPath_iOS: curUrlStr];
        if(isJbPath){
            isReachable = FALSE;
        } else{
//            isReachable = %orig(error);
            isReachable = %orig;
        }
    } else {
        isReachable = %orig;
    }

    // for debug
   if (isJbPath) {
        iosLogInfo("curUrlStr=%{public}@, error=%p -> isJbPath=%s -> isReachable=%s", curUrlStr, error, boolToStr(isJbPath), boolToStr(isReachable));
   }
    return isReachable;
}

%end
```
