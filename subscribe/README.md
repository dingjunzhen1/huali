# 个人黄历 · iPhone 订阅部署包

这个目录里的文件就是要放到公网静态托管上的全部内容。上传后拿到 https 直链，在 iPhone 日历里「添加订阅日历」填进去即可。

## 目录内容

| 文件 | 说明 |
| --- | --- |
| `personal_almanac_2026.ics` … `_2036.ics` | 按年拆分，每年 365/366 条，约 510 KB（简报版），**推荐订阅这些** |
| `personal_almanac.ics` | 全量单文件，4018 条 / 约 5.6 MB，一次订阅全年份但抓取较慢 |
| `index.html` | 索引页，用来确认部署成功、顺手点开文件 |
| `.nojekyll` | 告诉 GitHub Pages 不要跑 Jekyll 处理（避免多余构建） |

每条事件包含：今日判定（偏顺/平稳/偏谨慎，附打分明细）、今日三条、宜、忌、冲煞、节气、十神、吉神、凶煞、彭祖百忌、喜财福神方位、十二时辰吉凶。

## 部署方式一：GitHub Pages（有 GitHub 账号，全程网页点选，不用 git）

1. 新建仓库（Public）。免费账号的 Pages 只能来自公开仓库。
2. 把本目录里的**所有文件**上传到仓库根目录（Add file → Upload files → 拖进去 → Commit）。
3. 仓库 **Settings → Pages** → Source 选 `Deploy from a branch` → Branch 选 `main`、目录 `/ (root)` → Save。
4. 等 1~2 分钟，页面顶部会显示站点地址，形如：
   ```
   https://<用户名>.github.io/<仓库名>/
   ```
   打开它确认能看到索引页。
5. 订阅用的地址就是把文件名接在后面：
   ```
   https://<用户名>.github.io/<仓库名>/personal_almanac_2026.ics
   ```
   先在电脑浏览器里打开，能下载到 ics 文件才算成功。

> 免费版 GitHub Pages 站点是公开的，任何人拿到链接都能访问（描述里有「命主：男 · 壬午 辛亥 己丑 丁卯」）。在意隐私就别用公开托管，或改用不包含个人信息的那版文件。

## 部署方式二：阿里云 OSS / 腾讯云 COS（国内访问更稳）

**阿里云 OSS**
1. 创建 Bucket，读写权限选 **公共读**，地域就近。
2. 上传本目录所有文件到根目录。
3. 文件详情里复制 URL：`https://<bucket>.oss-cn-<地域>.aliyuncs.com/personal_almanac_2026.ics`
4. 想更省事可在 Bucket 的「静态页面」里把默认首页设为 `index.html`。

**腾讯云 COS**
1. 创建存储桶，访问权限选 **公有读私有写**。
2. 上传文件，复制对象地址：`https://<bucket>-<APPID>.cos.<地域>.myqcloud.com/personal_almanac_2026.ics`

可选：把文件的 `Content-Type` 设为 `text/calendar; charset=utf-8`。不设一般也能被识别，因为订阅端是按内容解析的。

## iPhone 端订阅

1. 打开「日历」App → 底部「日历」→ 左下「添加日历」→ **添加订阅日历**。
2. 「服务器」填 https 直链（例如 `https://xxx.github.io/almanac/personal_almanac_2026.ics`）。
3. 下一步 → 改名称（建议「黄历 2026」）和颜色 → 存储。
4. 其余年份重复 1~3 步，名称区分年份即可。

等价的设置路径：设置 → 日历 → 账户 → 添加账户 → 其他 → 添加已订阅的日历。

刷新频率：设置 → 日历 → 账户 → 抓取新数据 → 已订阅的日历，可选每 5 分钟 / 每小时 / 每天 / 手动。这只是上限，实际由 Apple 服务器按自己的节奏抓取，通常一天一次；订阅日历是只读的。

## 以后怎么更新

1. 本地重新生成（简报版 + 按年）：
   ```
   python bazi_almanac.py --brief --per-year --output subscribe\personal_almanac.ics
   python bazi_almanac.py --brief --output personal_almanac.ics
   ```
   喜用神改 `bazi_almanac.py` 顶部的 `XISHEN` / `JISHEN`，或用 `--xi 火,土 --ji 水,木,金` 临时覆盖。
2. 把新文件覆盖上传到同一路径（同名同址）。
3. 手机端等下一次自动抓取；想立刻生效就把订阅删掉重加一次。

## 上线前自检

- [ ] 电脑浏览器直接打开 https 直链，能下载到 `.ics`，不是登录页、不是 404、不是跳转
- [ ] 链接是 https，不是 http
- [ ] 文件里 `BEGIN:VCALENDAR` 开头、`END:VCALENDAR` 结尾
- [ ] iPhone 添加订阅后，翻到某一天能看到「XX日·破·慎 | 宜：… | 忌：…」，详情里有【今日判定】
