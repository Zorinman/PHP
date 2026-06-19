# Laravel 和 Symfony 的区别
## 它们的核心区别：

| 特性                   | Laravel                                                                                                              | Symfony                                                                                                         |
| :--------------------- | :------------------------------------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------- |
| **核心哲学**           | 开发者体验至上，追求优雅、简洁的语法，信奉“约定优于配置”，旨在让编程过程充满乐趣。                                   | 企业级应用基石，强调灵活性、稳定性和可复用性，信奉“显式配置”，提供对架构的精细控制。                            |
| **学习曲线**           | 平缓，对新手友好，有庞大的教程和文档支持，可以很快上手并做出东西。                                                   | 陡峭，需要理解服务容器、依赖注入等高级概念，更适合有经验的开发者。                                              |
| **ORM (对象关系映射)** | **Eloquent ORM**，采用 Active Record 模式，模型直接对应数据表，使用起来非常直观和快速。                              | **Doctrine ORM**，采用 Data Mapper 模式，更符合领域驱动设计，能处理更复杂的业务逻辑和映射关系，但学习成本更高。 |
| **模板引擎**           | **Blade**，语法轻量，允许在HTML中直接嵌入PHP代码，编译后性能优秀。                                                   | **Twig**，以安全和高扩展性著称（默认自动转义），语法更严格，功能强大。                                          |
| **扩展与模块化**       | 通过 **Service Provider** 和 **Facade** 体系扩展，集成了官方提供的丰富生态工具（如Horizon队列监控、Telescope调试）。 | 高度模块化，基于独立的 **Symfony组件**构建。这些组件质量极高，甚至被Laravel、Drupal等大量其他项目所使用。       |
| **适用场景**           | **快速开发和原型构建**，中小型项目，初创公司，以及希望快速将想法落地的团队。                                         | **大型、复杂的企业级应用**，对性能、可扩展性、长期维护性有高要求的项目（如金融、政府系统）。                    |


值得一提的是，这两个框架并非完全割裂。Laravel 本身也依赖了许多 Symfony 的核心组件，所以你选择任何一个，都能享受到 Symfony 组件带来的稳定性和强大功能。
## **Laravel 与 Symfony 命令对照**

| 功能                           | Laravel                     | Symfony                                       |
| ------------------------------ | --------------------------- | --------------------------------------------- |
| 命令入口                       | `php artisan`               | `php bin\console`                             |
| 检查项目根目录入口文件是否存在 | `dir artisan`               | `dir bin\console`                             |
| 安装 PHP 依赖                  | `composer install`          | `composer install`                            |
| 环境配置                       | `.env`                      | `.env` / `.env.local`                         |
| 数据库配置                     | `DB_HOST`、`DB_DATABASE` 等 | `DATABASE_URL`                                |
| 创建数据库表                   | `php artisan migrate`       | `php bin\console doctrine:migrations:migrate` |
| 初始化数据                     | `php artisan db:seed`       | `php bin\console doctrine:fixtures:load`      |
| 启动服务                       | `php artisan serve`         | `symfony serve`                               |
| 清缓存                         | `php artisan cache:clear`   | `php bin\console cache:clear`                 |
| 网站入口目录                   | `public/`                   | `public/`                                     |

- `php artisan migrate/php bin`与`console doctrine:migrations:migrate`：**根据项目里的迁移文件创建数据表**
- `php artisan serve`与`symfony serve`：**启动本地开发web服务器，默认访问地址通常是http://127.0.0.1:8000**
- `php artisan cache:clear`与`php bin\console cache:clear`:**清掉框架缓存。比如修改 .env、配置文件、路由文件后，项目未变化，则可清缓存。**
- `public/`:**访问web服务时，对外暴露目录是仅有public/，目录中的 `index.php` 作为入口文件请求会先进入这里，再交给 Laravel 或 Symfony 处理。**
- （非必须）`php artisan db:seed`与`php bin\console doctrine:fixtures:load`:**往表里插入默认数据，比如默认管理员、默认权限、测试用户。**


