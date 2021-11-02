
## Android开发规范

防患未然,提升质量意识,降低故障率和维护成本;

标准统一,提升协作效率;

追求卓越的工匠精神,打磨精品代码。

### 目录结构

#### project 结构
...

#### 主项目目录结构
...

### 命名规范

#### Java 命名规范

遵守 Java 基本命名规范

待补充

#### layout 文件命名

+ Activity 的 layout 文件以 activity 开头

+ Fragment 的 layout 文件以 fragment 开头

+ Dialog 的 layout 文件以 dialog 开头

+ ListView、RecycleView、GridView 的 item layout 文件以 item 开头

+ Header、Footer 文件 layout 以 header、footer 开头

+ include 的 layout 文件以 include 开头

+ 自定义 View 或者提取的公用 layout 以 view 开头

#### drawable 资源文件命名
+ shape 以 shape 开头

    ```
    shape[_corner][_circle][_color][_stroke_direction_color][_radius]
    
    corner: 是否圆角
    circle: 圆角是否为圆形
    color: 背景色
    stroke_direction_color: 边框_方向_边框颜色
    radius: 圆角半径

    // 默认边框颜色为 divider 颜色

    // 默认圆角为 4dp

    例如 shape_corner_circle_stroke_white.xml 透明圆形圆角白色边框
        shape_corner_brown_2.xml 背景色为 brown 圆角半径为2dp
        shape_white_stroke_top.xml 白色背景上方边框
    ```

+ selector 以 selector 开头
    ```
    selector[_默认 drawable][_点击、选中的 drawable]

    非通用 selector: selector_[名称]

    例如 selector_corner_white_stroke_blue.xml 默认圆角白色，点击或选中为白色带蓝色边框
        selector_arrow_gray_down_up.xml 默认箭头朝下，点击或选中箭头朝上
    ```

+ 图片资源放到 mipmap-xxhdpi 目录下
    ```
    ic_模块_名称:   // 图标

    img_模块_名称:  // 背景图，占位图等 

    // 从设计手中拿到的png图片要做无损压缩处理
    ```

#### anim 资源文件

#### color 资源文件
+ 基本颜色定义在 res/values/colors.xml 文件里

+ selector 颜色定义在 res/color 目录下

#### dimen 资源文件命名
+ 基础约定大小：base_

+ 字体大小：text_名称

+ 普通定义：模块_名称

#### style 资源文件命名
+ style 资源采用小写单词+下划线方式命名

#### string 资源文件命名
+ 字符串以小写单词+下划线的方式命名

+ 模块名_名称

#### Id资源命名

+ 原则上以驼峰法命名，View 组件的资源 id 需要以 View 的缩写作为前缀

+ 控件缩写

| 控件            | 缩写         |
|-----------------|--------------|
| LinearLayout    | ll           | 
| RelativeLayout  | rl           | 
| ConstraintLayout| cl           | 
| ListView        | lv           | 
| ScrollView      | sv           | 
| TextView        | tv           | 
| Button          | btn          | 
| ImageView       | iv           | 
| CheckBox        | cb           | 
| RadioButton     | rb           | 
| EditText        | et           | 

### UI 相关

+ **控件尺寸使用 8dp * n**

+ 图片无损压缩：https://tinypng.com

+ 默认背景灰色： #FFFFFF color/white

+ 页面内模块之间的间距： 10dp 如 dimen/common_margin

+ 内容与屏幕的左右边距： 10dp dimen/common_margin 和 16dp dimen/common_margin_16

+ 模块内部内容 padding： 8dp

+ 通用分割线： #F2F2F2 color/color_divider

+ 图标与文本间距：6dp dimen/image_to_text

+ 文本与文本间距：4dp dimen/text_to_text

+ 图片与图片间距： 10dp dimen/image_to_image

+ 页面Title高度： 48dp dimen/base_title_height

+ 滑动 tab 高度： 40dp dimen/base_tab_height

+ button 内 padding： 上下为8dp，左右为10dp

+ 普通字体颜色： #333333 color/text_black

+ 普通字体大小： 14sp dimen/text_normal

+ 普通一行 key value 显示视图高度： 40dp

+ 高于普通 key value 显示视图高度： 48dp

### 组件
+ Activity#onSaveInstanceState()方法不是 Activity 生命周期方法,也不保证 一定会被调用。它是用来在 Activity 被意外销毁时保存 UI 状态的,只能用于保存临 时性数据,例如 UI 控件的属性等,不能跟数据的持久化存储混为一谈。持久化存储 应该在 Activity#onPause()/onStop()中实行。

