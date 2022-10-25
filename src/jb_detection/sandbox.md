# 沙箱完整性校验

TODO：

* 【无需解决】已越狱iOS中fork()失败返回-1
* 【已解决】iOS中系统调用fork返回值的含义和逻辑

---

```c
- (IBAction)forkBtnClicked:(UIButton *)sender {
    _curBtnLbl.text = sender.titleLabel.text;
    NSLog(@"Fork() check");
    // SandBox Integrity Check
    int retPid = fork(); //返回值：子进程返回0，父进程中返回子进程ID，出错则返回-1
    NSString *forkResultStr = parseForkResult(retPid);
    NSLog(@"fork() return retPid=%d, forkResultStr=%@", retPid, forkResultStr);
    _detectResultTv.text = [NSString stringWithFormat:@"%@ -> %@", @"fork()", forkResultStr];
}
```
