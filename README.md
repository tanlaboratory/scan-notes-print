# scan-notes-print

扫描笔记彩色打印技能：将拍照或扫描的笔记、作业图片、PDF 和含扫描图片的 Word 文档处理成白底、清晰、保留彩色笔迹的打印版。

## 功能

- 保留红笔、蓝笔和黑笔，去纸张灰底、阴影和偏色。
- 使用常规像素处理与适度锐化，保留原始字形、公式和图形；不做 OCR 重写或生成式补字。
- 根据实际页面裁去装订线圈和外围，默认不裁切。
- 输出 A4 彩色 PDF；Word 输入同时输出保留原生可编辑文字的 DOCX。
- 固定脚本批量执行，优先检查缩略图，只展开异常页面，减少重复分析和 token 消耗。

## 安装到 Codex

将本仓库克隆到个人技能目录：

```sh
git clone https://github.com/tanlaboratory/scan-notes-print.git ~/.codex/skills/scan-notes-print
```

使用自定义 `CODEX_HOME` 时，安装在其 `skills/scan-notes-print` 目录下。已有同名技能时保留现有文件，先比较再更新。

## 使用

上传笔记后对 Codex 说：

> 用 $scan-notes-print 生成彩色清晰打印版，保留红笔，去掉装订边，只保留正文。

也可以要求“淡笔迹优先”或“再清晰一些”。完整工作流和参数见 [SKILL.md](SKILL.md)。

## 脚本使用

Python 3.9+。在 Codex 中优先使用内置工作区依赖，无需重复安装。独立运行时安装 [requirements.txt](requirements.txt) 中的依赖；PDF 输入还需要 Poppler 的 `pdftoppm`。

```sh
python scripts/scan_notes.py inspect --input notes.jpg --work-dir work/notes
python scripts/scan_notes.py build --input notes.jpg --work-dir work/notes --output-dir outputs --preset crisp
```

多张图片按参数顺序提供多个 `--input`。DOCX 必须单独输入，并通过 `--renderer` 提供 Codex 文档技能的 `render_docx.py` 路径。文档导出依赖其配套 LibreOffice 和 PDF 渲染工具；独立图片/PDF 模式不依赖 Word。

裁边配置示例（坐标为源图比例）：

```json
{"crops":{"1":[0.12,0.025,0.95,0.975]},"modes":{"2":"preserve"}}
```

使用 `--config crops.json`。按当前图片确定裁剪框，不直接套用示例坐标。`preserve` 用于已白底的截图或需要保留底色的彩色图表。

## 验证与限制

已验证图片、PDF 和 Word 输入，包含红蓝笔保留、白底效果、黑笔对比度、裁剪边界、源文件不变和 Word 原文保持可编辑。不能恢复原图中不存在的细节，也不会自动校正严重透视。打印建议选择 A4、彩色、高质量、实际大小。

本仓库不包含用户笔记或测试文档。许可证：[MIT](LICENSE)。
