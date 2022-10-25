# _dyld系列

TODO：

* 【已解决】iOS越狱检测：辅助用_dyld_get_image_header解析动态库文件信息
* 【已解决】iOS越狱检测：优化dyld的动态库文件和其他越狱文件列表
* 【已解决】iOS越狱检测：用_dyld_image_count() 和 _dyld_get_image_name()检测越狱相关动态库
* 【已解决】iOS正向越狱检测：_dyld_register_func_for_add_image及相关

---

* `_dyld`系列 = `_dyld`开头的一系列函数
  * 最基础也最常用的：`_dyld_image_count` + `_dyld_get_image_name`
  * 很少用到的：`_dyld_get_image_vmaddr_slide`
  * 更高级的：`_dyld_register_func_for_add_image` 和 `_dyld_register_func_for_remove_image`

## `_dyld_image_count` + `_dyld_get_image_name`

```c
- (void) dbgPrintLibInfo: (int)curImgIdx{
    // debug slide
    intptr_t curSlide = _dyld_get_image_vmaddr_slide(curImgIdx);
    NSLog(@"[%d] curSlide=0x%lx", curImgIdx, curSlide);

    // debug header info
    const struct mach_header* libHeader = _dyld_get_image_header(curImgIdx);
    if (NULL != libHeader){
        int magic = libHeader->magic;
        int cputype = libHeader->cputype;
        int cpusubtype = libHeader->cpusubtype;
        int filetype = libHeader->filetype;
        int ncmds = libHeader->ncmds;
        int sizeofcmds = libHeader->sizeofcmds;
        int flags = libHeader->flags;

        NSLog(@"[%d] magic=0x%x,cputype=0x%x,cpusubtype=0x%x,filetype=%d,ncmds=%d,sizeofcmds=%d,flags=0x%x",
              curImgIdx,
              magic, cputype, cpusubtype, filetype, ncmds, sizeofcmds, flags);
        // 2021-12-17 09:37:46.814810+0800 ShowSysInfo[11192:1067220] [0] magic=0xfeedfacf,cputype=0x100000c,cpusubtype=0x0,filetype=2,ncmds=23,sizeofcmds=3072,flags=0x200085
    } else {
        NSLog(@"[%d] mach_header is NULL", curImgIdx);
    }
}

- (IBAction)dyldImgCntNameBtnClicked:(UIButton *)sender {
    _curBtnLbl.text = sender.titleLabel.text;
    NSLog(@"_dyld_image_count and _dyld_get_image_name check");
    
//    //for debug
//    int testImgIdx = 282; // hooked:279 ~ real: 284
//    [self dbgPrintLibInfo: testImgIdx];

    uint32_t imageCount = _dyld_image_count();
    NSLog(@"dyld: imageCount=%d", imageCount);

    NSMutableArray *loadedDylibList = [NSMutableArray array];

    NSMutableArray *jbDylibList = [NSMutableArray array];
    
    for (uint32_t i = 0 ; i < imageCount; ++i) {
        const char* curImageName = _dyld_get_image_name(i);
        
        // for debug
//        bool isNeedDebug = (0 == i) || (1 == i) || (2 == i) || (275 == i);
        bool isNeedDebug = (277 == i) || (278 == i);

        if (NULL != curImageName){
            NSString *curImageNameStr = [[NSString alloc]initWithUTF8String: curImageName];
            NSLog(@"[%d] %@", i, curImageNameStr);

            [loadedDylibList addObject:curImageNameStr];

    //        if([JbPathList.jbDylibList containsObject:curImageNameStr]){
    //        if([JbPathList isJbDylib: curImageNameStr]){

            if(isJailbreakDylib(curImageName)){
                [jbDylibList addObject: curImageNameStr];

                // for debug
                isNeedDebug = true;
            }
        } else {
            NSLog(@"[%d] %s", i, curImageName);
        }

        // for debug
        if (isNeedDebug){
            [self dbgPrintLibInfo: i];
        }
    }

//    NSString *loadedDylibListStr = [CrifanLibiOS nsStrListToStr:loadedDylibList];
    NSString *loadedDylibListStr = [CrifanLibiOS nsStrListToStr:loadedDylibList isSortList:TRUE isAddIndexPrefix:TRUE];
    NSLog(@"dyld: loadedDylibListStr=%@", loadedDylibListStr);

    NSString *jbLibListStr = [CrifanLibiOS nsStrListToStr:jbDylibList isSortList:TRUE isAddIndexPrefix:TRUE];
    NSLog(@"dyld: jbDylibList=%@", jbLibListStr);

    NSString* dyldLibResultStr = @"";
    if (jbDylibList.count > 0){
        dyldLibResultStr = [NSString stringWithFormat: @"检测出越狱动态库 -> 越狱手机; 越狱动态库列表:\n%@", jbLibListStr];
    } else{
        dyldLibResultStr = @"未检测出越狱动态库 -> 非越狱手机";
    }
    NSLog(@"dyld: dyldLibResultStr=%@", dyldLibResultStr);

    _detectResultTv.text = dyldLibResultStr;
}
```

## `_dyld_register_func_for_add_image`

```c
static NSString* checkImageResult = @"未发现越狱库 -> 非越狱手机";
NSMutableArray *checkImageFoundJbLibList = NULL;

+ (void)load {
  static dispatch_once_t onceToken;
  dispatch_once(&onceToken, ^{
      checkImageFoundJbLibList = [NSMutableArray array];
      _dyld_register_func_for_add_image(_check_image);
  });
}

static void _check_image(const struct mach_header *header, intptr_t slide) {
    Dl_info info;
    size_t dlInfoSize = sizeof(Dl_info);
    memset(&info, 0, dlInfoSize);

    dladdr(header, &info);
    const char* curImgName = info.dli_fname;
    if(curImgName != NULL) {
        if (isJailbreakDylib(curImgName)) {
            NSString *curImgNameNs = [NSString stringWithUTF8String: curImgName];
            [checkImageFoundJbLibList addObject: curImgNameNs];
            NSString *jbLibListStr = [CrifanLibiOS nsStrListToStr:checkImageFoundJbLibList isSortList:TRUE isAddIndexPrefix:TRUE];
            checkImageResult = [NSString stringWithFormat: @"发现越狱动态库 -> 越狱手机\n%@", jbLibListStr];
            NSLog(@"%@", checkImageResult);
            // "Found Jailbreak dylib: /usr/lib/substitute-inserter.dylib -> 越狱手机"
        }
    }
    return;
}

- (IBAction)dyldRegImgBtnClicked:(UIButton *)sender {
    _curBtnLbl.text = sender.titleLabel.text;
    NSLog(@"dlyd register image add/remove check");
    NSString* resultStr = @"TODO";

    resultStr = checkImageResult;

    NSLog(@"resultStr=%@", resultStr);
    _detectResultTv.text = resultStr;
}
```
