# 是否可调试

```c
- (IBAction)isDebugableBtnClicked:(UIButton *)sender {
    _curBtnLbl.text = sender.titleLabel.text;
    NSLog(@"is debugable check");
    
//    /* tmp to debug getuid */
//    uid_t curUid = getuid();
//    NSLog(@"curUid=%d", curUid);
    
    int SYSCTL_OK = 0;
    NSString* resultStr = @"";
    BOOL isDebugable = FALSE;

    // Initialize mib, which tells sysctl the info we want, in this case
    // we're looking for information about a specific process ID.
    int name[4];             //里面放字节码。查询的信息
    name[0] = CTL_KERN;      //内核查询
    name[1] = KERN_PROC;     //查询进程
    name[2] = KERN_PROC_PID; //传递的参数是进程的ID
//    name[3] = getpid();      //获取当前进程ID
    
    int pidToCheck = -1;
    
    int currentPID = getpid();
    NSLog(@"currentPID=%d", currentPID);
    pidToCheck = currentPID;

//    //for debug
//    int parentPID = getppid();
//    NSLog(@"parentPID=%d", parentPID);
//    pidToCheck = parentPID;

    NSLog(@"pidToCheck=%d", pidToCheck);
    name[3] = pidToCheck;

    // [3]    int    13679

//    size_t infoSize = sizeof(kernelInfoProc);  // 结构体大小
    size_t infoSize = sizeof(struct kinfo_proc);
    struct kinfo_proc kernelInfoProc;  //接受查询结果的结构体
    // Initialize the flags so that, if sysctl fails for some bizarre reason, we get a predictable result.
//    kernelInfoProc.kp_proc.p_flag = 0;
    memset(&kernelInfoProc, 0, infoSize);

    // infoSize    size_t    648
//    int sysctlRet = sysctl(name, 4, &kernelInfoProc, &infoSize, 0, 0);
    int sysctlRet = sysctl(name, 4, &kernelInfoProc, &infoSize, NULL, 0);
    NSLog(@"sysctlRet=%d", sysctlRet);
    if(sysctlRet == SYSCTL_OK){
        int pFlag = kernelInfoProc.kp_proc.p_flag;
        NSLog(@"pFlag=0x%x", pFlag);
        isDebugable = ((pFlag & P_TRACED) != 0);
        NSLog(@"isDebugable=%s", boolToStr(isDebugable));
        if (isDebugable) {
            resultStr = @"可被调试 -> 越狱手机";
        } else {
            resultStr = @"不可被调试 -> 非越狱手机";
        }
    } else {
        NSLog(@"errno=%d\n", errno);
        char *errMsg = strerror(errno);
        NSLog(@"errMsg=%s\n", errMsg);
        resultStr = [NSString stringWithFormat:@"检测失败: %s", errMsg];
    }
    NSLog(@"resultStr=%@\n", resultStr);
    _detectResultTv.text = resultStr;
}
```

