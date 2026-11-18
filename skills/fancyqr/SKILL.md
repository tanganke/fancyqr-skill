---
name: fancyqr
description: Add styled QR codes with the fancyqr package, using only the options the project's TeX Live has, escaping URLs, keeping codes scannable, and adding fancyqr v2.5 to the project when its newer styles are wanted.
---

fancyqr (CTAN, by Florian Sihler) draws QR codes with rounded tiles, gradients, finder shapes and themed styles on top of the `qrcode` package: `\fancyqr[<options>]{<text>}`, and `\fancyqrset{<options>}` for every later code. Most mistakes come from using options the installed version does not have, so start there.

## 1. Find out which fancyqr the project compiles with

Each TeX Live year has a different fancyqr. On JointTex:

| Project's TeX Live | fancyqr | What it has |
| --- | --- | --- |
| 2024 | v2.2 | base set |
| 2025 | v2.3 | base set (same keys as v2.2) |
| 2026 | v2.4 | base set + v2.4 set |
| fancyqr files in the project (section 4) | v2.5 | everything below |

Do not trust the table over the log: add `\listfiles` as the first line of the root file, compile, and read the compile log for the line `fancyqr.sty  <date> version v2.x`. Remove `\listfiles` afterwards if the person did not want it. An error `Package xkeyval Error: '<key>' undefined in families 'qr,fancyqr'` means the key is from a newer version than the one loaded; do not guess another spelling.

**Base set (v2.2 and later).** Keys: `size` / `width` (= qrcode's `height`), `level` (`L`/`M`/`Q`/`H`), `padding` (4-module white border) or `tight` (no border; qrcode's default), `version` (0–40, 0 = smallest that fits), `nolink`, `color` (one color, turns the gradient off), `l color` / `left color`, `r color` / `right color`, `gradient` (true/false), `gradient angle`, `random color` (a list of colors; needs `\usepackage{pgfmath}` or it stops with "pgfmath not loaded"), `image`, `image padding`, `image x padding`, `image y padding`, `compensate`, `classic` (black square modules). Styles for `\FancyQrLoad{<name>}`: `default`, `flat`, `dots`, `blobs`, `glitch`, `frame`. Commands: `\FancyQrLoad`, `\FancyQrLoadDefault`, `\FancyQrDoNotPrintSquare{<w>}{<h>}`, `\FancyQrDoNotPrintRadius{<factor>}`, `\FancyQrHardCut`, `\FancyQrRoundCut`.

**v2.4 set.** Styles `rounded`, `glows`, `goo`. Keys `seed` (makes the random styles `blobs`, `glitch`, `goo` the same on every compile), `cache`, `overlap`, `rounding`, `inner rounding`, `finder` (`inherit`/`square`/`rounded`/`circle`), `finder core` (`auto`/`inherit`/`none`/`square`/`rounded`/`circle`/`star`), `finder color`, `finder radius`, `finder thickness`, `content color`. The default gradient changed in v2.4 from teal–magenta to dark red, so the same source looks different on TeX Live 2026 than on 2024/2025.

