# syscall

TODO：

* 【已解决】iOS的app中如何实现syscall函数调用
* 【已解决】iOS中用syscall调用stat64实现越狱文件检测
* 【整理】syscall内核系统调用和svc 0x80相关基础知识


---

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