+ 不建议使用隐式调用

+ 在 Activity 中显示对话框或弹出浮层时,尽量使用 DialogFragment,而非 Dialog/AlertDialog,这样便于随 Activity 生命周期管理对话框/弹出浮层的生命周期。

+ 源文件统一采用 UTF-8 的形式进行编码。

+ 禁止在设计布局时多次设置子 view 和父 view 中为同样的背景造成页面过 度绘制,推荐将不需要显示的布局进行及时隐藏。

+ 布局中不得不使用 ViewGroup 多重嵌套时,不要使用 LinearLayout 嵌套, 改用 RelativeLayout,可以有效降低嵌套数。

+ 不能在 Activity 没有完全显示时显示 PopupWindow 和 Dialog

+ 尽量不要使用 AnimationDrawable,它在初始化的时候就将所有图片加载到内存中,特别占内存,并且还不能释放,释放之后下次进入再次加载时会报错。

+ 不能使用 ScrollView 包裹 ListView/GridView/ExpandableListVIew;因为这 样会把 ListView 的所有 Item 都加载到内存中,要消耗巨大的内存和 cpu 去绘制图 面

+ 在 Activity.onPause()或 Activity.onStop()回调中,关闭当前 activity 正在执 行的的动画。

### 消息通信
+ 不要通过 Intent 在 Android 基础组件之间传递大数据(binder transaction 缓存为 1MB),可能导致 OOM

+ 线程池不允许使用 Executors 去创建,而是通过 ThreadPoolExecutor 的方 式,这样的处理方式让写的同学更加明确线程池的运行规则,规避资源耗尽的风险。

+ 不要在非 UI 线程中初始化 ViewStub,否则会返回 null

### 数据库
+ SharedPreference 中只能存储简单数据类型(int、boolean、String 等), 复杂数据类型建议使用文件、数据库等其他方式存储

+ SharedPreference 提交数据时,尽量使用 Editor#apply(),而非 Editor#commit()。一般来讲,仅当需要确定提交结果,并据此有后续操作时,才使 用 Editor#commit()。

    SharedPreference 相关修改使用 apply 方法进行提交会先写入内存,然后异步写入 磁盘,commit 方法是直接写入磁盘。如果频繁操作的话 apply 的性能会优于 commit, apply 会将最后修改内容写入磁盘。但是如果希望立刻获取存储操作的结果,并据此做相应的其他操作,应当使用 commit。

+ 数据库 Cursor 必须确保使用完后关闭,以免内存泄漏

+ 执行 SQL 语句时,应使用 SQLiteDatabase#insert()、update()、delete(), 不要使用 SQLiteDatabase#execSQL(),以免 SQL 注入风险

### 安全
+ 阻止 webview 通过 file:schema 方式访问本地敏感数据。

+ 不要把敏感信息打印到 log 中

+ 对于内部使用的组件,显示设置组件的"android:exported"属性为 false

+ 应用发布前确保 android:debuggable 属性设置为 false。

+ 将所需要动态加载的文件放置在 apk 内部,或应用私有目录中,如果应用 必须要把所加载的文件放置在可被其他应用读写的目录中(比如 sdcard),建议对不 可信的加载源进行完整性校验和白名单处理,以保证不被恶意代码注入

+ Android5.0 以后安全性要求较高的应用应该使用 window.setFlag (LayoutParam.FLAG_SECURE) 禁止录屏

+ Android WebView 组件加载网页发生证书认证错误时,采用默认的处理方法handler.cancel(),停止加载问题页面

    Android WebView 组件加载网页发生证书认证错误时,会调用 WebViewClient 类的 onReceivedSslError 方法,如果该方法实现调用了 handler.proceed()来忽略该证书错误,则会受到中间人攻击的威胁,可能导致隐私泄露

### 日志
+ 日志统一使用 LogUtil 工具类

+ tag 必须有意义

+ 不能使用 System.out.println 打印 log

### 其他
+ 使用 ARGB_565 代替 ARGB_888,在不怎么降低视觉效果的前提下,减少 内存占用

+ 尽量减少Bitmap(BitmapDrawable)的使用,尽量使用纯色(ColorDrawable)、 渐变色(GradientDrawable)、StateSelector(StateListDrawable)等与 Shape 结 合的形式构建绘图

+ 不要通过 Msg 传递大的对象,会导致内存问题






