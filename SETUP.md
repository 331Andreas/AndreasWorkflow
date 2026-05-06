  # 首次环境检查

本文件列出项目运行所需的全部环境依赖。首次打开本工作空间时，请逐项检查。

---

## 1. Python 环境

```powershell
python --version
```

**要求**：Python 3.8 或更高版本。

如未安装：[python.org/downloads](https://www.python.org/downloads/)

---

## 2. MarkItDown（文件转 Markdown 引擎）

```powershell
pip install "markitdown[all]"
```

验证：
```powershell
python -c "import markitdown; print(markitdown.__version__)"
```

`[all]` 安装所有可选依赖（含 OCR、音频转文字等）。

---

## 3. Obsidian 核心插件

在 Obsidian `设置 → 核心插件` 中启用：

- [ ] **Bases（数据库）** — `.base` 文件支持
- [ ] **Properties（属性）** — YAML frontmatter 支持

---

## 4. Obsidian 社区插件

在 Obsidian `设置 → 社区插件` 中安装并启用：

- [ ] **Dataview** — 动态查询引擎（案件概览的关联文档依赖）

---

## 5. 验证模板文件

确认以下文件存在：

- [ ] `模板库/案件概览模板.md` — 新建案件的标准模板

---

以上全部 ✅ 后，系统就绪。
