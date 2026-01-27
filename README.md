<h2 align="center">o0w0b の 启动页</h2>

<h3 align="center">一个基于 Vue 3 + Vite + Tailwind CSS 的应用启动页</h3>

![](https://image.thum.io/get/allowJPG/noanimate/https://o0w0b.top)

## 配置

所有配置都通过 `public/config.yaml` 文件进行管理。

### 基本配置

```yaml
# 页面标题
title: "我的应用中心"

# 显示选项
showClock: true      # 显示时钟
showDate: true       # 显示日期
showSearch: true     # 显示搜索框
showSystemStatus: true  # 显示系统状态

# 壁纸 (可选)
wallpaper: "https://example.com/wallpaper.jpg"
```

### 搜索引擎配置

```yaml
# 默认搜索引擎
defaultEngine: "google"

# 搜索引擎列表
searchEngines:
  google:
    name: "Google"
    url: "https://www.google.com/search?q={query}"
    icon: "mdi:google"
  bing:
    name: "Bing"
    url: "https://www.bing.com/search?q={query}"
    icon: "mdi:microsoft-bing"
```

### 应用分组配置

```yaml
appGroups:
  - id: "work"
    name: "工作工具"
    icon: "mdi:briefcase"
    apps:
      - title: "GitHub"
        url: "https://github.com"
        icon: "mdi:github"
        color: "#24292e"
        openInNew: true
      - title: "VS Code"
        url: "vscode://"
        icon: "mdi:microsoft-visual-studio-code"
        color: "#007acc"
        openInNew: false
```

## 开发

```bash
# 安装依赖
npm install

# 启动开发服务器
npm run dev

# 构建生产版本
npm run build
```

## 配置说明

- **图标**: 支持 Iconify 图标名或图片 URL
- **颜色**: 十六进制颜色代码
- **openInNew**: 是否在新窗口打开应用
- **壁纸**: 留空使用默认背景，或填写图片URL

修改 `public/config.yaml` 后刷新页面即可看到更改。
