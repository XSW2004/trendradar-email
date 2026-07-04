# TrendRadar 免费 QQ 邮箱推送部署说明

这个版本已经替你改好：

- GitHub Actions 每天运行一次。
- 运行时间为北京时间 20:00。
- 使用 QQ 邮箱 SMTP 推送报告。
- 移除了原项目的 7 天签到停用检查。
- 关闭默认 AI 分析和 AI 翻译，避免额外 API Key 依赖。

## 1. 准备 QQ 邮箱授权码

不要使用 QQ 密码。QQ 邮箱第三方 SMTP 登录需要使用授权码。

操作路径通常是：

1. 打开 QQ 邮箱网页版。
2. 进入「设置」。
3. 进入「账号」。
4. 开启 POP3/SMTP 或 IMAP/SMTP 服务。
5. 生成授权码。
6. 保存授权码，后面填到 GitHub Secret 的 `EMAIL_PASSWORD`。

## 2. 上传到 GitHub

1. 登录 GitHub。
2. 新建一个仓库，例如 `trendradar-email`。
3. 上传本项目压缩包解压后的全部文件。
4. 确认仓库里存在 `.github/workflows/crawler.yml`。

建议使用 public 仓库，这样更适合免费使用 GitHub Actions。

## 3. 添加 GitHub Secrets

进入你的仓库：

`Settings` -> `Secrets and variables` -> `Actions` -> `New repository secret`

添加下面 3 个 Secret：

| Name | Value |
| --- | --- |
| `EMAIL_FROM` | 你的 QQ 邮箱地址，例如 `123456@qq.com` |
| `EMAIL_PASSWORD` | QQ 邮箱授权码，不是 QQ 密码 |
| `EMAIL_TO` | 收件邮箱，可以和 `EMAIL_FROM` 相同 |

QQ 邮箱 SMTP 会被程序自动识别为：

- SMTP 服务器：`smtp.qq.com`
- 端口：`465`
- 加密：`SSL`

所以通常不用配置 `EMAIL_SMTP_SERVER` 和 `EMAIL_SMTP_PORT`。

## 4. 手动运行一次测试

进入仓库：

`Actions` -> `Get Hot News` -> `Run workflow`

运行后等待几分钟。成功时，你的 QQ 邮箱会收到一封 TrendRadar HTML 报告。

如果没有收到：

1. 打开本次 workflow run。
2. 查看 `Run crawler` 步骤日志。
3. 常见原因是 `EMAIL_PASSWORD` 填成了 QQ 密码，而不是授权码。
4. 检查 QQ 邮箱垃圾箱。

## 5. 自动运行时间

当前配置为：

```yaml
cron: "0 12 * * *"
```

GitHub Actions 使用 UTC 时间。北京时间是 UTC+8，所以 UTC 12:00 等于北京时间 20:00。

GitHub 定时任务可能会有几分钟延迟，这是正常现象。

## 6. 免费方案限制

这个方案不需要你打开电脑，但依赖 GitHub Actions 免费定时任务。

需要注意：

- GitHub Actions 定时任务不是严格准点，可能延迟。
- public 仓库长期没有任何活动时，GitHub 可能自动停用 scheduled workflow。
- 如果将来发现不再自动推送，手机打开 GitHub 仓库，进入 Actions 重新启用并手动运行一次即可。

## 7. 想改推送内容

常用配置文件：

- `config/frequency_words.txt`：修改你关心的关键词。
- `config/config.yaml`：修改平台、RSS、报告展示区域。

修改后提交到 GitHub，下一次定时运行会自动使用新配置。
