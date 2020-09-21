当前项目是基于 OpenGL 开发的，计划是把渲染 API 替换为 bgfx，有下面几个限制：

1、渲染上下文是上层创建，bgfx 内部直接复用不需要另外创建

这种情况 bgfx 提供了一种解决方案，是在 bgfx::init 的时候传入 platformData.nwh 窗口句柄，以及 platformData.context 渲染上下文，如果两者都有传入，那么 bgfx 内部就不会重新创建。

2、兼容之前的 gl shader

对于跨平台 shader 代码，bgfx 的方案是采用自定义语法写一套代码，然后通过 shaderc 工具编译出各个平台的代码，供各个不同的 API 使用。因为目前大量代码是 glsl shader，如果要兼容，那么需要修改 bgfx 创建 shader 的代码，如果是旧的 glsl shader，那么就直接使用 gl 底层创建。