# notices.json 格式说明

## 顶层结构

推荐使用对象结构：

```json
{
  "schema_version": 1,
  "updated_at": "2026-07-05 16:50:00",
  "notices": []
}
```

App 读取通知列表时会依次识别这些字段：

- `notices`
- `notifications`
- `items`

也支持顶层直接是数组，但不推荐，因为后续不好扩展元信息。

## 通知字段

| 字段 | 必填 | 类型 | 说明 |
| --- | --- | --- | --- |
| `id` | 是 | string | 通知唯一标识。建议稳定、可读，例如 `notice-20260705-001`。 |
| `title` | 是 | string | 通知标题。为空会被 App 过滤。 |
| `content` | 是 | string | 通知正文，支持 Markdown。为空会被 App 过滤。 |
| `published_at` | 建议 | string | 发布时间。推荐 `YYYY-MM-DD HH:mm:ss` 或 ISO 8601。 |
| `level` | 否 | string | 通知等级：`normal`、`urgent`、`pinned`。 |
| `pinned` | 否 | boolean | 是否置顶。`true` 时等同置顶通知。 |
| `expires_at` | 否 | string | 过期时间。到期后不再计入未读红点。 |
| `url` | 否 | string | 外部链接。存在时 App 会显示“打开链接”。 |

## 兼容字段名

为了兼容不同写法，App 还会识别这些别名：

- 等级：`level`，也可写 `type`。
- 发布时间：`published_at`、`publishedAt`、`created_at`、`date`。
- 过期时间：`expires_at`、`expiresAt`、`expire_at`。
- 置顶：`pinned`、`is_pinned`、`sticky`。

新文件建议统一使用 snake_case：`published_at`、`expires_at`。

## level 可选值

| 值 | 效果 |
| --- | --- |
| `normal` | 普通通知。 |
| `urgent` | 紧急通知，使用错误色强调。 |
| `pinned` | 置顶通知，在通知中心靠前。 |

兼容别名：

- 紧急：`urgent`、`emergency`、`critical`、`紧急`
- 置顶：`pinned`、`pin`、`top`、`sticky`、`置顶`
- 其他值都会按普通通知处理。

## Markdown 内容

`content` 支持 Markdown。JSON 中换行要写成 `\n`：

```json
{
  "content": "第一行\n\n- 要点一\n- 要点二"
}
```

可使用标题、列表、引用、链接等基础 Markdown。避免写过长内容，通知中心更适合短公告。

## 时间和过期规则

- `expires_at` 为空：通知长期有效。
- 当前时间大于等于 `expires_at`：通知过期。
- 过期通知不会计入红点，也不会作为未读活跃通知提示。
- 过期通知仍会出现在通知中心的“过期通知”分组。

建议时间格式：

```text
2026-07-05 16:50:00
2026-07-05T16:50:00+08:00
```

如果需要避免时区歧义，优先使用带时区的 ISO 8601。

## 已读判定

App 的已读指纹包含这些字段：

- `id`
- `level`
- `pinned`
- `published_at`
- `expires_at`
- `title`
- `content`
- `url`

因此：

- 修改标题、正文、链接、时间等内容后，同一条通知会重新变成未读。
- 如果只是修错别字，也会重新未读，这是当前 App 的预期行为。
- 删除远端通知后，App 不再保留它的通知内容。

## 排序规则

通知中心排序：

1. 当前有效的置顶通知靠前。
2. 其他通知按发布时间倒序。

启动提示排序：

1. 紧急通知。
2. 置顶通知。
3. 普通通知。
4. 同级按发布时间倒序。
