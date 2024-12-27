---
layout: default
title: "ThomeLuaPro 更新日志"
description: "ThomeLuaPro 项目的详细更新日志"
---

# ThomeLuaPro 开发人员名单
*(开发人员的名称的先后顺序与贡献度无关)*

- [MostBlack](https://github.com/TheMostBlack)
- [花](https://github.com/fI0wer)
- [Xiayu](https://github.com/xiayu372)
- [dingyi](https://github.com/dingyi222666)
- [难忘的旋律](https://github.com/nwdxlgzs)

---

## ThomeLuaPro 更新日志

### 1.0.0 Beta1
- **基于 AndroLua_Pro 5.0.24 版本，移植为 [PlutoLang](https://github.com/PlutoLang/Pluto)**  
- **支持省略：**  
  - `if` 语句的 then  
  - `switch` 语句的 do，同时支持使用 then  
  - `for` 语句的 do  
  - `try catch` 语句的 then  
- **构造函数名支持 `__construct` 和 `__init`**

- **支持 AndroLua_Pro 大部分常用 API 等**  
- **迁移至 AndroidX 和 AppCompat，最新 AppCompat 库与 Material Design Components (MDC)**

- **支持 `#define` 宏定义语句：**
  ```lua
  #define abc print("测试define")
  abc

  #define import(_package,clazz) local clazz = luajava.bindClass"_package.clazz"
  import(android.widget, LinearLayout)

  #define www \
  "abc"\
  ..\
  "c"

  print(www)
  ```

- **编译自带 Zlib 压缩 + Base64 编码，有效压缩 LuaC 体积，高效率**

- **支持加载 XML 布局**:

  **main.lua**:
  ```lua
  local ui = {}
  activity.setContentView(activity.loadXmlLayout("layout.xml", ui))

  function ui.测试.onClick()
    print(1)
  end
  ```

  **layout.xml**:
  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <LinearLayout
      xmlns:android="http://schemas.android.com/apk/res/android"
      android:layout_width="match_parent"
      android:layout_height="match_parent"
      android:orientation="vertical"
      android:gravity="center">

      <Button
          android:id="@+id/测试"
          android:layout_width="wrap_content"
          android:layout_height="wrap_content"
          android:text="1" />

  </LinearLayout>
  ```

  **当然，你也可以这样写**:
  ```lua
  local loadxml = function(...) return activity.loadXmlLayout(...) end

  local ui = {}
  activity.contentView = loadxml("layout.xml", ui)

  function ui.测试.onClick()
    print(1)
  end
  ```

- **SoraEditor 粘贴板粘贴非文本时出现的 `ClipData.Item {U (content) }` 已修复，某些符号吞宽度已修复**  
  *(2024-12-03 反馈，2024-12-04 修复)*

- **通过 pine 修复 EditorActionWindow 部分按钮未在指定情况下隐藏**

- **内置了 pine，可以像 xposed 一样 hook Java 方法（仅限自身）**

- **实现 SoraEditor 智能补全（智能 Java 方法补全，如果在 Java 层新增了函数，SoraEditor 中的补全会自动识别到这个新增的函数），C 层的 API 也同样支持补全**

- **自定义 print，支持显示长文本（部分手机的 toast 有长度限制）**

- **自动权限申请，打开软件时，若未赋予“管理所有文件”权限，会自动跳转至设置界面，指引用户赋予权限**

- **文件追踪功能完善，使用 LevelDB + Cseri Pro + 二级索引 实现**

- **新增 https 同步网络模块，基于 LuaSec 与 LuaSocket 与 OpenSSL**

- **修复 fileinfo 模块在获取文件名称和后缀时，后缀会包含乱码的问题**

---

### 1.0.0 Beta2
- 修复 IDE 的 Bug  
- `rawio` 提升效率，使用 `svc` 进行 IO 操作，纯 IO 的最快写法  
- 修复 `task` 的 Bug，修复打包 Bug，线程内 Table 操作等与主进程内 Table 操作等的 Bug  
- 基于 ApkEditor 重新实现打包功能的 Axml 生成逻辑  

---

### 1.0.0 Beta3
- 修复 IDE 的 Bug  
- 美化自定义 print 样式  
- 修复文件列表会刷新两次的 Bug，提升了效率  
- IDE 支持编写 Yaml，支持 Yaml 高亮  
- 使用 `manifest.yml` 代替 `init.lua`，但依然支持 `init.lua`，**但不推荐**，推荐使用 Yaml  
- 支持使用 Lua 编写 Welcome（但是需要你自己写申请权限），此模式的具体开启方式如下：

```yaml
manifest.yml:
welcome : true
```

- 内置了一个权限请求工具类，可以方便地进行请求权限操作  
- 并在项目文件夹内创建一个名为 `welcome` 的文件夹，如果启用了 LuaWelcome 模式，则会自动运行 `welcome` 文件夹内的 `main.lua` 文件  

**LuaWelcome 模式的权限请求示例**:
```lua
local Permission = luajava.bindClass("com.androlua.LuaPermission")
local permissions = luajava.bindClass("android.Manifest$permission")

local permission = Permission(activity)
local hasPermission = permission.checkPermission(permissions.MANAGE_EXTERNAL_STORAGE) -- 需要申请的权限

permission.callback = function(name, state)
  print(state) -- 如果state为true,则用户已成功赋予权限
end
```

- **LuaWelcome 模式的 API**:
  - **解压资源文件 API**:
    ```lua
    activity.unLuaPack(function()
      print("资源文件解压成功")
    end)
    ```
    > 解压成功后会自动运行传入的 callback

  - **进入程序主界面 API**:
    ```lua
    welcome.launchMain()
    ```

  - **版本是否变化，或是否覆盖安装了 App**:
    ```lua
    activity.checkInfo()
    ```
    为 `true` 则是，为 `false` 则不是

- **最简单的 LuaWelcome 模式示例**:
  ```lua
  local Permission = luajava.bindClass("com.androlua.LuaPermission")
  local permissions = luajava.bindClass("android.Manifest$permission")

  local permission = Permission(activity)
  local hasPermission = permission.checkPermission(permissions.MANAGE_EXTERNAL_STORAGE) -- 需要申请的权限

  permission.callback = function(name, state)
    if state then -- 如果state为true,则用户已成功赋予权限
      activity.unLuaPack(function() -- 解压资源文件
        welcome.launchMain() -- 解压成功,进入程序主界面
      end)
    end
  end

  -- 当然,你也可以使用Lua自己编写Welcome的界面
  ```

- **LuaWelcome 的 Activity 分离为单独的 activity**  
- 优化 welcome 解压逻辑，修复 LuaPermission 请求不存在的权限会爆栈，优化 LuaWelcome  
- 完善了工程模板  
- 修复 LuaZip 的 extract 函数在解压 Zip 时不解压 Zip 内的文件夹的 Bug  
- 修复部分工程打包后闪退的 Bug  
- 之前的版本，打包出来的 Apk 使用的是 V1 签名，会导致无法安装，现在是 V1+V2+V3+V4 签名  
- 修复打包出来的 Apk 会无法安装的 Bug（非签名问题），是因为错误地把 `resource.arsc` 进行了压缩，但 Android 不允许压缩 `resource.arsc`  
- 支持切换编辑器为 LuaEditor/SoraEditor  
- 支持在 `init.lua` / `yaml` 里设置启用 EdgeToEdge  

**manifest.yml 的格式如下**:
```yaml
manifest.yml:
appname : "MyApplication"
packagename : "com.ThomeLuaPro.MyApplication"
theme : "@com.google.android.material:style/Theme.Material3.DynamicColors.DayNight.NoActionBar"
debug : true
edgeToEdge : true
user_permission :
  - INTERNET
  - WRITE_EXTERNAL_STORAGE
```

---

### 1.0.0 Beta4
- 优化 LuaEditor，优化报错提示，添加了相关的 C API 等  
- 新增 Apk 体积精简化功能，对打包后的 Apk 体积进行精简（部分 Java 方法无法使用）

---

### 1.0.0 Beta5
- 修复新建项目界面输入法影响布局  
- 修复新建项目界面项目模板被遮挡  
- 文件标签支持长按  
- 彻底修复部分打包后的工程会闪退的 Bug  
- 修复打开工程后再关闭工程后，点击 Play 运行按钮时，会报错的 Bug  
- 优化运行工程前语法错误的提示，使其显示完整的错误信息  
- 为盲人提供了更好的支持（考虑屏幕朗读器）  
- Main 和 LuaActivity 开启了硬件加速  
- 修复进入导入分析时，代码没有保存的 Bug  
- 新增 LuaEditor 监听点击补全框 item 事件：
  ```lua
  luaEditor.setPanelItemClickListener{onClick=function(text)end}
  ```
- SoraEditor 支持代码格式化，详细的语法错误检查  
- **封装了一个 SoraEditor 的报错波浪线函数**：

  - 根据字符位置:
    ```lua
    local Editor = luajava.bindClass "com.androlua.pluto.EditorUtils"
    Editor.setError(editor, id, title, message, startIndex, endIndex)
    ```
  - 根据行列:
    ```lua
    local Editor = luajava.bindClass "com.androlua.pluto.EditorUtils"
    Editor.setError(editor, id, title, message, startCol, startRow,endCol, endRow)
    ```
  - 多个 error:
    - 根据字符位置:
      ```lua
      local Editor = luajava.bindClass "com.androlua.pluto.EditorUtils"
      Editor.addError(editor, id, title, message, startIndex, endIndex)
      ```
    - 根据行列:
      ```lua
      local Editor = luajava.bindClass "com.androlua.pluto.EditorUtils"
      Editor.addError(editor, id, title, message, startCol, startRow,endCol, endRow)
      ```

- 修复 welcome 报错的 Bug  
- 优化 LuaEditor 补全，修改 LuaEditor 补全框  
- 优化 IDE 流畅度  
- 内置 `CheckSyntax` 功能，用于最快速度的检查 Lua 代码的语法错误:
  ```lua
  -- 检查luaPath文件中的语法错误，第二个参数为传入的是否为文件路径，
  -- 传入true则第一个参数应当传入文件路径，否则第一个参数应当传入Lua代码，
  -- 返回错误信息或syntax correct(无错误)
  CheckSyntax.checkSyntax(luaPath, true)
  ```

- **IDE 后续会支持编写 Java/C，目前 C 的编写功能开发会很快开始进行，目前已经做到了只需要 30MB 就可以获得一个完整的 ndk**

---
