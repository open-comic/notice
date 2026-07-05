# Kira 远程通知文件说明

本目录用于维护 Kira App 的远程通知文件。当前 App 默认读取：

```text
https://cdn.caolib.qzz.io/kira/notices.json
```

实际生效文件是 `notices.json`。其他文件都是说明、示例和校验辅助文件。

## 文件列表

- `notices.json`：线上通知数据，App 会读取这个文件。
- `FORMAT.md`：通知 JSON 的字段说明和行为规则。
- `notices.schema.json`：JSON Schema，可用于编辑器校验。
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

## 发布前检查

1. 确认 JSON 语法合法，不能有注释、尾随逗号。
2. `id`、`title`、`content` 不能为空。
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
