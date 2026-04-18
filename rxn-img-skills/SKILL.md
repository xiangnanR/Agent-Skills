---
name: rxn-img
description: "Corporate tech infographic image generation skill. Use this skill when you need to generate professional illustrations in the 'Corporate Tech Infographic' style for: (1) Presentation slides (PPT/Keynote), (2) Document illustrations (Word/PDF reports), (3) Blog or article header images, (4) Product marketing visuals, (5) Cover images for whitepapers or reports. This style features isometric projection, flat design with subtle depth, corporate-friendly color palettes, and metaphor-driven visual storytelling for technology and AI topics."
---

# RXN 图像生成技能 — 企业科技信息图风格

## 快速参考

| 任务 | 指南 |
|------|------|
| 了解完整风格定义 | 阅读 [style-guide.md](style-guide.md) |
| 生成配图（Prompt 模板） | 阅读 [prompt-templates.md](prompt-templates.md) |
| 查看示例参考 | 阅读 [examples.md](examples.md) |

---

## 概述

本技能定义了一套 **"企业科技信息图"（Corporate Tech Infographic）** 视觉风格，用于通过 AI 图像生成工具（如 GenerateImage、DALL-E、Midjourney 等）创建专业、统一的配图。

**核心目标**：将复杂的技术概念通过视觉隐喻转化为直观、易懂、专业的图形表达。

**语言要求**：⚠️ **图片中出现的所有文字（标题、标签、说明、标注等）必须使用中文，仅专业术语（如 AI、API、SaaS、HTML 等行业通用缩写）可保留英文原文。** 这是一项强制性要求，适用于所有 Prompt 模板和生成的配图。

**适用场景**：
- ✅ 技术概念解释（AI/ML、软件架构、数据流）
- ✅ 演示文稿配图（投资人路演、内部培训、技术分享）
- ✅ 博客/文章插图（技术博客、行业分析）
- ✅ 产品营销素材（SaaS 着陆页、功能介绍）
- ✅ 封面图（报告封面、白皮书封面）

**不适用场景**：
- ❌ 需要高度艺术性和原创性的品牌视觉
- ❌ 面向消费者的情感化营销
- ❌ 需要真实摄影感的场景

---

## 工作流程

### 第一步：分析需求
- 确定配图用途（PPT / 文档 / 博客 / 封面）
- 确定核心概念和要传达的信息
- 确定构图类型（中心焦点 / 水平叙事流 / 多元素并列 / 层叠递进）
- 确定图片中需要出现的文字内容，并将其翻译为中文（专业术语除外）

### 第二步：选择 Prompt 模板
- 阅读 [prompt-templates.md](prompt-templates.md) 选择合适的模板
- 或参考 [examples.md](examples.md) 中的实际示例
- 确保模板中包含 `[LANGUAGE]` 区块，指定中文文字要求

### 第三步：填充内容并生成
- 将具体内容填入模板的 `{占位符}` 中
- **在 `[TEXT]` 区块中用中文写出图片中应显示的所有文字**
- 调整色彩方案（如需与品牌色一致）
- 调用图像生成工具（GenerateImage）

### 第四步：后期处理
- ⚠️ AI 图像生成对中文文字渲染能力有限，如文字出现乱码或缺失，建议预留文字区域，后期用设计工具添加中文文字
- 检查同一组配图的色彩一致性
- 确认比例符合用途（PPT: 16:9 横版，文档: 4:3 或方形）
- **确认图片中所有文字均为中文（专业术语除外）**

---

## 风格速查

| 要素 | 规范 |
|------|------|
| **语言** | 图片内文字一律使用中文，仅专业术语可保留英文 |
| **透视** | 等距透视（Isometric），30° 视角 |
| **设计语言** | 扁平化 + 微立体感，柔和阴影，几何简化 |
| **主色调** | 科技蓝 (#3B82F6)，占画面 40-60% |
| **辅助色** | 珊瑚红 (#E74C3C)、翡翠绿 (#27AE60)、琥珀黄 (#F5A623) |
| **中性色** | 冷灰色系 (#6B7280, #9CA3AF, #E5E7EB) |
| **点缀色** | 青色荧光 (#00D4FF)，用于发光效果 |
| **人物** | 无面部特征的几何简化人物 |
| **背景** | 纯白或极浅灰，大量留白 |
| **字体** | 中文优先使用思源黑体/Noto Sans SC，英文/术语使用 Inter/Roboto，层级分明 |

> 📖 完整风格定义请参阅 [style-guide.md](style-guide.md)

---

## 与其他技能的配合

- **pptx 技能**：生成 PPT 配图后嵌入演示文稿
- **docx 技能**：生成文档插图后嵌入 Word 文档
- **pdf 技能**：生成报告封面或章节配图

当需要将生成的配图嵌入到文档或演示文稿中时，应先使用本技能生成图片，再激活对应的文档技能进行集成。
