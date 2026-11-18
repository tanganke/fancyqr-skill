# fancyqr skill for JointTex

A JointTex Agent skill for [fancyqr](https://github.com/EagleoutIce/fancyqr), the LaTeX package by Florian Sihler that draws styled QR codes (rounded tiles, gradients, finder shapes, a logo in the middle, and themed styles such as halloween, snow, castle and christmas).

为 JointTex Agent 准备的 fancyqr 技能：让 Agent 在 LaTeX 文档里加好看又能扫的二维码。

## What the skill teaches the Agent

- **Which fancyqr the project has.** Each TeX Live year ships a different version (on JointTex: 2024 → v2.2, 2025 → v2.3, 2026 → v2.4), and most of the options in fancyqr's current README are v2.5 only. The Agent checks the compile log first and uses only what is installed.
- **Adding v2.5 to a project** when the newer styles are wanted: which 13 files, where they must go (beside the root file; a subfolder does not work), and how to fetch them with the Agent's own tools after asking.
- **Escaping the text** (`\#`, `\%`, `\&`, `\~`, never `\~{}`), and the trap that non-ASCII text under XeLaTeX/LuaLaTeX with v2.4 or older compiles without error but gives a code that does not scan.
- **Keeping it scannable:** contrast, quiet zone, size, error-correction level with a logo, and the styles that did not decode in testing (`frame` up to v2.4).

Every claim was checked by compiling on JointTex (TeX Live 2024, 2025 and 2026; pdfLaTeX and XeLaTeX) and decoding the resulting PDFs with ZXing.

## 技能要点

- 先看项目用的 TeX Live 年份对应哪个 fancyqr 版本（2024 → v2.2，2025 → v2.3，2026 → v2.4），只用已安装版本支持的选项；fancyqr README 里的大部分新选项只在 v2.5 才有。
- 需要 v2.5 的新样式时，把 13 个文件放到主 `.tex` 文件所在目录（放子目录无效），Agent 可在征得同意后自行下载。
- URL 里的特殊字符要转义；在 v2.4 及更早版本下用 XeLaTeX/LuaLaTeX 编码中文等非 ASCII 文本，会编译成功但二维码无法扫描。
- 保证可扫：对比度、留白、尺寸、加 logo 时用 `level=H`，以及测试中扫不出的样式。

## Install

Open the Plugins dialog in a JointTex project (Agent panel → Plugins), find **fancyqr** under Skills, and install it into the project. It is written to `.jointtex/skills/fancyqr/SKILL.md`; the Agent loads it when a QR code is asked for, or with `/skill fancyqr`.

## License

The skill text is MIT licensed (see `LICENSE`). fancyqr itself is under the LaTeX Project Public License 1.3c and is not included here.
