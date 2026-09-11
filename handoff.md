# tahun2-bc-bishun · 二年级写字（笔顺描红）

**状态**：✅ **已上线**（`https://tahun2-bc-bishun.vercel.app`），已上架课堂点子铺。**v1.1**。
**最后更新**：2026-09-11（Claude Sonnet 5）

## v1.1（2026-09-11）修多音字读错音 + 加拼音标注
老师回报：字音朗读有些字听起来是错的。**根因**：`speechSynthesis` 读孤立的单个汉字，
遇到多音字（如「兴」「处」「参」）引擎会猜一个读音，常常不是课本这一课教的那个。

**修法**（跟 tahun1/tahun3-bc-bishun 同一套，三个工具一起修）：
1. **新增 `js/pinyin-data.js`**：234 字逐个配 `{py, word}`。221 字单音字，`pypinyin` 默认读音
   直接可信；13 字是课本原文里真的出现过不同读音的多音字，逐个对照课本原句人工核定
   （例如「行」在「种子的旅行」用 xíng，就不用「一行有一行」那个 háng）。每条都跑过
   校验：word 含 ch、`pypinyin(word)` 在该位置算出的读音等于存的 py。
2. **画面显示拼音**：三步（看一看/描一描/写一写）+ 小考都在田字格上方标"字 拼音"小标签。
   图缺失或查无这个字时标签自动隐藏，不影响功能。
3. **朗读改读「字，词」**：听读音 + 自动读音都从读孤字改成读"字，词"（如"高兴""与其"），
   词的上下文强制引擎读对音。

⚠️ 生字表 6 个待老师核的格（`核对清单.md`）跟这次改动无关，仍然待核。

## 上线事实（2026-09-10）
- 工具 repo：`github.com/kongsi-idea/tahun2-bc-bishun`（first commit `84f5571`）
- Vercel 项目 `tahun2-bc-bishun`（scope kongsi-idea，`prj_85Hl8hkXM87NPehJX6oZ4MeA73jP`），
  `vercel deploy --prod` 部署，alias `tahun2-bc-bishun.vercel.app` 已自动指向最新
  ⚠️ Vercel↔GitHub 自动部署**没接上**（`vercel link` 时 connect 报错）——push 不会自动上线，
  要 `cd tahun2-bc-bishun && npx vercel deploy --prod --yes --scope kongsi-idea`。老师想要的话去
  Vercel Dashboard 手动 connect repo
- Hub：`kongsi-idea` commit `9e02dab`（app.js TOOLS + 4 缩略图 + coverage 矩阵）；
  `kongsi-idea.vercel.app` alias 手动补指到 `kongsi-idea-1gueqrrf9-...`（那个 alias 不会自动跟部署）
- **线上 E2E 实测**（生产 URL，headless 真实鼠标）：27 单元 / 234 格 / HW_DATA 234 字；
  选名字→选单元→看一看→写一写，**脚本真实描完整个「万」字触发盖章**「写好了！得到一颗 ★」；
  console 0 error。Hub 首页「二年级写字」卡出现，详情页「开始使用」→ 正式网址

## ⚠️ 上线时**没做**、老师要补的（按重要性）
1. **生字表核对**（`核对清单.md`）——234 格里 ~6 格提取文本有问题/存疑（十六单元课二乱码、
   十八单元「脑」字、6 个单元疑缺课二）。改 `units.js` 一行 → `node build-data.mjs` → 
   `vercel deploy --prod`。**没有数据模型改动，学生在用时热更新是安全的**（跟一年级版同款）
2. **Supabase migration 没跑**：`kongsi-idea/supabase/migration-2026-09-10-tahun2-bc-bishun-progress.sql`
   （Dashboard SQL Editor 贴，或给 `sbp_` PAT）。**没跑之前**：进度同步的 RPC 调用静默失败 →
   工具照常用，进度只存 localStorage（电脑室一人一机完全够用）；跑了之后跨电脑接续才生效
3. **kelasku 里没有二年级班级**：查过 `classes` 表只有 `JBC1037-1I` / `JBC1037-1G`，没有 2I/2G。
   `?code=` 带班级代码的名单功能要老师先在 kelasku 建二年级班（play_code 老师定）。
   不建的话学生走「跳过 → 打名字」，一样能用
4. **真机实测**：headless 用 Playwright 的 CDP 鼠标能触发笔顺判定（已验），但学校 Windows+Chrome+
   学校鼠标、判定松紧对二年级合不合适、三步流程节奏、音效音量，还是要老师在电脑室实际走一遍

## 这是什么
照 `tahun1-bc-bishun` 的架构做的二年级版：换一套数据（二年级习写生字）+ 换一套视觉皮肤
（**任天堂 / 马力欧风**，跟一年级那版「文房 / 宣纸」刻意分道）。
逻辑几乎原样搬：看一看（笔顺动画 + 自动读字音）→ 描一描 → 写一写（凭记忆，写对得 ★），
每单元「小考」随机抽最多 8 字，全单元写完卡片盖金星「棒」章。

