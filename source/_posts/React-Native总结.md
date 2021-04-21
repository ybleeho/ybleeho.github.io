---
title: React Native总结
date: 2021-03-24 16:29:49
tags:
---

经过两三个月，随着Android端和iOS端的ReactNative项目上线，想把第一次从0到上线的React Native经验总结一下。选用React Native的原因是因为对JS和React熟悉， 加之项目需要双端快速上线，又只有我一个人可以投入这个项目开发，Native需要时间去熟悉， 所以RN成了第一选择。看别人的总结总把React Native说成一个坑，但遇到问题后， 大部分问题都可以解决实现。如果React Native解决不了的问题也可以通过跟Native做桥接完美实现功能。特别是像国内的一些SDK的RN版本少或者很多年没有维护（如IM等等），需要写原生再去用RN去调用原声代码。

#### 项目结构

刚开始摸索阶段可能是花时间最多的阶段， 最后决定项目由React Native Cli + React Navigation + React Hook来完成。不用Expo的原因是因为后面很难用Native模块去扩展，如果是一些简单的项目可以用Expo去实现。在Navigation里面有React Navigation和React Native Navigation，看了文档以后感觉React Navigation更简洁并且Star数也比较高，所以选择了React Navigation。 在代码风格方面使用React Hook， Hook提供的useContext ，可以完美的代替redux，写法又简洁又大部分的RN文档又是Hook的方式， 所以选择React Hook。

代码结构大概是

```
.
├── App.js
├── README.md
├── __tests__
├── android
├── app.json
├── babel.config.js
├── index.js
├── ios
├── metro.config.js
├── node_modules
├── package.json
├── src
├── yarn-error.log
└── yarn.lock
//src
.
├── assets
├── components
├── constants
├── context
├── hooks
├── lib
├── navigations
├── request
├── screens
├── style
└── utils
```

最外层几乎是默认的init结构， src目录下assert文件存储图片，icon，动效等等文件。 只需要放iOS的2x和3x的图片。components主要放一些组件，constansts常量如屏幕宽度，高度， 等等，context存储比如AuthContext， LoadingContext 等等全局的一些context。lib存储了一些第三方的library的一些扩展或者重写。navigations主要放了BottomTabNav， AuthNav， AppNav等等路由。request存放请求相关。screens存放每个页面。



#### 页面结构

```
.
└── RootNavigator
    ├── AppStackNavigator
    │   ├── BottomTabNavigator
    │   │   ├── Tab1
    │   │   ├── Tab2
    │   │   ├── Tab3
    │   │   └── Tab4
    │   ├── Detail1Screen
    │   └── Detail2Screen
    └── AuthStacNavigator
```

路由结构还是比较清晰，AppStackNav和AuthStackNav用token去控制进入哪个Nav， 主要注意的是Bottom Tab和 Detai的顺序， Bottom Tab和 DetailScreen需要平级， 才能Push的时候Detail页面盖住Bottom Tab。还有一个问题是TopTabNav。 在这边我是实用react-native-tab-view 来代替 TopTabNav。 如果需要Bottom Tab的动效，可以参考https://blog.csdn.net/hejun041/article/details/101026140 。有一个问题是pop时候如何传给上一个页面数据， 这个问题解决办法是navigate到下一个组件的时候传入一个函数getBackData然后在pop时候调用这个函数。RN会抛出警告ignore掉就好。方法是

```
LogBox.ignoreLogs([
    'Non-serializable values were found in the navigation state',
]);
```

关于StackNavigator的配置, 我的配置是如下图所示， Slide需要统一成iOS方式。然后每个页面的title或者shadow。注意的是gestureEnabled在iOS是true，需要都设置成true。headerBackTitleVisible 设置成false

```
 <AppStack.Navigator
            mode="card"
            initialRouteName={'App'}
            screenOptions={{
                headerBackTitleVisible: false,
                ...TransitionPresets.SlideFromRightIOS
            }}
        >
```

```
{
        title,
        headerTitleStyle: { fontSize: 18 },
        headerTitleAlign: 'center',
        headerBackImage: () => <HeaderLeft />,
        headerBackTitleVisible: false,
        gestureEnabled: true,  // Defaults to true on iOS, false on Android.
 }
```

#### 样式

React Native主要使用flex布局， 问题不大， 字体大小，宽度等等没有单位采用的是dp， 默认字体不是PingFang SC 需要全局设置。下面是我全局使用的一些样式。

```

TextInput.defaultProps.selectionColor = '#FF7756'
TextInput.defaultProps.placeholderTextColor = 'rgba(0, 0, 0, 0.24)'
//https://stackoverflow.com/questions/35255645/how-to-set-default-font-family-in-react-native
let oldRender = Text.render;
Text.render = function (...args) {
    let origin = oldRender.call(this, ...args);
    return React.cloneElement(origin, {
        style: [{ fontFamily: 'PingFang SC', letterSpacing: 0.3 }, origin.props.style]
    });
};
```

