# 上线指南（GitHub Pages + 腾讯 EdgeOne 国内加速）

这是一个**纯静态网站**（一个 `index.html` + `uploads/` 图片 + `audio/` 音频），不需要任何构建。
托管思路：**GitHub Pages 当源站（origin）→ 腾讯 EdgeOne 套在前面做国内 CDN 加速 → 绑你的子域名**。

---

## 第 1 步：开 GitHub Pages（源站）

仓库已经在 GitHub：`lyramira-art/ashen-commission-site`。把本地改动推上去后：

```bash
git add -A
git commit -m "网站 v3：压平设计稿为可上线静态站 + 真实播放器"
git push
```

然后在网页上：**仓库 → Settings → Pages → Build and deployment**
- Source：**Deploy from a branch**
- Branch：**master** / **(root)** → Save

一两分钟后，源站地址就是 `https://lyramira-art.github.io/ashen-commission-site/`。
先用这个地址确认页面/播放都正常，再接 EdgeOne。

> 站点全部用**相对路径**（`uploads/…`、`audio/…`），所以不管是 github.io 子路径、子域名还是 CDN 回源，都能正常加载，无需改动。

---

## 第 2 步：腾讯 EdgeOne 加速 + 绑子域名

两种接法，二选一：

### 方案 A（推荐·省事）：EdgeOne 回源到 GitHub Pages
1. EdgeOne 控制台 → 添加站点（用你的主域名 `mszook.art`）。
2. 加一条**域名接入 / 加速域名** = 你的子域名（例如 `music.mszook.art`）。
3. **源站**填 `lyramira-art.github.io`，回源协议 HTTPS，**回源 HOST 也填 `lyramira-art.github.io`**（关键：GitHub 靠 HOST 找到是哪个站）。
4. 按 EdgeOne 给的 CNAME，在你的域名 DNS 里把子域名 CNAME 过去。
5. 在 EdgeOne 开 HTTPS（它能签发免费证书）。

这样**不用**在 GitHub Pages 里设自定义域名，避开 GitHub 的强制跳转，最稳。

### 方案 B：直接用 EdgeOne Pages 托管（不经过 GitHub Pages）
EdgeOne 有类似 Cloudflare Pages 的静态托管：连本仓库 → 构建命令留空、输出目录填 `/`（根目录）→ 绑子域名。国内节点直出，最快，但和 GitHub Pages 二选一即可。

### ⚠️ 必看：ICP 备案
用**中国大陆**节点加速自定义域名，域名需要 **ICP 备案**。
- `mszook.art` 若还没备案：要么先走备案（通过腾讯云，约 1–3 周），要么 EdgeOne 加速区域先选**「全球（不含中国大陆）」**——能用、但国内不加速，等备案下来再切「中国大陆 / 全球」。
- 备案要求域名在国内注册商名下，`.art` 目前可备案，具体以腾讯云 / 工信部名单为准。

> 如果你决定走**方案 A 且在 GitHub Pages 设自定义域名**，告诉我确切的子域名，我在仓库里加一个 `CNAME` 文件（内容就是那个子域名）。现在没加，是因为域名/接法还没定死。

---

## 第 3 步：把素材放进去（决定页面“满不满”）

网站已做成**你放文件就自动生效、不放就优雅占位**的样子。按需丢：

| 放哪 | 文件 | 现在没有时的表现 |
|---|---|---|
| `audio/hai-no-utopia.mp3` | 代表作试听（建议 30–60s 预览） | 播放器退化为“演示进度条”，不出声 |
| `audio/lament.mp3` | 客单作品试听 | 同上 |
| `uploads/cover-hai.jpg` | 代表作封面 | 显示靛蓝底 + ✦ 占位 |
| `uploads/cover-lament.jpg` | 客单作品封面 | 同上 |
| `uploads/avatar.jpg` | 曲师介绍头像 | 圆形靛蓝底 + ✦ 占位 |
| `uploads/qr-qq.png` / `uploads/qr-wx.png` | 委托弹窗二维码 | 显示「放 QQ / 微信二维码」文字占位 |

改完 `git add -A && git commit && git push` 即可，EdgeOne 记得**刷新缓存**（或等缓存过期）。

---

## 待办清单（内容层面，上线前建议改）

- [ ] **作品②文案**：`index.html` 里 “Lament of Broken Ideals” 的简介现在是从代表作复制过来的，改成这首自己的介绍。
- [ ] **网易云 / Bilibili 链接**：作品卡片上的这两个按钮现在是 `href="#"`，换成完整版真实链接。
- [ ] **联系方式核对**：QQ/微信/B站/抖音/小红书 账号，页面里现在是设计稿里的示例值，逐个确认。
- [ ] **字体（国内速度）**：现在用 Google Fonts；国内可能慢/加载不出（会回退到系统衬线体，不影响可读）。想彻底解决：把 `<head>` 里那条 `fonts.googleapis.com` 换成国内镜像，或把用到的字重自托管到 `uploads/fonts/`。需要我做的话说一声。
- [ ] **banner 图**：`uploads/banner-low.png` 有 ~5.8MB，偏大。压到 ~300KB 以内首屏会快很多。

---

## 本地预览

```bash
python3 -m http.server 8000
# 打开 http://localhost:8000/
```
（老的 `Mszook 委托主场 v3.dc.html` 设计稿在浏览器里直接打开是**不完整**的——它依赖 Claude Design 的运行时。以本仓库的 `index.html` 为准。）
