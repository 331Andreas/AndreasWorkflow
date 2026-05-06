---
name: litigation-analyst
description: 诉讼案件材料管理与归档。触发词：诉讼、案件管理、案件概览、BASE、Dataview数据库、案件信息建档、材料整理、OCR处理、材料归档、新建案件。
version: "5.0"
metadata:
  last_updated: 2026-05-06
---

# Litigation Analyst — 诉讼案件材料管理

## 一、三阶段工作流

### Phase 1：材料接收与整理

**Step 1**：扫描收料库材料，对扫描件/图片执行 OCR（调用 markitdown skill），确保内容可读。处理后版本保存，原件留备份。

**Step 2**：分析全部文件，输出 `02_材料清单.md`。完成后暂停，等待确认。

---

### Phase 2：案件信息建档

**Step 3**：建立 `03_案件信息.md`，包含以下模块：

**基本信息表**
| 项目 | 内容 |
|------|------|
| 案号 | |
| 案件名称 | |
| 案由 | |
| 当前诉讼阶段 | 一审 / 二审 / 再审 / 执行 |
| 承办法院 | |
| 承办法官 | |
| 书记员 | |
| 开庭时间 | |
| 立案时间 | |
| 我方代理律师 | |

**当事人信息表**
| 诉讼地位 | 姓名/名称 | 身份证号/统一社会信用代码 | 住所/注册地 | 法定代表人/负责人 | 联系方式 | 代理律师 |
| ---- | ----- | ------------- | ------ | --------- | ---- | ---- |
| 原告   |       |               |        |           |      |      |
| 被告   |       |               |        |           |      |      |
| 第三人  |       |               |        |           |      |      |

**工作时间节点表**
| 序号 | 事项 | 截止日期 | 负责人 | 状态 | 备注 |
|------|------|----------|--------|------|------|
| 1 | | | | 待办/进行中/已完成 | |

**Mermaid 可视化四图**（详见 legal-litigation-viz skill）
- 案件大事记时间线 (timeline)
- 法律关系图 (graph TD)
- 诉讼流程图（标注当前节点）(flowchart LR)
- 诉讼时效甘特图 (gantt)

缺失信息以 `⚠️ 待补充` 标注并提示。

---

### Phase 3：文件归档

**Step 4**：建立案件文件夹结构（已存在则跳过）

```
案件库/{案件名}_{年度}/
├── {案件名}_案件概览.md
├── 01_材料原件/
├── 02_材料清单.md
├── 03_案件信息.md
├── 04_法条检索/
├── 05_案例检索/
├── 06_检索报告/
└── 07_诉讼文书/
```

完成后提示用户存放材料。

---

## 二、案件概览标准格式

每个案件须包含 `案件概览.md`（模板：`模板库/案件概览模板.md`）。

### YAML Frontmatter 标准字段

```yaml
---
tags:
  - case
阶段: 一审/二审/再审/执行/协助
案号: （XXXX）X民初/民终XXXX号
案由: 合同纠纷/劳动纠纷/...
法院: XX人民法院
法官: 姓名
法官联系方式: 电话
开庭时间: YYYY-MM-DD 或 ⚠️ 待补充
律师: 强晖
结果:
  - "-（未结）" 或 结案描述
年度: YYYY
下个任务: 下一步要做什么
---
```

### 关联文档模板（紧接 frontmatter）

用 fold 折叠，分4类 dataview 展示：

```markdown
# 案件名称

> 🔗 [[../BASE|← 返回BASE]]

> [!note]- 📎 关联文档
>
> **📁 材料原件**
> ```dataview
> TABLE WITHOUT ID file.link AS "📄"
> FROM "案件库/{案件名}_{年度}/01_材料原件"
> SORT file.name ASC
> ```
>
> **📋 材料清单 & 案件信息**
> ```dataview
> TABLE WITHOUT ID file.link AS "📄"
> FROM "案件库/{案件名}_{年度}"
> WHERE file.name = "02_材料清单" OR file.name = "03_案件信息"
> SORT file.name ASC
> ```
>
> **📚 法条检索**
> ```dataview
> TABLE WITHOUT ID file.link AS "📄"
> FROM "案件库/{案件名}_{年度}/04_法条检索"
> SORT file.name ASC
> ```
>
> **📖 案例检索**
> ```dataview
> TABLE WITHOUT ID file.link AS "📄"
> FROM "案件库/{案件名}_{年度}/05_案例检索"
> SORT file.name ASC
> ```

## 案情概要
```

---

## 三、Obsidian .base 数据库写法

### ⚠️ 铁律

- 参考来源：`D:\同步空间\BaiduSyncdisk\诉讼案件数据库智能版\` 下的 .base 文件
- **绝对不能用** `source:`, `properties: [案号]`, `---` frontmatter 这些网上文档的写法

### 正确语法

```yaml
filters:
  and:
    - file.basename.containsAny("案件概览")   # 顶层数据源筛选
views:
  - type: table
    name: 视图名
    filters:
      and:
        - 条件
    order:                    # 用 order 定义显示列，不是 properties
      - file.name             # 文件名列
      - 属性名                # frontmatter 属性直接写
    sort:                     # 排序
      - property: 属性名
        direction: ASC/DESC
    columnSize:               # 列宽
      file.name: 200
      note.属性名: 150
```

### 规则速查

- 列名：文件名用 `file.name`，frontmatter 字段直接写中文名
- columnSize：`note.属性名: 像素`
- 筛选非空：`"!属性名.isEmpty()"`
- 筛选排除：`属性名 != "值"`
- 在办过滤：`阶段 != "结案" AND 阶段 != "谈案失败"`

---

## 四、Dataview 兼容性备忘

> 本环境 Dataview 实测行为（与标准文档有差异）：

| 写法 | 状态 |
|------|------|
| `FROM ""` | ✅ 唯一支持写法 |
| `FROM "路径"` | ❌ |
| `contains(file.name, "关键词")` | ✅ |
| `contains(file.folder, "关键词")` | ✅ 唯一路径筛选方式 |
| `startswith(file.folder, "...")` | ❌ |
| `file.folder = "..."` | ❌ |
| `FROM #case` 标签筛选 | ⚠️ 不稳定 |

---

## 五、用户习惯

| 习惯 | 说明 |
|------|------|
| 直接执行，不写报告 | 不喜欢冗长说明，要结果 |
| 文件入案件用 Markdown | 从外部文件放入案件时转为 .md 保存 |
| 会反复要求修改 | 会反复审视并调整输出 |
| 倾向自行解决技术问题 | 遇到兼容性问题倾向于自己转换格式 |
