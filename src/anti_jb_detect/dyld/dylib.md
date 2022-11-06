# dylib

TODO：

* 【已解决】iOS反越狱检测：dladdr的hook绕过
* 【已解决】iOS反越狱检测：逆向hook的dlopen+dlsym

---

## dladdr

```c

/*==============================================================================
 Hook: dladdr()
==============================================================================*/
// https://developer.apple.com/library/archive/documentation/System/Conceptual/ManPages_iPhoneOS/man3/dladdr.3.html


/*==============================================================================
    hook dladdr()
==============================================================================*/

void* generateHookedDladdrAddress(void *origAddr);

const long DLADDR_HOOKED_ADDRESS_BASE = 0xF00000000000;
//const unsigned long DLADDR_HOOKED_ADDRESS_MAX = 0xFFFF000000000000;

void* generateHookedDladdrAddress(void *origAddr) {
//    if ((long)origAddr < (long)DLADDR_HOOKED_ADDRESS_MAX) {
    void* hookedAddr = origAddr;
    if ((long)origAddr > (long)DLADDR_HOOKED_ADDRESS_BASE) {
        hookedAddr = origAddr;
    } else {
        hookedAddr = (void*)((long)origAddr + DLADDR_HOOKED_ADDRESS_BASE);
    }
    return hookedAddr;
}

static bool isHookedDladdrAddress(const void *addr){
    bool isHookedAddr = false;
    long addrLong = (long) addr;
//    if ((addrLong > DLADDR_HOOKED_ADDRESS_BASE) && (addrLong < DLADDR_HOOKED_ADDRESS_MAX)) {
    if (addrLong > DLADDR_HOOKED_ADDRESS_BASE) {
        isHookedAddr = true;
    }

    return isHookedAddr;
}

static void* hookedToOrigDladdrAddr(const void *hookedAddr){
    return (void*) ( (long)hookedAddr - DLADDR_HOOKED_ADDRESS_BASE );
}

int dladdr(const void *, Dl_info *);
//int dladdr(void *, Dl_info *);
//extern int dladdr(const void *, Dl_info *);

//%hookf(int, dladdr, void *addr, Dl_info *info){
%hookf(int, dladdr, const void *addr, Dl_info *info){
    iosLogDebug("addr=%p,info=%p", addr, info);
    int finalRet = DLADDR_FAILED;

    if (NULL == addr) {
        iosLogInfo("addr is %s", "NULL");
    } else {
        void* origAddr = (void*)addr;

        bool isHookedAddr = isHookedDladdrAddress(addr);
        if (isHookedAddr) {
            origAddr = hookedToOrigDladdrAddr(addr);

            iosLogDebug("addr=%p -> isHookedAddr=%s -> origAddr=%p", addr, boolToStr(isHookedAddr), origAddr);
            
            if (NULL == origAddr) {
                iosLogInfo("addr=%p -> isHookedAddr=%s -> origAddr=%p", addr, boolToStr(isHookedAddr), origAddr);
            }
        }
        
    //    int origRet = %orig;

        //        int origRet = DLADDR_FAILED;
//        if (NULL == origAddr) {
//            origRet = DLADDR_FAILED;
//        } else {
//            origRet = %orig(origAddr, info);
//        }

        int origRet = %orig(origAddr, info);
        finalRet = origRet;

        bool isNotHookedAddr = !isHookedAddr;
        bool isNeedHook = cfgHookEnable_dylib_dladdr && isNotHookedAddr;
        if (isNeedHook) {
            //    if (dladdrRetInt > 0) {
            if (DLADDR_FAILED != origRet) {
                if (NULL != info) {
                    const char* curImageName = info->dli_fname;
                    bool isJbDyib = isJailbreakDylib(curImageName);
                    if (isJbDyib) {
                        finalRet = DLADDR_FAILED;

                        iosLogInfo("addr=%p -> origRet=%d -> dli_fname=%{public}s, dli_fbase=%p, dli_sname=%{public}s, dli_saddr=%p -> isJbDyib=%s -> finalRet=%d", addr, origRet, info->dli_fname, info->dli_fbase, info->dli_sname, info->dli_saddr, boolToStr(isJbDyib), finalRet);
    //                    iosLogInfo("isJbDyib=%s", boolToStr(isJbDyib));
    //                    iosLogInfo("addr=%p -> origRet=%d", addr, origRet);
    //                    iosLogInfo("dli_fname=%{public}s, dli_fbase=%p, dli_sname=%{public}s, dli_saddr=%p", info->dli_fname, info->dli_fbase, info->dli_sname, info->dli_saddr);
    //                    iosLogInfo("finalRet=%d", finalRet);

                        size_t dlInfoSize = sizeof(Dl_info);
                        memset(info, 0, dlInfoSize);
                    }
                }
            }
        }
    }

    return finalRet;
}

/*
	TODO:
		https://developer.apple.com/library/archive/documentation/System/Conceptual/ManPages_iPhoneOS/man3/dyld.3.html
		https://man7.org/linux/man-pages/man3/dladdr.3.html
		may need support:
			int dladdr1(const void *addr, Dl_info *info, void **extra_info, int flags);
*/
```

