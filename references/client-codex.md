# Codex 机制依据

核实日期：2026-09-08。来源：[官方 AGENTS.md 说明](https://learn.chatgpt.com/docs/agent-configuration/agents-md)。适用于该页描述的发现机制，未绑定或实测本地版本。

- 全局位置使用 CODEX_HOME；未设置时默认为 ~/.codex。优先首个非空 AGENTS.override.md，其次 AGENTS.md。
- 项目根通常为 Git 根；沿根至当前目录的祖先链，每目录最多取一份：AGENTS.override.md、AGENTS.md、配置的 project_doc_fallback_filenames。无项目根时只检查当前目录。
- 根至当前目录串接，较近目录的指导可覆盖先前指导。空文件跳过；project_doc_max_bytes 默认 32 KiB。配置可改变相关行为。

本 Skill 的操作边界：只沿此已知位置链探测，不寻找所有子目录。可通过只读项目根查询及已知配置取得必要字段，未知配置不按默认行为定案。不是实际注入日志，不认定每个候选已加载。

本页没有为任意 Markdown 链接或 @ 写法建立自动导入契约；不套用 Claude 的语法。组织策略、附加运行参数、其他规则功能影响无法核实时列未覆盖。不得为审查修改 CODEX_HOME、配置或真实全局文件。

## 配置定位与解析（2026-09-09 补核）

依据：[官方配置说明](https://learn.chatgpt.com/docs/config-file/config-basic)。只读取与入口有关的字段，不输出配置全文。

1. 从当前环境取得 CODEX_HOME 的值（只读，不重设）；缺省使用用户主目录的 .codex。探测其中 config.toml，再在已确定的项目根至当前目录链上探测 .codex/config.toml，不递归搜配置。
   这些配置探测不依赖 AGENTS.md 存在；只有目录／配置存在时也继续。先取得可确认的有效回退文件名，再判默认入口缺失后的候选，不能跳过配置直接宣称未发现项目指令。
2. 用 TOML 解析器提取 project_doc_fallback_filenames、project_doc_max_bytes，以及与当前项目相关的信任状态。解析失败报告具体位置与限制，不用零散正则猜测完整 TOML 语义。
3. 若会话明确提供 --profile，按该名称定位 Codex home 下的 <名称>.config.toml；明确的 --config 覆盖优先。文档顺序为会话覆盖、可信项目配置（近目录优先）、选定 profile、用户配置、Unix 的 /etc/codex/config.toml、默认值。只读与当前字段判断有关的层，缺少有效配置／信任证据时不声称最终值已确定。
4. 回退文件名按同目录的配置名称顺序定位；普通正文相对引用依据其明确约定或引用格式判断，不假定都相对 cwd。没有唯一依据时标路径基准待确认，不全盘找同名目标。

缺省位置不存在可记录「未发现配置」，不可访问或解析失败记录「配置未确认」。启动参数、托管限制、版本差异未知只限制对应机制结论；不检查认证文件，不枚举所有 profile，不为确认可信状态改变设置。
