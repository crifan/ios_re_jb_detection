# C函数

TODO：

* 【未解决】iOS越狱检测之打开文件方式

## open系列

TODO：

【未解决】iOS越狱检测之打开文件：open系列函数

### open

TODO：

* 【已解决】iOS越狱检测：iOS的app中用open打开文件
* 【基本解决】iOS越狱检测之打开文件：正向调用fopen

```c
        isUseFd = TRUE;
        retFd = open(filePathStr, O_RDONLY);
```

### opendir

TODO：

【已解决】iOS越狱检测之打开文件之底层C函数：opendir

```c
    } else if (FUNC_OPENDIR == funcType) {
        DIR* retDir = opendir(filePathStr);
        if (NULL != retDir){
            NSLog(@"opendir OK: filePathStr=%s -> retDir=%p", filePathStr, retDir);
            NSLog(@"\tDIR: __dd_fd=%d,__dd_loc=%ld,__dd_size=%ld,__dd_buf=%s,__dd_len=%d,__dd_seek=%ld,__padding=%ld,__dd_flags=%d",
                retDir->__dd_fd, retDir->__dd_loc, retDir->__dd_size, retDir->__dd_buf, retDir->__dd_len, retDir->__dd_seek, retDir->__padding, retDir->__dd_flags);
            isOpenOk = TRUE;
        } else {
            NSLog(@"opendir fail for filePathStr=%s", filePathStr);
            isOpenOk = FALSE;
        }

        NSLog(@"opendir filePathStr=%s -> retDir=%p -> isOpenOk=%s", filePathStr, retDir, boolToStr(isOpenOk));
```

### __opendir2

```c
    } else if (FUNC___OPENDIR2 == funcType) {
        DIR* retDir = __opendir2(filePathStr, DTF_HIDEW|DTF_NODUP);
        if (NULL != retDir){
            NSLog(@"__opendir2 OK: filePathStr=%s -> retDir=%p", filePathStr, retDir);
            NSLog(@"\tDIR: __dd_fd=%d,__dd_loc=%ld,__dd_size=%ld,__dd_buf=%s,__dd_len=%d,__dd_seek=%ld,__padding=%ld,__dd_flags=%d",
                retDir->__dd_fd, retDir->__dd_loc, retDir->__dd_size, retDir->__dd_buf, retDir->__dd_len, retDir->__dd_seek, retDir->__padding, retDir->__dd_flags);
            isOpenOk = TRUE;
        } else {
            NSLog(@"__opendir2 fail for filePathStr=%s", filePathStr);
            isOpenOk = FALSE;
        }

        NSLog(@"__opendir2 filePathStr=%s -> retDir=%p -> isOpenOk=%s", filePathStr, retDir, boolToStr(isOpenOk));
    }
```

## access系列

### access

TODO：

* 【已解决】iOS越狱检测之打开文件：access

```c
    } else if (FUNC_ACCESS == funcType) {
        int retValue = access(filePathStr, F_OK);
        NSLog(@"access %s -> %d", filePathStr, retValue);

        if (retValue != ACCESS_OK){
            isOpenOk = FALSE;
        } else {
            isOpenOk = TRUE;
        }
```

### faccessat

TODO：

* 【已解决】iOS越狱检测之打开文件：faccessat
* 【已解决】iOS中越狱检测之打开文件：faccessat正向检测

```c
    } else if (FUNC_FACCESSAT == funcType) {
        int curDirFd = 0;
        int retValue = ACCESS_FAILED;

//        // 1. test relative path
////        const char* curDir = "/private/var/mobile/Library/Filza/";
////        const char* curFile = "scripts/README.url";
//
////        const char* curDir = "/private/var/mobile/Library/";
////        const char* curFile = "Filza/scripts/README.url";
////        const char* curDir = "/private/./var/../var/mobile/Library/./";
////        const char* curFile = "Filza/./scripts/../scripts/README.url";
//        const char* curDir = "/usr/lib";
//        const char* curFile = "libsubstrate.dylib";
//
//        curDirFd = open(curDir, O_RDONLY);
//        NSLog(@"curDir=%s -> curDirFd=%d", curDir, curDirFd);
//
////        // for debug: get file path from fd
////        char filePath[PATH_MAX];
////        int fcntlRet = fcntl(curDirFd, F_GETPATH, filePath);
////        const int FCNTL_FAILED = -1;
////        if (fcntlRet != FCNTL_FAILED){
////            NSLog(@"fcntl OK: curDirFd=%d -> filePath=%s", curDirFd, filePath);
////        } else {
////            NSLog(@"fcntl fail for curDirFd=%d", curDirFd);
////        }
//
//        retValue = faccessat(curDirFd, curFile, F_OK, AT_EACCESS);
//        NSLog(@"faccessat curDir=%s,curFile=%s -> %d", curDir, curFile, retValue);

        // 2. test input path
        const int FAKE_FD = 0;
        curDirFd = FAKE_FD;
        retValue = faccessat(curDirFd, filePathStr, F_OK, AT_EACCESS);
        NSLog(@"faccessat curDirFd=%d, filePathStr=%s -> %d", curDirFd, filePathStr, retValue);

        if (retValue != ACCESS_FAILED){
            isOpenOk = TRUE;
        } else {
            isOpenOk = FALSE;
        }
```