## dlopen

```c

/*==============================================================================
 Hook: dlopen()
==============================================================================*/
// https://developer.apple.com/library/archive/documentation/System/Conceptual/ManPages_iPhoneOS/man3/dlopen.3.html
void* dlopen(const char* path, int mode);

%hookf(void*, dlopen, const char* path, int mode){
    iosLogDebug("path=%{public}s, mode=0x%x", path, mode);
    void* dlopenRetPtr = NULL;

    if (cfgHookEnable_dylib) {
        bool isJbDylib = isJailbreakDylib(path);
        if (isJbDylib) {
            dlopenRetPtr = NULL;
        } else {
//            dlopenRetPtr = %orig(path, mode);
            dlopenRetPtr = %orig;
        }

        if (isJbDylib) {
            iosLogInfo("path=%{public}s, mode=0x%x -> isJbDylib=%s -> dlopenRetPtr=%p", path, mode, boolToStr(isJbDylib), dlopenRetPtr);
        }
    } else {
//        dlopenRetPtr = %orig(path, mode);
        dlopenRetPtr = %orig;
    }

    return dlopenRetPtr;
}

////void* _dlopen(const char* path, int mode);
//void* __ZL15dlopen_internalPKciPv(const char* path, int mode);
//
////%hookf(void*, _dlopen, const char* path, int mode){
//%hookf(void*, __ZL15dlopen_internalPKciPv, const char* path, int mode){
//    iosLogInfo("path=%{public}s, mode=0x%x", path, mode);
//    return %orig;
//}
```

## dlclose

```c

/*==============================================================================
 Hook: dlclose()
==============================================================================*/

int dlclose(void* handle);

%hookf(int, dlclose, void* handle){
    bool isJbLib = false;

    Dl_info info;
    size_t dlInfoSize = sizeof(Dl_info);
    memset(&info, 0, dlInfoSize);

//        dladdr(mhp, &info);
    void* hookedAddr = generateHookedDladdrAddress(handle);
    dladdr(hookedAddr, &info);

    const char* curImgName = info.dli_fname;
    if(curImgName != NULL) {
        isJbLib = isJailbreakDylib(curImgName);
    }

    if (isJbLib) {
        iosLogInfo("handle=%p -> is jb lib: %s", handle, curImgName);
    }

    int closeRet = %orig;
    iosLogInfo("handle=%p -> closeRet=%d", handle, closeRet);
    return closeRet;
}
```

## dlsym

```c
/*==============================================================================
 Hook: dlsym()
==============================================================================*/
// https://developer.apple.com/library/archive/documentation/System/Conceptual/ManPages_iPhoneOS/man3/dlsym.3.html
void* dlsym(void* handle, const char* symbol);

%hookf(void*, dlsym, void* handle, const char* symbol) {
    iosLogDebug("handle=%p, symbol=%{public}s", handle, symbol);
    void* dlsymRetPtr = NULL;

    if (cfgHookEnable_dylib) {
        bool shouldHook = false;
        bool isJbFuncName = isJailbreakDylibFunctionName(symbol);
        bool isPtrace = 0 == strcmp(symbol, "ptrace");
        shouldHook = isJbFuncName || isPtrace;
        iosLogDebug("isPtrace=%s, shouldHook=%s", boolToStr(isPtrace), boolToStr(shouldHook));
    //    if (isJbFuncName) {
        if (shouldHook) {
            dlsymRetPtr = NULL;
        } else {
//            dlsymRetPtr = %orig(handle, symbol);
            dlsymRetPtr = %orig;
        }

    //    if (isJbFuncName) {
        if (shouldHook) {
    //        iosLogInfo("handle=%p, symbol=%{public}s -> isJbFuncName=%s -> dlsymRetPtr=%p", handle, symbol, boolToStr(isJbFuncName), dlsymRetPtr);
            iosLogInfo("handle=%p, symbol=%{public}s -> isJbFuncName=%s, isPtrace=%s -> shouldHook=%s -> dlsymRetPtr=%p", handle, symbol, boolToStr(isJbFuncName), boolToStr(isPtrace), boolToStr(shouldHook), dlsymRetPtr);
        }
    } else {
//        dlsymRetPtr = %orig(handle, symbol);
        dlsymRetPtr = %orig;
    }

    return dlsymRetPtr;
}
```