# 新Laravel和Symfony项目的启动

## 整体流程

从 GitHub 拉取一个 PHP 项目后，

一般流程是：

```text
安装 PHP 依赖
配置环境变量
配置数据库
执行数据库迁移
安装前端依赖
启动本地服务
```


## 环境和依赖
**GitHub 上通常缺少三类本地生成内容：**
```
vendor/        Composer 安装的第三方 PHP 依赖目录
.env           本地环境配置
node_modules/  前端依赖
```

**所以启动本质上是补齐这些东西：**
```
composer install       根据conposer文件安装 PHP 第三方依赖 vendor/
完善.env              补齐本地配置
配置数据库             让项目知道连哪里
migrate               用来创建或更新数据库表
npm install/run dev    补齐并编译前端资源（如果有）
serve                 用来启动本地开发服务器
```

**以上涉及的相关知识：**
```
composer.json 说明项目需要什么 PHP 包
composer.lock 锁定具体安装版本
artisan 是 Laravel 命令入口
bin/console 是 Symfony 命令入口

```
## Composer 命令解释

### 1. `composer install`

```bash
composer install
```

含义：

```text
根据 composer.json / composer.lock 安装 PHP 依赖。
```

作用：

```text
生成 vendor/ 目录
生成 vendor/autoload.php
让 Laravel / Symfony 能加载第三方包
```



---

### 2. `composer update`

```bash
composer update
```

含义：

```text
重新解析依赖版本，并更新 composer.lock。
```

作用：

```text
升级依赖包
改写 composer.lock
```

适用场景：

```text
你是项目维护者
你明确想升级依赖
你知道升级可能带来的兼容问题
```

新手刚拉项目时，一般不要优先执行 `composer update`。

---

### 3. `composer require`

```bash
composer require 包名
```

含义：

```text
给项目新增一个 PHP 依赖包。
```

例如：

```bash
composer require guzzlehttp/guzzle
```

表示：

```text
安装 Guzzle，并写入 composer.json 的 require。
```

---

### 4. `composer require --dev`

```bash
composer require --dev 包名
```

含义：

```text
安装开发环境使用的依赖包。
```

常见开发依赖：

```text
测试工具
IDE 提示工具
代码格式化工具
静态分析工具
```

例如 Laravel IDE Helper：

```bash
composer require --dev barryvdh/laravel-ide-helper
```



## 核心文件和目录解释
### 1. `composer.json`

```text
composer.json = PHP 项目的依赖说明书
```

里面记录：

```text
项目需要哪些 PHP 包
项目要求什么 PHP 版本
项目有哪些自动加载规则
项目有哪些脚本命令
```

例如：

```json
{
  "require": {
    "laravel/framework": "5.8.*",
    "guzzlehttp/guzzle": "^6.3"
  }
}
```

意思是：

```text
这个项目依赖 Laravel 框架和 Guzzle HTTP 客户端。
```

---

### 2. `composer.lock`

```text
composer.lock = 依赖版本锁定文件
```

作用：

```text
锁定每一个依赖包的具体版本。
保证不同电脑安装出来的依赖版本一致。
```

例如：

```text
composer.json 写：laravel/framework 5.8.*
composer.lock 锁定：laravel/framework v5.8.38
```

区别：

```text
composer.json：告诉 Composer 我需要什么范围的包
composer.lock：告诉 Composer 最终实际安装哪个精确版本
```

---

### 3. `vendor/`


里面通常有：

```text
vendor/autoload.php
vendor/laravel/
vendor/symfony/
vendor/guzzlehttp/
vendor/composer/
```

注意：

```text
vendor/ 通常不会提交到 GitHub。
clone 项目后，需要通过 composer install 重新生成。
```







---

## 五、Laravel 项目启动流程

