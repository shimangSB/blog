# 博客

一个静态博客，用最朴素的方式搭的：写 markdown，用一个 Python 脚本转成网页。

线上地址：**https://shimangsb.github.io/blog/**

## 目录结构

这个仓库的目录是**按最终网址排布的**，所以看目录就知道网站长什么样：

| 路径 | 网址 | 说明 |
| --- | --- | --- |
| `blog_src/` | — | **你写文章的地方**，只放 `.md` 文件 |
| `blog/` | `/blog/` | 脚本自动生成的网页，**不要手动改** |
| `shuangpin-practice/` | `/blog/shuangpin-practice/` | 双拼练习小站 |
| `tools/` | — | 构建和发布脚本 |

> 仓库名必须正好是 `blog`，网址才会是 `/blog/`。GitHub Pages 的规则是
> `用户名.github.io/仓库名/`，所以这条路径由仓库名决定。

## 写一篇新文章

1. 在 `blog_src/` 里新建 `随便什么名字.md`
2. 开头加 front matter，下面正常写 markdown：

   ```markdown
   ---
   title: 文章标题
   date: 2026-01-15
   tags: [双拼, 输入法]
   summary: 一句话摘要，显示在列表页。
   ---
   ```

3. 双击 `tools/publish-blog.bat`（生成网页 + 提交 + 推送）

## 三个脚本

| 文件 | 用途 |
| --- | --- |
| `tools/build-blog.bat` | 只生成网页，不发布 |
| `tools/preview-blog.bat` | 本地预览，浏览器打开 `http://localhost:8000/blog/` |
| `tools/publish-blog.bat` | 生成 + 提交 + 推送到 GitHub |

## 推送机制（重要）

`publish-blog.bat` 会先试标准的 `git push`。但**这台机器上的 Git 凭据管理器会卡住**
（沙箱环境下 GCM 依赖命名管道通信，取不到凭据），所以失败或超时会自动改用
`tools/push-via-api.py` 走 GitHub REST API 推送，效果等价。

API 推送需要 token，放在环境变量 `GH_TOKEN` 里。第一次运行会提示你粘贴，
之后会存进当前用户的用户环境变量，后续双击就不用再输了。

> 注意：token 等同于账号密码。**不要**把它粘进聊天、issue 或提交到仓库里。
> 如果怀疑泄露，立刻去 https://github.com/settings/tokens 删掉重建。

这台机器上实测 `github.com:443` 时不时连不上，但 `api.github.com` 一直正常，
所以 API 通道更可靠。

## 约定

- **`blog/` 里的东西全是自动生成的**，不要手动改；改了下次构建会被覆盖。
- 想改博客样式，改 `tools/build-blog.py` 里的 `CSS_CONTENT`。
- 文章不写 `title` 时会自动取正文第一个 `#` 标题；不写 `date` 会取文件修改时间。
- front matter 里写 `draft: true` 的文章只在本地存在，不会生成到 `blog/`。
- 博客只依赖 Python 标准库（PyYAML 是可选的），不装任何包也能跑。

## 支持的 markdown

标题、粗体、斜体、行内代码、代码块、链接、图片、有序/无序列表、引用、分隔线、表格、`<https://…>` 自动链接。

## 自测

博客的 markdown 渲染自测：

```bash
python tools/test-md.py
```

双拼站的转换逻辑自测：

```bash
cd shuangpin-practice && node test.js
```
