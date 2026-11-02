# 版本检查与更新提示

客户端每次启动会向 GitHub 查一次最新 Release，**有新版才提示**，结果缓存 6 小时。

```
GET https://api.github.com/repos/chadyi/StudyOnTV/releases/latest
```

| 字段 | 用途 |
|---|---|
| `tag_name` | 版本号，写成 `v1.2.3` |
| `body` | 更新说明，直接显示在提示里；**含 `[force]` 就强制更新** |
| `assets[].browser_download_url` | 有 `.apk` 就点「立即更新」直接下载，没有就打开 Release 页 |

## 两种提示

| 类型 | 触发 | 表现 |
|---|---|---|
| 强制更新 | `body` 里有 `[force]` | 全屏页，返回键无效，只能点「立即更新」 |
| 普通更新 | 版本号更高但没有 `[force]` | 右上角小卡片，可关闭 |

## 发版流程

```bash
# 1. 改版本号（app/native/app/build.gradle.kts 里的 versionCode / versionName）
# 2. 打包
cd app/native && ./gradlew assembleDebug      # 或 assembleRelease（需签名）
# 3. 发布
gh release create v0.2.0 app/native/app/build/outputs/apk/debug/app-debug.apk \
  --title "v0.2.0" \
  --notes "· 新增题库导入导出
· 修复锁屏偶发不弹的问题
· 支持 100 以内加减法题库"
```

要强制更新时，在说明里加一行：

```
[force]
本次必须更新
```

发布后客户端最长 6 小时内（或下次启动）就会看到提示。
