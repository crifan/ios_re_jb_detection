# 越狱相关进程

TODO：

* 【未解决】iOS越狱检测：检测是否有越狱相关进程

---

```c

- (IBAction)processCheckBtnClicked:(UIButton *)sender {
    _curBtnLbl.text = sender.titleLabel.text;
    NSLog(@"process check");
    NSString* resultStr = @"TODO";

    NSArray *processes = [CrifanLibiOS runningProcesses];
    NSLog(@"processes=%@", processes);

    if (NULL == processes) {
        resultStr = @"此检测手段已失效：sysctl(CTL_KERN, KERN_PROC, KERN_PROC_ALL)";
    }

    // proc_listpids(type, typeinfo, buffer, buffersize)
    // type = PROC_ALL_PIDS, typeinfo = 0 (use proc_listallpids)
    // type = PROC_PGRP_ONLY, typeinfo = process group id (use proc_listpgrppids)
    // type = PROC_TTY_ONLY, typeinfo = tty
    // type = PROC_UID_ONLY, typeinfo = uid
    // type = PROC_RUID_ONLY, typeinfo = ruid
    // type = PROC_PPID_ONLY, typeinfo = ppid (use proc_listchildpids)
    // Call with buffer = NULL to return number of pids.
//    int numberOfProcesses = proc_listpids(PROC_ALL_PIDS, 0, NULL, 0);
//    NSLog(@"numberOfProcesses=%d", numberOfProcesses);

    NSLog(@"resultStr=%@", resultStr);
    _detectResultTv.text = resultStr;
}
```
