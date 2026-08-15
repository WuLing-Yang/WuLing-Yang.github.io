# Urim 的博客

一个纯静态个人博客：用 **Markdown 写文章 → 一条命令发布**到 GitHub Pages。

```text
blog/
├── build.py            # 构建器（增量编译，只重建变化的文章）
├── deploy.py           # 一键发布：构建 + 自动 commit + 推送
├── publish.bat         # 双击即可发布（Windows）
├── typora-upload.bat   # Typora 图片上传命令（粘贴图片自动进 content/assets）
├── content/            # 你的文章都在这里（Markdown）
│   ├── AI/             #   └ 子文件夹 = 分类
│   ├── 随笔/
│   ├── 示例/
│   ├── about.md        #   页面（type: page）
│   └── assets/         #   粘贴图片默认存这里
└── site/               # 生成结果（部署到 GitHub 的内容）
    ├── index.html      # 首页（目录树 + 分类文章卡片）
    ├── about.html
    └── posts/*.html    # 文章页
```

---

## 快速开始

### 首次

```bash
cd E:/workspace/Record/blog
python deploy.py        # 构建 + 提交 + 推送，完成后线上生效
```

### 日常（三步）

1. **写文章**：在 `content/` 下新建 `.md`（可放任意子文件夹 = 分类）
2. **发布**：
   ```bash
   python deploy.py                 # 自动 commit 信息（列出改动文件）
   python deploy.py "发布 AI 笔记"    # 自定义 commit 信息
   ```
   或双击 `publish.bat`，或终端里用别名 `pub "信息"`

---

## 写文章：front matter

每篇文章开头可写 YAML 元信息（`---` 包裹）：

```markdown
---
title: 文章标题
date: 2026-08-16
tags: [AI, 随笔]
summary: 一句话摘要，显示在首页卡片上。
slug: my-post          # 可选，自定义 URL 名（默认用文件名）
category: 随笔          # 可选，覆盖文件夹分类
type: page             # 可选，写 page 则不列入文章列表（如 about）
---

正文从这里开始……
```

- **分类**：默认取所在文件夹名（`content/AI/xxx.md` → 分类「AI」）；根目录文件归入「未分类」；可用 `category:` 覆盖
- **日期**：不写则取文件修改时间
- **标签**：首页卡片会显示

---

## 图片：三种用法，都能正确处理

构建时**所有本地图片会自动 base64 内嵌**到 HTML（单文件、永不丢图、离线可看）。

### 方式 A：Typora 粘贴（推荐）

配置好 `typora-upload.bat` 后（见下方「Typora 配置」），粘贴的图片自动：
1. 复制到 `content/assets/`
2. 在 markdown 里写相对路径 `assets/图片名.png`

### 方式 B：自己建图片文件夹 + 相对路径引用

在**文章所在目录**（或它下面的子目录）建图片文件夹，Markdown 里写相对路径即可：

```text
content/随笔/我的文章.md
content/随笔/images/示意图.png     ← 新建的图片文件夹
```

```markdown
![示意图](images/示意图.png)
```

**解析规则**：构建时按「**文章所在目录 → content 根目录**」依次查找。
所以上面这行会先在 `content/随笔/images/` 找，找不到再回退到 `content/images/`。

> 注意：Markdown 里写相对路径时，是**相对文章所在位置**，不是相对博客根目录。

### 方式 C：绝对路径

也支持 Windows 绝对路径（如 `E:\workspace\Record\assets\a.png`），构建时自动内嵌。

**外链图片**（`http(s)://` 开头）保持原样，不做内嵌。

---

## Typora 配置（让编辑 + 发布顺滑）

1. Typora 打开文件夹：**文件 → 打开文件夹**，选 `E:\workspace\Record\blog\content`
2. 图片自动上传：**文件 → 偏好设置 → 图像 → 图像上传设置 → 自定义命令**，填：
   ```
   E:\workspace\Record\blog\typora-upload.bat
   ```
   （之后粘贴/拖入图片会自动复制到 `content/assets/` 并写相对路径）
3. 写完后发布：终端 `pub "标题"` 或双击 `publish.bat`

---

## 终端配置速查

在 Git Bash 里（已配置 `~/.bashrc`）：

| 命令 | 作用 |
|------|------|
| `blog` | 进入博客目录 |
| `build` | 仅构建 |
| `pub` / `pub "信息"` | 构建 + 自动提交（可自定义信息）+ 推送 |
| `gs` `ga` `gc` `gp` | git status / add / commit / push |
| `ll` | 详细列表 |

Windows Terminal 快捷键：`Ctrl+Shift+G` 新开 Git Bash、`Ctrl+Shift+L` 清屏、`Alt+Shift+D` 分屏、`F11` 全屏。

---

## 常用命令汇总

```bash
python build.py             # 增量构建（只重建变化文章）
python build.py --full      # 强制全量重建
python deploy.py            # 构建 + 自动 commit + 推送
python deploy.py "自定义信息" # 自定义 commit 信息
python deploy.py --no-push  # 只构建 + commit，不推送
python build.py -s          # 本地预览 http://127.0.0.1:8124
```

博客地址：**https://wuling-yang.github.io/**（GitHub Pages 部署，推送后 1~2 分钟生效）

---

## 常见问题

**改了文章但线上没变？**
等 1~2 分钟（GitHub Pages 构建），或确认 `git push` 成功（`git log` 看提交）。

**图片没显示？**
检查：图片路径是否真实存在、相对路径是否相对文章所在目录、文件名大小写是否一致。

**发布要重新登录吗？**
不需要，gh 凭证已存系统；重启电脑后照常 `pub`。
