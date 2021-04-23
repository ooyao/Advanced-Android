[TOC]

# ARouter 简介

ARouter 的出现和组件化开发有很大的关系，在组件化开发中会将整个工程按照业务划分为多个组件，可以独立运行。但不可避免的各个组件之间需要页面跳转，Android 提供的 Activity 跳转在组件化中存在很大的弊端，所以 Android 中的路由框架应运而生。各大厂也退出了自己的路由框架，例如有[阿里 ARouter](https://github.com/alibaba/ARouter/blob/master/README_CN.md)、[美团 WMRouter](https://github.com/meituan/WMRouter)、[滴滴 DRouter](https://github.com/didi/DRouter)等。这些框架的核心原理类似，由于 ARouter 更受欢迎，所以我们就以 ARouter 为例，探索一下 Android 路由框架的内部原理。



# ARouter 功能介绍

ARouter 的功能相对比较丰富，以下是官方文档列出的功能介绍。

## 功能介绍

1. **支持直接解析标准URL进行跳转，并自动注入参数到目标页面中**
2. **支持多模块工程使用**
3. **支持添加多个拦截器，自定义拦截顺序**
4. **支持依赖注入，可单独作为依赖注入框架使用**
5. **支持InstantRun**
6. **支持MultiDex**(Google方案)
7. 映射关系按组分类、多级管理，按需初始化
8. 支持用户指定全局降级与局部降级策略
9. 页面、拦截器、服务等组件均自动注册到框架
10. 支持多种方式配置转场动画
11. 支持获取Fragment
12. 完全支持Kotlin以及混编
13. **支持第三方 App 加固**(使用 arouter-register 实现自动注册)
14. **支持生成路由文档**
15. **提供 IDE 插件便捷的关联路径和目标类**
16. 支持增量编译(开启文档生成后无法增量编译)



## 典型应用

1. 从外部URL映射到内部页面，以及参数传递与解析
2. 跨模块页面跳转，模块间解耦
3. 拦截跳转过程，处理登陆、埋点等逻辑
4. 跨模块API调用，通过控制反转来做组件解耦



关于 ARouter 的使用方式可以参考[官方文档](https://github.com/alibaba/ARouter/blob/master/README_CN.md)。



# ARouter 技术方案

ARouter 的依赖配置

```groovy
dependencies {
    compile 'com.alibaba:arouter-api:x.x.x'
    annotationProcessor 'com.alibaba:arouter-compiler:x.x.x'
}
```



可以看出 ARouter 提供了两个 SDK，分别面向两个不同的阶段。

- arouter-api：面向运行期、为开发者提供 API 支持。
- arouter-compiler：面向编译期，其中包含三个注解处理器。
  - Router Processor：路由注解处理器
  - Interceptor Processor：拦截器注解处理器
  - Autowire Processor：自动装配注解处理器



ARouter 在编译期通过`arouter-compiler`收集被`Route`、`Interceptor`、`Autowired`注解标注的类信息并生成相关的的类保存映射关系等信息。在 App 启动时初始化加载编译期生成的类等信息，然后就可以通着这些信息来完成页面的路由、拦截器、自动装配等功能了。



# ARouter Activity 跳转原理

## 初始化

首先我们需要做一些配置。

```kotlin
const val ROUTE_APP_DEBUG = "/app/app_debug"

@Route(path = ROUTE_APP_DEBUG, name = "调试页面")
class AppDebugActivity : AppCompatActivity() {
 ... 
}
```



使用`@Route`注解为 Activity 配置一个`path`。也可以包含`name`、`extras`等配置。配置好之后还需要调用`ARouter.init(context)`进行初始化。之后就可以使用`ARouter`提供的 API完成 Activity 的跳转了。

```kotlin
// 在任意位置跳转到AppDebugActivity页面
ARouter.getInstance().build(ROUTE_APP_DEBUG).navigation()
```



通过上述的配置即可完成页面跳转功能，相信很多同学已经猜到了`path = ROUTE_APP_DEBUG`一定是和`AppDebugActivity`有一个映射关系。正是如此，在 ARouter 底层确实把`path`和`Activity`做了映射关系。

在编译期`Router Processor`会生成两个类。

```java
package com.alibaba.android.arouter.routes;

public class ARouter$$Root$$moduleName implements IRouteRoot {
  @Override
  public void loadInto(Map<String, Class<? extends IRouteGroup>> routes) {
    // app group信息
    routes.put("app", ARouter$$Group$$app.class);
  }
}
```



每个`module`都会生成一个`ARouter$$Root$$moduleName`的类，其包名是固定的`com.alibaba.android.arouter.routes`, 类名是`ARouter$$Rout$`加上当前`module`的名字组成。其中包含一个`loadInto`方法，里面包含了当前`module`中所有的`group`类信息。每个`module`可能会包含多个`group`。



（`group`可以通过`@Route`注解的`group`参数指定，默认使用`path`参数的第一部分当做`group`名称。）

```java
package com.alibaba.android.arouter.routes;

public class ARouter$$Group$$app implements IRouteGroup {
  @Override
  public void loadInto(Map<String, RouteMeta> atlas) {
    atlas.put("/app/app_debug", RouteMeta.build(RouteType.ACTIVITY, AppDebugActivity.class, "/app/app_debug", "app", null, -1, -2147483648));
  }
}
```



在相同的包下面还会生成对应的`group`类。其类名为`ARouter$$Group$`加上`group`名字。这个类也包含了一个`loadInto`方法，内部实现就是将`path`和 `Activity`的信息存到 map 中。这里包含 Activity 的类信息、参数信息、入场动画等。



至此编译期准备工作就已经完成了。在运行时`ARouter.init(context)`会先加载上面的信息。然后在调用`ARouter.getInstance().build(ROUTE_APP_DEBUG).navigation()`时通过 `path`获取到对应的 `Activity`信息，然后进行跳转。



下面看下运行时`ARouter `是如何处理的

```java
// ARouter 使用了单例模式
public static ARouter getInstance() {
  if (!hasInit) {
    throw new InitException("ARouter::Init::Invoke init(context) first!");
  } else {
    if (instance == null) {
      synchronized (ARouter.class) {
        if (instance == null) {
          instance = new ARouter();
        }
      }
    }
    return instance;
  }
}

// ARouter 初始化方法，传入 Application 对象
public static void init(Application application) {
  if (!hasInit) {
    logger = _ARouter.logger;
    _ARouter.logger.info(Consts.TAG, "ARouter init start.");
    // ARouter 内部通过_ARouter 完成所有功能。（重点）
    hasInit = _ARouter.init(application);

    if (hasInit) {
      // 初始化之后调用afterInit方法，内部初始化了所有的拦截器
      _ARouter.afterInit();
    }

    _ARouter.logger.info(Consts.TAG, "ARouter init over.");
  }
}
```



ARouter 内部的功能都是通过`_ARouter`来完成的。



```java
// _ARouter.java
protected static synchronized boolean init(Application application) {
  mContext = application;
  // 物流中心？（重点）
  LogisticsCenter.init(mContext, executor);
  logger.info(Consts.TAG, "ARouter init success!");
  hasInit = true;
  mHandler = new Handler(Looper.getMainLooper());

  return true;
}
```



在`_ARouter.init`方法中又调用了`LogisticsCenter.init`。这是 ARouter 运行时的比较关键的代码。它会找到上面编译期生成的类，然后将所有的`group`进行缓存。

```java
public synchronized static void init(Context context, ThreadPoolExecutor tpe) throws HandlerException {
  mContext = context;
  executor = tpe;

  try {
    long startInit = System.currentTimeMillis();
    //billy.qi modified at 2017-12-06
    //load by plugin first
    loadRouterMap();
    if (registerByPlugin) {
      logger.info(TAG, "Load router map by arouter-auto-register plugin.");
    } else {
      Set<String> routerMap;
			// 如果是 debug 模式或者APP版本号有变化则重新加载所有信息，否则使用 SP 缓存的数据
      // It will rebuild router map every times when debuggable.
      if (ARouter.debuggable() || PackageUtils.isNewVersion(context)) {
        logger.info(TAG, "Run with debug mode or new install, rebuild router map.");
        // These class was generated by arouter-compiler.
        // 获取com.alibaba.android.arouter.routes包里的所有类名
        // 这里是个重点，getFileNameByPackageName内部会做一些 Android 版本的兼容处理。
				// 并处理 MultiDex 的情况，从多个 dex 文件中找到路由信息。
        routerMap = ClassUtils.getFileNameByPackageName(mContext, ROUTE_ROOT_PAKCAGE);
        if (!routerMap.isEmpty()) {
          // 缓存到 SP 中
          context.getSharedPreferences(AROUTER_SP_CACHE_KEY, Context.MODE_PRIVATE).edit().putStringSet(AROUTER_SP_KEY_MAP, routerMap).apply();
        }
				// 更新 App 的版本信息
        PackageUtils.updateVersion(context);    // Save new version name when router map update finishes.
      } else {
        logger.info(TAG, "Load router map from cache.");
        // 加载 SP 中缓存的数据
        routerMap = new HashSet<>(context.getSharedPreferences(AROUTER_SP_CACHE_KEY, Context.MODE_PRIVATE).getStringSet(AROUTER_SP_KEY_MAP, new HashSet<String>()));
      }

      logger.info(TAG, "Find router map finished, map size = " + routerMap.size() + ", cost " + (System.currentTimeMillis() - startInit) + " ms.");
      startInit = System.currentTimeMillis();

      // 遍历所有类名
      for (String className : routerMap) {
        if (className.startsWith(ROUTE_ROOT_PAKCAGE + DOT + SDK_NAME + SEPARATOR + SUFFIX_ROOT)) {
          // This one of root elements, load root.
          // 如果是com.alibaba.android.arouter.routes.ARouter$$Root 开头则表示是group信息
          // 将 root 中的group信息保存到Warehouse.groupsIndex
          ((IRouteRoot) (Class.forName(className).getConstructor().newInstance())).loadInto(Warehouse.groupsIndex);
        } else if (className.startsWith(ROUTE_ROOT_PAKCAGE + DOT + SDK_NAME + SEPARATOR + SUFFIX_INTERCEPTORS)) {
          // Load interceptorMeta
          // 如果是com.alibaba.android.arouter.routes.ARouter$$Interceptors 开头则表示是interceptor信息
          // 将拦截器信息保存到Warehouse.interceptorsIndex中
          ((IInterceptorGroup) (Class.forName(className).getConstructor().newInstance())).loadInto(Warehouse.interceptorsIndex);
        } else if (className.startsWith(ROUTE_ROOT_PAKCAGE + DOT + SDK_NAME + SEPARATOR + SUFFIX_PROVIDERS)) {
          // Load providerIndex
          // 如果是com.alibaba.android.arouter.routes.ARouter$$Providers 开头则表示是provider信息
          // 将provider信息保存到Warehouse.providersIndex中
          ((IProviderGroup) (Class.forName(className).getConstructor().newInstance())).loadInto(Warehouse.providersIndex);
        }
      }
    }

    logger.info(TAG, "Load root element finished, cost " + (System.currentTimeMillis() - startInit) + " ms.");

    if (Warehouse.groupsIndex.size() == 0) {
      logger.error(TAG, "No mapping files were found, check your configuration please!");
    }

    if (ARouter.debuggable()) {
      logger.debug(TAG, String.format(Locale.getDefault(), "LogisticsCenter has already been loaded, GroupIndex[%d], InterceptorIndex[%d], ProviderIndex[%d]", Warehouse.groupsIndex.size(), Warehouse.interceptorsIndex.size(), Warehouse.providersIndex.size()));
    }
  } catch (Exception e) {
    throw new HandlerException(TAG + "ARouter init logistics center exception! [" + e.getMessage() + "]");
  }
}
```



上面先将所有的`group`类加载到内存中，并没有将所有`group`中的`Activity`信息加到到内存中，是因为 ARouter 中有一个按需加载的设计，因为对于型 app 中可能有很多页面，但是每次用户只会用到几个页面，如果一次性的全加载到内存中则会导致不必要的资源浪费。所以 ARouter 会先加载`group`类信息，在需要启动页面的时候才去加载对应`group`中的`Activity`信息。即按需加载。



## navigation实现



如果要跳转，需要调用如下代码：

```kotlin
// 在任意位置跳转到AppDebugActivity页面
ARouter.getInstance().build(ROUTE_APP_DEBUG).navigation()
```



这里 ARouter 先调用 build 方法，并传入 path。我们已经知道 ARouter 内部都是通过_ARouter 来实现的。



```java
// ARouter.build
public Postcard build(String path) {
  return _ARouter.getInstance().build(path);
}
```



最终 _ARouter.build 会返回一个 Postcard 对象，然后在调用Postcard的navigation()方法完成跳转任务。



先来看下_ARouter.build流程



```java
protected Postcard build(String path) {
  if (TextUtils.isEmpty(path)) {
    throw new HandlerException(Consts.TAG + "Parameter is invalid!");
  } else {
    // 如果设置了path 替换服务则先执行
    PathReplaceService pService = ARouter.getInstance().navigation(PathReplaceService.class);
    if (null != pService) {
      path = pService.forString(path);
    }
    return build(path, extractGroup(path), true);
  }
}

protected Postcard build(String path, String group, Boolean afterReplace) {
  if (TextUtils.isEmpty(path) || TextUtils.isEmpty(group)) {
    throw new HandlerException(Consts.TAG + "Parameter is invalid!");
  } else {
    if (!afterReplace) {
      PathReplaceService pService = ARouter.getInstance().navigation(PathReplaceService.class);
      if (null != pService) {
        path = pService.forString(path);
      }
    }
    // 创建并返回 Postcard 对象
    return new Postcard(path, group);
  }
}
```



这里先检查是否有PathReplaceService，如果有则先执行，这个 Service 是 ARouter 提供的一个服务，用于做 path 替换，例如进入 A 页面需要进行登录，则可以定义一个PathReplaceService，然后在里面判断如果path 是 A 页面并且没有登录，则返回登录页面的 path，这样就可以全局的处理 path 替换的操作了。



最后创建了一个 Postcard 对象，这里调用了一个extractGroup(uri.getPath())方法，从方法名可以看出这个方法是通过 path 提取 group 的。



```java
private String extractGroup(String path) {
  // path必须以”/“开头
  if (TextUtils.isEmpty(path) || !path.startsWith("/")) {
    throw new HandlerException(Consts.TAG + "Extract the default group failed, the path must be start with '/' and contain more than 2 '/'!");
  }

  try {
    // 截取第一个”/“后面的字符串当做 group 名字
    String defaultGroup = path.substring(1, path.indexOf("/", 1));
    
    if (TextUtils.isEmpty(defaultGroup)) {
      // path 至少要包含两个”/“
      throw new HandlerException(Consts.TAG + "Extract the default group failed! There's nothing between 2 '/'!");
    } else {
      return defaultGroup;
    }
  } catch (Exception e) {
    logger.warning(Consts.TAG, "Failed to extract default group! " + e.getMessage());
    return null;
  }
}
```



接下来就是创建 Postcard 了。

```java
public Postcard(String path, String group) {
  this(path, group, null, null);
}

public Postcard(String path, String group, Uri uri, Bundle bundle) {
  setPath(path);
  setGroup(group);
  setUri(uri);
  this.mBundle = (null == bundle ? new Bundle() : bundle);
}
```



Postcard 的构造方法很简单，就是保存 path、group、uri和 bundle 信息。

- path：activity 对应的 path
- gourp：path 对应的 group
- uri：通过 H5、scheme 等方式跳转会用到
- bundle：activity 跳转时传递的参数



Postcard 最终调用会 ARouter 的navigation()方法，ARouter 又会调用_ARouter的navigation()方法，完成实际的任务。

```java
// Postcard.java
public Object navigation() {
  return navigation(null);
}

// Postcard.java
public Object navigation(Context context) {
  return navigation(context, null);
}

// Postcard.java
public Object navigation(Context context, NavigationCallback callback) {
  return ARouter.getInstance().navigation(context, this, -1, callback);
}

// ARouter.java
public Object navigation(Context mContext, Postcard postcard, int requestCode, NavigationCallback callback) {
  return _ARouter.getInstance().navigation(mContext, postcard, requestCode, callback);
}

// _ARouter.java
protected Object navigation(final Context context, final Postcard postcard, final int requestCode, final NavigationCallback callback) {
  PretreatmentService pretreatmentService = ARouter.getInstance().navigation(PretreatmentService.class);
  // 如果配置了预处理服务则先处理预处理逻辑
  if (null != pretreatmentService && !pretreatmentService.onPretreatment(context, postcard)) {
    // Pretreatment failed, navigation canceled.
    return null;
  }

  try {
    // 去Warehouse中查找信息填充 postcard
    LogisticsCenter.completion(postcard);
  } catch (NoRouteFoundException ex) {
    logger.warning(Consts.TAG, ex.getMessage());

    if (debuggable()) {
      // 如果是 debug 模式，并且找不到对应的 Route 信息，则会弹出 toast 提示
      // Show friendly tips for user.
      runInMainThread(new Runnable() {
        @Override
        public void run() {
          Toast.makeText(mContext, "There's no route matched!\n" +
                         " Path = [" + postcard.getPath() + "]\n" +
                         " Group = [" + postcard.getGroup() + "]", Toast.LENGTH_LONG).show();
        }
      });
    }

    if (null != callback) {
      // 如果设置了NavigationCallback，则在发生异常的时候会调用 onLost 方法
      callback.onLost(postcard);
    } else {
      // No callback for this invoke, then we use the global degrade service.
      DegradeService degradeService = ARouter.getInstance().navigation(DegradeService.class);
      if (null != degradeService) {
        // 如果没设置NavigationCallback，并且设置了DegradeService，则会执行DegradeService的 onLost 方法
        degradeService.onLost(context, postcard);
      }
    }

    return null;
  }

  // 如果包含 path 信息，则会执行NavigationCallback 的onFound方法
  if (null != callback) {
    callback.onFound(postcard);
  }

  // 如果使用绿色通道则会跳过所有拦截器，否则会按顺序执行所有的拦截器
  if (!postcard.isGreenChannel()) {   // It must be run in async thread, maybe interceptor cost too mush time made ANR.
    // 
    interceptorService.doInterceptions(postcard, new InterceptorCallback() {
      /**
                 * Continue process
                 *
                 * @param postcard route meta
                 */
      @Override
      public void onContinue(Postcard postcard) {
        _navigation(context, postcard, requestCode, callback);
      }

      /**
                 * Interrupt process, pipeline will be destory when this method called.
                 *
                 * @param exception Reson of interrupt.
                 */
      @Override
      public void onInterrupt(Throwable exception) {
        if (null != callback) {
          callback.onInterrupt(postcard);
        }

        logger.info(Consts.TAG, "Navigation failed, termination by interceptor : " + exception.getMessage());
      }
    });
  } else {
    return _navigation(context, postcard, requestCode, callback);
  }

  return null;
}
```



上面流程最终会走到_ARouter的`_navigation()`方法



```java
private Object _navigation(final Context context, final Postcard postcard, final int requestCode, final NavigationCallback callback) {
  // 初始化 context
  final Context currentContext = null == context ? mContext : context;

  // 判断类型
  switch (postcard.getType()) {
    case ACTIVITY:
      // Build intent
      // 如果是 Activity 类型则创建 Intent
      final Intent intent = new Intent(currentContext, postcard.getDestination());
      // 设置 bundle 参数
      intent.putExtras(postcard.getExtras());

      // Set flags.
      // 设置 Intent flags
      int flags = postcard.getFlags();
      if (-1 != flags) {
        intent.setFlags(flags);
      } else if (!(currentContext instanceof Activity)) {    // Non activity, need less one flag.
        // 如果 content 不是 Activity 类型会添加FLAG_ACTIVITY_NEW_TASK flag
        intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
      }

      // Set Actions
      // 设置 actions
      String action = postcard.getAction();
      if (!TextUtils.isEmpty(action)) {
        intent.setAction(action);
      }

      // Navigation in main looper.
      runInMainThread(new Runnable() {
        @Override
        public void run() {
          // 启动 activity
          startActivity(requestCode, currentContext, intent, postcard, callback);
        }
      });

      break;
    case PROVIDER:
      // provider 类型
      return postcard.getProvider();
    case BOARDCAST:
    case CONTENT_PROVIDER:
    case FRAGMENT:
      // 处理 Fragment
      Class fragmentMeta = postcard.getDestination();
      try {
        // 反射创建对象
        Object instance = fragmentMeta.getConstructor().newInstance();
        // 如果是 Fragment 类型则设置参数
        if (instance instanceof Fragment) {
          ((Fragment) instance).setArguments(postcard.getExtras());
        } else if (instance instanceof android.support.v4.app.Fragment) {
          ((android.support.v4.app.Fragment) instance).setArguments(postcard.getExtras());
        }
				// 返回 Fragment
        return instance;
      } catch (Exception ex) {
        logger.error(Consts.TAG, "Fetch fragment instance error, " + TextUtils.formatStackTrace(ex.getStackTrace()));
      }
    case METHOD:
    case SERVICE:
    default:
      return null;
  }

  return null;
}
```



这里主要看 Activity、和 Fragment 创建过程。在启动 Activity 的过程需要用到一个context 参数，默认会使用`application`，也就是在`ARouter.init(content)`参数传进去的 context。另外 ARouter的 navigation也可以传入 context 参数，这里推荐传入 Activity。如果没有传入 Activity 则会使用`application`，这样就需要为 Intent 设置FLAG_ACTIVITY_NEW_TASK。否则在启动 activity 时会出现异常。



如果类型是 FRAGMENT则会利用反射创建对象并设置参数，并返回 Fragment 实例。



下面是`_ARouter#startActivity`方法。

```java
// 启动 activity
private void startActivity(int requestCode, Context currentContext, Intent intent, Postcard postcard, NavigationCallback callback) {
  if (requestCode >= 0) {  // Need start for result
    if (currentContext instanceof Activity) {
      ActivityCompat.startActivityForResult((Activity) currentContext, intent, requestCode, postcard.getOptionsBundle());
    } else {
      logger.warning(Consts.TAG, "Must use [navigation(activity, ...)] to support [startActivityForResult]");
    }
  } else {
    ActivityCompat.startActivity(currentContext, intent, postcard.getOptionsBundle());
  }

  // 设置转场动画
  if ((-1 != postcard.getEnterAnim() && -1 != postcard.getExitAnim()) && currentContext instanceof Activity) {    // Old version.
    ((Activity) currentContext).overridePendingTransition(postcard.getEnterAnim(), postcard.getExitAnim());
  }

  // 回调
  if (null != callback) { // Navigation over.
    callback.onArrival(postcard);
  }
}
```



这个方法就是直接调用系统的 startActivity 或startActivityForResult来启动 Activity 了。其中包含了转场动画的处理，跳转完成 ARouter 会调用 onArrival 方法。



至此 ARouter 跳转 Activity 的流程就完结了。



虽然我们这里只分析了 Activity 的跳转流程，但是这个流程包含了 ARouter 所有的关键原理。其他例如依赖注入、提供的各种服务等功能实现的思想都是与之类似的。



## ARouter 思想总结

大致总结一下 ARouter 的思想：

1. 利用编译时注解配置各种信息。例如`@Route`配置路由信息、`@Autowired`参数自动装配信息等。
2. 利用 APT 在编译期间搜集以上注解信息和相关类信息。然后生成指定的类和方法用于保存这些信息。
3. 在运行时先将第二步搜集到的信息加载到内存中或缓存起来。（缓存、按需加载等手段都是优化手段）
4. 再执行真实操作过程中预支了一些接口回调或者服务，已满足更多的需求场景。
5. 在第三步中拿出相关信息，然后进行组装。然后完成实际的跳转或者其他操作。





































# 参考

[ARouter GitHub](https://github.com/alibaba/ARouter)  

[开源最佳实践：Android平台页面路由框架ARouter](https://developer.aliyun.com/article/71687)



