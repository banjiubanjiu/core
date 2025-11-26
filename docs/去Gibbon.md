# 去除 Gibbon 品牌元素指引

以下列出前端可见的 Gibbon 品牌元素及建议替换方案，按优先级排序。文件路径为仓库根目录相对路径。

## 页脚 “Powered by Gibbon”
- 位置：`resources/templates/footer.twig.html`（常规页面），`resources/templates/foot.twig.html`（打印等），`resources/templates/fullscreen.twig.html`（全屏视图）。
- 改法：调整或删除
  ```twig
  {{ __('Powered by') }} <a ...>Gibbon</a> {{ versionName }}
  ```
  可改成 “Powered by ai5e” 或自定义链接；同时可去掉创始人/版权/GPL 链接行。
- 影响：若改动文本，`tests/acceptance/WelcomeCept.php` 里的断言需对应修改。

## Logo 与默认品牌图
- 登录页/顶栏、报表、邮件等默认使用 `organisationLogo`，未配置时回退到 `themes/Default/img/logo.png`（登录/页头），`themes/Default/img/logoFooter.png`（页脚/报告），`themes/Default/img/gibbon-white.svg`（深色/反白）。
- 替换方案：
  - 后台设置：System Admin → Theme → 设置组织名称/Logo（首选，避免改源码）。
  - 或直接替换上述图片文件为 ai5e 版本（保持文件名不变），必要时替换 `favicon.ico`（根目录）。
  - 邮件/报表也引用 `organisationLogo` 回退同路径，替换后同步生效。

## “Gibbon” 文案与外链
- Getting Started 引导与部分 UI 链接：`resources/templates/ui/gettingStarted.twig.html`（链接到 docs/support/gibbonedu.com），`resources/templates/page.twig.html`（Help 链接默认指向 Gibbon 文档）。需要换成自有文档/支持地址时，直接替换相关 URL 和文案。
- SSO 按钮、帮助、Credits/Translators/Support 外链均在各 Twig 模板顶部（搜索 `gibbonedu.org`）。根据需要替换或移除。

## 版本号显示
- 页脚使用模板变量 `versionName`（来自 `version.php`）。若不想展示 “v30.0.00dev”，可在页脚模板只保留自定义品牌文案，或在 `version.php` 中自定义显示值（不推荐改核心版本号）。

## 版权注释
- 多数 Twig/CSS 头部有 Gibbon 版权注释，仅在源代码中可见，不影响前端。可保留以减少维护成本。若需彻底去除，对应文件头部注释可删，但建议保留 GPL 版权说明。

## Docker 构建提示
- 替换资源/模板后，使用 Docker 时需重建并重启应用服务：
  ```bash
  cd docker
  docker compose build app
  docker compose up -d
  ```

## 操作优先级建议
1) 后台配置组织名称与 Logo（无需改码）。
2) 修改 `footer*.twig` 文案，去掉/改为 ai5e 品牌。
3) 替换 `themes/Default/img/logo*.png/svg` 与 `favicon.ico` 为自有素材。
4) 视需要更新 Getting Started/Help 等外链指向自有站点。
5) 若改动测试字符串，同步调整 `tests/acceptance/WelcomeCept.php` 断言。
