# Claude Code 机制依据

核实日期：2026-09-08。来源：[官方 Memory 说明](https://code.claude.com/docs/en/memory)。描述当前文档约定，未实测本地版本；不泛化到 Cowork。

- 用户入口 ~/.claude/CLAUDE.md；项目入口 CLAUDE.md 或 .claude/CLAUDE.md，可有 CLAUDE.local.md。
- 当前目录及祖先的 CLAUDE.md／CLAUDE.local.md 在启动读取；内容合并，不作为互相完全替换。后代入口按访问触发，本审查不为发现它们遍历项目。
- @ 引用可导入文件；AGENTS.md 不因名字而原生读取，可被导入。外部导入的授权状态影响实际读取，不能仅看引用就说已加载。
- .claude/rules/ 可包含路径条件；用户规则位置为 ~/.claude/rules/。规则发现是客户端机制，不授权体检遍历整个项目；本 Skill 只在入口或明确配置指向规则目录时展开，其他自动规则覆盖注明未核实。

按需核对配置排除、paths 条件、导入解析和授权。无法确认路径基准、版本、托管规则或附加目录时，停止该机制推断，仍可审阅明确指令正文。不以文件长度建议当硬性格式限制，不为审查调用 /init 或 /import。

## 配置定位与导入解析（2026-09-09 补核）

依据：[官方设置说明](https://code.claude.com/docs/en/settings)及上方 Memory 来源。

- 用户设置探测 ~/.claude/settings.json；若环境明确设置 CLAUDE_CONFIG_DIR，按该配置目录定位用户设置。Windows 主目录来自 USERPROFILE，不硬编码用户名。重定向对某类记忆入口的影响未核实时记录限制，不推定所有文件一起搬迁。
- 项目共享设置探测会话工作目录的 .claude/settings.json。项目本地设置通常为 .claude/settings.local.json；当前文档的 Git 根／主工作树定位存在版本、平台和所有权例外，Windows 使用与共享设置同位置。非 Windows 需按来源中「Where Claude Code keeps the local file」条件核实，不递归搜所有同名配置。
- 仅提取 claudeMdExcludes、与加载有关的已知环境值；规则文件的 paths 是其自身 frontmatter，不当 settings.json 字段。会话已知 --setting-sources、--settings、附加目录参数另行记录；未知覆盖或托管来源不猜测生效合并结果，不读取含会话凭证的 ~/.claude.json。
- @ 导入相对路径以包含导入的文件所在目录为基准，可用绝对路径；代码行内片段和围栏中的 @ 不按导入展开。普通 Markdown 引用仍按其格式或明确约定解析，不能一律套 @ 语义。
- 官方导入最多四跳与本 Skill 的五层保护是不同边界。记录宿主可能不再展开的部分；不把更深引用误称实际已加载，也不为证明导入成功执行会话操作。
- 设置缺失、访问被拒绝、JSON 解析失败、授权未知分别记录。外部导入权限未确认不能判实际已读；配置与版本证据不足仍可做正文检查。
