---
name: markitdown
description: 使用 Microsoft MarkItDown 将任意文件（PDF/图片/Word/Excel/PPT/HTML等）转换为 Markdown。用于收料环节统一格式。
trigger: 收料、转换文件、OCR扫描件、提取文字、文件转md
---

# MarkItDown 文件转换

## 前置条件
- Python 3.8+
- 已安装 `markitdown` 包：`pip install "markitdown[all]"`
  - `[all]` 包含所有可选依赖（pdfplumber、easyocr、speechrecognition 等）

## 工作流

### Step 1: 确认环境
```powershell
python -c "import markitdown; print(markitdown.__version__)"
```
如未安装，执行：
```powershell
pip install "markitdown[all]"
```

### Step 2: 转换文件
```powershell
python -m markitdown "源文件路径" -o "输出路径.md"
```

支持的格式：
- PDF（含扫描件 OCR）
- 图片（PNG/JPG/GIF/BMP/TIFF）
- Word (.docx)
- Excel (.xlsx)
- PowerPoint (.pptx)
- HTML / XML
- CSV / JSON
- EPUB
- 音频（转文字）

### Step 3: 归档到案件
转换后的 .md 文件按案件文件规范存入对应位置：
- 材料原件 → `案件库/{案件名}_{年度}/01_材料原件/`
- 其他材料按类型归档

## 注意事项
- 扫描件 OCR 精度取决于图片清晰度，建议 300dpi 以上
- 大文件转换可能耗时，耐心等待
- 原始文件保留在收料库，不删除
