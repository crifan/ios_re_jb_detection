# 是否可调试

TODO：

* 【已解决】iOS反越狱检测：是否可被调试

---

## sysctl

```c
/*==============================================================================
 Hook: sysctl
==============================================================================*/

int sysctl(int *name, u_int namelen, void *oldp, size_t *oldlenp, void *newp, size_t newlen);

%hookf(int, sysctl, int *name, u_int namelen, void *oldp, size_t *oldlenp, void *newp, size_t newlen){
    iosLogDebug("name=%p, namelen=%d, oldp=%p, oldlenp=%p, newp=%p, newlen=%ld", name, namelen, oldp, oldlenp, newp, newlen);

//    int sysctlRet = SYSCTL_FAIL;
//    sysctlRet = %orig(name, namelen, oldp, oldlenp, newp, newlen);
	int sysctlRet = %orig;

	if (cfgHookEnable_sysctl_sysctl) {
		// for Anti-Debug
		bool isGetpid = (name[0] == CTL_KERN && name[1] == KERN_PROC && name[2] == KERN_PROC_PID);
		if (isGetpid) {
			struct kinfo_proc *info = NULL;
			info = (struct kinfo_proc *)oldp;
			int oldPFlag = info->kp_proc.p_flag;
			info->kp_proc.p_flag &= ~(P_TRACED);
			int newPFlag = info->kp_proc.p_flag;

			iosLogInfo("name=%p, namelen=%d, oldp=%p, oldlenp=%p, newp=%p, newlen=%ld -> isGetpid=%s -> oldPFlag=0x%x, newPFlag=0x%x -> sysctlRet=%d", name, namelen, oldp, oldlenp, newp, newlen, boolToStr(isGetpid), oldPFlag, newPFlag, sysctlRet);
		}
	}

	return sysctlRet;
}
```

## sysctlnametomib

```c
/*==============================================================================
 Hook: sysctlnametomib
==============================================================================*/

// https://developer.apple.com/library/archive/documentation/System/Conceptual/ManPages_iPhoneOS/man3/sysctlnametomib.3.html
int sysctlnametomib(const char *name, int *mibp, size_t *sizep);

%hookf(int, sysctlnametomib, const char *name, int *mibp, size_t *sizep){
//    iosLogInfo("name=%p, mibp=%p, sizep=%p", name, mibp, sizep);
    int retInt = SYSCTL_FAIL;
    retInt = %orig;
    iosLogInfo("name=%{public}s, mibp=%p, sizep=%p -> retInt=%d", name, mibp, sizep, retInt);
    return retInt;
}
```