- 27 单元（识字一~五 + 第 1~22 单元），**234 个习写格**
- 纯前端无 build。`js/char-data.js`（234 字笔画，533KB）由 `node build-data.mjs` 从
  `hanzi-writer-data@2.0.1` 打包，离线可用。**缺字 0**。
- 笔画引擎 Hanzi Writer 3.7.0（`vendor/`，MIT）
- 声音 WebAudio 生成式 + 读字音用浏览器内建 `speechSynthesis`（zh-CN，无网络/密钥）

## 视觉皮肤：任天堂 / 马力欧（_style-lab/STYLES.md §02）
天蓝渐层底 + 远云 + 远丘 + 砖块地平线（背景降饱和做纵深，不抢操作区）；
田字格是全场最大的主角（奶油米金面板 + 金色虚线辅助线 + 金框）；
立体描边标题字（`-webkit-text-stroke` + 多层 text-shadow 挤出）；
软胶质感按钮（下沿厚度 `box-shadow: 0 Npx 0 深色` + 顶部 inset 高光）；
队伍蓝 = 继续/主按钮，蘑菇绿 = 对/进度，火花橙红 = 错，金币金 = 星星/奖励。
配色 token 全在 `css/style.css` 顶部 `:root`。
→ 待回填 memory `design-aesthetic-preferences.md` 的「近期风格清单」（上线后填）。

## 与 tahun1 版的差异（除了皮肤和数据）
- `SLUG` = `tahun2-bc-bishun`
- localStorage key 前缀 `bishun2_`（`bishun2_prog__…` / `bishun2_muted` / `bishun2_code`），
  跟一年级版隔离——同一台电脑室机器上两个年级的进度不会串
- Supabase：表 `tahun2_bc_bishun_progress` + RPC `submit_tahun2_bc_bishun_progress`
- Hanzi Writer 描红配色改成暖棕墨 + 蓝色运笔 + 绿色高亮（配合新皮肤）
- 完成章从朱砂「优」改成金星「棒」

## ✅ 已验证（Python Playwright，headless，1280×900 + 390×844）
- 选名字（跳过代码 → 手动输入名字）→ 首页 27 单元卡渲染，UNITS.length=27，total_cells=234，overall 计数正确
- 第一单元 → 8 字格（万千列世界奔牵浪），练习页三步都能切，Hanzi Writer SVG 正常渲染，「共 3 笔」正确，笔顺动画跑得动，自动读音回调触发
- 小考开得起来（「第 1 / 8 题」），田字格空白（memory quiz 正确）
- **console 0 error**（favicon 内联无 404；Supabase 脚本从 kongsi-idea.vercel.app 载入正常，读不到名单时静默降级到手动输入）
- 手机 390 宽：2 列卡片网格，标题/计数/按钮都正常

## DSKP 索引
中文课本内容已核（DSKP 3.1 笔画笔顺 / 5.1 笔画结构，跟一年级版同条）；
马来文官方单元名称未查证 → **没进** `kongsi-idea/data/dskp-index.js`，
`published-tools-coverage.md` 备注已标。要收录得先核官方 DSKP PDF 的马来文用词。

## 回滚
- 工具：`cd teaching-tools/tahun2-bc-bishun && git revert HEAD && git push`
  → `npx vercel deploy --prod --yes --scope kongsi-idea`
  （首版就一个 commit，真要下架直接删 Vercel 项目 + GitHub repo）
- Hub 下架：`kongsi-idea/app.js` 删掉 `tahun2-bc-bishun` 那个 TOOLS 条目 → commit/push →
  `npx vercel deploy --prod --yes --scope kongsi-idea` →
  `npx vercel alias set <新url> kongsi-idea.vercel.app --scope kongsi-idea`
- DB（跑了 migration 之后要撤）：
  `drop table public.tahun2_bc_bishun_progress cascade;`
  `drop function public.submit_tahun2_bc_bishun_progress(text,text,text,jsonb);`
- `node_modules/` 是半途中断的复制残留（只有几个文件），gitignored，`build-data.mjs` 会跳过它
  改用 `../tahun1-bc-bishun/node_modules/hanzi-writer-data`；要自建可 `npm i`

## 教学依据
- DSKP：3.1（笔画笔顺、田字格正楷）、5.1（笔画结构）——跟一年级版同条，二年级继续练
- 学生困难假设：延续一年级——对笔顺不熟、凭感觉写；二年级字更复杂（结构、部件多），笔顺更要练熟
- 生字来源：二年级华文课本第 153-154 页习写生字表（提取重建，见 `核对清单.md`）
