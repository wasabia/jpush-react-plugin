# JPush React Native Plugin

###Android Usage

- 使用命令行在你的React Native Project目录中安装：

```
npm install jpush-react-native --save

rnpm link jpush-react-native
```

- 使用Android Studio import你的React Native应用（选择你的React Native应用所在目录下的android文件夹即可）

- 修改android项目下的setting.gradle配置：

> setting.gradle

```
include ':app', ':jpush-react-native'
project(':jpush-react-native').projectDir = new File(rootProject.projectDir, '../node_modules/jpush-react-native/android')

```

- 修改app下的build.gradle配置：

> your react native project/android/app/build.gradle

```
dependencies {
    compile fileTree(dir: "libs", include: ["*.jar"])
    compile project(':jpush-react-native')
    compile "com.facebook.react:react-native:+"  // From node_modules
}
```

- 现在重新sync一下项目，应该能看到jpush-react-native作为一个android Library项目导进来了

![](https://github.com/KenChoi1992/SomeArticles/blob/master/screenshots/plugin1.png)

- 打开jpush-react-native的build.gradle文件，修改相关配置：

> jpush-react-native/android/build.gradle

![](https://github.com/KenChoi1992/SomeArticles/blob/master/screenshots/plugin2.png)

将此处的yourAppKey替换成你在官网上申请的应用的AppKey

- 打开app下的MainActivity，在ReactInstanceManager的build方法中加入JPushPackage：

> app/MainActivity.java

![](https://github.com/KenChoi1992/SomeArticles/blob/master/screenshots/plugin3.png)

- 在JS中import JPushModule，然后即调用相关方法：
```
import JPushModule from 'jpush-react-native';

...

componentDidMount() {
    JPushModule.addReceiveCustomMsgListener((message) => {
      this.setState({pushMsg: message});
    });
    JPushModule.addReceiveNotificationListener((message) => {
      console.log("receive notification: " + message);
    })
  }

  componentWillUnmount() {
    JPushModule.removeReceiveCustomMsgListener();
    JPushModule.removeReceiveNotificationListener();
  }
```

关于JPushModule的具体方法可以参考jpush-react-native文件夹下的index.js文件，此处将方法罗列如下：

- initPush()
- getInfo(map)
```
JPushModule.getInfo((map) => {
      this.setState({
            appkey: map.myAppKey,
            imei: map.myImei,
            package: map.myPackageName,
            deviceId: map.myDeviceId,
            version: map.myVersion
      });
    });
```
- stopPush()
- resumePush()
- setTag(array, callback, callback)
- getRegistrationID(callback)
- setAlias(alias, callback, callback)
- setStyleBasic()
- setStyleCustom()
- addReceiveCustomMsgListener(callback)
- removeReceiveCustomMsgListener(event)
- addReceiveNotificationListener(callback)
- removeReceiveNotificationListener(event)

**关于接口的使用请参考demo，下载zip后解压，使用Android Studio打开，修改AppKey，以及gradle相关配置（主要是平台版本号），然后在终端中使用命令react-native run-android运行，JS用法可以参考PushDemo/react-native-android文件夹下的文件**


####iOS Usage
- 打开iOS工程，在rnpm link 之后，RCTJPushModule.xcodeproj 工程会自动添加到 Libraries 目录里面
- 在iOS工程target的Build Phases->Link Binary with Libraries中加入libz.tbd、CoreTelephony.framework、Security.framework
- 在AppDelegate.h 文件中 填写如下代码，这里的的appkey、channel、和isProduction填写自己的
```
static NSString *appKey = @"";     //填写appkey
static NSString *channel = @"";    //填写channel   一般为nil
static BOOL isProduction = false;  //填写isProdurion  平时测试时为false ，生产时填写true
```
- 在AppDelegate.m 的didFinishLaunchingWithOptions 方法里面添加如下代码
```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
if ([[UIDevice currentDevice].systemVersion floatValue] >= 8.0) {
    //可以添加自定义categories
    [JPUSHService registerForRemoteNotificationTypes:(UIUserNotificationTypeBadge |
                                                      UIUserNotificationTypeSound |
                                                      UIUserNotificationTypeAlert)
                                          categories:nil];
  } else {
    //iOS 8以前 categories 必须为nil
    [JPUSHService registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge |
                                                      UIRemoteNotificationTypeSound |
                                                      UIRemoteNotificationTypeAlert)
                                          categories:nil];
  }
  
  [JPUSHService setupWithOption:launchOptions appKey:appKey
                        channel:channel apsForProduction:isProduction];
}
```
- 在AppDelegate.m 的didRegisterForRemoteNotificationsWithDeviceToken 方法中添加 [JPUSHService registerDeviceToken:deviceToken]; 如下所示
```
- (void)application:(UIApplication *)application
didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
  [JPUSHService registerDeviceToken:deviceToken];
}
```

###关于更新React Native

**进入当前项目的目录**
- 在命令行中使用：

> react-native --version

就可以查看当前使用的版本

- 在命令行中输入：

> npm info react-native

就可以查看React Native的历史和最新版本

- React Native可以直接更新到某个版本：

> npm install --save react-native@0.23.0

就可以更新到0.23.0版本

如果升级后出现类似于
```
react-native@0.23.0 requires a peer of react@^0.14.5 but none was installed.
```

执行:
> npm install --save react

或者：
> npm install --save react@0.14.5

即可。

如果更新后执行react-native run-android不能正确运行，而是出现类似：
```
 Could not find com.facebook.react:react-native:0.23.0.
```

错误，或者在Android Studio中直接运行app时报错：
```
Android Studio failed to resolve com.facebook.react:react-native:0.23.0
```

那么可以按照下列命令修复，首先在命令行中执行：
> npm i

执行完毕且不报错后，执行下面的命令，**注意，在执行命令之后，某些文件可能会产生冲突，请确保你的本地文件记录可以恢复**（在Android Studio中可以查看历史记录来恢复文件）
> react-native upgrade

执行上面的命令可能会提示你是否覆盖文件。在解决冲突之后重新运行App即可。

---
贡献者列表
- [bang88](https://github.com/bang88)
