# Kira 远程配置文件说明

本目录用于维护 Kira App 的远程通知与高级设置配置。当前 App 默认读取：

```text
https://cdn.caolib.qzz.io/kira/notices.json
https://cdn.caolib.qzz.io/kira/copy-settings.json
```

实际生效文件是 `notices.json` 与 `copy-settings.json`。其他文件都是说明、示例和校验辅助文件。

## 文件列表

- `notices.json`：线上通知数据，App 会读取这个文件。
- `copy-settings.json`：线上高级设置配置（目前只有 COPY `version_code`）。
- `FORMAT.md`：通知 JSON 的字段说明和行为规则。
- `notices.schema.json` / `copy-settings.schema.json`：JSON Schema，可用于编辑器校验。
- `example.notices.json`：示例文件，不要直接当线上文件发布。
- `EDGEONE_DEPLOY.md`：EdgeOne Pages/CDN 发布注意事项。

## 最小可用格式

```json
{
  "schema_version": 1,
  "updated_at": "2026-07-05 16:50:00",
  "notices": [
    {
      "id": "notice-20260705-001",
      "level": "normal",
      "title": "通知标题",
      "content": "通知正文，支持 Markdown。",
      "published_at": "2026-07-05 16:50:00"
    }
  ]
}
```

## 高级设置配置（copy-settings.json）

```json
{
  "schema_version": 1,
  "updated_at": "2026-11-13 15:17:53",
  "version_code": "85"
}
```

- `version_code` 必须是纯数字字符串，App 会写入高级设置的 `versionCode`。
- 字段缺失或非法时 App 保留本地旧值，不会报错弹窗。
- 每日自动更新与高级设置里的「填充」按钮都读取这个文件；请求带时间戳与 no-cache 头。
- 发布前把 `updated_at` 改成实际修改时间。

## 发布前检查

1. 确认 JSON 语法合法，不能有注释、尾随逗号。
2. `id`、`title`、`content` 不能为空；`copy-settings.json` 的 `version_code` 不能为空。
3. `published_at` 建议填写真实发布时间。
4. 如果只是修改同一条通知，保持 `id` 不变；App 会因为内容变化重新视为未读。
5. 如果是全新通知，使用新的 `id`。
6. 如果通知已经结束，添加或更新 `expires_at`，不要直接删除，除非不希望用户在通知中心看到历史。

## App 行为摘要

- App 会按 `notices` 数组读取通知。
- 当前有效且未读的通知会计入红点数量。
- 进入通知中心不会自动标记已读，用户点击“全部已读”后才会标记。
- 已过期通知不会计入红点，但仍会显示在通知中心的过期分组。
- 置顶通知在通知中心排序靠前。
