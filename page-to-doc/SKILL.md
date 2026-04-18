---
name: page-to-doc
description: 从网页采集内容生成 Word 文档。自动解析页面 CSS 样式，只提取可见内容，忠实还原字体、颜色、布局到 Word 文档。
---

# 页面采集生成文档技能

本技能用于从网页采集内容并生成 Word 文档，自动解析页面样式并忠实还原到文档中。

## 核心原则

### 1. 只采集可见内容
- ❌ 不添加任何网页中不存在的辅助标签（如【简介】、【开发者信息】等）
- ❌ 不添加网页中没有的说明文字
- ❌ 不添加页脚/版权信息（除非网页明确显示）
- ❌ 不添加装饰性元素（除非网页本身有）

### 2. 忠实还原样式
- ✅ **以原页面的 CSS 样式为准**，不套用固定规范
- ✅ 动态解析 `h1`, `h2`, `h3`, `p`, `ul`, `ol` 等元素的样式
- ✅ 提取 `color`, `font-size`, `font-weight`, `line-height` 等属性
- ✅ 映射到 Word 文档中保持视觉效果一致

### 3. 保持内容结构
- ✅ 保持原有的章节层级（H1 → H2 → H3 → H4）
- ✅ 保持列表结构（有序/无序）
- ✅ 保持段落顺序
- ✅ 保持表格结构（如有）

## 样式解析规则

### CSS 到 Word 的映射

| CSS 属性 | Word 映射 | 示例 |
|----------|----------|------|
| `font-size: 24px` | `Pt(24)` | 24px → 24pt |
| `font-size: 14pt` | `Pt(14)` | 14pt → 14pt |
| `color: #333` | `RGBColor(51, 51, 51)` | #333 → RGB(51,51,51) |
| `color: #e50b20` | `RGBColor(229, 11, 32)` | #e50b20 → RGB(229,11,32) |
| `font-weight: bold` | `font.bold = True` | 加粗 |
| `line-height: 1.8` | `line_spacing = Pt(21.6)` | 1.8 倍行距 |
| `text-align: center` | `alignment = CENTER` | 居中 |
| `margin: 15px 0` | `space_before/after = Pt(15)` | 段落间距 |
| `padding-left: 30px` | `left_indent = Cm(1)` | 左缩进 |
| `border-left: 4px solid #e50b20` | 左边框（如支持） | 左边框 |

### 标题层级处理

```python
# H1 - 主标题
if element.name == 'h1':
    heading = doc.add_heading(text, level=0)
    # 应用 CSS 中 h1 的样式

# H2 - 大章节
if element.name == 'h2':
    heading = doc.add_heading(text, level=1)
    # 应用 CSS 中 h2 的样式

# H3 - 中章节
if element.name == 'h3':
    heading = doc.add_heading(text, level=2)
    # 应用 CSS 中 h3 的样式

# H4 - 小章节
if element.name == 'h4':
    heading = doc.add_heading(text, level=3)
    # 应用 CSS 中 h4 的样式
```

## 处理流程

### 1. 采集页面
```python
# 使用 browser 工具访问页面
browser(action='open', targetUrl='https://example.com/page')
browser(action='act', kind='wait', timeMs=3000)

# 获取页面内容
content = browser(action='act', kind='evaluate', fn='() => document.body.innerText')
html = browser(action='act', kind='evaluate', fn='() => document.documentElement.outerHTML')
```

### 2. 解析样式
```python
from bs4 import BeautifulSoup
import re

soup = BeautifulSoup(html, 'html.parser')

# 提取 CSS
style_tag = soup.find('style')
if style_tag:
    css_text = style_tag.string
    # 解析 CSS 规则
    h1_style = parse_css_rule(css_text, 'h1')
    h2_style = parse_css_rule(css_text, 'h2')
    # ...
```

### 3. 提取可见内容
```python
# 只提取可见文本，忽略隐藏元素
visible_elements = []
for element in soup.find_all(['h1', 'h2', 'h3', 'h4', 'p', 'ul', 'ol']):
    # 检查是否可见（无 display:none, visibility:hidden 等）
    if is_visible(element):
        visible_elements.append(element)
```

### 4. 生成 Word 文档
```python
from docx import Document
from docx.shared import Pt, RGBColor, Cm
from docx.enum.text import WD_ALIGN_PARAGRAPH
from docx.oxml.ns import qn

doc = Document()

# 设置页面
sections = doc.sections
for section in sections:
    section.page_width = Cm(21)  # A4
    section.page_height = Cm(29.7)
    section.top_margin = Cm(2.54)
    section.bottom_margin = Cm(2.54)
    section.left_margin = Cm(3.18)
    section.right_margin = Cm(3.18)

# 添加内容
for element in visible_elements:
    if element.name == 'h1':
        heading = doc.add_heading(element.get_text().strip(), 0)
        apply_style(heading, h1_style)
    elif element.name == 'p':
        p = doc.add_paragraph()
        p.add_run(element.get_text().strip())
        apply_style(p, p_style)
    # ...

# 保存
doc.save(output_path)
```

## 禁止添加的内容

### ❌ 不添加辅助标签
```
错误示例：
【简介】
【开发者信息】
【联系方式】
【页脚信息】
```

### ❌ 不添加装饰元素
```
错误示例：
──────────────────────────────
（除非网页本身有分隔线）
```