Laravel 项目根目录通常包含：

```text
artisan
composer.json
app/
routes/
config/
database/
resources/
public/
```

---

### 1. 确认是 Laravel 项目

PowerShell：

```powershell
dir artisan
```

Git Bash / macOS / Linux：

```bash
ls artisan
```

含义：

```text
artisan 是 Laravel 的命令行入口文件。
如果当前目录有 artisan，说明你大概率在 Laravel 项目根目录。
```

---

### 2. Laravel 标准启动模板

```powershell
cd "项目目录"

dir artisan

composer install

copy .env.example .env

php artisan key:generate

php artisan migrate

php artisan db:seed

npm install
npm run dev

php artisan serve
```

---

## 六、Laravel 每个命令解释

### 1. `dir artisan`

```powershell
dir artisan
```

含义：

```text
查看当前目录下是否存在 artisan 文件。
```

目的：

```text
确认当前目录是不是 Laravel 项目根目录。
```

如果报：

```text
找不到 artisan
```

说明：

```text
你可能 cd 到了错误目录。
```

---

### 2. `composer install`

```powershell
composer install
```

含义：

```text
安装 Laravel 项目的 PHP 依赖。
```

目的：

```text
生成 vendor/
生成 vendor/autoload.php
让 Laravel 框架和第三方包可用
```

---

### 3. `copy .env.example .env`

```powershell
copy .env.example .env
```

含义：

```text
复制一份本地环境配置文件。
```

目的：

```text
让 Laravel 有自己的本地配置。
```

Git Bash 写法：

```bash
cp .env.example .env
```

---

### 4. `php artisan key:generate`

```powershell
php artisan key:generate
```

拆开理解：

```text
php       使用 PHP 解释器
artisan   Laravel 命令行入口
key:generate 生成应用密钥
```

作用：

```text
生成 APP_KEY，并写入 .env。
```

APP_KEY 用于：

```text
加密 Cookie
加密 Session
加密敏感数据
```

如果没有 APP_KEY，Laravel 可能报：

```text
No application encryption key has been specified.
```

---

### 5. 配置数据库

修改 `.env`：

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=demo
DB_USERNAME=root
DB_PASSWORD=root
```

含义：

```text
告诉 Laravel 连接哪个数据库。
```

---

### 6. `php artisan migrate`

```powershell
php artisan migrate
```

含义：

```text
执行数据库迁移。
```

作用：

```text
根据 database/migrations 目录下的迁移文件创建数据库表。
```

例如创建：

```text
users
password_resets
jobs
roles
permissions
```

---

### 7. `php artisan db:seed`

```powershell
php artisan db:seed
```

含义：

```text
执行数据库填充。
```

作用：

```text
插入初始化数据。
```

常见初始化数据：

```text
默认管理员
默认角色
默认权限
测试数据
系统配置
```

不是所有项目都需要执行，具体看 README。

---

### 8. `npm install`

```powershell
npm install
```

含义：

```text
安装前端依赖。
```

作用：

```text
生成 node_modules/
安装 Vue、React、Vite、Webpack、Laravel Mix 等前端工具。
```

如果项目没有 `package.json`，通常不需要执行。

---

### 9. `npm run dev`

```powershell
npm run dev
```

含义：

```text
编译前端资源。
```

作用：

```text
把 resources/js、resources/css 等资源编译到 public 目录。
```

老 Laravel 项目可能使用：

```bash
npm run watch
```

或：

```bash
npm run production
```

---

### 10. `php artisan serve`

```powershell
php artisan serve
```

含义：

```text
启动 Laravel 本地开发服务器。
```

默认访问地址：

```text
http://127.0.0.1:8000
```

注意：

```text
php artisan serve 只适合本地开发，不是正式部署方式。
```

---

## 七、Symfony 项目启动流程

Symfony 项目根目录通常包含：

```text
bin/console
composer.json
src/
config/
public/
templates/
var/
```

---

### 1. 确认是 Symfony 项目

PowerShell：

```powershell
dir bin\console
```

Git Bash / macOS / Linux：

```bash
ls bin/console
```

含义：

```text
bin/console 是 Symfony 的命令行入口文件。
如果存在 bin/console，说明你大概率在 Symfony 项目根目录。
```

---

### 2. Symfony 标准启动模板

```powershell
cd "项目目录"

