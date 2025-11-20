<p align="center">
    <a href="https://gibbonedu.org/" target="_blank"><img width="200" src="https://gibbonedu.org/img/gibbon-logo.png" alt="Gibbon Logo"></a><br>
    Gibbon 是一套灵活的开源校务管理平台，旨在让教师、学生、家长与学校的日常工作更加简单。
</p>

------

Gibbon Core
===========

## 项目概览
Gibbon Core 是 Gibbon 校务系统的主体代码库，提供招生、课表、教学、沟通、财务、图书、评估等核心功能，并通过模块与主题实现深度扩展。该仓库包含 PHP 后端、前端资源、模板、安装程序、数据库脚本以及自动化测试，是自建或二次开发 Gibbon 的基础。

## 核心特性
- **模块化校务功能**：内建招生、出勤、行为、学术评估、课程表、财务、消息、资源库等二十余个官方模块，可按需启用。
- **多角色权限模型**：支持学生、家长、教师、导师、行政及自定义角色，细粒度权限及工作流助力校内协同。
- **多语言与本地化**：借助 `i18n/` 与 [POEditor](https://poeditor.com) 提供的协作翻译，界面文本可即时切换，适合全球部署。
- **可插拔主题与 UI 组件**：`themes/`、`resources/` 与 `lib/` 中的前端资源支持自定义品牌、可视化和编辑体验。
- **自动化流程与通知**：`cli/` 中的脚本可通过 cron 运行，处理出勤提醒、行为汇总、财务通知等日常任务。
- **开放式 API 与集成**：依赖 Slim 4、Twig、Monolog、PHPMailer、Google API、OAuth2、Omnipay 等成熟库，方便与 LMS、支付或消息系统整合。

## 技术栈与架构
- **语言与框架**：PHP 8.0+、Slim 4 路由层、Twig 模板引擎，自研服务容器（league/container）与数据访问层（Aura SQLQuery）。
- **数据库**：MySQL 8.0+（InnoDB）。`gibbon.sql` 与 `gibbon_demo.sql` 提供空库与示例数据，`CHANGEDB.php` 用于升级时的迁移。
- **前端**：jQuery、htmx、TinyMCE、FullCalendar、Chart.js、ACE 等库存放于 `lib/`，并通过 `resources/assets`、`resources/build` 管理打包资源。
- **业务分层**：`src/` 下按主题划分（Auth、Install、Services、Domain、Tables、View 等），配合 `modules/` 提供面向功能的界面和 API。

## 目录速览
- `src/`：核心业务逻辑、服务、数据模型、身份验证、安装器与 UI 组件。
- `modules/`：官方模块（Admissions、Attendance、Finance、Markbook、Planner 等）。可在此基础上开发自定义模块。
- `themes/`：默认与传统主题；自定义主题可复制扩展。
- `lib/`：第三方前端库与富文本组件。
- `resources/`：静态资源、构建脚本、导入模板与可复用视图片段。
- `cli/`：可由任务计划或 cron 调用的自动化脚本。
- `installer/`：Web 安装器界面及逻辑。
- `uploads/`：用户上传、缓存及运行时写入目录（生产环境需赋予可写权限）。
- `tests/`：Codeception（install/acceptance）与 PHPUnit（unit）测试套件，以及测试环境配置。
- `gibbon.sql / gibbon_demo.sql`：初始化数据库及演示数据脚本。

## 环境需求
- PHP 8.0 及以上版本，需启用 `intl`、`mbstring`、`gettext`、`curl`、`zip`、`xml`、`gd`、`PDO` 等扩展。
- MySQL 8.0（或兼容发行版），UTF-8/utf8mb4 字符集。
- Web 服务器需支持 URL 重写（例如 Apache 2.4 + `mod_rewrite`，或配置等价的 Nginx 规则）。
- 建议 PHP 设置：`max_input_vars >= 8000`、`max_file_uploads >= 20`、`allow_url_fopen = 1`、`session.gc_maxlifetime >= 1200`，以及合理的 `post_max_size` / `upload_max_filesize`。

## 安装流程
1. **获取代码与依赖**
   ```bash
   git clone https://github.com/GibbonEdu/core.git
   cd core
   composer install --prefer-dist --no-dev
   ```
2. **配置服务器**：将虚拟主机根目录指向仓库根目录，启用 HTTPS 及 URL 重写；为 `uploads/`、`resources/assets/compiled/`、`config.php`（安装完成后生成）授予写权限。
3. **准备数据库**：创建空数据库与拥有全部权限的用户，可选择提前导入 `gibbon.sql`（空库）或 `gibbon_demo.sql`（演示数据）。
4. **运行安装器**：浏览器访问 `https://<your-domain>/install/`，填写数据库与站点信息，安装器会生成 `config.php` 并初始化数据。
5. **生产配置**：根据需要设置 cron 任务以运行 `cli/` 下的脚本，配置邮件、单点登录、支付接口等高级功能。
6. **升级**：跟随 [官方安装文档](https://docs.gibbonedu.org/introduction/installing-gibbon) 中的 “Cutting Edge Code” 或稳定版本指引，使用 `update.php` 与 `CHANGEDB.php` 完成数据库迁移。

## 开发与测试流程
- 安装开发依赖：`composer install`。
- 常用脚本：
  ```bash
  composer test            # 同时执行 Codeception 与 PHPUnit
  composer test:phpunit    # 单元测试
  composer test:codeception # 安装/验收测试（需可写测试环境）
  composer test:phpstan    # 静态分析
  composer test:codesniffer # PSR-2 代码规范
  ```
- 测试使用 `tests/_envs` 中的配置加载 `config.php`，请确保测试环境数据库与文件目录独立，避免污染真实数据。
- 贡献指南、代码规范与行为准则位于 `.github/CONTRIBUTING.md` 与 `.github/CODE_OF_CONDUCT.md`，开发工作流参考 [Developer Workflow](https://docs.gibbonedu.org/development/getting-started/developer-workflow)。

## 国际化、本地化与可扩展性
- 所有界面文本均可在 [POEditor](https://poeditor.com) 协作翻译，完成后同步至 `i18n/`。
- 自定义语言或术语可通过 `i18n/custom/` 覆盖，或在模块内提供翻译文件。
- 模块（`modules/`）、主题（`themes/`）与 Twig 视图（`resources/templates/`）支持热插拔，方便学校根据流程拓展功能。
- CLI 脚本与丰富的事件钩子让你可以集成 MIS、LMS、支付网关或消息系统。

## 支持与社区
- 文档：<https://docs.gibbonedu.org>
- 安装、升级与故障排查：参阅文档中的 Getting Started 与 Administration 章节。
- 社区支持：<https://ask.gibbonedu.org>。
- 最新开发版本：<https://github.com/GibbonEdu/core>（Cutting Edge 代码）—— 请仅在测试或备份充分的情况下使用。

## 许可证
Gibbon 以 [GNU GPL v3](https://github.com/GibbonEdu/core/blob/master/LICENSE) 协议发布。若在此基础上二次开发或分发，请遵循许可证要求并回馈改进，帮助全球学校享受更好的开源工具。