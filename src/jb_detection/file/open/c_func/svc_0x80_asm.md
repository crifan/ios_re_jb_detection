# svc 0x80内联汇编

TODO：

* 【整理】syscall内核系统调用和svc 0x80相关基础知识
* 【已解决】iOS正向越狱检测：app中实现svc 0x80实现系统调用
* 【已解决】iOS中优化asm汇编代码新增syscall的number参数
* 【整理】iOS中syscall的系统调用编号number的定义

---

```c

//---------- svc 0x80 define ----------

//#define asm_set_syscall_number(SYSCALL_NUMBER) "mov x16, #SYSCALL_NUMBER\n"
//
//#define asm_svc_0x80_stat64() \
//    "mov x0, %[pathname_p]\n" \
//    "mov x1, %[stat_info_p]\n" \
//    asm_set_syscall_number(SYS_stat64) \
//    "svc #0x80\n" \
//    "mov %[ret_p], x0\n"

//                     "mov x16, #338\n" \

//__attribute__((always_inline)) long svc_0x80_stat_stat64(int syscall_number, const char * pathname, struct stat * stat_info) {
//        long ret = 0;
//        long long_syscall_number = syscall_number;
//        __asm__ volatile(
//             "mov x0, %[pathname_p]\n"
//             "mov x1, %[stat_info_p]\n"
//             "mov x16, %[long_syscall_number_p]\n"
//             "svc #0x80\n"
//             "mov %[ret_p], x0\n"
//            : [ret_p]"=r"(ret)
//            : [long_syscall_number_p]"r"(long_syscall_number), [pathname_p]"r"(pathname), [stat_info_p]"r"(stat_info)
//             : "x0", "x1", "x16"
//        );
//        return ret == 0 ? ret : -1;
//}

__attribute__((always_inline)) int svc_0x80_stat_stat64(int syscall_number, const char * pathname, struct stat * stat_info) {
    register const char * x0_pathname asm ("x0") = pathname; // first arg
    register struct stat * x1_stat_info asm ("x1") = stat_info;  // second arg
    register int x16_syscall_number asm ("x16") = syscall_number; // special syscall number store to x16
    register int x4_ret asm("x4") = OPEN_FAILED; // store result
    __asm__ volatile(
         "svc #0x80\n"
         "mov x4, x0\n"
        : "=r"(x4_ret)
        : "r"(x0_pathname), "r"(x1_stat_info), "r"(x16_syscall_number)
//         : "x0", "x1", "x4", "x16"
    );
    return x4_ret;
}

//__attribute__((always_inline)) int svc_0x80_open(const char * pathname, int flags, mode_t mode) {
__attribute__((always_inline)) int svc_0x80_open(const char * pathname, int flags) {
    register const char * x0_pathname asm ("x0") = pathname; // first arg
    register int x1_flags asm ("x1") = flags;  // second arg
//    register unsigned int x2_mode asm ("x2") = (unsigned int)mode;  // third arg
    register int x16_syscall_number asm ("x16") = SYS_open; // special syscall number store to x16
    register int x4_ret asm("x4") = OPEN_FD_INVALID; // store result
    __asm__ volatile(
//         "mov x16, #5\n" // SYS_open
         "svc #0x80\n"
         "mov x4, x0\n"
        : "=r"(x4_ret)
        : "r"(x0_pathname), "r"(x1_flags), "r"(x16_syscall_number)
// : "r"(x0_pathname), "r"(x1_flags), "r"(x2_mode), "r"(x16_syscall_number)
//         : "x16"
//         : "x0", "x1", "x5", "x16"
    );
    return x4_ret;
}

//---------- svc 0x80 call ----------

...
    } else if (FUNC_SVC_0X80_STAT == funcType) {
        isUseStatInfo = TRUE;
        //Note: for open normal file, return 0 is OK, but st_mode is abnormal !
        openResult = svc_0x80_stat_stat64(SYS_stat, filePathStr, &stat_info);
    } else if (FUNC_SVC_0X80_STAT64 == funcType) {
        isUseStatInfo = TRUE;
        openResult = svc_0x80_stat_stat64(SYS_stat64, filePathStr, &stat_info);
...
    } else if (FUNC_SVC_0X80_OPEN == funcType) {
        isUseFd = TRUE;
//        retFd = svc_0x80_open(filePathStr, O_RDONLY, MODE_NONE);
        retFd = svc_0x80_open(filePathStr, O_RDONLY);

```