dir bin\console

composer install

# 配置 .env 或 .env.local

php bin\console doctrine:database:create

php bin\console doctrine:migrations:migrate

npm install
npm run dev

symfony serve
```

如果没有 Symfony CLI：

```powershell
php -S 127.0.0.1:8000 -t public
```

---

## 八、Symfony 每个命令解释

### 1. `dir bin\console`

```powershell
dir bin\console
```

含义：

```text
查看当前目录下是否存在 Symfony 命令行入口。
```

目的：

```text
确认当前目录是不是 Symfony 项目根目录。
```

---

### 2. `composer install`

```powershell
composer install
```

含义：

```text
安装 Symfony 项目的 PHP 依赖。
```

作用：

```text
生成 vendor/
安装 Symfony 组件
安装 Doctrine、Twig、Security 等依赖
```

---

### 3. 配置 `.env` 或 `.env.local`

Symfony 常见数据库配置：

```env
DATABASE_URL="mysql://root:root@127.0.0.1:3306/demo?serverVersion=8.0"
```

含义：

```text
告诉 Symfony 使用哪个数据库。
```

Laravel 常用多个 `DB_*` 配置。

Symfony 常用一个 `DATABASE_URL` 配置。

---

### 4. `php bin\console`

```powershell
php bin\console
```

拆开理解：

```text
php         使用 PHP 解释器
bin\console Symfony 命令行入口文件
```

它类似于 Laravel 的：

```powershell
php artisan
```

对照：

```text
Laravel: php artisan xxx
Symfony: php bin\console xxx
```

---

### 5. `php bin\console doctrine:database:create`

```powershell
php bin\console doctrine:database:create
```

含义：

```text
根据 DATABASE_URL 创建数据库。
```

例如：

```env
DATABASE_URL="mysql://root:root@127.0.0.1:3306/blog"
```

执行后会尝试创建：

```text
blog
```

数据库。

---

### 6. `php bin\console doctrine:migrations:migrate`

```powershell
php bin\console doctrine:migrations:migrate
```

含义：

```text
执行 Symfony / Doctrine 数据库迁移。
```

作用：

```text
根据 migrations 目录下的迁移文件创建或修改数据库表。
```

相当于 Laravel 的：

```powershell
php artisan migrate
```

---

### 7. `symfony serve`

```powershell
symfony serve
```

含义：

```text
使用 Symfony CLI 启动本地开发服务器。
```

常见访问地址：

```text
https://127.0.0.1:8000
```

或：

```text
http://127.0.0.1:8000
```

如果没有安装 Symfony CLI，可以用：

```powershell
php -S 127.0.0.1:8000 -t public
```

含义：

```text
使用 PHP 内置服务器，并把 public 目录作为网站入口。
```

---

## 九、

---




---




---

## 十二、最终精简模板

### Laravel

```powershell
git clone 项目地址
cd "项目目录"

dir artisan

composer install

copy .env.example .env

php artisan key:generate

# 修改 .env 数据库配置

php artisan migrate
php artisan db:seed

npm install
npm run dev

php artisan serve
```

---

### Symfony

```powershell
git clone 项目地址
cd "项目目录"

dir bin\console

composer install

# 修改 .env 或 .env.local 数据库配置

php bin\console doctrine:database:create
php bin\console doctrine:migrations:migrate

npm install
npm run dev

symfony serve
```

如果没有 Symfony CLI：

```powershell
php -S 127.0.0.1:8000 -t public
```
