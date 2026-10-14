# 风格提取指南

把参考作品的视觉基因读成结构化《风格档案》，供 HTML 构建阶段逐条对照。这一步决定成品"像不像"，是整个 skill 最容易翻车的地方——抓不准标志性元素，后面做得再精致也是"好看但不像"。

## 工具准备（一次性，隔离环境不污染本机）
```bash
PY=/Users/senaaa/.workbuddy/binaries/python/versions/3.13.12/bin/python3
$PY -m venv /Users/senaaa/.workbuddy/binaries/python/envs/default
/Users/senaaa/.workbuddy/binaries/python/envs/default/bin/pip install pymupdf python-pptx
```

## 按来源提取

### A. 参考是 .pptx 原件
- 文字/表格：`from pptx import Presentation`，遍历 `slides` → `shapes`，提取 `text_frame`、表格。
- 内嵌图片：导出 media 存到 `assets/`（可用 tencent-pptx 附带的 `doc_image_extractor.py`，或自行遍历 `slide.part` 的 image parts），后续**优先复用真实图**。
- 同时把 PPTX 渲染为图（见 C 的渲染思路）以便视觉分析样式。

### B. 参考是 PDF
```python
import fitz, os
doc = fitz.open(pdf_path)
print("PAGE_COUNT", doc.page_count)
for i in range(min(doc.page_count, 12)):
    pix = doc[i].get_pixmap(matrix=fitz.Matrix(100/72, 100/72))
    pix.save(f"ref_pages/page_{i+1:02d}.png")
# 试提文字，判断是文字型还是图片型
print(doc[0].get_text().strip()[:200])
```
- 文字型 PDF：可精确读文字/配色提示；图片型 PDF：完全靠视觉分析还原。

### C. 参考是截图
直接用 Read 工具看图，无需渲染。

## 分析维度（产出《风格档案》）
1. **配色板**：取色（或视觉估计 Hex）列出 主色 / 辅色 / 强调色 / 背景色。不要写"黄色系"这种模糊词，要落到具体 Hex。
2. **字体气质**：标题（粗宋 / 黑体 / 无衬线全大写英文 …），正文（黑体 / 衬线 …）。
3. **版式网格**：单页 or 跨页对开；左右分栏比例；页眉/页脚/页码位置；内容安全边距。
4. **标志性元素（signature）**：列出 3–5 个"没有它就不像"的元素。常见类型：
   - 超大章节数字（01 / 02 / 03 …实心或描边）
   - 竖排描边英文（如空心 `CONTENTS`）
   - 满底色块（整页纯色满底，而非小色块）
   - 毛笔画/印章类 logo 装饰
   - 双白线 / 分割线位置
   - 全出血大图、色胶囊标签、星级、国旗条等
5. **页面类型清单**：封面 / 目录 / 章节页 / 图文页 / 表格对比页 / 图片网格 / 封底，各举一个代表页并简述构成。

## 自检
风格档案写完后，用一句话概括："这份参考最像 X，和别的设计最大的不同是 Y。"
若说不出 Y，说明标志性元素没抓准——回到参考图重看，重点盯"第一眼和你印象里其他模板不同的地方"。
