
   🚀 GitHub Copilot CLI 完全使用手册

   📋 目录

     - 基础使用 (#1-基础使用)
     - 交互模式命令 (#2-交互模式命令)
     - 权限系统 (#3-权限系统)
     - 配置管理 (#4-配置管理)
     - 自定义 Agents (#5-自定义-agents)
     - 高级技巧 (#6-高级技巧)
     - 常见场景 (#7-常见场景)

   -------------------------------------------------------------------------------

   1. 基础使用

   1.1 启动方式

   交互模式（推荐）

     npx @github/copilot

   非交互模式（脚本/自动化）

     # 单次执行任务
     npx @github/copilot -p "你的问题" --allow-all-tools
     
     # 指定工作目录
     npx @github/copilot -p "分析当前项目" --add-dir E:\web\myproject
     
     # 允许所有路径（危险！）
     npx @github/copilot -p "任务" --allow-all-paths --allow-all-tools

   会话管理

     # 继续上次会话
     npx @github/copilot --continue

     # 选择历史会话恢复
     npx @github/copilot --resume

     # 恢复指定会话 ID
     npx @github/copilot --resume <session-id>

   -------------------------------------------------------------------------------

   2. 交互模式命令

   进入交互模式后，可以使用以下命令（以 / 开头）：

   2.1 基础命令

   ┌────────────────┬──────────────────────────────┐
   │ 命令           │ 作用                         │
   ├────────────────┼──────────────────────────────┤
   │ /help          │ 显示命令帮助                 │
   ├────────────────┼──────────────────────────────┤
   │ /exit 或 /quit │ 退出 CLI                     │
   ├────────────────┼──────────────────────────────┤
   │ /clear         │ 清空对话历史                 │
   ├────────────────┼──────────────────────────────┤
   │ /feedback      │ 提供反馈                     │
   ├────────────────┼──────────────────────────────┤
   │ /session       │ 显示当前会话信息             │
   ├────────────────┼──────────────────────────────┤
   │ /usage         │ 显示使用统计（token 消耗等） │
   └────────────────┴──────────────────────────────┘

   2.2 文件系统管理

     # 查看/切换工作目录
     /cwd                    # 显示当前目录
     /cwd E:\web\telethon    # 切换到指定目录

     # 管理可访问目录
     /add-dir E:\web\project123    # 添加信任目录
     /list-dirs                     # 列出所有信任目录

   2.3 AI 模型管理

     /model                      # 显示当前模型
     /model gpt-5                # 切换到 GPT-5
     /model claude-sonnet-4.5    # 切换到 Claude Sonnet 4.5
     /model claude-haiku-4.5     # 切换到 Claude Haiku（更快更便宜）

   可用模型：

     - claude-sonnet-4.5 ✅ （默认，推荐）
     - claude-sonnet-4
     - claude-haiku-4.5（速度快，成本低）
     - gpt-5

   2.4 权限管理

     # 重置已允许的工具列表
     /reset-allowed-tools

   2.5 用户管理

     /user show       # 显示当前用户
     /user list       # 列出所有已登录用户
     /user switch     # 切换用户
     /login           # 登录
     /logout          # 登出

   2.6 MCP 服务器管理

     /mcp show        # 显示 MCP 配置
     /mcp add         # 添加 MCP 服务器
     /mcp edit        # 编辑 MCP 配置
     /mcp delete      # 删除 MCP 服务器
     /mcp disable <server-name>   # 禁用指定服务器
     /mcp enable <server-name>    # 启用指定服务器

   2.7 Agent 管理

     /agent           # 浏览并选择可用 agents

   2.8 高级功能

     # 远程协作（AI 生成 PR）
     /delegate <prompt>

     # 配置终端（支持多行输入）
     /terminal-setup

     # 主题管理
     /theme show                # 显示当前主题
     /theme set dark            # 设置深色主题
     /theme set light           # 设置浅色主题
     /theme set auto            # 自动检测
     /theme list                # 列出所有主题

   -------------------------------------------------------------------------------

   3. 权限系统

   3.1 权限模式

   Copilot CLI 有三种权限模式：

   模式 1: 交互确认（默认）

   每次工具调用前都会询问：

     Allow? (y/n/always/never)

   模式 2: 部分允许

     # 允许所有 Git 命令
     npx @github/copilot --allow-tool 'shell(git:*)'

     # 允许文件编辑
     npx @github/copilot --allow-tool 'write'

     # 允许特定命令
     npx @github/copilot --allow-tool 'shell(npm install)'

   模式 3: 完全自动（危险）

     npx @github/copilot --allow-all-tools

   3.2 权限语法

   Shell 命令权限

     # 允许所有 shell 命令
     --allow-tool 'shell'

     # 允许所有 git 命令
     --allow-tool 'shell(git:*)'

     # 允许所有 npm 命令
     --allow-tool 'shell(npm:*)'

     # 允许特定命令
     --allow-tool 'shell(git push)'

   文件操作权限

     # 允许所有文件编辑
     --allow-tool 'write'

   MCP 工具权限

     # 允许某个 MCP 服务器的所有工具
     --allow-tool 'github-mcp-server'

     # 允许特定工具
     --allow-tool 'github-mcp-server(search_code)'

   3.3 拒绝规则（优先级最高）

     # 允许所有 git 命令，但禁止 push
     npx @github/copilot --allow-tool 'shell(git:*)' --deny-tool 'shell(git push)'

     # 允许所有工具，但禁止特定 MCP 工具
     npx @github/copilot --allow-all-tools --deny-tool 'MyMCP(dangerous_tool)'

   -------------------------------------------------------------------------------

   4. 配置管理

   4.1 配置文件位置

     Windows: C:\Users\<用户名>\.copilot\config.json
     Linux/Mac: ~/.copilot/config.json

   4.2 配置项详解

   你的当前配置：

     {
       "banner": "never",                    // 启动横幅：never/once/always
       "model": "claude-sonnet-4.5",         // AI 模型
       "render_markdown": true,              // 渲染 Markdown
       "screen_reader": false,               // 屏幕阅读器优化
       "theme": "auto",                      // 主题：auto/dark/light
       "trusted_folders": [                  // 信任目录（自动允许读写）
         "D:\\web",
         "E:\\web"
       ]
     }

   4.3 环境变量配置

     # 设置默认模型（Windows PowerShell）
     $env:COPILOT_MODEL = "claude-sonnet-4.5"

     # 自动允许所有工具
     $env:COPILOT_ALLOW_ALL = "true"

     # 自定义配置目录
     $env:XDG_CONFIG_HOME = "E:\my-copilot-config"

     # 使用 GitHub Token（跳过登录）
     $env:GITHUB_TOKEN = "ghp_xxxx"

     # 自定义 Custom Instructions 目录
     $env:COPILOT_CUSTOM_INSTRUCTIONS_DIRS = "E:\web\copilot-instructions,D:\projects\instructions"

   4.4 日志配置

     # 启用 Debug 日志
     npx @github/copilot --log-level debug

     # 自定义日志目录
     npx @github/copilot --log-dir E:\copilot-logs

     # 日志级别
     --log-level none        # 无日志
     --log-level error       # 仅错误
     --log-level warning     # 错误+警告
     --log-level info        # 错误+警告+信息（默认）
     --log-level debug       # 全部（包括调试）
     --log-level all         # 同 debug

   日志位置： %USERPROFILE%\.copilot\logs\

   -------------------------------------------------------------------------------

   5. 自定义 Agents

   5.1 什么是 Agent？

   Agent 是预设的 AI 角色，拥有特定技能和知识。你已经安装了 25 个 Skills！

   5.2 已安装的 Skills

     .system                              # 系统核心
     agent-development                    # Agent 开发
     algorithmic-art                      # 算法艺术
     brand-guidelines                     # 品牌指南
     canvas-design                        # Canvas 设计
     chat-history-exporter                # 聊天记录导出
     frontend-design                      # 前端设计
     gitingest                            # Git 仓库分析 ✅
     hook-development                     # Hook 开发
     plugin-skill-extractor               # 插件提取
     rapid-prototype-prompt-generator     # 快速原型生成
     ros2-wsl2-usbipd-serial-fishbot-tool # ROS2 机器人工具
     rust-learning-guide                  # Rust 学习指南
     skill-development                    # Skill 开发
     stm32-f103-beginner                  # STM32 入门
     stripe-best-practices                # Stripe 支付最佳实践
     theme-factory                        # 主题工厂
     tia-portal-scl-lad-workflow          # TIA Portal 工作流
     web-artifacts-builder                # Web 构件生成器
     wsl-codex-cli                        # WSL Codex 集成

   5.3 使用 Agent

     # 启动时指定 Agent
     npx @github/copilot --agent gitingest -p "分析 Telethon 仓库"

     # 交互模式中切换
     /agent
     # 然后从列表中选择

   5.4 创建自定义 Agent

   在项目根目录或 Git 仓库根目录创建 AGENTS.md：

     # 我的自定义 Agent

     ## Python 专家

     你是 Python 异步编程专家，精通 Telethon 库。

     ### 技能
     - 理解 MTProto 协议
     - 精通 asyncio 编程
     - 熟悉 Telegram Bot 开发

     ### 风格
     - 代码简洁
     - 注重性能
     - 详细注释

   然后启动：

     cd E:\web\my-telethon-project
     npx @github/copilot --agent "Python 专家"

   -------------------------------------------------------------------------------

   6. 高级技巧

   6.1 禁用内置 MCP 服务器

     # 禁用 GitHub MCP 服务器
     npx @github/copilot --disable-builtin-mcps

     # 禁用特定 MCP 服务器
     npx @github/copilot --disable-mcp-server github-mcp-server

   6.2 添加自定义 MCP 服务器

     # 通过 JSON 配置添加
     npx @github/copilot --additional-mcp-config '{"my-server": {"command": "node", "args": ["server.js"]}}'

     # 通过文件添加
     npx @github/copilot --additional-mcp-config @E:\mcp-config.json

   6.3 并行工具执行

     # 禁用并行执行（更安全）
     npx @github/copilot --disable-parallel-tools-execution

   6.4 无障碍模式

     # 启用屏幕阅读器优化
     npx @github/copilot --screen-reader

   6.5 流式输出控制

     # 禁用流式输出（完整响应后再显示）
     npx @github/copilot --stream off

     # 启用流式输出（默认）
     npx @github/copilot --stream on

   6.6 禁用颜色输出

     # 用于管道或日志
     npx @github/copilot --no-color

   -------------------------------------------------------------------------------

   7. 常见场景

   7.1 代码审查

     npx @github/copilot -p "审查 telethon/client/messages.py，找出潜在 bug" \
       --add-dir E:\web\telethon \
       --allow-tool 'write'

   7.2 重构代码

     npx @github/copilot -p "将 main.py 重构为异步模式" \
       --allow-tool 'write' \
       --model claude-sonnet-4.5

   7.3 生成文档

     npx @github/copilot -p "为 telethon/ 目录生成 API 文档" \
       --add-dir E:\web\telethon \
       --allow-tool 'write'

   7.4 Git 操作

     # 自动生成 Commit Message
     npx @github/copilot -p "分析 git diff 并生成 commit message" \
       --allow-tool 'shell(git:*)'

     # 生成 PR 描述
     npx @github/copilot -p "生成 PR 描述" \
       --allow-tool 'shell(gh:*)'

   7.5 批量文件处理

     npx @github/copilot -p "将 src/ 下所有 .js 文件转为 TypeScript" \
       --add-dir E:\web\myproject\src \
       --allow-tool 'write' \
       --allow-tool 'shell(tsc)'

   7.6 调试问题

     # 交互式 Debug
     npx @github/copilot
     # 然后输入：
     # "我运行 python bot.py 时报错 'FloodWaitError'，帮我分析"

   7.7 学习新技术

     npx @github/copilot --agent rust-learning-guide -p "教我 Rust 所有权系统"

   7.8 结合管道使用

     # 分析命令输出
     git log --oneline -10 | npx @github/copilot -p "总结这些提交" --allow-all-tools

     # 分析错误日志
     python app.py 2>&1 | npx @github/copilot -p "这个错误什么原因？" --allow-all-tools

   -------------------------------------------------------------------------------

   8. 创建别名（推荐）

   编辑 PowerShell Profile：

     notepad $PROFILE

   添加以下内容：

     # === GitHub Copilot CLI 别名 ===

     # 基础别名
     function cop { npx @github/copilot $args }

     # 快速提问（自动允许所有工具）
     function cop-quick { npx @github/copilot -p $args --allow-all-tools }

     # 继续上次会话
     function cop-continue { npx @github/copilot --continue }

     # 代码审查（针对 E:\web）
     function cop-review {
         npx @github/copilot -p $args --add-dir E:\web --allow-tool 'write'
     }

     # Git 助手
     function cop-git {
         npx @github/copilot -p $args --allow-tool 'shell(git:*)' --allow-tool 'shell(gh:*)'
     }

     # Telethon 专家（使用自定义 Agent）
     function cop-tg {
         npx @github/copilot --agent gitingest -p $args --add-dir E:\web\telethon
     }

     # Debug 模式
     function cop-debug {
         npx @github/copilot -p $args --log-level debug --allow-all-tools
     }

   重载 Profile：

     . $PROFILE

   使用示例：

     cop                                    # 交互模式
     cop-quick "帮我分析这个错误"             # 快速提问
     cop-continue                           # 继续会话
     cop-review "审查 main.py"              # 代码审查
     cop-git "生成 commit message"          # Git 助手
     cop-tg "分析 Telethon 的事件系统"      # Telethon 专家
     cop-debug "为什么崩溃了？"             # Debug 模式

   -------------------------------------------------------------------------------

   9. 最佳实践

   ✅ 推荐

     - 交互模式优先：复杂任务用交互模式，简单任务用 -p
     - 最小权限原则：不要默认 --allow-all-tools，根据需要授权
     - 使用 Agents：针对特定领域任务使用对应 Agent
     - 信任目录：将常用项目添加到 trusted_folders
     - 会话管理：善用 --continue 和 --resume 节省 token

   ❌ 避免

     - 盲目允许所有工具（除非脚本场景）
     - 不查看就执行 shell 命令
     - 在敏感目录使用 --allow-all-paths
     - 忽略日志（出问题时检查 ~/.copilot/logs/）

   -------------------------------------------------------------------------------

   10. 故障排查

   问题 1: 认证失败

     npx @github/copilot /login
     # 或
     $env:GITHUB_TOKEN = "ghp_your_token"

   问题 2: 工具权限被拒绝

     # 检查当前权限
     /reset-allowed-tools

     # 或启动时指定
     npx @github/copilot --allow-tool 'write'

   问题 3: 找不到命令

     # 确认 npm 全局安装
     npm list -g @github/copilot

     # 如果没有，重新安装
     npm install -g @github/copilot

   问题 4: 性能慢

     # 切换到更快的模型
     /model claude-haiku-4.5

     # 或禁用并行执行
     npx @github/copilot --disable-parallel-tools-execution

   问题 5: 中文乱码

     # 设置 UTF-8（已在你的 Profile 中配置）
     [Console]::OutputEncoding = [System.Text.Encoding]::UTF8

   -------------------------------------------------------------------------------

   11. 查看使用统计

   交互模式中：

     /usage

   会显示：

     - Token 消耗
     - API 调用次数
     - 会话时长
     - 成本估算