关于SafeArea我是用的是react-native-safe-area-context， 它提供上下左右的安全区域， 需要注意的是React Navigation 已经加上了SafeArea所以不用考虑。

关于StatusBar

```
    if (isAndroid) {
        StatusBar.setTranslucent(true);
        StatusBar.setBackgroundColor('transparent');
        StatusBar.setBarStyle('dark-content');
    }
```

关于fontWeight

```
'normal' | 'bold' | '100' | '200' | '300' | '400' | '500' | '600' | '700' | '800' | '900'
```

iOS和android的fontWeight表现不一样， 在android的500，600 不太明显 ， iOS600跟android的700差不多。

关于shadow

iOS和android的样式也是不一样的

```
//ios
        shadowColor: '#000000',
        shadowOpacity: 0.1,
        shadowRadius: 3,
        shadowOffset: {
            height: 0,
            width: -2,
        },
        //android
        elevation: 1,
```

android的阴影很难跟iOS一样去统一， 点9图又很麻烦， 其它方式又很卡， 所以凑合用吧。

关于Button

推荐使用TouchableOpacity自行去实现。 渐变色用react-native-linear-gradient

关于图片

如果需要按比例显示， Image的contain会剧中图片使用react-native-scalable-image 来实现居左或靠上的图片

还有iOS 14以下Image的webp图片显示会有问题， 可能需要转换格式。

关于键盘

是个头疼问题， 采用的是KeyboardAvoidingView。需要注意的是要考虑Nav的 header高度。

```react
<KeyboardAvoidingView style={{ flex: 1, width: SCREEN_WIDTH }} behavior={Platform.OS === "ios" ? "padding" : "null"} keyboardVerticalOffset={Platform.select({ ios: useHeaderHeight() })} >
```



#### 关于Splash Screen

使用react-native-splash-screen 或者需要动效的话使用react-native-lottie-splash-screen （注意iOS项目配置Swift转OC）

#### 关于ImagePicker和拍摄

因为没有找到合适的Picker跟项目的流程一样，所以用@react-native-community/cameraroll 和Flatlist自己写了一个ImagePicker以及上面添加一个Preview的页面， 不算很复杂。短按拍摄，长按拍段视频也可以用onPress和onLongPress实现。拍摄进度使用的是 react-native-circular-progress。

#### 关于iOS设备登录和内购

不太复杂， 使用的是 @invertase/react-native-apple-authentication和 react-native-iap, 唯一注意的地方是内购需要防止丢单，需要App监听回调， 跟后端交互以后需要ACK一下。

#### 关于原生交互

一些SDK可能没有提供RN版本的封装， 这时候可能需要写Native代码然后实现桥接。例如使用的腾讯IM， 阿里人脸认证等等， 都使用原生去封装了一下。 原声代码不是很难， 基本上Copy就可以， 关于桥接 RN提供了回调， Promise 还有Emiter等等方式， 封装方式也比较简单， 很容易去实现，官网有具体实现方法。

#### 关于WebView

RN在跟Webview交互的时候可能需要向Webview发送数据或者拿到数据这时候， react-native-webview也提供了这个方法 。`injectedJavaScriptBeforeContentLoaded` 这个方法会在webview load之前执行， 但是发现有些版本较低或者部分手机无法执行这个方法。 `injectedJavaScript`这个方法是都会执行， 但是有些时候还没有执行完webview的init js代码就执行了， 最后没办法 只能使用`injectedJavaScript` 然后Webview延迟大概500ms-1s去执行， 拿到App的数据。 然后如果Webview向App发送事件使用 `onMessage`就可以， 比如在Webview里面按返回执行的逻辑可以在`onMessage`拿到事件以及数据在执行。

#### 关于Flatlist

这部分可能是比较麻烦， 而且优化内容比较多的一个地方。 Flatlist提供了下拉刷新， 以及加载更多的功能， 以及renderHeader 等等方法 。 对于item可能需要`React.memo`等等方法在state变化时，防止重新渲染， 提高性能（优化放在必要时候）。还有`initialNumToRender`如果不去设置这个值每次会render10个数据， 所以当一个屏幕高度内有10+个数据时候会出现先render10个的现象，这时候可以把这个值设置高一些或者设置成数据长度。

#### 总结

经过这段时间的摸索， 感觉React Native还是值得去尝试的， 虽然可能有一些琐碎的问题， 所谓的坑， 但问题都不大，并且也可以跟原生交互。 像对于移动端开发不是很熟悉，但对Javascript， React熟悉的朋友们都值得去尝试， App的体验跟Native相差无几， 可能在项目初期会花的时间比较多，但对于后期的维护来说实在是太方便， 一次改两个平台，太香！！！









