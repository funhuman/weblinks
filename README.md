# weblinks
WebLinks is a web bookmark tool implemented with pure front-end technologies. ／一个纯前端技术实现的网页收藏工具

## 简介

WebLinks 是一个网页收藏工具，采用 HTML + CSS + JS 技术的网页工具，所有数据存储在浏览器 localStorage 中，实现轻量化、免服务器依赖的网页管理。

## 命名规范

- GitHub Repository Name: weblinks
- Project Name: WebLinks
- Project Code: WLKS

## 设计与实现

### 核心设计原则

- 简洁直观：界面清爽，操作简单
- 高效管理：支持添加、修改、搜索、分页浏览
- 计数与统计：记录点击次数，防止短时间内重复计数
- 可导入导出：保证数据可备份和迁移
- 扩展性：支持 URL 占位符 %s 替换，实现带参数打开

### 功能与实现细节

#### 数据结构

##### Bookmarks（网页收藏条目）

```json
{
  "id": "唯一 ID",
  "code": "简码（3~4 字母）",
  "title": "网页名称",
  "url": "网页链接，可包含 %s",
  "clickCount": 0,
  "lastCounted": 0,
  "updatedAt": 0
}
```

- code：用于快速访问与默认搜索，忽略大小写
- url：支持 %s 占位符，用于参数化打开
- clickCount：累计点击次数
- lastCounted：上一次被计数的时间戳，用于点击去重
- updatedAt：最后修改时间（时间戳）

##### ClickLogs（点击日志）

```json
{
  "bookmarkId": "对应 bookmark id",
  "clickedAt": 0,
  "count": 1
}
```

用于记录每次被计数的点击行为，主要用于审计与后续扩展，不参与实时排序。

##### ClickInterval（计数间隔）

- 单位：分钟
- 存储位置：localStorage
- 可手动修改
- 用于控制同一网页在指定时间内是否重复计数

#### 打开网页

- 点击卡片：触发内部“打开按钮”逻辑
- 点击按钮：直接打开网页（浏览器允许弹窗的前提下）

##### 行为说明

- 支持 URL 中的 %s 占位符
- 若提供参数输入：
  - 打开时将所有 %s 替换为参数内容
- 在 ClickInterval 指定时间内：
  - 重复打开同一 bookmark 不重复计数

#### 搜索

搜索规则：

- 默认搜索简码（code），忽略大小写
- - 开头：搜索网页名称（title）
- = 开头：搜索 URL（url）

示例：

- gh → 搜索简码
- -github → 搜索网页名称
- =github.com → 搜索 URL

搜索结果：

- 默认显示前 20 条
- 下滑加载更多（分页 / 无限滚动）

#### 添加 / 修改

- 输入字段：
  - 简码（Code）
  - 网页名称
  - URL
- 同一表单支持：
  - 新增
  - 编辑
- 保存按钮根据状态显示：
  - 添加
  - 修改

#### 导入 / 导出

##### 导出

- 导出为 JSON 文件
- 包含：
  - bookmarks
  - clickLogs

##### 导入

- 必须先执行导出，导入后直接覆盖现有数据
- 防止误操作导致数据丢失

#### UI 设计

- 使用 TailwindCSS
- 卡片式列表布局
- URL 过长时末尾显示 ...
- 时间显示格式：

```text
YYYY-MM-DD HH:MM
```

#### 扩展性

- localStorage 中的 clickInterval 可直接调整计数策略
- ClickLogs 结构为后续统计与分析预留空间
