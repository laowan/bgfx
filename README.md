当前项目是基于 OpenGL 开发的，计划是把渲染 API 替换为 bgfx，有下面几个限制：

**1、渲染上下文是上层创建，bgfx 内部直接复用不需要另外创建**

这种情况 bgfx 提供了一种解决方案，是在 bgfx::init 的时候传入 platformData.nwh 窗口句柄，以及 platformData.context 渲染上下文，如果两者都有传入，那么 bgfx 内部就不会重新创建。

**2、前后缓存切换是上层控制，bgfx 不用理会**

目前 bgfx 内部会在调用 renderFrame 时 swapbuffer，这个逻辑需要修改 bgfx 的代码去掉。

**3、兼容之前的 gl shader**

对于跨平台 shader 代码，bgfx 的方案是采用自定义语法写一套代码，然后通过 shaderc 工具编译出各个平台的代码，供各个不同的 API 使用。因为目前项目存在大量 glsl shader 代码，如果用 bgfx 的语法全部重新，工作量很大，如果要兼容，那么需要修改 bgfx 创建 shader 的代码，直接支持读取编译 glsl shader。如果输入 shader 是 glsl 写的，那么底层只能使用 gl 渲染，只用输入 shader 是 bgfx 语法写的才支持其他图形 API。