**v2.5 only.** Styles `halloween`, `snow`, `castle`, `christmas`. Keys `style=<name>` (a style for one code), `add=<style>` (another style's decorations, e.g. `style=castle,add=halloween`), `background`, `background radius`, `background margin`, `alignment` (`finder`/`inherit`/`hide`), `alignment color`, `alignment core color`, `finder core color`, `timing color`, `figures`, `no figures`, `figure chance`, `place`, `extras`, `no extras`, `faint`, `eci`, `persist`, `externalize`; `finder core=pumpkin` or `halloween`. Correct non-ASCII text (section 3).

When the person asks for something only v2.5 has, say which version the project has, then offer the two ways: the closest look the installed version can draw, or adding v2.5 to the project (section 4). Do not switch the project's TeX Live year on your own; it changes every package in the document.

## 2. Writing the code

- Load it in the preamble: `\usepackage{fancyqr}`. It needs nothing else (pict2e, xfp, qrcode come with it).
- Choose a style for one code by wrapping it in a group, since `\FancyQrLoad` is local: `{\FancyQrLoad{dots}\fancyqr{...}}`. In v2.5 prefer `\fancyqr[style=dots]{...}`. Up to v2.4, `\FancyQrLoadDefault` after `dots` or `blobs` keeps their circle finder patterns; go back with a closing group or `\FancyQrLoad{default}` instead.
- Give a random style a `seed` (v2.4+) so the figure does not change between compiles: `blobs`, `glitch`, `goo`, and in v2.5 also `halloween`, `snow`, `christmas`.
- A logo or text in the middle: `image=<LaTeX>` with `level=H` and `image padding=1`. The image keeps its own size; it does not scale with the code. Keep it to roughly a fifth of the code's width or less.
- Size and placement: `size=3cm` (or `\fancyqrset{size=...}` once). A code is a box like an image: put it in a `figure`, a `minipage`, a `tabular` cell or a beamer column as usual. Several codes in a row can overflow the line; check for Overfull \hbox warnings.
- Clickable as well as scannable: with `hyperref` loaded, qrcode's `link` key (on by default) makes the code a link to its text; `nolink` turns it off. Check the link in the PDF if it matters.

## 3. The text inside the code

- Escape these characters with a backslash in the argument: `\#`, `\%`, `\&`, `\~`, and also `\$`, `\^`, `\_`, `\{`, `\}`, `\\` when they occur. Write `\~` alone, never `\~{}` (that stops the compile with "Use of \qr@decimaltohex doesn't match its definition"). `_`, `?`, `=`, `/`, `:` and `-` can be written as they are. Example, tested to decode exactly: `\fancyqr{https://example.org/a_b?x=1\&y=2\#frag\%20\~z}` encodes `https://example.org/a_b?x=1&y=2#frag%20~z`.
- A macro holding the URL works: `\newcommand\repo{https://...}` then `\fancyqr{\repo}`.
- Keep it short. Every extra character adds modules, makes each module smaller at the same size, and leaves less room for decoration. Prefer the shortest stable URL the person gives you; never invent a shortened link.
- Non-ASCII text (accents, CJK, emoji):
  - v2.5: works on pdfLaTeX, XeLaTeX and LuaLaTeX, marked as UTF-8 (`eci=false` only if the person reports a scanner that rejects it).
  - v2.4 and older with **XeLaTeX or LuaLaTeX**: it compiles without any error but the code is wrong and does not scan (tested). Do not use it there: percent-encode the URL (`%C3%BC` for ü, so write `\%C3\%BC`), use pdfLaTeX, or add v2.5.
  - v2.4 and older with pdfLaTeX: the bytes are UTF-8 without a marker; common scanners guessed right in testing, but say so to the person if the text matters.

## 4. Adding fancyqr v2.5 to the project

TeX Live's own copy is always found before a copy in a subfolder (`\input@path` does not help: `\usepackage{fancyqr}` still loads TeX Live's), so the files must sit in the **same folder as the root `.tex` file**, where the compile runs. All 13 files are needed: `fancyqr.sty` and the 12 `fancyqr-style-*.code` files (`blobs`, `castle`, `christmas`, `dots`, `flat`, `frame`, `glitch`, `glows`, `goo`, `halloween`, `rounded`, `snow`). With only some of them, a style loads TeX Live's older file next to the new package and breaks or looks wrong; `classic` loads `flat` itself.

Ask the person first, since this adds files to the project. Then either they upload the files from CTAN (`https://ctan.org/pkg/fancyqr`) or GitHub (`https://github.com/EagleoutIce/fancyqr`), or you fetch them:

1. `download_file` with `https://github.com/EagleoutIce/fancyqr/archive/refs/tags/v2.5.zip`. GitHub answers with a redirect to `codeload.github.com`; call `download_file` again with the address it hands back (the person approves that host).
2. `list_archive` on the downloaded ZIP and note the top folder (expected `fancyqr-2.5/`).
3. `extract_archive` with `entries` set to the 13 file paths inside that folder and `to` set to `.jointtex/uploads/fancyqr`, where nothing is compiled.
4. `move_path` each of the 13 files from there to the root file's folder.
5. Compile with `\listfiles` once and confirm the log says `v2.5`.

Do not edit these files (fancyqr is under the LPPL 1.3c: a changed copy must be renamed). To go back to TeX Live's version, delete all 13 together. v2.5 compiled on JointTex with TeX Live 2024 and 2026 under pdfLaTeX, and 2026 under XeLaTeX. Leave `externalize` off: it needs shell escape, which JointTex does not allow; the default `persist` is fine (its `.fqr` records go to the compile output, not the project).

## 5. Keeping it scannable

Decoration costs reliability. Defaults that scanned in every test: dark modules on a white or very light background, `padding` (or at least four modules of white around the code from the layout), `size` of 2 cm or more for print (3 cm for posters seen from a distance), `level=M` or higher, and `level=H` whenever there is an `image`, heavy decoration, `goo`, `glitch`, or the v2.5 themed styles.

- Avoid light colors in `random color`, a gradient that fades into the background, and `content color` close to white.
- `frame` up to v2.4 draws only outlines and did **not** decode in testing, even at 300 dpi; use it only as decoration, or use v2.5, where it scans.
- v2.5's `halloween`, `snow`, `castle`, `christmas` and `castle,add=halloween` all decoded in testing at 3 cm; `castle` puts a grass background by default.
- `alignment=hide` (v2.5) removes the alignment pattern; test it before keeping it.

You cannot scan a QR code yourself. After compiling, look at the page image (`read_pdf` with an image) to check layout and that the three finder patterns are clear, then ask the person to scan it from the screen with a phone before the document is final, and offer `classic` or plain `default` as a fallback if a decorated code does not scan.
