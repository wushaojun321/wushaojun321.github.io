# 个人博客网站

基于 Hugo 和 Paper 主题的个人博客网站，发布到 GitHub Pages。

## 常用命令

### 1. 启动本地预览

```bash
hugo server -D
```

或者使用以下命令启动开发服务器并自动打开浏览器：

```bash
hugo server -D --open
```

参数说明：
- `-D` 或 `--buildDrafts`：包含草稿文章
- `--open`：自动在浏览器中打开网站

### 2. 发布

构建静态网站到 `docs` 目录：

```bash
hugo
```

提交到 GitHub 并发布：

```bash
git add .
git commit -m "更新博客内容"
git push origin main
```

## 其他常用命令

### 创建新文章

```bash
hugo new posts/文章标题.md
```

### 构建并清理缓存

```bash
hugo --cleanDestinationDir
```

## 项目结构

- `content/` - 博客内容（Markdown 文件）
- `docs/` - 生成的静态网站文件（GitHub Pages 部署目录）
- `themes/paper/` - Paper 主题
- `config.toml` - Hugo 配置文件

## 网站信息

- 本地预览地址：http://localhost:1313
- 线上地址：https://wushaojun321.github.io/