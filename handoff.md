# tahun2-bc-bishun · 二年级写字（笔顺描红）

**状态**：🟡 **本地已建好，未上线**。等老师本机实测 + 核对生字表 + 跑 migration。
**最后更新**：2026-09-10（Claude Sonnet 5）

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

## ⚠️ headless 验不了、要老师真机测的
- **描一描 / 写一写的笔顺判定**：Hanzi Writer 的 quiz 判定需要「真实鼠标」，合成事件不触发
  （tahun1 版同样的坑，学校鼠标环境没问题）。老师要在 Windows + Chrome + 学校鼠标上：
  实际描完一个字、凭记忆写对一个字、盖章、进度写进 localStorage
- 笔顺判定松紧（`leniency` 现在 trace=1.6 / write=1.6 / quiz=1.7）对二年级合不合适
- 三步流程会不会太长、音效/读音音量在教室公共喇叭上合不合适
- 二年级学生认字量下，界面文案有没有太难的字

## 📋 待办（顺序）
1. **老师核对生字表** → 见 `核对清单.md`（十六单元课二乱码、十八单元「脑」字、6 个单元疑缺课二）。
   改 `js/units.js` 一行 → `node build-data.mjs` 重跑
2. **老师本机实测**：`cd tahun2-bc-bishun && python3 -m http.server 8080` 打开，或直接开 index.html；
   走完整流程，确认「可以发布」
3. **跑 migration**：`kongsi-idea/supabase/migration-2026-09-10-tahun2-bc-bishun-progress.sql`
   （Dashboard SQL Editor 贴，或给 `sbp_` PAT 让 agent 用 Management API 跑）
   ⚠️ kongsi-idea 的 Supabase 免费版闲置 7 天会再暂停（见 BOARD），跑之前先确认 project 是活的
4. **确认二年级班级名册在 kelasku**：用 anon key 查 `classes?play_code=in.(JBC1037-2I,JBC1037-2G)`
   （具体 play_code 待老师确认——一年级是 `JBC1037-1I/1G`）
5. **部署**：`git init` → `gh repo create kongsi-idea/tahun2-bc-bishun --public --source=. --push`
   → `vercel link --scope kongsi-idea` → `vercel deploy --prod --yes` → 确认 alias
6. **Hub 登记**：`kongsi-idea/app.js` TOOLS 数组加条目（照 tahun1-bc-bishun 那条改）+ 4 张真实截图
   + `published-tools-coverage.md` 加一行 + `npm run status:sync`
7. **DSKP 索引**：中文课本内容已核（DSKP 3.1 笔画笔顺 / 5.1 笔画结构，同一年级版）；
   马来文官方单元名称未查证 → 先不进 `dskp-index.js`，coverage 备注标「马来文用词待查证」

## 回滚 / 清理
- 工具还没进任何 repo，不需要回滚
- `node_modules/` 是半途中断的复制残留（只有几个文件），gitignored，`build-data.mjs` 会跳过它
  改用 `../tahun1-bc-bishun/node_modules/hanzi-writer-data`；老师要自建可 `npm i`
- DB（跑了 migration 之后要撤）：
  `drop table public.tahun2_bc_bishun_progress cascade;`
  `drop function public.submit_tahun2_bc_bishun_progress(text,text,text,jsonb);`

## 教学依据
- DSKP：3.1（笔画笔顺、田字格正楷）、5.1（笔画结构）——跟一年级版同条，二年级继续练
- 学生困难假设：延续一年级——对笔顺不熟、凭感觉写；二年级字更复杂（结构、部件多），笔顺更要练熟
- 生字来源：二年级华文课本第 153-154 页习写生字表（提取重建，见 `核对清单.md`）
