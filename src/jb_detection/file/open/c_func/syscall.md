# syscall

TODO：

* 【已解决】iOS的app中如何实现syscall函数调用
* 【已解决】iOS中用syscall调用stat64实现越狱文件检测
* 【整理】syscall内核系统调用和svc 0x80相关基础知识
  * 【整理】iOS中syscall的系统调用编号number的定义
  * 【整理】iOS中的带函数原型和说明的系统调用system call

---

## SYS_fork

```c
NSString * parseForkResult(int forkRetPid){
    NSString *forkResultStr = NULL;
    if (forkRetPid < 0){
        forkResultStr = @"无法fork->旧版iOS:非越狱, 新版iOS:无法判断";

        // log print erro info
        NSLog(@"errno=%d\n", errno);
        char *errMsg = strerror(errno);
        NSLog(@"errMsg=%s\n", errMsg);
    } else{
        forkResultStr = @"可以fork -> 旧版iOS：越狱手机";
    }

    return forkResultStr;
}

- (IBAction)syscallForkBtnClicked:(UIButton *)sender {
    _curBtnLbl.text = sender.titleLabel.text;
    NSLog(@"syscall(fork) check");
    int retPid = syscall(SYS_fork);

    NSString *forkResultStr = parseForkResult(retPid);
    NSLog(@"syscall(fork) return retPid=%d, forkResultStr=%@", retPid, forkResultStr);
    _detectResultTv.text = [NSString stringWithFormat:@"%@ -> %@", @"syscall(fork)", forkResultStr];
}
```

## SYS_stat

```c
    } else if (FUNC_SYSCALL_STAT == funcType) {
        isUseStatInfo = TRUE;
        //Note: for open normal file, return 0 is OK, but st_mode is abnormal !
        openResult = syscall(SYS_stat, filePathStr, &stat_info);
```

## SYS_stat64

```c
    } else if (FUNC_SYSCALL_STAT64 == funcType){
        isUseStatInfo = TRUE;
        openResult = syscall(SYS_stat64, filePathStr, &stat_info);
```

## SYS_lstat

```c
    } else if (FUNC_SYSCALL_LSTAT == funcType){
        isUseStatInfo = TRUE;
        openResult = syscall(SYS_lstat, filePathStr, &stat_info);
```

## SYS_fstat

```c
    } else if (FUNC_SYSCALL_FSTAT == funcType){
        isUseStatInfo = TRUE;

        int curFd = open(filePathStr, O_RDONLY);
        if (curFd > 0){
            openResult = syscall(SYS_fstat, curFd, &stat_info);
        } else {
            isOpenOk = FALSE;
        }
```

## SYS_fstatat

```c
    } else if (FUNC_SYSCALL_FSTATAT == funcType){
        // NOTE: syscall(SYS_fstatat) not work until 20220316 -> awalys return -1

        // int fstatat(int fd, const char* pathname, struct stat* buf, int flags);
        isUseStatInfo = TRUE;

//        int curFd = open(filePathStr, O_RDONLY);
//        if (curFd > 0){
//            openResult = syscall(SYS_fstatat, curFd, filePathStr, &stat_info, F_DUPFD);
//        } else {
//            isOpenOk = FALSE;
//        }

//        int notUsedDirfd = -1;
//        openResult = syscall(SYS_fstatat, notUsedDirfd, filePathStr, &stat_info, F_DUPFD);
//        openResult = syscall(SYS_fstatat, notUsedDirfd, filePathStr, &stat_info, 0);
        openResult = syscall(SYS_fstatat, AT_FDCWD, filePathStr, &stat_info, 0);
```

## SYS_statfs

```c
    } else if (FUNC_SYSCALL_STATFS == funcType){
        isUseStatInfo = TRUE;
        // int statfs(const char *path, struct statfs *buf);
        openResult = syscall(SYS_statfs, filePathStr, &stat_info);
```

## SYS_fstatfs

```c
    } else if (FUNC_SYSCALL_FSTATFS == funcType){
        isUseStatInfo = TRUE;
        // int fstatfs(int fd, struct statfs *buf);
        int curFd = open(filePathStr, O_RDONLY);
        if (curFd > 0){
            openResult = syscall(SYS_fstatfs, curFd, &stat_info);
        } else {
            isOpenOk = FALSE;
        }
    }
```

## SYS_open

```c
    } else if (FUNC_SYSCALL_OPEN == funcType){
        isUseFd = TRUE;
//        retFd = syscall(SYS_open, filePathStr, O_RDONLY);
        retFd = syscall(SYS_open, filePathStr, O_RDONLY, MODE_NONE);
    }
```

## SYS_access

```c
    } else if (FUNC_SYSCALL_ACCESS == funcType) {
        int retValue = syscall(SYS_access, filePathStr, F_OK);
        NSLog(@"SYS_access %s -> %d", filePathStr, retValue);
        if (retValue != ACCESS_OK){
            isOpenOk = FALSE;
        } else {
            isOpenOk = TRUE;
        }
    }
```

## SYS_faccessat

```c
    } else if (FUNC_SYSCALL_FACCESSAT == funcType) {
        // NOTE: syscall(SYS_faccessat) not work until 20220317 -> awalys return -1

        int curDirFd = 0;
        int retValue = ACCESS_FAILED;

        const int FAKE_FD = 0;
        curDirFd = FAKE_FD;
//        retValue = syscall(SYS_faccessat, curDirFd, filePathStr, F_OK, AT_EACCESS);
        retValue = syscall(SYS_faccessat, curDirFd, filePathStr, F_OK, 0);
        if (retValue != ACCESS_FAILED){
            isOpenOk = TRUE;
        } else {
            isOpenOk = FALSE;
        }
    }
```
