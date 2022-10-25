# ObjC运行时

TODO：

* 【已解决】iOS越狱检测：objc_copyImageNames检测image
* 【无法解决】iOS越狱检测和反越狱检测：objc_getClass
* 【无需解决】iOS越狱检测和反越狱检测：NSClassFromString

---

## `objc_copyImageNames`

```c

- (IBAction)objCopyBtnClicked:(UIButton *)sender {
    _curBtnLbl.text = sender.titleLabel.text;
    NSLog(@"objc_copyImageNames check");
    unsigned int outImageCount = 0;
    const char **imageList = objc_copyImageNames(&outImageCount);
    NSLog(@"outImageCount=%d, imageList=%p", outImageCount, imageList);
    
    NSMutableArray *jbImageList = [NSMutableArray array];

    if ((outImageCount > 0) && (imageList != NULL)) {
        for (int i = 0; i < outImageCount; i++) {
            const char* curImagePath = imageList[i];
            bool isJbPath = isJailbreakPath(curImagePath);
            NSLog(@"[%d] %s -> isJbPath=%s", i, curImagePath, boolToStr(isJbPath));
            if (isJbPath) {
                NSString* curImagePathNs = [NSString stringWithFormat:@"%s", curImagePath];
                [jbImageList addObject: curImagePathNs];
            }
        }
    }

    NSString *jbImageListStr = [CrifanLibiOS nsStrListToStr:jbImageList isSortList:TRUE isAddIndexPrefix:TRUE];
    NSLog(@"jbImageListStr=%@", jbImageListStr);

    NSString* resultStr = @"";
    if (jbImageList.count > 0) {
        resultStr = [NSString stringWithFormat:@"检测出越狱库image -> 越狱手机\n%@", jbImageListStr] ;
    } else {
        resultStr = @"未检测出越狱库image -> 非越狱手机";
    }
    NSLog(@"resultStr=%@", resultStr);
    _detectResultTv.text = resultStr;
}
```
