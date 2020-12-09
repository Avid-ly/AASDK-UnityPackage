## 修复Unity IDE中同时接入AF+FB导致FB登录完成回调不响应的问题

### 一、原因

在AF提供的最新`unity插件（6.1.0）`中的[AppsFlyerAppController](https://github.com/AppsFlyerSDK/appsflyer-unity-plugin/blob/master/Assets/AppsFlyer/Plugins/iOS/AppsFlyerAppController.mm)类中调用了如下方法

```
IMPL_APP_CONTROLLER_SUBCLASS(AppsFlyerAppController)
```

这个方法的逻辑如下：

```
#define IMPL_APP_CONTROLLER_SUBCLASS(ClassName) 
@interface ClassName(OverrideAppDelegate)       
{                                               
}                                               
+(void)load;                                    
@end                                            
@implementation ClassName(OverrideAppDelegate)  
+(void)load                                     
{                                               
    extern const char* AppControllerClassName;  
    AppControllerClassName = #ClassName;        
}                                               
@end                                            
```

其中

```
AppControllerClassName = #ClassName;  
```

表示将`AppControllerClassName`的值置为`AppsFlyerAppController`


但在工程的`Main.mm`中我们可以看到如下代码

```
const char* AppControllerClassName = "UnityAppController";
UIApplicationMain(argc, argv, nil, [NSString stringWithUTF8String: AppControllerClassName]);
```

其中`UnityAppController`为unity工程的根视图，一般关于游戏声明周期的处理开发者都会写在此方法中（包括Facebook登录完成的回调也是在这个类里处理的）


[AppsFlyerAppController](https://github.com/AppsFlyerSDK/appsflyer-unity-plugin/blob/master/Assets/AppsFlyer/Plugins/iOS/AppsFlyerAppController.mm)类直接将unity项目的根视图`UnityAppController`替换为`AppsFlyerAppController`，导致开发者在`UnityAppController`中编写的代码无法生效

### 解决方案

下载修改后的`AppsFlyerAppController`文件替换工程里的`AppsFlyerAppController`文件即可

[下载地址](https://support.appsflyer.com/attachments/token/hFODAo0hi7ynX0HAedlR7ftYM/?name=AppsFlyerAppController+%281%29.mm)