### ❌ 不添加网页中没有的信息
```
错误示例：
最后更新日期：2024 年
版权所有：XXX 公司
来源：https://...
（除非网页明确显示这些内容）
```

## 输出文件

### HTML 文件
- 路径：`{workspace}/{主题}_full.html`
- 内容：保留原页面样式的完整 HTML

### Word 文档
- 路径：`{workspace}/{主题}.docx`
- 内容：动态映射样式的 Word 文档
- 字体：优先使用网页指定的字体， fallback 到 Microsoft YaHei

## 示例代码

```python
#!/usr/bin/env python3
"""
页面采集生成 Word 文档 - 标准模板
"""
from docx import Document
from docx.shared import Pt, RGBColor, Cm
from docx.enum.text import WD_ALIGN_PARAGRAPH
from docx.oxml.ns import qn
from bs4 import BeautifulSoup
import re

def parse_color(color_str):
    """解析 CSS 颜色值为 RGB"""
    if not color_str:
        return RGBColor(51, 51, 51)  # 默认深灰
    
    color_str = color_str.strip()
    
    # #RGB 或 #RRGGBB
    if color_str.startswith('#'):
        hex_color = color_str[1:]
        if len(hex_color) == 3:
            hex_color = ''.join([c*2 for c in hex_color])
        try:
            return RGBColor(
                int(hex_color[0:2], 16),
                int(hex_color[2:4], 16),
                int(hex_color[4:6], 16)
            )
        except:
            return RGBColor(51, 51, 51)
    
    # rgb(r, g, b)
    rgb_match = re.match(r'rgb\(\s*(\d+)\s*,\s*(\d+)\s*,\s*(\d+)\s*\)', color_str)
    if rgb_match:
        return RGBColor(
            int(rgb_match.group(1)),
            int(rgb_match.group(2)),
            int(rgb_match.group(3))
        )
    
    return RGBColor(51, 51, 51)

def parse_font_size(size_str):
    """解析 CSS 字体大小为 Pt"""
    if not size_str:
        return Pt(12)
    
    size_str = size_str.strip()
    
    # px
    px_match = re.match(r'(\d+(?:\.\d+)?)px', size_str)
    if px_match:
        return Pt(float(px_match.group(1)) * 0.75)
    
    # pt
    pt_match = re.match(r'(\d+(?:\.\d+)?)pt', size_str)
    if pt_match:
        return Pt(float(pt_match.group(1)))
    
    return Pt(12)

def html_to_word(html_file, docx_file):
    """将 HTML 转换为 Word 文档"""
    
    # 读取 HTML
    with open(html_file, 'r', encoding='utf-8') as f:
        html_content = f.read()
    
    soup = BeautifulSoup(html_content, 'html.parser')
    
    # 提取 CSS
    style_tag = soup.find('style')
    css_text = style_tag.string if style_tag else ''
    
    # 解析各元素样式
    styles = {
        'h1': parse_css_rules(css_text, 'h1'),
        'h2': parse_css_rules(css_text, 'h2'),
        'h3': parse_css_rules(css_text, 'h3'),
        'h4': parse_css_rules(css_text, 'h4'),
        'p': parse_css_rules(css_text, 'p'),
    }
    
    # 创建文档
    doc = Document()
    
    # 设置页面
    for section in doc.sections:
        section.page_width = Cm(21)
        section.page_height = Cm(29.7)
        section.top_margin = Cm(2.54)
        section.bottom_margin = Cm(2.54)
        section.left_margin = Cm(3.18)
        section.right_margin = Cm(3.18)
    
    # 提取并添加可见内容
    for element in soup.find_all(['h1', 'h2', 'h3', 'h4', 'p', 'ul', 'ol']):
        if not is_visible(element):
            continue
        
        text = element.get_text().strip()
        if not text:
            continue
        
        if element.name == 'h1':
            heading = doc.add_heading(text, 0)
            apply_style(heading, styles['h1'])
        elif element.name == 'h2':
            heading = doc.add_heading(text, 1)
            apply_style(heading, styles['h2'])
        elif element.name == 'h3':
            heading = doc.add_heading(text, 2)
            apply_style(heading, styles['h3'])
        elif element.name == 'h4':
            heading = doc.add_heading(text, 3)
            apply_style(heading, styles['h4'])
        elif element.name == 'p':
            p = doc.add_paragraph()
            p.add_run(text)
            apply_style(p, styles['p'])
        elif element.name in ['ul', 'ol']:
            for li in element.find_all('li'):
                p = doc.add_paragraph(style='List Bullet')
                p.add_run(li.get_text().strip())
    
    # 保存
    doc.save(docx_file)
    print(f"✅ Word 文档已保存：{docx_file}")

if __name__ == "__main__":
    html_to_word(
        '/path/to/page_full.html',
        '/path/to/output.docx'
    )
```

## 使用说明

### 基本用法
```bash
# 1. 采集页面
browser(action='open', targetUrl='https://example.com/page')

# 2. 保存 HTML
write(path='page_full.html', content=html)

# 3. 转换为 Word
python3 html_to_word.py page_full.html page.docx
```

### 注意事项
1. 确保只提取可见内容
2. 动态解析 CSS 样式，不套用固定模板
3. 保持原有内容结构
4. 字体 fallback 到 Microsoft YaHei

---

**最后更新**：2026-03-18

**作者**：xiangnanR

**版本**：1.0
