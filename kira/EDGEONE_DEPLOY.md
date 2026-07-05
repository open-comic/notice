# EdgeOne 发布说明

Kira App 当前默认读取：

```text
https://cdn.caolib.qzz.io/kira/notices.json
```

因此 EdgeOne 上需要确保这个路径能直接返回 JSON 文件。

## 推荐目录结构

如果仓库根目录直接作为静态站点输出目录：

```text
repo-root/
  kira/
    notices.json
    README.md
    FORMAT.md
    notices.schema.json
    example.notices.json
    EDGEONE_DEPLOY.md
```

访问路径应为：

```text
https://cdn.caolib.qzz.io/kira/notices.json
```

如果当前目录本身就是 `kira/`，则部署输出目录的上一级需要能映射出 `/kira/notices.json`。

## 静态项目构建配置

这个通知仓库只需要静态文件，不需要 Node 依赖安装或构建。如果 EdgeOne Pages 自动执行 `pnpm install` 并失败，通常是因为仓库中存在 `package.json`、`pnpm-lock.yaml`，或控制台配置了安装命令。

可在项目根目录放置 `edgeone.json`，跳过安装和构建：

```json
{
  "installCommand": "echo skip install",
  "buildCommand": "echo skip build",
  "outputDirectory": "."
}
```

如果 EdgeOne 控制台允许留空，也可以直接留空：

- Install Command：留空
- Build Command：留空
- Output Directory：`.` 或实际静态目录

## 缓存建议

通知文件建议使用较短缓存，避免发布后长时间不生效。

建议：

```text
Cache-Control: public, max-age=300
```

如果需要紧急更新，可在 EdgeOne 控制台刷新缓存，或临时更改文件内容中的 `updated_at` 方便排查。

## 发布后验证

发布后至少检查这几项：

1. 浏览器打开 `https://cdn.caolib.qzz.io/kira/notices.json` 可以看到 JSON。
2. 返回状态码是 200。
3. 响应内容不是 HTML 错误页。
4. JSON 没有注释和尾随逗号。
5. 中文显示正常，文件使用 UTF-8 编码。

可用命令：

```powershell
Invoke-RestMethod https://cdn.caolib.qzz.io/kira/notices.json
```

或：

```powershell
curl https://cdn.caolib.qzz.io/kira/notices.json
```

## 不建议

- 不要把 `example.notices.json` 当线上文件。
- 不要使用 `http://`，App 现在默认使用 HTTPS。
- 不要给通知文件设置过长 CDN 缓存。
- 不要为了删除红点直接删除所有通知；应让用户在 App 内手动标记已读。