## stat系列

TODO：

* 【未解决】iOS越狱检测之打开文件之stat系列函数

### stat

TODO：

* 【已解决】iOS越狱检测之打开文件：stat函数
* 【已解决】iOS用stat打开和检测文件是否存在检测是否越狱

```c
    if (FUNC_STAT == funcType){
        isUseStatInfo = TRUE;
        openResult = stat(filePathStr, &stat_info);
```

### lstat

TODO：

* 【已解决】iOS越狱检测之打开文件：lstat正向越狱检测
* 【已解决】lstat检测普通文件但却通过S_IFLNK误判出是软链接

```c
    } else if (FUNC_LSTAT == funcType) {
        isOpenOk = FALSE;
        bool isLink = FALSE;

        struct stat statInfo;
        int lstatRet = lstat(filePathStr, &statInfo);
        if (STAT_OK == lstatRet){
//            isLink = statInfo.st_mode & S_IFLNK;
            isLink = S_ISLNK(statInfo.st_mode);
            if (isLink) {
                isOpenOk = TRUE;
            }
        }

        NSLog(@"lstat filePathStr=%s -> isLink=%s -> isOpenOk=%s", filePathStr, boolToStr(isLink), boolToStr(isOpenOk));
```

### fstat

```c
    } else if (FUNC_FSTAT == funcType) {
        isOpenOk = FALSE;
        int tmpFd = open(filePathStr, O_RDONLY);

        if (tmpFd > 0){
            isOpenOk = TRUE;

            struct stat statInfo;
            memset(&statInfo, 0, sizeof(struct stat));
            int fstatRet = fstat(tmpFd, &statInfo);
            if (STAT_OK == fstatRet) {
                isOpenOk = TRUE;
            } else {
                isOpenOk = FALSE;
            }
        } else {
            // when fd < 0, normally is -1, means open file failed
            isOpenOk = FALSE;
            NSLog(@"open() failed for %@", filePath);
        }
```

### fstatfs

```c
    } else if (FUNC_FSTATFS == funcType) {
        isOpenOk = FALSE;
        int tmpFd = open(filePathStr, O_RDONLY);

        if (tmpFd > 0){
            isOpenOk = TRUE;

            struct statfs statfsInfo;
            memset(&statfsInfo, 0, sizeof(struct statfs));
            int fstatfsRet = fstatfs(tmpFd, &statfsInfo);
            if (STATFS_OK == fstatfsRet) {
                isOpenOk = TRUE;
            } else {
                isOpenOk = FALSE;
            }
        } else {
            // when fd < 0, normally is -1, means open file failed
            isOpenOk = FALSE;
            NSLog(@"open() failed for %@", filePath);
        }
```

## realpath

```c
    } else if (FUNC_REALPATH == funcType) {
        char parsedRealPath[PATH_MAX];
        char *resolvedPtr = realpath(filePathStr, parsedRealPath);
        if (NULL != resolvedPtr){
            NSLog(@"realpath OK: filePathStr=%s -> parsedRealPath=%s", filePathStr, parsedRealPath);
            isOpenOk = TRUE;
        } else {
            NSLog(@"realpath fail for filePathStr=%s", filePathStr);
            isOpenOk = FALSE;
        }
        NSLog(@"realpath filePathStr=%s -> isOpenOk=%s", filePathStr, boolToStr(isOpenOk));
```

