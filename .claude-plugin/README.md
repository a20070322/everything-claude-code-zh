### 插件清单注意事项

如果你计划编辑 `.claude-plugin/plugin.json`,请注意 Claude 插件验证器会强制执行几个**未文档化但严格的约束**,这些约束可能导致安装失败并出现模糊的错误(例如 `agents: Invalid input`)。特别是,组件字段必须是数组,`agents` 必须使用显式文件路径而不是目录,并且需要 `version` 字段才能可靠地进行验证和安装。

这些约束从公开示例中并不明显,并且过去已导致反复的安装失败。这些约束在 `.claude-plugin/PLUGIN_SCHEMA_NOTES.md` 中有详细记录,在更改插件清单之前应该查看该文档。
