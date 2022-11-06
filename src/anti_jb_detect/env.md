# 环境变量

```c
/*==============================================================================
 Hook: getenv(DYLD_INSERT_LIBRARIES)
==============================================================================*/

char * getenv(const char* name);
const char* DYLD_INSERT_LIBRARIES = "DYLD_INSERT_LIBRARIES";

%hookf(char *, getenv, const char* name){
    //    char* getenvRetStr = %orig(name);
    char* getenvRetStr = %orig;

    if (cfgHookEnable_misc) {
        //    iosLogDebug("name=%s", name);
        //    NSLog(@"getenv name");

        // "_CFXNOTIFICATIONREGISTAR2_ENABLED" will cause crash
        if (strStartsWith(name, "DYLD_")){
//        if (!strStartsWith(name, "_")){
//            iosLogInfo("not start with '_', name=%s", name);
            iosLogInfo("DYLD_ name=%s", name);
        }

        if(0 == strcmp(name, DYLD_INSERT_LIBRARIES)){
            iosLogInfo("name=%s -> getenvRetStr=%{public}s", name, getenvRetStr);
            getenvRetStr = NULL;
        } else {
            if (strStartsWith(name, "DYLD_")){
                iosLogInfo("name=%s -> getenvRetStr=%{public}s", name, getenvRetStr);
            }
        }
    }

    return getenvRetStr;
}
```
