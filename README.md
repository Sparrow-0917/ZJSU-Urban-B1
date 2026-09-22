[deepseek_tharness 安装方法.txt](https://github.com/user-attachments/files/32517780/deepseek_tharness.txt)
========================================
DeepSeek Harness 安装与使用说明
========================================
版本锁定：@deepseek-ai/dsh@0.1.7-alpha.1
适用系统：Windows（PowerShell） / macOS / Linux
更新日期：2026-09-22

----------------------------------------
一、前置条件
----------------------------------------
1. 安装 Node.js，版本要求：
   ^22.19.0 或 >=24.0.0

2. 检查 Node.js 版本：
   node -v

   如果版本不符合，请前往 https://nodejs.org 下载安装最新 LTS 版本。


----------------------------------------
二、安装与启动
----------------------------------------
1. 打开 PowerShell（或终端），运行以下命令：

   npx --yes @deepseek-ai/dsh@0.1.7-alpha.1 web

2. 首次运行会自动下载依赖包，请耐心等待（可能需要几分钟）。

3. 启动成功后，终端会输出类似地址：

   dsh web: http://127.0.0.1:3080

   浏览器会自动打开该地址。如果没有自动打开，手动在浏览器输入即可。

4. 常用启动参数：

   --no-open       启动时不自动打开浏览器
   --port 3081     指定其他端口（默认 3080）

   示例：
   npx --yes @deepseek-ai/dsh@0.1.7-alpha.1 web --no-open
   npx --yes @deepseek-ai/dsh@0.1.7-alpha.1 web --port 3081


----------------------------------------
三、首次配置
----------------------------------------
1. 进入 Web UI 后，点击左下角：
   Settings（设置） -> Models（模型）

2. 在 DeepSeek 卡片中填入你的 API Key。
   API Key 获取地址：https://platform.deepseek.com

3. 保存后无需重启，立即生效。

4. 回到主页，选择一个工作区（Workspace）目录，即可开始使用。


----------------------------------------
四、常见报错与解决方法
----------------------------------------
1. ETARGET：找不到匹配版本
   报错示例：
   npm error code ETARGET
   npm error notarget No matching version found for
   @deepseek-ai/dsh-client-ui-sidebar-documentpreview@^0.1.5-rc.3

   原因：
   预发布版本（rc / alpha）分阶段发布，依赖可能不完整；
   或 npm 镜像源同步延迟。

   解决方法：
   - 指定具体版本（推荐）：
     npx --yes @deepseek-ai/dsh@0.1.7-alpha.1 web
   - 临时使用官方源：
     npx --yes --registry=https://registry.npmjs.org/ @deepseek-ai/dsh@0.1.7-alpha.1 web
   - 清理缓存后重试：
     npm cache clean --force
     npx --yes @deepseek-ai/dsh@0.1.7-alpha.1 web


2. EADDRINUSE：3080 端口被占用
   报错示例：
   Error: listen EADDRINUSE: address already in use 127.0.0.1:3080
   Failed plugins (1): webserver (required)

   原因：
   之前启动的 DSH 进程仍在运行，端口未释放。

   解决方法：
   - 先打开浏览器访问 http://127.0.0.1:3080
     如果能打开，说明服务已在运行，直接用即可，无需重启。
   - 如果确实想重启，结束占用端口的进程：
     Get-NetTCPConnection -LocalPort 3080 -State Listen | ForEach-Object { Stop-Process -Id $_.OwningProcess -Force }
   - 或换一个端口启动：
     npx --yes @deepseek-ai/dsh@0.1.7-alpha.1 web --port 3081
     然后访问 http://127.0.0.1:3081


3. npm warn deprecated node-domexception
   报错示例：
   npm warn deprecated node-domexception@1.0.0:
   Use your platform's native DOMException instead

   说明：
   这只是提醒，不是错误，不影响安装和运行，忽略即可。


4. npm warn using --force
   报错示例：
   npm warn using --force Recommended protections disabled.

   说明：
   使用了 --force 参数，npm 提示安全保护被禁用。
   命令已正常执行，忽略即可。


----------------------------------------
五、清理缓存与卸载
----------------------------------------
注意：清理前请先关闭正在运行的 DSH 服务（关闭命令行窗口或按 Ctrl+C）。

1. 清理 npx 缓存（安全，推荐优先执行）
   在 PowerShell 中运行：

   $npxCachePath = Join-Path $env:LocalAppData "npm-cache\_npx"
   if (Test-Path $npxCachePath) {
       Remove-Item -Path $npxCachePath -Recurse -Force
       Write-Host "npx 缓存已清理。"
   } else {
       Write-Host "未找到 npx 缓存目录。"
   }

2. 清理 npm 全局缓存（可选，释放空间）
   npm cache clean --force

   说明：只清理包的压缩缓存，不会卸载已全局安装的包。

3. 查看 DSH 个人数据目录大小
   $dshPath = "$env:USERPROFILE\.dsh"
   if (Test-Path $dshPath) {
       $size = (Get-ChildItem $dshPath -Recurse -ErrorAction SilentlyContinue | Measure-Object -Property Length -Sum).Sum / 1MB
       Write-Host ".dsh 目录大小: $([math]::Round($size, 2)) MB"
   }

4. 彻底删除 DSH 个人数据（危险！）
   警告：删除 ~/.dsh 会永久清除所有会话记录、配置、API 密钥，不可恢复。
   除非确定不再使用，否则不要执行。

   备份（可选）：
   Copy-Item -Path "$env:USERPROFILE\.dsh" -Destination "$env:USERPROFILE\.dsh-backup" -Recurse

   删除：
   Remove-Item -Path "$env:USERPROFILE\.dsh" -Recurse -Force


----------------------------------------
六、命令速查表
----------------------------------------
检查 Node 版本        node -v
启动 DSH              npx --yes @deepseek-ai/dsh@0.1.7-alpha.1 web
不自动打开浏览器      npx --yes @deepseek-ai/dsh@0.1.7-alpha.1 web --no-open
换端口启动            npx --yes @deepseek-ai/dsh@0.1.7-alpha.1 web --port 3081
查找占用 3080 的进程  Get-NetTCPConnection -LocalPort 3080 -State Listen
结束占用 3080 的进程  Get-NetTCPConnection -LocalPort 3080 -State Listen | ForEach-Object { Stop-Process -Id $_.OwningProcess -Force }
清理 npx 缓存         Remove-Item -Path (Join-Path $env:LocalAppData "npm-cache\_npx") -Recurse -Force
清理 npm 缓存         npm cache clean --force
查看可用版本          npm view @deepseek-ai/dsh versions --json


----------------------------------------
七、注意事项
----------------------------------------
1. 不要关闭正在运行 DSH 的命令行窗口，关掉窗口等于停止服务。

2. 下次使用前，先打开浏览器访问 http://127.0.0.1:3080
   如果能打开，说明服务已在运行，直接用即可，不必重新启动。

3. DeepSeek Harness 处于开发者预览阶段，版本迭代较快，
   建议锁定 0.1.7-alpha.1 这个已验证可用的版本。

4. 官方 GitHub 仓库：
   https://github.com/deepseek-ai/deepseek-harness

5. API Key 获取地址：
   https://platform.deepseek.com


========================================
文档结束
========================================
