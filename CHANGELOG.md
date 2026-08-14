# Changelog

本檔自 `VERSION` 投影；版號於 PR merge 當下釘選（feat→minor、fix→patch）。

> **📣 更名告示（2026-08）**：GitHub 組織已更名 `twStellerWhale-Ocean2` → `twStellarWhale-Ocean`。repo 網址自動轉址，但 **GHCR 不轉址**——下文歷史紀錄中的 `ghcr.io/twstellerwhale-ocean2/…` 已失效，請一律改用 `ghcr.io/twstellarwhale-ocean/…`（其後路徑與版本 tag 不變；歷史版本 image／chart 皆已在新位址可拉）。

## 1.1.1 — 2026-07-30

增量 #198（依賴安全升版＋相依授權豁免定案，fix）。

- **`postcss`（HIGH）／`body-parser`（LOW）已升至修復版**：三處建置單元 `npm audit` 由 5 項降至 2 項。
- **`react-router-dom` 6.30.4 → 7.18.2**：修掉**確實適用**之 open redirect（`<Link>`／`useNavigate` 反斜線，CVE-2025-68470 bypass）；6.x 線無修復版。v7 之 `v7_startTransition`／`v7_relativeSplatPath` 由 future flag 轉為預設行為，經回歸驗證無破——**原始碼零改動**（16 個引用點皆使用經典 API）。
- **相依授權與漏洞豁免收斂為單一正本**（design ＜II.C.(B).1＞）：`react-leaflet` 之 `Hippocratic-2.1`（允許清單外）經裁定接受；`react-router` 其後兩則 RSC Mode CSRF advisory 判**不適用**——本方案為純 client SPA（`createRoot`，全庫無 SSR／RSC），不進 RSC mode。修該兩則須升 `react-router` v8，而 v8 peer 為 React ≥19.2.7、與 Shell 之 React 18 shared singleton 契約衝突，列後續增量。

## 1.1.0 — 2026-07-30

增量 #182（右欄兩固定分頁改為功能說明／操作說明，feat）：右欄分工軸由「內容 vs 播放形式」改為**當頁 vs 全站**同軸對舉。

- **[使用說明] → [功能說明]**：本頁 wi 由水平 chip 列（只顯碼）改**垂直列示、顯示碼與 wi 全名同列**；選定後列其步驟，點有錨步驟仍可單步 coach-mark 定位。
- **[展示導覽] → [操作說明]**：由「wi 錨首段兩層樹」改**程序面全層樹**（域／orgSop／teamSop／prsnSop／wi），點任一 wi 直接導入其所屬頁、不隨頁切換；per-viewer 不可達者灰化為講解型。
- **層級唯一正本改讀手冊 fragment `sop:` 欄**（manifest 新增 `sop` 欄位契約，前端只切分不推算）。舊制自 wi 錨首段倒推之路線已於 #183 刪三支函式，此為同源第四處、一併收斂。通用作業（`prsnSop#0.1`–`#0.7`）自成頂層節——其 `0` 與維保域之 `0` 僅編號空間相同、語意不同；缺層不補假節點，`sop:` 缺者掛「未分層」。
- **自動播放全套除役**：播放列／速度／子樹連走／收合時 mini 播放列／跨路由續傳狀態機（`tour.ts` 整組退場，僅 `waitForElement` 收斂至 `locate.ts`）。`driver.js` 相依**保留**——其另一用途為 [功能說明] 單步定位。
- **預期副作用（非退步）**：全層樹依 `sop:` 分層後，維保 wi（錨 `wi-3-*`、程序面實為 `orgSop#0.2`／`#0.3`）由原「系統維保」獨立節改掛域 0 之下；已立測試斷言固化，勿誤判為迴歸。
- 連動：`prsnSop#0.6` 改「開啟操作說明」、`#0.7` 改「查看本頁功能說明」；說明中心鈕改「開啟操作說明」；未存變更守衛清單以「操作說明樹點擊導入」取代「展示導覽進入」並廢除換步不攔特例；`spec#10` 刪「可自動播放連走」；手冊 fragments／快速入門／擷取腳本／六張示意圖同步。

**另修施工中發現之兩項既有缺陷**（非本增量造成；因擋住端對端測試而順修，詳見 #182 ＜III＞）：

- **[通用演習控制頁] 載入情境後整頁崩潰**：`/api/exercise/load` 回傳之一次性憑證帶 `role`（情境宣告之 display 字串），前端型別卻誤宣告為繼承 `ExerciseAccountRow`（`roleIds: string[]`），render 時 `a.roleIds.join()` 對 undefined 取值 → React 卸載整棵子樹。兩個把關同時失效：JSON 回應為 cast（tsc 照不到）、`fakeApi` fixture 照著錯誤的前端型別捏造（單元測試永遠走不到該路徑）。已修正型別、改雙來源安全取值，並新增 `Exercise.test.tsx` 載入／重產成功路徑回歸測試（fixture 一律依後端實際回應）。
- **左欄 L1 域按鈕未渲染 `domain#N` 顯示碼**：design ＜II.B.(B).3＞ 明訂三層皆帶顯示碼，實作只做了 L2／L3；驗它的端對端斷言長年被前一項崩潰擋在後面、從未真正執行，等於空掛。已補渲染並修正該斷言之讀取來源（L1 在 `cat-tabs`、L2／L3 在 `left-tree`）。
- 另修三條隨 wi 編號改版而失效的端對端斷言：單步定位改自手冊 manifest 取步驟索引，並涵蓋「無錨／有錨但元素不在場／有錨且在場」三種結果。

## 1.0.0 — 2026-07-29

增量 #183（純核心平台＋官方通用指管領域包，feat!／breaking）：收斂為純核心＋單一官方通用指管包。

- **移除官方領域包**：cyber／strategy／logistics 自本庫移除（產品收斂純核心；核心早已零領域包 #58）。
- **核心系統改名**：`sysCepheus`→`sysCore`（資料夾、chart `solcepheus-syscore-chart`、image `solcepheus-syscore-modcore`／`-modweb`、全庫引用）。
- **官方通用指管包**（`sysGenericC2`，發行 `syspackgeneric`／包 slug `generic`）：svc-kit 底座七法 compute（領域中性 rollup、整體取瓶頸非平均 spec#7）＋wui-kit WUI remote 四頁（態勢＝通用 COP）＋獨立 chart，接 umbrella（condition-gated、預設裝）。
- **核心資料模型**：`OperationalCycle`（開立/封存 session）→ `Project`（專案·CRUD·單位自管·作業期間降為欄位）；pack 呼叫契約 `ctx.projectId`。
- **權限項目（wi）改依畫面發放**（非依六步驟）：官方通用指管包補上域號（原缺→決策頁核定鈕恆關、專案面板不出現、頁面接不到手冊說明）；新增稽核查閱、授權變更核可、卸載核可等權限項目；維運 CLI 之數字碼除役、改以工作指導契約表達。
- **專案功能補齊**：作業期間成為專案欄位（起訖時程）、可多筆並行、支援刪除（掛有目標者擋刪）；決策頁面板由「單一開立／封存」改為**專案清單**（新增可填時程、逐筆封存、刪除二次確認）。
- **修**：前端專案 API 路徑錯誤（改名時漏改、所有請求恆 404，功能等同不存在）。
- **升級搬移**：既有資料庫之 `operational_cycles`→`projects` 與相依欄位改名補上冪等搬移（此前僅改新建 schema，舊庫升級後既有專案與其下六步驟物件視同消失）；專案新欄位以 `ADD COLUMN IF NOT EXISTS` 補齊。
- **示範與演習資料改領域中性**：唯一演習情境由資安主題改「上下級聯合演練」（原綁已移出之官方包、載入後跑成無殼）；示範單位／帳號／領域標籤去資安·戰略主題。
- **使用手冊對齊產品收斂**：＜I 快速入門＞＜II 產品概述＞＜IV 附錄＞ 原仍整章描述已移出本庫的官方資安／戰略綜管兩包（「官方兩包」「資安操作台」「NIST CSF」「戰略＋戰術兩層」）並與已改寫的 ＜III＞ 正面矛盾（連平台維運的域號都兩套）；改寫為「純核心＋官方通用指管包、上下兩級同型」單一敘事，並加機判擋已除役敘事再度出貨。
- **產品正式名定為「CEPHEUS 模組式管理平台」**：原顯示「CEPHEUS 災防管理系統」——災防已隨本增量退為**首要適用情境**、不再限定產品名（design 早已定名，程式與手冊未跟上）。此名見於登入頁、Top App Bar、說明中心、手冊封面與全部證據截圖。
- **dev 反代補上官方包**：`vite.config.ts` 仍只反代已移除的 cyber／strategy，**沒有 generic 一條**——dev 下 `remoteEntry.js` 落入 SPA fallback（回 index.html、狀態仍是 200，用 curl 查狀態碼查不出來），域頁永遠退成通用 fallback；開發與證據截圖看到的都不是實際出貨的畫面。
- **證據截圖全量重拍**：原 18 張全是改版前的產品（戰略決策頁、資安素材頁），且擷取腳本本身仍指著已移除的域與早已不存在的示範帳號——腳本壞著則截圖永遠修不回來。腳本改走 `/d/generic/*` 與現行帳號，決策頁定裝圖改以**指揮官**身分拍（核定鈕依 wi 閘控，參謀拍不到）。
- **值勤交接**：決策頁新增交接面板（輪替時點交在途目標與未決方案、填註記，摘要寫入稽核鏈），依 `wi-1-3-6` 閘控。
- **兩面正名**：流程面→**程序面**、操作面／功能面→**人機面**（`Sop`＝標準作業程序、`Hmi`＝人機介面）；骨幹顯示碼廢縮寫，一律用完整節點名（`orgSop#N`／`teamSop#N.M`／`prsnSop#N.M.K`／`domain#N`／`modHmi#N.M.K`）。本版把改名**落實到示意圖、使用手冊與程式**（前版僅設計文件與顯示碼實作到位）——含說明中心與左欄 tooltip 之詞彙對照（原仍在解釋畫面上已不存在的 `os-`／`fc-` 等碼）。
- **掛載契約進位至 v2**：host 形狀二度變更（移除 `role` 改 `wis`／`canApprove`、新增 `projectId`）。此契約在碼中有四份副本且刻意不跨套件 import，`tsc` 抓不到單邊漂移，故新增 `testScripts/packContractLint.ps1` 機判版號與欄位集一致；素材頁 WUI 欄增「契約版本不符」態（此前版本不符只會靜默退通用頁、畫面無任何告警）。
- **修**：官方包自述 manifest 與 dev 種子之 `contractVersion` 漏隨進位（→整包判不相容、域頁靜默退通用頁）；上送態勢分誤把 0–100 值再乘 100（→逾值域被靜默丟成 `null`、上級彙整恆 0）。
- **breaking／升級注意**：核心 chart 更名（舊 `solcepheus-syscepheus-chart` 凍結於 GHCR、不再更新）；資料表 `operational_cycles`→`projects`（FK `cycle_id`→`project_id`）；umbrella 子 chart 由 cyber/strategy 改 generic；**領域包須同步升至掛載契約 v2**（v1 包將判不相容並降級為通用頁）；**態勢分值域統一為 0–100**——舊官方包（已移出本庫）之 evaluator 約定為 0–1，既有部署升級後那批歷史研判紀錄會顯示為「整體 0/100」（僅影響歷史顯示，不影響新資料與追溯鏈）。

## 0.32.0 — 2026-07-22

增量 #140（SOP 顯示碼深化，feat）：#128 顯示碼三項打磨。

- **單 wi 頁顯碼**：使用說明分頁於單 wi 頁（多數頁）標題旁顯一枚不可點 wi 顯示碼 token（原僅多 wi 頁出 chip 選擇列）。
- **tooltip 統一**：左欄 funcCat／funcPage 顯示碼 token 由原生 `title` 屬性改 MUI `Tooltip`（原生 `title` 觸控裝置不出、鍵盤不可及）。
- **fallback label**：`tourTree` 無包環境 section fallback label 由舊全名格式 `orgSop#N` 改中性字（`領域 N`），不與新 `os-N` 顯示碼混示同列。

純前端呈現；design 既有「顯示碼」契約（token tooltip／使用說明 chip）已涵蓋，無 design 變更。附帶修正 #139 遺留之 `HomeRedirect.test` getMe 型別（缺 `visibleUnits`；vitest esbuild 不型檢、`tsc` build 才顯）。

## 0.31.1 — 2026-07-22

增量 #139（認證授權 UX 收尾，fix）：v0.26.0 發車評審點名之認證授權缺口。

- **① 越權被拒留稽核**：`requireWi`（device／缺 wi 兩路）與 auth 中介 device 白名單越界三處 403 原未稽核（`README`／design 之「越權留稽核」未兌現）——三處皆 best-effort `appendAudit({action:"denied", method, path, reason, need})`（稽核失敗不改變 403；`denied` 既排除 WS 廣播）。
- **② 未知路由不掉登入頁**：catch-all `*` 原一律導 `/login`（已登入者 token 未失效卻見登入表單）——改用 `HomeRedirect`（已登入→角色首頁、未登入→`/login`）。
- **③ 他域頁籤過濾（判定已被取代、不作抵觸性硬過濾）**：funcDomain 可見性本即 **wi 驅動**（硬規則③、design ＜III.B.(A)＞），#163 內建 lead/member **刻意跨域零鎖死**、域專屬細分由**維保自訂角色**達成（後續）；`領域服務` chip 已由 `DomainPageHost` 依路由域解析。硬 `unitDomain` 過濾將違反硬規則③故不採（決策留痕於 #139／#168）。

## 0.31.0 — 2026-07-22

增量 #151（可編輯表單未存變更離開守衛，feat）：管理網站常規缺項——`docs/design.md` 與 `modWeb` 皆無未存守衛（0 命中）。

- 新增共用 `components/UnsavedGuard`：`UnsavedGuardProvider`（layout route）＋`useUnsavedGuard(dirty)`＋`useGuardedNav`＋單一確認框。dirty 時 ①`beforeunload` 攔關分頁／重整；②殼層 `Layout` 導覽（左欄選單／帳號／說明／登出）改走 `guardedNav`，開「捨棄未儲存的變更？」〔捨棄並離開／留在本頁〕，捨棄才放行、留下停原頁。
- 套用：電視牆設定頁（標題）、帳號資訊頁（變更密碼欄）、單元編組頁（未送出之新增單位／座標／隸屬邊輸入——react-flow 為衍生視圖、即時提交、無待存拓樸緩衝）、決策頁（新目標草稿）。
- `design.md` ＜III.C.(C)＞ 新增「未存變更離開守衛（全操作台通用）」規格。**MVP 界定**：瀏覽器上一頁／下一頁（popstate，需遷 react-router 資料路由＋`useBlocker`）與 401 強制導出不攔、列後續增量。純前端、未遷路由架構（不動 `main.tsx`／測試 `renderUtil`）。

## 0.30.1 — 2026-07-22

增量 #150（指揮管制最低能力三缺口收斂，fix）：[sysTechType指管] 最低能力清單三缺口逐項明落點，杜絕「留白」與「過度擴張」並存之含混。

- **態勢可存取性（gap#3，已修）**：態勢嚴重度改**色＋形＋文三重編碼**——除三級燈號色外並帶形狀（`●` 可用／`▲` 注意／`■` 危急）與文字標籤，**不以顏色為唯一資訊管道**（WCAG 2.1 §1.4.1 Use of Color〔Level A〕，紅綠色覺障礙者與投影牆／小螢幕皆可區分嚴重度）；集中定義於 modWeb `components/severity.ts`（`statusColor`／`statusSymbol`／`statusLabel` 同一分界），套用台灣地圖標記常駐 tooltip 與圖例、情態牆無座標單位 chip。
- **圖層面板（gap#3）／告警受理狀態機（gap#1）／時間軸回放（gap#2）**：明列**後續增量**並記錄 MVP 範圍界定與理由——固定圖層已達「無地圖不算 COP」最低能力、分級呈現與跨域告警可視（情資頁 DataGrid＋情態牆燈號）已具、`SituationSnapshot` 不可覆寫之追溯鏈已具回放資料基礎；獨立告警受理佇列（acknowledge／assign／close＋wi）、多圖層可視控制、時點查詢（`GET /api/snapshots?at=`）與 COP 回放 UI 屬增益、非最低能力門檻。design ＜III.B.(A)＞ 補「指管最低能力落點與 MVP 範圍界定」、＜III.C.(A)＞ 燈號規格補三重編碼。

## 0.30.0 — 2026-07-22

增量 #153（決策/態勢頁承接證據與信心，feat）：spec#3「評量分數可空、須附證據/信心/解釋」原有契約與 `EvaluationResult` 欄位，但決策頁評量跑了不顯示、態勢頁無信心/證據——退回「工單狀態機而非決策支援」。新增共用 `EvaluationResultPanel`（分數可空/等級/信心％/證據來源可展開逐項摘要/解釋/版本·時間）；genericPages 決策頁評量後即呈現、可檢視各目標評量結果，態勢頁研判列補信心％與證據來源快照。design ＜III.C.(C)＞ 決策/態勢頁版型欄補評量結果面板規格。純前端呈現＋設計，複用既有端點與型。

## 0.29.0 — 2026-07-22

增量 #152（作業期間生命週期歸屬，feat）：`OperationalCycle`（`/api/cycles`）有核心物件與端點卻無角色/頁/wi 承接開立/封存——正線起點只能靠演習載入或直呼 API。補齊歸屬：各域決策頁新增值勤 wi `wi-{N}-3-2`（開立/封存作業期間），`POST`/`PATCH /api/cycles` 由 `requireWi` 閘控（值勤動作——組員/組長/維保超管持有；`GET` 依可視範圍不變），內建角色 role_wi 對齊；modWeb 決策頁宿主新增作業期間面板（開立/封存，`useCan` 依 wi 元件層閘控）。演習載入建 cycle 之路徑不動。design.md ＜III.B.(A)/III.C.(C)＞ 補歸屬與頁表 wi。

## 0.28.0 — 2026-07-21

增量 #163（RBAC 權限模型，feat；流程面增量之核心）：把 3-role enum（adm/lead/member）硬切換為以 wi 為最小單位之 RBAC——prsnSop＝角色群組、wi＝權限項目、帳號指派至角色，funcPage 可見性與元件閘控皆由有效 wi 集算出，無角色名硬編（硬規則③）。新增 roles/role_wi/user_roles 三表（與 grants 可視單位正交）、/api/roles CRUD 與帳號角色指派端點、帳號授權頁之角色管理 UI。**硬切換遷移**：drop users.role，以 IF EXISTS 守衛之冪等區塊把現有 adm/lead/member 對映到通用內建角色（維保超管/指管組長/指管組員；跨域、零鎖死、免 binding 解析）後才 drop 欄，未對映即中止。JWT 不再帶 role、角色變更即時生效。包 WUI 核可鈕改依 canApprove（持本域核可 wi）而非 role。經子代理安全審查修掉「維保全看塌掉／開機掛掉／越權配 wi」等關鍵缺陷。wi 依權限粒度細切留後續。

## 0.27.0 — 2026-07-20

增量 #148（左欄導覽改操作面三層，feat）：導覽不再由 SOP 三層機械衍生，改為**操作面**自有三層——funcDomain（頂列圖示按鈕）／funcCat（功能列類別條目）／funcPage（功能列具體頁）；流程面（orgSopDomain／orgSop／teamSop／prsnSop／wi）移至右欄說明與 RBAC，兩面**交會於 wi**、各自維護不互相衍生。關係基數定案：funcPage↔wi 一對多且一 wi 只屬一 funcPage（wi 編號改依 funcPage 發放）、prsnSop↔wi 多對多。顯示碼左欄改 fd/fc/fp（自導覽樹位置產生，不再由 wi 錨倒推）、流程面 os/ts/ps 改僅現於右欄；**wi 錨字串一律不動**（語意由位置碼降為穩定 ID），對外手冊 deep link 零遷移。同批收斂原 L0 cat 與 L1 section 之 1:1 冗餘層，並補做 formatVersion 3.1→3.5 之技術選型四層遷移（techApp→sysTechType／techStack→modTechStack／techItem→cmpTechItem，contract-common 副本同步正本）。RBAC 權限模型、督核核可 wi 與 wi 依權限粒度重切歸後續「流程面」增量，design 已明文標示分界。

## 0.26.0 — 2026-07-18

增量 #136（左側導覽 SOP 顯示碼，feat）：左側功能導覽先前只有層級圖示、沒有編號。現在導覽的三層都帶上 SOP 顯示碼：

- **分區標頭** 顯 `os-3`（orgSop）、**群組** 顯 `ts-3.1`（teamSop）、**選單項** 顯 `ps-3.6.1`（prsnSop）——與圖示並存、名稱不變，滑鼠停留顯示該層全名。
- 這樣一來，四層縮寫 `os-`/`ts-`/`ps-`/`wi-` 在介面上都看得到了（先前只有 `os-`／`wi-` 出現在展示導覽的 SOP 樹）；不進展示導覽、日常在左側導覽也能直接讀到、引用每一項的編號。

## 0.25.1 — 2026-07-18

增量 #134（v0.25.0 發車評審回修，fix）：發車前 solution 級評審攔下兩件、回修後以本版發行：

- **使用手冊版釘與升級指令追平**：快速入門／安裝段的 helm 指令版釘（原停 0.1.5／0.22.1／0.2.0 世代）對齊本版 0.1.9／0.25.1／0.2.1；「執行修復」的升級指令改為官方 OCI chart 釘版寫法（原教僅開發者可用的原始碼路徑，客戶照做會失敗）；素材頁裝後確認措辭對齊「已登記未套用」；手冊操作章導言改指向說明中心的 SOP 層級詞彙對照。
- **維運視角正線界**：維運（adm）的單位清單預設不再包含演習子樹——先前演習載入後，維運視角的情態牆會把演習單位混入正線全國 COP。管理面需要全單位時（電視牆設定的投影單位挑選——投影演習單位本就須明示指定）以明示參數取用。

## 0.25.0 — 2026-07-18

增量 #128（SOP 層級縮寫前綴顯示碼，feat）：介面上的 SOP 編號不再只是裸數字——一律帶**層級縮寫前綴**，一看（一說）就知道指的是哪一層：

- **顯示碼**：`os-`＝組織層職責（orgSop）、`ts-`＝小組層作業（teamSop）、`ps-`＝個人層作業（prsnSop）、`wi-`＝工作指導（最小操作步驟）。SOP 樹的分節標頭顯 `os-3`、節點與右欄使用說明的編號顯 `wi-1.1.1`、演習情境卡的涵蓋範圍顯 `os-1`。
- **與手冊一一對應**：介面顯示碼與使用手冊段落編號同一套（`wi-1.1.1` 即手冊 `wi-1-1-1` 段），口頭溝通、搜尋、引用都能直接定位。
- **詞彙對照**：說明中心「使用說明」Tab 新增 SOP 層級詞彙對照；編號 token 滑鼠停留也會顯示層級全名。
- 左側導覽選單維持原樣（縮排與層級圖示已表達層次，不加前綴）。

## 0.24.0 — 2026-07-18

增量 #127（維保超級使用者，feat）：維保人員（adm）正式定位為**超級使用者**——左側導覽出現全部領域操作台頁籤，開任一域的情資／態勢／決策／管辦頁，**所見與該域指揮官一致**，供維保掌握全部軟體狀況、重現並排查各使用者回報的介面問題。

- **全看（正線界）**：維保可視範圍由「授權單位子樹」擴為全部正線單位；**演習子樹除外**——正線與演習隔離不變式優先，維保檢視演習仍用演習帳號。
- **域頁視角**：維保無所屬領域單位，開域頁自動以該域**最上位套用單位**視角呈現；該域尚未套用任何單位時顯示空狀態。
- **展示導覽**：SOP 樹上的域頁節點對維保由灰化講解型轉為可導航。
- **補償控管**：維保帳號具名不共用（手冊明訂）、寫入操作全程入不可竄改稽核紀錄。

## 0.23.0 — 2026-07-18

增量 #108（態勢研判放上真資料，feat）：域頁「產生態勢研判」原本吃前端內建的示範情資，現改為吃**下屬單位上送的真實態勢回報**匯總而成——每個下屬單位是地圖上一個來源節點，分數與座標取自其上送的報告與單位位置。

- **資安 COP**：下屬 CI 資產單位各自上送其 NIST CSF 控制成熟度與未修弱點，統管單位的地圖與雷達即由這些真實回報彙整；下屬僅上送整體分時，以該分數均攤各構面（降級呈現）。
- **戰略跨域 COP**：下屬各域上送的分數依其領域自動分組為跨域 Function，整體以瓶頸（最弱環節）呈現、非平均。
- **空狀態＝正確**：尚無下屬上送態勢時不再以示範資料填充，改提示「待下屬於管辦頁彙整回報上送後再產生研判」。
- **追溯**：每次研判快照記錄其來源回報，補全「情資→快照」追溯鏈。
- 示範編組與演習情境同步改為真實資料模型（下屬 CI 單位與各域回報預鋪）。

（完整的自訂 Function 拓樸、情資頁欄位補強等域頁深化項目列後續增量。）

## 0.22.1 — 2026-07-18

增量 #118（簽章與資料庫金鑰整治，fix；安全 hotfix）：修正正式部署的簽章金鑰預設值可被偽造 token 的問題——先前 `JWT_SECRET`／`DB_PASSWORD` 硬寫已知預設 `change-me-in-prod`，若運維未覆寫，任何人可用已知簽章金鑰偽造管理員或牆機裝置憑證、免登入取得權限（比公開密碼帳號更易利用）。

- **金鑰自動生成**：JWT 簽章金鑰與資料庫密碼於安裝時**自動生成強隨機值**（比照初始管理帳號密碼、`helm lookup` 冪等），正式部署不再殘留已知預設。要自管請明示；GitOps／CD 環境務必明示（否則每次同步換值）。
- **啟動防呆（fail-closed）**：非 Helm 部署（compose／手動）若 `JWT_SECRET` 仍為預設佔位值，核心在正式（pg）模式會拒絕啟動、不帶病上線。

**升級注意**（既有部署升級到本版、且原本用預設 `change-me-in-prod`）：

- **JWT 簽章金鑰會自動輪替為強隨機值**（修好既有部署的可偽造 token）——**既有登入 session 會失效需重新登入**，屬金鑰輪替之預期行為；已明示自管金鑰者不受影響。
- **資料庫密碼於升級時「保留既值」、不自動輪替**（僅首裝即強隨機）：postgres 資料目錄已以舊密初始化，於升級直接換密會令連線認證失敗；且該密碼僅供叢集內部（ClusterIP）postgres、非偽造 token 向量、風險較低。既有部署要輪替 db 密碼須另走 postgres `ALTER USER` 並同步 `--set secrets.dbPassword`（列後續強化）。

## 0.22.0 — 2026-07-18

增量 #52（modWeb 介面／UX 打磨二·#47 認證失效後續，feat）：情態牆失效時的體驗打磨。

- **失效不黑屏**：情態牆牆機在連線／憑證失效時，**保留最後已知的作戰圖**（地圖與各單位態勢），頂部顯示降級橫幅（標明失效原因與最後成功更新時間）並自動重試——指揮台不再瞬間全黑。
- **文案精準**：斷線與憑證失效分別提示；kiosk 失效副文不再誤寫「請重新登入」（牆機刻意不提供登入表單，改提示由維運遠端重新產生牆機連線）；失效副文對比提亮至 AAA。
- **逾時回原頁**：一般操作台登入逾時重登後，回到逾時前的工作頁（而非固定回首頁）。

（403 表單控件停用／空表格 empty-state 文案散落多頁，承接次批。）

## 0.21.0 — 2026-07-18

增量 #57（電視牆顯示端登入與 token 換發，feat）：解決情態牆牆機無人值守、token 失效後無人重登的問題。

- **牆機一鍵簽入**：維運在電視牆設定頁選投影單位、按「產生牆機連線」，得到一組一次性配對碼與連線網址；在牆機瀏覽器開啟該網址即完成簽入，取得**長效（30 天）唯讀裝置憑證**——之後不需有人到牆機前用帳號密碼登入。
- **失效遠端重發**：牆機憑證過期或系統換密鑰後，維運遠端重新產生配對碼即可，免到場。
- **最小權限**：裝置憑證唯讀、且只能存取所選單位的情態牆資料（不能操作系統、不能讀其他資料）；外洩也只能看該牆面。

## 0.20.0 — 2026-07-18

增量 #114（發車評審既有缺陷整治，feat）：把情態牆做成真正的作戰圖，並整治一批安裝／介面缺陷。

- **情態牆 `/wall` 真 COP**：從原本「只有卡片、沒有地圖」升級為**全國跨域共同作戰圖**——各管理單位依地理位置標在**台灣地圖**上，標記顏色即該單位最新回報的態勢分（綠可用／黃注意／紅危急），下級到上級的上送以連鎖線呈現；沒有座標的單位列在右側「最近上送」側欄。全程顯示**單位名稱**（不再是 `U-CYBER` 這種代號），並保留即時推播。單位座標可於單元編組頁設定（示範資料已內建三處）。
- **安裝可攜性（B3）**：README 安裝前環境檢查腳本新增 **kube-dns ClusterIP 偵測**——非 k3s 叢集（kubeadm／EKS／GKE 的 `10.96.0.10`）會提示對應的 `--set modWeb.dnsResolver=…`，避免領域包畫面因 DNS resolver 不符而載不出來。
- **釘版安裝（B6）**：README 三條 `helm install` 標上 `--version`，並新增 chart 版號與平台版對照表，安裝可重現。
- **介面打磨**：全域搜尋／告警鈴標示為「預留功能」並停用（不再看似可用）；領域包套用清單顯示單位名與包名稱（不再是代號）；同一單位重複套用同一包不再產生重複列；說明中心版面卡片化；登入頁顯示版本號（回報問題時可附）。

（裝後確認準則改字見 v0.19.x 之 #115。）

## 0.19.2 — 2026-07-18

增量 #113（正式部署之預設憑證整治，fix；hotfix 單獨發車）：修正「正式環境內建公開密碼帳號」與「自動登記憑證明文寫死」兩條互鎖的安全缺陷。

- **正式（pg）部署不再內建示範帳號與示範編組**：全新安裝為空編組、無任何 `demo1234` 帳號；本機開發（mem）模式之示範帳號不受影響（`SEED_DEMO` 可明示覆寫）。
- **首次登入改用初始管理帳號 `admin`**：密碼於安裝時自動隨機生成（或 `--set secrets.adminPassword` 明示），取回方式見裝後 NOTES；您改過的密碼不會被重啟還原。
- **umbrella 自動登記改用專用機器帳號**：隨機密碼存於 `<release>-registrar` Secret、不再落在 values 明文；該帳號由系統管理、介面與 API 均不可改密（防止「改密後升級中斷」）；GitOps／CD 環境請以 `registrar.existingSecret` 引用既有 Secret。舊 `registrar.username/password` 設定鍵已廢除——沿用舊 values 檔會於安裝時明確報錯提示。
- **升級注意（自 v0.19.1 含以前）**：資料庫內既有 `demo1234` 示範帳號不會被自動刪改，請登入後逐一改密（帳號停用功能列後續版本）；系統開機偵測到會於日誌（`[SECURITY]`）與稽核（`demoCredentialWarning`）告警。**回退注意**：rollback 至 v0.19.1 含以前版本時，舊版程式開機會無條件重種示範帳號——`demo1234`（含 adm 角色之 `ops`）將靜默回歸；回退後如再升回本版，請依偵測告警逐一改密。

## 0.19.1 — 2026-07-17

增量 #111（發車評審回修，fix）：v0.19.0 釋出前測攔下「多副本靜默漏事件」，修畢以本版發出（0.19.0 未 tag、由本版收束）。

- **chart 守衛**：`modCore.replicas > 1` 時 `helm install/upgrade` 直接失敗並說明原因——本版情態牆推播為行程內事件匯流排，多副本會靜默漏事件（畫面仍顯示「即時推播中」），故產品不支援之組態由 chart 拒絕，不只寫在文件裡。多副本擴充列後續版本。
- **新增安裝後提示（NOTES）**：`helm install` 完成即顯示對外入口實址、預設中性網域之覆寫提醒、裝後確認步驟（含「態勢頁應見台灣地圖 COP；未見請檢查 `modWeb.dnsResolver` 是否符合你的叢集 kube-dns」）與本版限制。
- **修正**：umbrella 自動登記時，領域包名稱顯示為「資安」而非「資安領域包」。

## 0.19.0 — 2026-07-17

增量 #53（WebSocket 推播·情態牆即時更新，feat）：情態牆改即時推播——下級回報上送後**數秒內**自動反映（標題下方顯示「即時推播中」）；推播中斷時自動退回每 5 秒更新並持續重連，憑證失效仍為就地全螢幕失效狀態（kiosk 不跳登入表單）。推播沿用與查詢相同的授權範圍（含演習隔離），不多推一筆。**部署注意**：modCore 請維持 `replicas: 1`（本版推播為行程內事件匯流排；多副本擴充列後續版本）。

## 0.18.0 — 2026-07-17

增量 #65（域頁 COP 地圖化與領域深化，feat）：資安態勢頁改**台灣地圖 COP**（多 CI 標記＋NIST CSF 燈號、點選看五構面細況；底圖隨包內建、零外部圖磚）＋KPI 條＋五功能分項圖表＋情資流；戰略態勢頁改**跨域 COP**（來源地圖分布＋來源→Function 連鎖圖、瓶頸輸入實線標示、貢獻來源明細）。快照實體項可帶 `loc: [lat, lng]` 座標（無座標降級清單呈現）。官方兩包版本 0.2.0（image／chart 同步）。

## 0.17.3 — 2026-07-17

增量 #97（chart 預設 host 去硬編網域，fix）：chart 預設值原硬編展測機網域、客戶自架照預設值不可用——改 host 預設鏈 `solcepheus.<baseDomain>`（`ingress.host` 留空供明示覆寫、`baseDomain` 預設中性值 `local`、host＝發行物名全名 `solcepheus`）；umbrella 同步（`--set solcepheus-syscepheus-chart.ingress.baseDomain=你的網域` 一處覆寫）；chartLint 增 host 機判（GATE 1.22 對齊）。**升級注意**：既有部署升級時請帶網域覆寫——umbrella 安裝者用 `--set solcepheus-syscepheus-chart.ingress.baseDomain=<你的網域>`、只裝核心者用 `--set ingress.baseDomain=<你的網域>`（鍵擺錯層值透不進去、host 會落回 `solcepheus.local`）；對外網址自 `cepheus.<網域>` 改為 `solcepheus.<網域>`（舊網址失效；要保留舊網址可改明示覆寫 `…ingress.host=cepheus.<網域>`，優先於預設鏈）。自架 cert-manager 模式下 host 變更會換 TLS secret 名並觸發重簽發（舊 secret 留孤兒可手動清）；邊緣側非萬用字元設定者（tunnel／DNS 逐名登記）須同步改 hostname。

## 0.17.2 — 2026-07-17

增量 #103（發車評審回修，fix）：v0.17.1 釋出前測（成品業界評審）攔下「文件宣稱與成品不符」，修畢以本版發出（0.17.1 未 tag、由本版收束）。

- README「介面右上角」段對齊實況：移除未實作的「單位切換」，全域搜尋／告警如實標「本版為預留介面」。
- 告警鈴移除恆亮的裝飾紅點（無告警來源前不掛紅點）。
- 手冊「本機試用」段與登入節標明**開發者路徑**（需原始碼）；一般使用者走 K8s／Helm 安裝。
- 三一致 `0.17.2`（modWeb fresh rebuild、modCore retag）。

## 0.17.1 — 2026-07-17

增量 #100（orgsopCat 一域一頁籤，fix；BatchFix #91 之④）：修正 0.17.0 左欄分類頁籤的切法。

- **一個業務域一個頁籤**：戰略統管、資安各自成頁籤（＋維保），不再合併成單一「指管」頁籤；日後掛新領域包即自動多一枚頁籤，同一頁籤下也預留擴充新作業域（如資安稽核）的結構。
- 頁籤圖示依域對照（資安→盾牌、戰略→地球、維保→扳手），未知域用通用圖示。
- 手冊截圖隨新頁籤全量重生；`VERSION`／chart／image tag 三一致 `0.17.1`（modWeb fresh rebuild、modCore retag）。

## 0.17.0 — 2026-07-17

增量 #95（三欄殼與作業脈絡輔助，feat；BatchFix #91 之③）：收服務門面客訴 svc#5「實際使用無從下手」——把說明與導覽從說明中心搬到手邊、左選單呈現作業層級語意。

- **左欄**：新增 orgsopCat **圖示頁籤**（機械導出：域 sections→指管、系統維保→維保）；SOP 樹各層加圖示（orgSop 建築物／teamSop 多人／prsnSop 單人）；可收合成 80dp 細軌（展開 280dp，錨 hmiIntf 管理網站契約）。
- **右欄（新）**：常駐可收合固定分頁、內容隨當前頁 prsnSop 切換——**使用說明**：條列本頁作業每一步（手冊 fragments 單一來源），點單步驟＝coach-mark 在畫面上**定位提示**該去哪、怎麼操作；**展示導覽**：原導覽面板能力原樣遷入（SOP 樹＋播放列，行為不減），入口雙軌（分頁常駐＋說明中心）。首次登入右欄預設展開至使用說明；左右欄收合狀態記 session。
- **手冊管線**：manifest 增列 per-wi `steps[]`（fragment 首個有序清單導出；選配步驟錨註記〔錨:…〕、呈現正文剝除、格式機判）；手冊截圖 27 張隨新殼全量重生。
- **測試**：modWeb 142 unit（涵蓋 95.3%／91.2%）；exercise.e2e 增三欄殼與單步定位正向斷言（證據 `18-shell-help-locate.png`）；uiPolish／authExpiry／packWui 回歸全過。
- **三一致**：`VERSION`／核心 chart＋appVersion／image tag → `0.17.0`（modWeb fresh rebuild〔碼＋手冊變〕、modCore retag）；umbrella appVersion＋dep→`0.17.0`、chart version 沿 `0.1.0`。

## 0.16.4 — 2026-07-17

增量 #92（產品頁客訴總檢整，docs；#93/#94 併入；BatchFix #91 之②）：收服務門面三張客訴——svc#2 產品頁混入開發資訊、svc#3 離線安裝宣稱不實、svc#4 文風生硬。病根＝門面 README 手工分岔、無受版控源頭。

- **README 重構為產品頁唯一源頭**：安裝段升級為客戶主動線——`helm install … oci://ghcr.io/twstellerwhale-ocean2/…` 直接自 GHCR 安裝、不需下載原始碼（`helm show chart` 讀回實證）；repo 內 chart 路徑降為開發者提示。
- **開發資訊出清（svc#2）**：發行名遷移段自 README 移除，升級注意事項一律指向 `CHANGELOG.md` 與各版 Release notes。
- **離線說法如實（svc#3）**：Release 附 chart `.tgz`（SHA256）可離線留存驗證；安裝拉 image 需對外網路、**不支援全離線安裝**；封閉網段以私有 registry ＋ `--set` 覆寫 image 位址。
- **文風與結構（svc#4）**：本機 mem 快速開始降為「本機快速開始（開發者）」章、安裝段加三步導言；dev repo 名 `solCepheus1`→`solCepheus` 更正。
- **手冊同步**：`01-quickstart`、`wi-3-5-1` 安裝指令改 OCI 主動線（原始碼路徑留開發者註）；三表面重建。
- **門面投影規則（防再犯）**：design ＜III.C.(D)＞ 補「門面 README＝根 README 逐字投影＋機械調整清單」；SKILL `2tech-trainFlow-3pub` facade 同步條增逐字投影規則＋「開發向資訊不得入門面」同步前掃描（命中回 dev 修、不得只改門面）。
- **三一致**：`VERSION`／核心 chart＋appVersion／image tag → `0.16.4`（發車時 modWeb fresh rebuild〔手冊變〕、modCore retag）；umbrella appVersion＋dep→`0.16.4`、chart version 沿 `0.1.0`。
- **gate**：GATE 1.21 README 機判／`helm lint`／`helm template`／`chartLint`／`docLint`／`repoLint` 0／乾淨脈絡手冊角度審查。

## 0.16.3 — 2026-07-17

增量 #90（README 補安裝前環境檢查腳本，docs；BatchFix #91 之①）：0.16.2 展測機客戶模擬實裝實撞「ingress-nginx 未標預設 IngressClass、`className` 留空成孤兒 Ingress 無聲 404」之**產品文件面收尾**（對齊範本庫 `comIntf通用K8sHelm部署格式` 檢核表#12／3code GATE 1.21 機判）。

- **README「K8s（Helm）」段新增裝前環境檢查內嵌段**：bash＋pwsh 雙版本（Windows PowerShell 5.1 與 PowerShell 7 皆可）、註明「整段複製執行」；判定四態——kubectl 連不上／無 IngressClass（含 port-forward 具體指令）／有預設（留空即可）／有 class 無預設（明示 `--set`），另涵蓋多預設邊角；umbrella 與核心 chart 兩安裝動線之 `--set` 鍵對應明講。
- **CREATE 蓋章附註**：預設 IngressClass 只在 Ingress 建立當下蓋章——改回留空須先 `kubectl delete ingress modweb` **再** `helm upgrade`（順序顛倒對外入口會斷到下次 upgrade）。
- 手冊 `wi-3-5-1` 補「裝前先跑 README 環境檢查」；手冊三表面重建。design ＜III.C.(D)＞ 補引檢核表#12。
- **三一致**：`VERSION`／核心 chart＋appVersion／modCore＋modWeb image tag → `0.16.3`（手冊 HTML 隨 modWeb bundle：發車時 modWeb 須 fresh rebuild、modCore＝retag）；umbrella appVersion＋dep 核心→`0.16.3`、chart version 沿 `0.1.0`；packs `0.1.0` 不動。
- **測試**：腳本照 README 原文逐字抽出實走——「連不上」態於真環境（bash／pwsh7／PS5.1）、其餘四態以 kubectl shim（三 shell 各四態）全過；本機叢集未起、「有預設」態之真叢集驗證留待發車環實裝（k3s traefik 出廠標預設）。
- **gate**：GATE 1.21 README 機判／`helm lint`／`helm template`／`chartLint`／`docLint`／`repoLint` 0。

## 0.16.2 — 2026-07-16

發車安檢修復（chore/release）：**0.16.1 發車時點 Trivy 掃描攔到 modWeb image 4 個 HIGH**——curl／libcurl `8.19.0-r0` 之 CVE-2026-5773／CVE-2026-6276（0.14.1 建置當時掃綠、其後新公告——「merge 時乾淨 ≠ 發車時乾淨」實證，GATE 擋下）。

- **修復**：modWeb image 改 **fresh rebuild**（runtime stage 既有 `apk upgrade` 即收 curl `8.20.0-r0`；**零程式碼／Dockerfile 改動**）；重建複掃 HIGH/CRITICAL＝0。
- **三一致**：`VERSION`／核心 chart＋appVersion／modCore＋modWeb image tag → `0.16.2`（modWeb＝fresh rebuild、modCore＝0.15.3 內容 retag[掃綠]）；umbrella appVersion→`0.16.2`、dep 核心→`0.16.2`、chart version 沿 `0.1.0`；packs `0.1.0` 不動（掃綠、retag 新名）。
- 0.16.0／0.16.1 皆**未發車、無 tag、非誤發**（發行名兩輪遷移之中間釘選版號），由本版收束一次發出。
- **gate**：Trivy（modWeb rebuild＝0、其餘 4 image＝0）／`helm lint` 5 chart／`helm template`／`chartLint`／`docLint`／`repoLint` 0。

## 0.16.1 — 2026-07-16

增量 #87（**發行名嚴格歸層補完**，fix，承 #85）：#85 遷移後仍有兩層殘缺——**「pack」不在 sol/sys/mod/cmp 結構詞彙**（`solcepheus-pack-*[-chart]` 為命名窮舉外的第四種形）、且**多 sys 化後 `solcepheus-mod{core,web}` 為 `sol-mod` 省略 sys 段**亦窮舉外；lint 只驗頭尾不驗中段而放行。USR 裁決：**包=sys 嚴格歸層＋全套、不留技術債**。

- **最終嚴格名**（0.16.0 未發車、零對外遷移成本）：

  | 顆粒 | 最終名 |
  |---|---|
  | umbrella chart | `solcepheus-chart`（不變） |
  | 核心 chart | `solcepheus-syscepheus-chart`（不變） |
  | modCore／modWeb image | `solcepheus-syscepheus-modcore`／`solcepheus-syscepheus-modweb` |
  | 包 image ×3 | `solcepheus-syspack{cyber,strategy,logistics}`（包＝獨立系統 `sysPack*`，`sol-sys`） |
  | 包 chart ×3 | `solcepheus-syspack{cyber,strategy,logistics}-chart` |

- **breaking（umbrella values 鍵，未發佈故無實際受影響者）**：`solcepheus-pack-*-chart.*` → `solcepheus-syspack*-chart.*`；核心鍵 `solcepheus-syscepheus-chart.*` 不變。
- **design**：新增「發行名宣告」段——明定本方案為**多 sys 方案**（`sysCepheus`＋`sysPackCyber`／`sysPackStrategy`／`sysPackLogistics`）、image 名首次正式入宣告（原僅宣告 chart 名，係宣告缺口）。
- **lint 根治（本次放行病根）**：`chartLint` 重寫＋`docLint` D08 補**中段嚴驗**——發行名須恰為 `sol`／`sol-sys`／`sol-sys-mod`（chart 加 `-chart`），sys/mod 段須對照 design 宣告詞彙；chartLint 附**負向自測**（`-SelfTest`：舊名 `pack-cyber`／`solcepheus-pack-cyber-chart`／`solcepheus-modcore` 等必 FAIL）。
- **舊名處置**：已發佈之 `solcepheus-pack-*`／`solcepheus-mod*` image 依「不刪已發佈」保留、發車時末版註記指向新名；#86 引入之 `solcepheus-pack-*-chart` 名從未發佈、無殘留。
- **三一致**：`VERSION`／核心 chart＋appVersion／mod image tag → `0.16.1`；packs chart 新名 version 沿 `0.1.0`；umbrella appVersion→`0.16.1`。
- **gate**：`helm lint` 5 chart／`helm template` 斷言／`chartLint`（含負向自測）／`docLint`／`repoLint`／`uiLint` 0；手冊三表面重建。

## 0.16.0 — 2026-07-16

增量 #85（**chart 發行名遷移 `-chart` 結構名**，feat）：對齊 trainFlow 命名紀律（發行名結構合規）——現行 chart 名為規則（2026-07-14）前已公開之存量名，USR 裁決全面遷移。

- **新名對照**（宣告即定案、design ＜III.C.(D)＞ 同步）：

  | 舊名 | 新名 |
  |---|---|
  | `sol-cepheus`（umbrella） | `solcepheus-chart` |
  | `sys-cepheus`（核心） | `solcepheus-syscepheus-chart` |
  | `pack-cyber`／`pack-strategy`／`pack-logistics` | `solcepheus-pack-{cyber,strategy,logistics}-chart` |

- **breaking（umbrella values 鍵）**：子 chart 值域鍵＝chart 名，隨遷移改——舊 `--set sys-cepheus.*`／`pack-cyber.*` 失效，改用 `--set solcepheus-syscepheus-chart.*`／`solcepheus-pack-cyber-chart.*`。
- **不變（零 runtime 影響）**：k8s 物件/Service 名（`pack-{slug}` 反代契約、`modweb`/`modcore` 等，template 寫死或吃 `.Values.slug`）、image 名（本已合結構）、npm 包名、包身分名；既有安裝以新 chart `helm upgrade` 可續（Helm 不鎖 chart 名）。
- **舊名處置**：GHCR 舊名 package 依「不刪已發佈」保留但不再更新；末版 Release notes 指向新名。
- **機判補齊**：`testScripts/docLint.ps1` 同步 canon D08（發行名結構）；新增 `testScripts/chartLint.ps1`（design 宣告名＝`Chart.yaml` name＋`-chart` 後綴結構）。
- **三一致**：`VERSION`／核心 chart＋appVersion／modCore＋modWeb image → `0.16.0`（內容不變 retag）；umbrella 與 packs chart 為**新名新譜系**、version 沿 `0.1.0` 首發（umbrella appVersion→`0.16.0`、dep 核心→`0.16.0`）。
- **gate**：`helm lint` 5 chart／`helm template`（新名、預設含 Ingress、umbrella 新鍵透傳）／`docLint`(D08)／`chartLint`／`repoLint`／`uiLint` 0；手冊三表面重建（wi-3-5-1 新鍵）。

## 0.15.3 — 2026-07-16

再跳號重發（chore/release）：**v0.15.2 亦誤發——與 v0.15.1 同一病根:主 checkout 卡在 `0.14.2`（工作區髒[改動 umbrella `charts/*.tgz` ＋殘留 `dist-*` 夾]擋住 `git pull`），發車腳本一直在 0.14.2 上跑;tag `v0.15.2` 掛 0.14.2、chart 為 `sys-cepheus-0.14.2`（無 Ingress），作廢。** 已清主 checkout（`reset --hard origin/main` ＋清 `dist-*` ＋刪本地歪 tag）根治病根;跳號 `0.15.3` 重發。

- **內容**（承 0.15.0–0.15.2，相對上個**有效**發佈 `v0.14.2`）：opt-in Ingress（甲案 host＝`cepheus.<baseDomain>`、`className` 留空用叢集預設 controller、TLS 兩模式）＋umbrella 透傳＋README。碼與 0.15.1/0.15.2 應含者相同,無功能改動。
- **發車腳本再加硬**：發車前驗**工作區乾淨**（`git status` 空）＋**無同名舊 tag**（除既有 `git fetch`＋`--ff-only` 快轉＋`HEAD==origin/main`＋`VERSION` 驗），任一不符即硬停（防「stale/dirty checkout 誤發」復發）。
- **三一致**：`VERSION`／`sys-cepheus` chart＋appVersion／modCore＋modWeb image → `0.15.3`（內容不變 retag）；umbrella appVersion→`0.15.3`、dep→`0.15.3`、chart version 沿用 `0.1.0`；packs `0.1.0` 不動。
- **gate**：`helm lint` 5 chart／`helm template`（預設含 `kind: Ingress` 無 `ingressClassName`）／`docLint`／`repoLint`／`uiLint` 0。

## 0.15.2 — 2026-07-16

跳號重發（chore/release）：**v0.15.1 首發誤在未 `git pull` 之 checkout（停在 `0.14.2`）上執行——tag `v0.15.1` 掛到 0.14.2 commit、Release chart 為 `sys-cepheus-0.14.2.tgz`（無 Ingress），該版作廢。** 依「tag 不可變」不刪不移，跳號 `0.15.2` 重發；碼與 0.15.1 應含者完全相同（#80 opt-in Ingress ＋ #82 className 預設留空），僅 `VERSION`／chart／image tag 三一致升 `0.15.2`。

- **內容**（承 0.15.0／0.15.1，相對上個**有效**發佈 `v0.14.2` 之新增）：opt-in Ingress（甲案 host＝`cepheus.<baseDomain>`、`className` 留空用叢集預設 controller、TLS 兩模式）＋umbrella 透傳＋README 安裝說明。
- **發車腳本 preflight 硬化**：`git fetch` ＋ 驗 `HEAD==origin/main` ＋ `VERSION==目標版`，checkout 未更新即硬停（防本次「stale checkout 誤發」復發）。
- **三一致**：`VERSION`／`sys-cepheus` chart＋appVersion／modCore＋modWeb image tag → `0.15.2`（內容不變 retag）；umbrella appVersion→`0.15.2`、dep→`0.15.2`、chart version 沿用 `0.1.0`；packs `0.1.0` 不動。
- **gate**：`helm lint` 5 chart；`helm template`（預設含 `kind: Ingress` 且無 `ingressClassName`／`enabled=false` 無 Ingress／TLS 兩模式）；`docLint`／`repoLint`／`uiLint` 0。

## 0.15.1 — 2026-07-16

增量 #80 後續（Ingress `className` 預設留空，fix）：**0.15.0 之 `values.ingress.className` 預設寫死 `nginx`——在只有 traefik 的叢集（如 k3s）裝上去、controller 不認即靜默不通（重蹈「裝起來外面連不到」），只有 nginx 叢集免 `--set`。改為預設留空、交由叢集自己的預設 ingress controller 接手。**

- **template**：`className` 留空 → 省略 `ingressClassName` → 由叢集預設 IngressClass 接手（k3s→traefik、ingress-nginx 叢集→nginx，**兩邊皆免 `--set`**）；要指定仍可 `--set ingress.className=nginx`／`traefik`。
- **values**：`ingress.className: nginx` → `""`（含註解說明）。
- **三一致**：`VERSION`／`sys-cepheus` chart＋appVersion／modCore＋modWeb image tag → `0.15.1`（內容不變 retag）；umbrella appVersion→`0.15.1`、dep `sys-cepheus`→`0.15.1`、chart version 沿用 `0.1.0`；packs `0.1.0` 不動。
- **design ＜III.C.(D)＞／README**：`className` 慣例改「留空＝叢集預設、可攜」（原寫死 nginx）。
- **gate**：`helm lint` 5 chart；`helm template` 斷言（預設 Ingress **無** `ingressClassName`＝走叢集預設／`--set className` 時有）；k3s 實裝經 traefik 預設、**免 `--set`** `curl` 200；docLint／repoLint／uiLint 0。

## 0.15.0 — 2026-07-15

增量 #80（modWeb 補 opt-in Ingress——掛既有 K8sGtw 對外，feat）：**chart 原只到 modWeb `ClusterIP`、`helm install` 裝完只在叢集內可達（只能 `kubectl port-forward`）——design ＜III＞ 部署圖有 Ingress 角色卻畫在 app release 外、chart 未附那張「門牌」。補 opt-in Ingress，讓 solCepheus 能掛既有邊緣（Cloudflare Tunnel／ingress-nginx／客戶自備閘道）對外可達。**

- **opt-in Ingress template**：新增 `sysCepheus/deploy/templates/ingress.yaml`（`{{- if .Values.ingress.enabled }}` 包裹）＋ `values.ingress` 區塊。預設 `enabled: true`（預設 `helm template` 即含 `kind: Ingress`，杜絕「裝起來外面連不到」）；純內網／`port-forward` 用設 `false` 回退 ClusterIP-only。
- **甲案（萬用邊緣＋共用網域值）**：`host: cepheus.<baseDomain>`，`baseDomain` 為叢集/umbrella **共用值**、部署者覆寫**真實對外 host**（不硬編他人網域、**不採「內部名＋邊緣改寫」**——避免綁改寫器/單一 vendor、對外交付客戶自架仍可用）；`className: nginx`。
- **TLS 兩模式（依邊緣）**：① 自架 ingress-nginx＋cert-manager → 設 `issuer`（chart 自動加 `cert-manager.io/cluster-issuer` annotation＋產 `tls` 區塊）；② 外部邊緣終結 TLS（如 Cloudflare Tunnel）→ `issuer` 留空、`tls: false`（TLS 在邊緣、Ingress 免 `tls`），**預設走外部邊緣**。
- **umbrella 透傳**：`deploy-umbrella` values 增 `sys-cepheus.ingress`（`enabled`／`host`），共用 host 一處設。
- **README**：K8s 段補安裝說明——`baseDomain` 來源、邊緣前置（Cloudflare Tunnel 萬用 `*.<baseDomain>` 設一次／或自架 ingress-nginx＋cert-manager）、TLS 落點、`enabled=false` 回退。
- **分層（核/殼）**：邊緣只收 `host → modweb:80`；app 內部 path 路由（`/api`→modcore、`/packs/{slug}`→pack、SPA）仍由 modWeb nginx 處理，Ingress `path: /` 全打到 modweb。
- **發車三一致**：`VERSION`／`sys-cepheus` chart＋appVersion／modCore＋modWeb image tag 隨升 `0.15.0`（modCore／modWeb 程式未變、內容同 `0.14.2` retag）；umbrella appVersion→`0.15.0`、dep `sys-cepheus`→`0.15.0`、umbrella chart version 沿用 `0.1.0`；packs `0.1.0` 不動。
- **design ＜III.C.(D)＞**：Ingress 角色從「假設外部提供」落成「chart 附 opt-in template」；補 `values.ingress` 慣例與邊緣/app 兩層分工；intTest 增 Ingress toggle 驗證。
- **gate**：`helm lint` 全 chart 0 fail；`helm template` 斷言（預設含 `kind: Ingress`＋host＋backend `modweb:80`／`enabled=false` 無 Ingress／TLS 兩模式各自正確）；docLint（3.1）／repoLint 0 違規。

## 0.14.2 — 2026-07-14

增量 #74（umbrella／核心／各包 chart image 參照補 GHCR 前綴，fix）：**自 GHCR 開箱一鍵安裝全數 `ImagePullBackOff`——五處 chart image 為裸名、被 k8s 補 `docker.io/library/` 誤拉；補 `ghcr.io/twstellerwhale-ocean2/` 前綴後開箱即拉**（facade `svcCepheus1#1` 回報）。

- **五處 image 值補完整 GHCR 路徑**：`sysCepheus/deploy/values.yaml`（modCore／modWeb）＋`packs/{cyber,strategy,logistics}/deploy/values.yaml`——`solcepheus-*` → `ghcr.io/twstellerwhale-ocean2/solcepheus-*`（對齊 design ＜III.C.(D)＞ 已明定發佈路徑、與同 org `solkidgalgame` chart 同慣例）；`postgres:16-alpine` 與 registrar 之 `curlimages/curl` 為 Docker Hub 官方 image、維持裸名不動。
- **發車版號對齊**：維持發車三一致（VERSION＝image tag＝chart version）——modCore／modWeb image 隨 VERSION 升 `0.14.2`（程式未變、內容同 `0.14.1` retag）、sys-cepheus chart `0.14.1`→`0.14.2`、umbrella appVersion→`0.14.2`；packs 獨立版 `0.1.0` 內容不變不動；umbrella chart version 沿用 `0.1.0`（republish 覆寫 DOA、facade `--version 0.1.0` 不變）。行為零變更（無 src／依賴／UI 改動）。
- **根因與 GATE 盲點**：裸名於**本機建置**安裝時就近命中本機 image（cri-dockerd 免 import），歷輪 intTest 本機路徑遮蔽此缺陷；缺陷僅於自 registry 拉取（facade 路徑）外顯——屬 impl↔design 漂移。
- **design ＜III.C.(D)＞**：補「各子 chart image 參照須帶 GHCR 完整路徑、不得裸名」條；README 快速開始補離線（本機自建 image）`--set` 覆寫說明。
- **gate**：`helm lint` 5 chart 0 fail；`helm template` 斷言 umbrella 渲染之每一 `solcepheus` image 皆帶 GHCR 前綴、無裸名殘留；docLint（3.1）／repoLint 0 違規。

## 0.14.1 — 2026-07-10

增量 #68（發車安檢修復：容器 runtime 基底強化，fix）：**v0.14.0 發車被成品漏洞掃描（Trivy）攔下——9 個 High 全屬基底 image 附帶物，runtime image 最小化後歸零**。

- **五個 Dockerfile runtime stage 同型強化**：node 系（modCore／pack-cyber／pack-strategy／pack-logistics）移除 npm CLI（含 corepack/npx；執行僅需 `node`，undici/picomatch/sigstore 之 High 全歸於 npm 內建依賴）；全部五顆 runtime `apk upgrade` 收基底 OS 套件修復版（modWeb nginx 之 c-ares/libexpat）。行為零變更（應用碼與依賴不動、啟動命令原樣）。
- **design ＜III.C.(D)＞**：新增「runtime image 最小化（全部容器一體適用）」慣例——發佈列車以成品漏洞掃描（Trivy，High 以上擋發車）驗收。
- **CHANGELOG 投影修正**：#58 條目補回漏建的 `## 0.14.0` 段標（原誤併於 0.13.0 段）。
- **gate**：五 image 重建後 Trivy High/Critical 全歸零；以強化 image 重裝 umbrella（k3s）solution smoke 18/18 全綠；docLint/repoLint/uiLint 0 違規。

## 0.14.0 — 2026-07-10

增量 #58（領域包全外掛化，feat!）：**廢「內建參考包」——所有領域包一律獨立部署外掛（含官方資安／戰略綜管）、核心 release 零領域包、領域 WUI 隨包自帶**。

- **modCore**：七法呼叫收斂 **HTTP 單一路徑**（刪 `DomainRegistry`／`builtins`／`cyber`／`strategy` 行程內實作——核心 repo 真正零域碼）；舊登記無 `apiBaseUrl` 視同不可達（`NoShellError` 人工回退＋素材頁「待重新登記」）；新增 `GET /api/packages/{id}/health` 健康代理；一包一把 per-pack 密鑰（`PACK_SECRETS` JSON map、fallback `PACK_SECRET`）；演習情境改**引用已登記包**（不自造登記；未安裝→無殼降級展示、`requiredPacks` 入情境卡片）；mem 快速開始 `seedDevPacks` 預登記官方兩包（多行程動線）。
- **modWeb（Shell）**：領域 WUI 掛載契約——`__CEPHEUS_SHARED__`（react/react-dom singleton）＋remote 副作用註冊＋Shell 注入唯一 API 客戶端與單位 context（401/403/友善映射保證涵蓋 remote 頁）；remote 載入失敗／包無 WUI → **降級通用頁 fallback、不白屏**（頁頂標降級原因）；域導覽（L1 section／頁名／主色）由包 `manifest.wui` 驅動、**modWeb 零域組態**（刪 domainConfig 與域專屬頁）；nginx 動態反代 `/packs/{slug}/` → `pack-{slug}:8090`（FQDN＋Downward API namespace、envsubst `DNS_RESOLVER`）；素材頁新欄位（WUI 自帶／健康可達／待重新登記）。
- **packs/cyber、packs/strategy（官方外掛包）**：evaluator 自 modCore 遷出（行為不變、含 spec#7 非平均不變式）＋ `svc-kit` 容器底座（manifest／七法／密鑰／WUI 靜態資產）＋ `wui-kit` 四頁模板（自 modWeb 遷出）＋ vite IIFE remote build；各自 image／chart（Service 名 `pack-{slug}`、label `cepheus.pack/id`=slug——K8s label 限 ASCII、packageId 由 manifest 自述）。
- **deploy-umbrella（sol-cepheus）**：核心＋官方兩包子 chart 一鍵聚合安裝＋ registrar hook Job（冪等登記；選裝 umbrella＝人工信任決策、留稽核）。
- **gate**：modCore 106 unit（cov 97.3/84.2）＋modWeb 100 unit（cov 94.2/90.1）＋packs 12 unit 全綠；intTest#27（remote 渲染／HTTP 全鏈／中斷降級）Playwright 對運行中全端實測全過；intTest#20/#21/#23–25 回歸全過；intTest#28 umbrella 於 k3s 實裝驗證（登記／探索／健康／WUI 反代）；builds 綠。

## 0.13.0 — 2026-07-10

增量 #62（說明中心＋展示導覽重構，spec#10）：**說明入口內移＋導覽升級為產品能力**——`?` 不再彈新視窗、導覽沿 SOP 樹全功能可走且可自動播放、展示進入回歸一般帳號紀律。

- **說明中心**：Top App Bar `?` 改開站內 [通用說明頁]（顯示名「說明中心」、route `/help`、舊 `/about` 轉址不 404）；Tabs 使用說明／版本資訊／CHANGELOG／版權·授權（版本/改版/OSS 與手冊 manifest **同源代入**）；「開啟完整使用手冊」新視窗**深連結來源頁**（按 `?` 前所在頁之 wi 錨）；「啟動展示導覽」按鈕；帳號選單瘦身（移除「關於」）。
- **展示導覽重構（產品能力、不綁情境資產）**：右側可收合面板＝**SOP 樹**（orgSop→wi 全條列；樹與講解文字同源手冊 manifest 新增 `wis[{anchor,title,summary}]`、摘要 ≤120 字必填機判）；點 wi＝導航＋coach-mark、點上層節點＝連走 subtree（跨路由續傳）；播放列＝**自動播放**（基準秒×速度 0.5/1/2×）＋暫停/上一步/下一步/進度，**頁面互動即自動暫停**（高亮元素可實際操作）、收合轉右下迷你播放列；per-viewer 不可達節點（無權限頁/CLI/登入類）灰化**講解型**（不導航、僅面板講解）；導覽狀態於登出/401 導出清除。
- **免帳號機制廢除**：`/api/auth/demo-login`、`/api/exercise/entry`、`EXERCISE_DEMO_ENTRY` 與 demo 帳號旗標全拆；登入頁撤展示/手冊入口（`/manual/` 靜態資源仍免登入可達、僅撤入口）。**展示進入＝演習帳號一般登入**：載入情境時逐帳號**隨機密碼**（5-5 可讀字元集）一次性發放——演習控制頁「演習帳號發放」表（含複製；密碼僅載入當下顯示）、維保可重產（`POST /api/exercise/passwords`，舊密即失效、留稽核）；情境 JSON 無明文密碼、status 不含密碼。
- **手冊**：wi-0-5（說明中心）/wi-0-6（SOP 樹導覽＋自動播放）改寫、wi-0-7/wi-0-8 除役、wi-3-11-1 補帳號發放；manifest 增 `wis`。
- **gate**：modCore 112 unit（cov 94.9/81.6）＋modWeb 113 unit 全綠；intTest#22 PASS；intTest#23–26 Playwright 對運行中全端實測全過（帳號發放/隔離重置/SOP 樹導覽/自動播放/說明中心/負向 404，52 斷言）；tsc/vite/esbuild build 綠。

## 0.12.0 — 2026-07-10

增量 #48（內建演習/展示模式，spec#10）：**情境載入＋依 orgSop 展示導覽**——複雜 C2 功能可被展示、訓練、快速上手，且不污染正式運作。

- **modCore**：情境載入器（`scenarios/` 一情境一 JSON、**加情境不改碼**）——載入＝單一資料庫交易在專屬**演習根單位**下建立全鏈預鋪子樹（單位/帳號/授權/cycle/情資→快照→研判→目標→方案→決策→任務→回報），隔離**複用** `GRANTS`＋`UNIT_EDGES` 遞迴 CTE、不另設旁路；重置＝封存不刪（僅可附加不變式全程成立；演習帳號/token 一律 401 導出）；免帳號 `demo-login`（`EXERCISE_DEMO_ENTRY` 開關、預設關、未備妥友善拒絕）；`GET /api/exercise/status` **per-viewer**（正線使用者永不顯示演習標示）。
- **modWeb**：[通用演習控制頁]（情境卡片＋載入/重置維保閘控＋**依 orgSop 展示選單**）、演習色帶橫幅＋浮水印（〔結束〕人人可見、〔重置〕僅維保）、登入頁「展示模式進入」、driver.js coach-mark 導覽（sessionStorage **跨路由續傳**、wait-for-element 逾時跳步）、域頁操作單位解析（可視範圍內綁定該域之單位）。
- **情境資產**：內建「淨水廠 CI 攻擊跨域事故」（資安/戰略雙 orgSop 導覽各 6 步、CSF/Function 研判預鋪）。
- **手冊**：新增 wi-3-11-1（載入/重置演習情境）、wi-0-7（展示模式進入）、wi-0-8（啟動展示導覽）三節＋實測截圖；README 演習/展示模式章。
- **gate**：modCore 128 unit（cov 95.7/82.0）＋modWeb unit 全綠涵蓋 ≥80；intTest#22 PASS；intTest#23–25 Playwright 對運行中全端實測全過（載入/隔離/重置/展示進入/跨頁導覽，32 斷言）；tsc/vite/esbuild build 綠。

## 0.11.2 — 2026-07-10

增量 #59（modWeb 介面/UX 打磨·第二批 F3–F6，fix）：

- **F3 登入落點與麵包屑「首頁」同源收斂**：`nav.ts` `homeTarget` 升為**登入預設落點單一目標函式**（角色＋單位領域感知三態：已知→單位領域態勢頁；查無或失敗→fallback 戰略態勢頁；查詢中→等待）；auth 增單位領域 session 快取（unitId/role 為界查一次、失敗收斂不長存未知態）；`HomeRedirect` 與麵包屑首段改共用同一函式，消雙源漂移。design ＜II.C.(C)＞ 新增「登入預設落點／根轉導（單一來源）」條。
- **F4 動作鈕防折行**：theme `MuiButton` `whiteSpace: nowrap` 一處修（「訂定目標」「登錄情資」等不再折兩行）。
- **F5 管辦頁回報摘要化**：戰略/資安管辦頁執行回報欄改欄位/摘要文字（`summarizeReport`），禁直出原始 payload JSON；design 管辦頁規格同步補約束。
- **F6 編組頁深色主題**：react-flow `colorMode="dark"`，Controls 白底套暗色（對齊 [hmiIntf通用視覺規範]）。
- **gate**：modWeb unit 72 例（nav 三態 12＋auth unitDomain 4＋summarizeReport 4）；`uiPolish.e2e` 對運行中全端實測 F3–F6；build／audit／uiLint／repoLint／docLint 全綠。

## 0.11.1 — 2026-07-09

增量 #46（modWeb 介面/UX 打磨·F1 假麵包屑）：頁首麵包屑改**真導覽**。

- **modWeb**：新增 `src/nav.ts`（導覽樹單一來源＋`deriveBreadcrumb`），Layout 與 PageHeader 共用；麵包屑由導覽樹＋當前路由自動衍生——首段「首頁」**角色感知可點**（指管→戰略情資頁、維運→系統監控頁；當前頁即目標時退化不可點）、中段 L1 區段**分類標籤不可點**、末段 `aria-current="page"`；移除 11 頁手填 `module=`，根治「通用」命名前綴外溢導覽（[通用關於頁]／[通用帳號資訊頁] 縮為 `首頁 / 當前頁`）。
- **design**：＜II.C.(C)＞ 麵包屑條文重寫（真導覽規格；設計層乾淨脈絡審查三重大發現——跨域越權首頁、兄弟頁連結違 APG、L1 名多源——已回修）。
- **license 裁決**：`react-leaflet`（Hippocratic-2.1，既有地圖依賴）經 USR 接受豁免、後續增量沿用。
- **手冊**：wi-* 步驟截圖以 `capture.mjs` 對最終碼重生（帶新麵包屑）、手冊/PDF 重建。
- **gate**：modWeb unit 全綠（新增 `nav.test.ts` 8 例）；Playwright 對運行中全端 15 頁走測＋真點擊導覽驗證；build／audit／gitleaks／uiLint／repoLint／docLint 全綠。

## 0.11.0 — 2026-07-09

增量 #45（產品使用手冊 doc pipeline）：以 `docs/manual/` **wi fragments 為單一來源**，build 出三表面，README／完整手冊／app 內說明同源同厚。

- **doc pipeline**（`npm run build:manual[:pdf]`）：四部手冊大綱＋28 個 prsnSop/wi fragment（穩定錨 `wi-*`、每 wi 一實截圖、CLI wi 指令輸出代圖）→ ① styled HTML 手冊（完整 TOC，bundle 進 modWeb `/manual/` 免登入自 serve、air-gapped 可用）② PDF（含 outline，`docs/manual/solCepheus-manual.pdf`）③ VERSION／CHANGELOG／OSS 授權清單 build 同源代入（手冊附錄＋通用關於頁）。
- **modWeb 情境式使用說明**：各頁 Top App Bar `?` 新分頁 deep-link `/manual/index.html#<wi>`（active Tab 之 wi、無 Tab 錨第一條；dev 未 build feature-detect 隱藏）；[通用關於頁] 使用說明 Tab 改手冊入口、版權·授權 Tab 同源清單；登入頁加未登入可讀手冊連結；汰換薄摘要與孤兒 `src/help`。
- **README**：收斂為 design ＜I＞ 對應之 GitHub 入口（產品概述／角色表／使用者旅程 per-wi 實截圖／效益成效語氣＋醒目手冊連結）。
- **附帶清理**：design 章 I 正典化（初始需求→需求分析）＋本地 docLint 同步正本；contract-common 九檔同步範本庫（`techStackNodeSys` 錯名→`NodeSvr`）。
- **gate**：intTest#22（手冊三表面 13 判定）PASS；modWeb unit 全綠、涵蓋 ≥80；tsc／vite build 綠；docLint／repoLint／uiLint 0。

## 0.10.1 — 2026-07-09

增量 #47（modWeb 認證失效與 API 錯誤處理，fix）：token 失效不再把使用者卡在壞掉的「已登入」殼裡、原始 API JSON 不再直達使用者。

- **401 全域攔截**：`req`（唯一 fetch chokepoint）攔 401 → 全域清 session → 導 `/login`＋「登入逾時，請重新登入」info 提示；`/api/auth/login` 之 401（帳密錯）就地呈現、不觸發全域（防迴圈）。
- **開機驗 token**：App 啟動以 `GET /api/me` 驗 stored token——401 才清並導登入、網路瞬斷不誤登出；驗證成功回寫 user。
- **錯誤友善映射**：`ApiError` 帶結構化 `code`＋友善文案（原始 status/code/body 留瀏覽器 console 供維運排錯）；fetch 網路層失敗映射「無法連線伺服器」；**403 就地顯示「權限不足」、不清 session**（與 401 嚴格區分）。
- **/wall kiosk 例外**：認證失效就地全螢幕「連線／憑證失效」＋自動重試、不變成掛在牆上的登入表單（精確匹配、不誤納 `/wallconfig`）。
- **各頁錯誤呈現收斂**：useAsync 取數錯誤併入頁頂 Alert、不再被靜默吞掉。
- **測試**：新增 intTest#20/#21 之 Playwright e2e（`sysCepheus/tests/authExpiry.e2e.mjs`）＋README 證據截圖；modWeb 26 unit 綠。
- **附帶**：design ＜I＞ 章名正典化（初始需求→需求分析，main 既存 docLint C01 紅）、`testScripts/docLint.ps1` 同步正典。

## 0.10.0 — 2026-07-09

增量 #44（領域包外部化與可發現掛載）：領域包從 in-process 升為**可獨立部署之外部服務**，兌現 `spec#5`「裝全新包不動核心 release」＋新增 `spec#9`（探索登記）。

- **modCore**：`callDomain` 遠端分支——package 帶 `apiBaseUrl` → HTTP `POST /domain/{method}`（共享密鑰 bearer、逾時→`NoShellError` 人工回退）；內建參考包仍走行程內。新增 `GET /api/packages/discover`（可選探索：in-cluster 唯讀 list services＋取 `/domain/manifest`、**發現≠信任**人工確認、無叢集手動 fallback）。
- **pack-logistics**（新外部示範包）：後勤運輸七法 over HTTP＋`/domain/manifest`＋`/healthz`，`node:http` 零執行期相依，自帶 image／chart、掛 `cepheus.pack/id`。
- **modWeb**：[通用領域包素材頁] 加掃描探索／確認登記／外部包手動登記。
- **Helm**：核心探索 ServiceAccount＋RBAC（唯讀 list services）＋`PACK_SECRET`；外部包獨立 chart。
- **清 #29 遺留**：modCore 分支涵蓋率 70.33%→80.16%、design ＜I＞ 章名 docLint。

## 0.9.0 — 2026-07-09

增量 #29（管理基元＋領域包 大改）：核＋殼重建為領域無關 Holon API＋領域包，取代 OODA/Measure/三域。

- **modData CORE_OBJECTS**：management_units／unit_edges（隸屬有向圖·層級 derived）／users／grants／operational_cycles／objectives（issuer/receiver/owner/parent＋六關係）／objective_relations／evaluation_policies·results／intelligence_items／situation_snapshots／situation_assessments／action_plans／decision_orders／actors／tasks／task_assignments／execution_reports／domain_reports／packages／domain_bindings／audit_logs；**僅可附加觸發器**（稽核與追溯鏈物件）＋**遞迴 CTE 隔離**。
- **modCore（Holon API）**：六步驟指管循環；**同型 Objective 多視角**（issuer／receiver／owner，不複製、父子不成環）；**三型評量**（formula/rule/rubric/nl/mixed，質性 score 可空、存證據信心）；**領域包生命週期**（素材→載入→套用→卸載，純資料·無 DDL·無重部署）＋共通 Domain API＋**無殼人工回退**（spec#8）；**決策雙重輸出**（delegate→子 Objective＋單位／assign→Task＋Actor＋Assignment）→執行回報**上送 DomainReport**。
- **領域包**：資安（多 CI × NIST CSF、最弱 CI 瓶頸）、戰略綜管（以 Function 取瓶頸、**整體非平均下級分數**，spec#7）。
- **modWeb**：重接 Holon API——域頁（情資／態勢／決策／管辦，資安·戰略綜管，domainConfig 驅動）＋通用頁（系統監控／單元編組 react-flow／帳號授權／領域包素材／電視牆設定／帳號資訊·變更密碼／關於）＋情態牆。
- **gate**：modCore 66 unit＋8 pg 整合（對真 PostgreSQL 驗遞迴隔離＋append-only 觸發器）、modWeb 12 test，涵蓋 ≥80；tsc／esbuild bundle／vite build 綠。

## 0.8.0 — 2026-07-02

增量 #21（issue #21 落地遞迴管理單位理論，核心＋國家監控/資安/志工三域；模型 A：各域自己的具名頁）。

- **遞迴管理單位理論**（design ＜II.A/B＞ 成篇）：同型單位遞迴、目標下行→成效上行閉環；核維持領域無關 OODA 引擎。
- **指標正名 `目標`→`Measure`**：kind `MOE`（效果/父）/`MOP`（任務/子）、`parentId` 遞迴分解、`issuer/receiver` 跨機構分配；`current_value` 由領域 evaluator 產出，**核不加總**（baseline 達成率均值；國家整體監控域用瓶頸 min 語意）。成效上行雙層＝核 WI 狀態機 rollup ＋ 殼指標推導。
- **單位關係改 `UNIT_EDGES`** 結構通道（隸屬有向圖）：層級改 **derived**（不寫死）；隔離可視集沿 edges 遞迴展開。
- **單元編組頁改 `react-flow` node-graph**（#2.7.1）：拖拉節點連線建隸屬通道（擋成環）、選節點看/分配該單元 Measure。
- **三域殼＋各自 evaluator**：`national-monitor`（上層彙整、瓶頸 min）／`cyber`（幾何平均風險彙整）／`volunteer`（彙集到位率 Σ到位/Σ需求）各帶自己的 rollup evaluator，**核不代算**；`config` 預設掛三域。
- **模型 A（各域自己的具名頁；rework）**：design ＜I/II/III＞ 重出——4 orgSop（國家/資安/志工指管＋系統維保）、各域自己的 team／teamSop／prsnSop 三錨鏈與 **12 具名頁**（`資安情態頁 ≠ 志工情態頁 ≠ 國家整體監控情態頁`）；核底版 `[通用…頁]` **僅在未掛領域殼時作 fallback**（此時上下層 Measure 無法自動推導），硬共用＝技術債（明文寫入設計）。modWeb 前端新增 `domainConfig` 註冊表（＝殼 manifest 前端投影）驅動各域具名頁（`/d/:domain/{intel,situation,decide,act}`）＋`DomainMissing` fallback；Nav 改三域 L1 section＋系統維保。
- **API**：新增 `/api/measures`、`/api/edges`；`intTest#14`（國家 MOE 分解→跨機構分配資安/志工→回報→國家域 evaluator 瓶頸彙整）。
- **gate**：modCore 82 / modWeb 64 tests、cov≥80（modWeb branch 81%）、audit 0、repoLint／docLint(3.2)／uiLint PASS；3 域 COP 示意圖（國家藍/資安紅/志工綠）＋§5 18 頁活系統逐頁審（活系統驗遞迴 rollup：國家 MOE=40=min(資安40%,志工78%)）＋`docs/test-summary.pdf`（A5）。

## 0.7.3 — 2026-07-01

增量 #10（issue #8 lint 腳本資料夾正名為 testScripts）。

- **repo 結構**：`scripts/` 資料夾正名為 `testScripts/`（`git mv` 保留歷史），明示其為測試／lint 檢查腳本落點；`repoLint.ps1`／`uiLint.ps1` 用法註解同步更新為 `testScripts/`。SKELETON 標準樹未納此資料夾，`docs/design.md`／`README.md` 無引用、不受影響。
- **方法論側（上游前置）**：SKILL canon `2tech-devSet3-incr-2plan`／`incr-3code` 之 `scripts/`→`testScripts/` 正名先行（含 docLint／repoLint／uiLint 落點與 FORMAT／SKELETON／GATE／SKILL 命令引用），避免下次 lint 依 canon 漂回舊名。CHANGELOG 0.7.2 及更早歷史條目引用之 `scripts/` 為當時實況、不回改。
- **gate**：docLint／repoLint／uiLint **0 違規**；本增量無 `src` 變更，modWeb/modCore 建置與測試不受影響。

## 0.7.2 — 2026-07-01

增量 #9（issue #16 design 外層三章依 sol/sys/mod 高度正名）。

- **formatVersion 3.1 → 3.2**：一級章名依設計高度正名——**I 初始需求→方案設計（sol）／II 方案設計→系統設計（sys）／III 系統設計→模組設計（mod）**。僅章名變，內層四步結構／spec 鏈／SOP 三錨／techApp／(C) 三高度與示意圖一律不動；spec#／需求原地保留於章 I（現稱方案設計）之 ＜D.(A)＞。羅馬數字位置引用（＜II.C.(C)＞ 等）不受影響。
- **方法論側（kdbUserSkills PR #22）**：docLint 加版本感知 `3.2`、FORMAT §1/§3-5/§8/§9（新舊對照表＋理據）、SKELETON 升 3.2、SKILL/GLOSSARY 章名引用對齊；3.0/3.1 零影響。
- **本 repo**：`scripts/docLint.ps1` 同步 3.2；`docs/design.md` 三章名＋兩處「本層」自稱（II 系統設計可驗證／III 模組設計可驗證）＋附錄 formatVersion 註記對齊；`contract-common/techApp/techApp指管.md` 章名引用改位置式（版本安全）。CHANGELOG 3.0→3.1 歷史條目不回改。
- **gate**：docLint **3.2** PASS／repoLint／uiLint **0 違規**；本增量無 `src` 變更，modWeb/modCore 建置與測試不受影響。

## 0.7.1 — 2026-07-01

增量 #8（issue #15 design 三層附介面示意圖）。

- **方法論側（kdbUserSkills PR #21）**：`2tech-devSet3-incr-2plan` 新增**硬規則⑤**——人機介面三高度除文字規格外，plan 期以 Claude 視覺設計能力各附一張 640×480 示意圖；FORMAT §6 增「三層示意圖」段落、SKILL ＜III＞ 增產圖步驟、CHECKLIST 增驗收項。圖為設計期參考稿、非契約、以文字規格為準，docLint 不檢查（結構相容 3.0/3.1）。
- **design（本 repo）**：於 ＜I.C.(C)＞／＜II.C.(C)＞／＜III.C.(C)＞ 各嵌一張示意圖——**I=主題風格**（Dark Operation Center 定式外殼＋色彩 token 定本 `theme.ts`）、**II=版面設定**（導覽三層 orgSop→teamSop→prsnSop＋單頁骨架與動作歸位＋MD3 adaptive nav 選用）、**III=頁面設計**（代表頁 整體情態頁 COP 地圖中心：多圖層地圖＋環圈/長條/趨勢圖表＋策略分析決策卡＋活動流，達 [techApp指管] §B 介面 bar）。圖檔置 `docs/design-visual/`（SVG 原稿＋Chrome headless 渲染 640×480 PNG）。
- **README**：不改（產品手冊；示意圖屬內部設計參考稿、非產品截圖，已由既有 `docs/design.md` 指標涵蓋）。
- **gate**：docLint 3.1／repoLint／uiLint **0 違規**；本增量無 `src` 變更，modWeb/modCore 建置與測試不受影響（app 行為未變）。§5 鏡頭 C 逐張目視審通過（地圖即產品／高密度／圖表多樣／決策支援／資料自洽）。

## 0.7.0 — 2026-07-01

增量 #7（issue #14 頂欄帳號選單與使用說明頁，對齊業界操作框架）。

- **design（甲案 HMI canon refine，不新增 spec#、不動 ＜I＞）**：＜I.C.(C)＞外殼「使用者」展開為帳號選單（頭像→個人資訊／登出）、新增使用說明（`?`）、註明不設設定齒輪；＜II.C.(C)＞Top App Bar 組成更新、使用說明頁列為全域工具頁（不進 Drawer）、通則「設定類收右上」→「帳號/設定類收右上」；＜III.C.(C)＞頁清單新增 **[通用使用說明頁]**（全域 `/help`、內容取自 README 單一來源）。
- **modWeb 實作**：`Layout` 頂欄改帳號選單（MD3 Menu：單位／角色＋登出）、新增使用說明 `?` 入口、移除裸登出 icon；新增 `Help` 頁（`/help`）以 react-markdown＋remark-gfm 渲染 README（reading 版面＋目錄）。README 單一來源：build context 內投影副本 `src/help/readme.md`＋防漂移測試（`npm run sync:readme` 重生）。
- **gate**：modWeb **58 tests／build PASS**、docLint 3.1／repoLint／uiLint **0 違規**、§5 逐頁審**無務必要修**；出 `docs/test-summary.pdf`（A5）。

## 0.6.0 — 2026-06-30

增量 #6（techApp 三層技術選型契約落實 ＋ COP 拉到指管 bar）。

- **方法論側（kdbUserSkills，另 PR #18）**：技術選型「分三高度各為契約」原僅 techStack 落地；補齊 **techApp指管**（C2/COP 系統類型，收回 GATE §A 能力清單 ＋ 新增反「作業感」**指揮台介面 bar**：地圖即產品／高密度／圖表多樣／焦點版面／決策支援）與 **6 份 techItem 型態契約**（資料庫=PostgreSQL／網站平台=nginx／地圖=Leaflet／視覺化=recharts／即時推播=WebSocket／授權權杖=JWT）；`techCat` 全域更名 `techApp`；散落 canon（GATE §A／FORMAT §2.5／範例 inline）收回契約為單一 normative。
- **design 同步**：＜I.C.(A)＞ 宣告 `techApp＝[techApp指管]`、＜II/III.C.(A)＞ techItem 改引用契約；contract-common 納 techApp/techItem 副本。docLint 3.1 PASS。
- **modWeb COP 拉 bar（dogfood：skill 改好 → 驅動 repo 改好）**：依 techApp指管 §B——`MapCop` 加**多圖層開關**（依型別 情資／環境／資源／目標 可顯隱、顯示各層點數與「上圖／有座標」計數）；`CopCharts` 加**態勢物件組成環圈圖**（pie／donut，使圖表種類多樣、非單一長條）；情態頁策略分析**決策卡顯示理由／影響／急迫**（決策支援增益、非工單狀態機）。
- gate：modWeb **54 tests／build PASS**；docLint 3.1 PASS。

## 0.5.0 — 2026-06-29

增量 #5（design 升 formatVersion 3.1 ＋ 三層導覽 IA 實做）。

- **formatVersion 3.0 → 3.1**：一級收為三章（I 初始需求／II 方案設計／III 系統設計）、內層四步重組（B 運作想定／C 組態設定／D 規格效益），IV 成效驗收併入各層 D（需求層 I.D 回扣需求）；techCat=C2/COP 移入 ＜I.C.(A) 技術選型＞、各層補 (D) 軟硬項目／(A) 技術選型／(D) 部署做法。
- **導覽衍生規則（新；硬規則④）**：IA 三層直接由 SOP 三層機械衍生——**orgSop → Drawer 主項/section（L1）、teamSop → Drawer 次層群組（L2）、prsnSop → Drawer 第三層選單項/leaf（L3，執行 .1 各一項；督核 .2 同表單角色閘控、不另立）**；**leaf→頁多對一**：可重用同型頁者多 leaf 指同頁＋不同組態（不爆頁），唯獨特視圖才開專屬頁——同時達成反擁擠（複雜 teamSop 如滲透測試逐項拆）與反矯枉（可重用共頁）。對映 MD3 nav 巢狀三層。落入 ＜II.C.(C)＞，＜III.C.(C)＞ 頁表加導覽欄。
- **補缺口**：teamSop#2.9-安裝任務域（prsnSop#2.9.1 CLI 安裝、#2.9.2 監控頁督核）補入 ＜III.C.(C)＞ 頁表（先前漏列）。
- **modWeb 實做三層 Drawer 導覽**（impl）：左手列由 SOP 三層機械衍生——section（指管操作台／維運台）→ teamSop 群組 → prsnSop 選單項；整體代辦頁（#1.3.1 調度／#1.4.1 下達）、通用系統監控頁（#2.1.1–#2.4 維保 OODA）以**多 leaf 帶 `?wi=` 指同頁＋不同組態**（PageHeader 顯情境 chip），示範「同頁不同組態、不爆頁亦不擠頁」。
- **§5 收尾審查（鏡頭 A/B/C；Playwright 雙角色實截真實 UI）**：抓到並回修 1 **務必要修**——維運台未依角色閘控（指揮官 lead 誤入系統監控頁→`403 forbidden`、健康誤示異常）→ Layout 依 `user.role` 過濾 section（指管操作台＝member/lead、維運台＝adm）；後續辦理 8、可接受 4。報告見 `docs/test-summary.pdf`（A5、含修前/修後）。
- gate：modWeb **54 tests／build／uiLint PASS**（具名頁 8、route 18）；docLint 3.1 PASS（0 違規）。

## 0.4.0 — 2026-06-28

增量 #4（cut2）：兌現 design ＜III.B＞ 核心抽象——讓「領域無關微核」名實相符。

- **modCore 核心抽象**：統一核心物件 `CORE_OBJECTS{id,type,ownerUnit,domain,metadata JSONB,時戳}`（資源/目標/環境/情資/COP快照/決策/執行概況），以 `core_objects` 取代具體 `intel`；情資＝`type=情資` 物件之具名視圖。
- **OODA 處理器契約**：情資整理（objects→COP 快照）、策略分析（COP→決策）兩處理器；核底版通用實作（彙總/缺口清單），殼可於 `pack.processors.{orient,decide}` 宣告 **handler id** 客製（內建資安處理器：加權嚴重度→riskScore、高嚴重排前），掛殼即改變 COP、免改核（spec#4）。
- **新端點**：`/api/objects`（通用物件 CRUD）、`/api/cop`（情資整理 live 可重算）、`/api/decisions`（策略分析產決策核心物件，連回 COP快照）；全程隔離＋稽核＋事件。
- **modWeb 即時化**：`useEventStream` 接 `/ws?since=seq`（記 last_seq、event→重抓、斷線退避重連補抓）；情態牆顯示 LIVE/斷線；整體情態頁顯示 COP（處理器/重點/風險分）＋策略分析（產建議→採納為代辦）；token/user 持久化（刷新免重登）。
- **modData 驗證**：pgRepo 以 docker PostgreSQL 16 **實跑** `test:integration`（容量原子/稽核鏈/core_objects round-trip＋WS 續傳，4 pass）——兌現先前「已寫未驗」缺口。
- **GATE**：modCore 59 / modWeb 48 tests；coverage modCore 98.2/89.0/96.5/98.7、modWeb 90.2/82.4/85.9/91.0（≥80）；整合 4 pass（真 PG）；repoLint/docLint/uiLint PASS；方案層 smoke exit 0。

增量 #5（cut3 ＋ 補實國家級 C2/COP）：

- **領域擴充契約落實（cut3）**：殼＝原廠自帶 Helm chart（API server＋前端 bundle＋manifest），維保 `helm install` 後**自我報到** `POST /api/domains`，核免改版/重編即供應；處理器可跑於領域自有 API server（核遠端呼叫、驗信封、不可用 fallback）；intTest#13 證明執行期動態掛殼。design ＜II.B＞/＜III.A,B＞ 深化並理順。
- **地圖中心 COP（國家級）**：宣告 techCat=C2/COP，情態頁／情態牆由卡片數字重做為**地圖中心**（react-leaflet＋CartoDB 暗底圖、災點/資源/單元依嚴重度上色、作用中災情、告警）；治先前「情態牆＝3 數字配黑底」之 design≠impl。
- **資料視覺化**：recharts 災情嚴重度／代辦狀態長條圖。
- **維保 OODA 工作流**：監控頁加維保 WI（研判故障→維保排程→執行修復），補 teamSop#2.2/2.3。
- **/ws 連線鑑權**：握手以 verifyClient 驗 JWT、拒未授權升級（spec#1）。
- **§5 逐頁審＋修**：清登入示範憑證、監控健康改反映實況；逐頁審見 `docs/ui-review.md`。
- **GATE**：modCore 62 / modWeb 54 tests、coverage ≥80、build 0、docLint/uiLint/helm lint PASS、smoke exit 0；釋出前測試報告（含國家級截圖）重生於 `docs/test-summary.pdf`。
- **FORMAT 3.1 提案**：`docs/format-3.1-proposal.md`（techCat/techStack/techItem＋新四步），結構重排待 skill 更新後正式套用。

## 0.3.0 — 2026-06-27

增量 #3：design 收斂為 MVP（純核＋單一國家戰略單位），依深化後核/殼設計重建前端。

- **design**：核/殼角色深化（CORE＝領域無關 OODA 微核：統一物件＋階段編排＋領域處理器契約＋React 底版＋落帳；殼＝per-stage 外掛）；收斂為 MVP（清四域功能分析）；三層 (C) 人機介面改寫並引公開標準（MD3 / WCAG / WAI-ARIA APG）＋頁面命名紀律（領域+功能+頁、通用明寫、標 CORE 階段）。
- **modWeb 重建**：暗色 MD3 指揮台（Top App Bar＋左側分組 Navigation Drawer，錨 [hmiIntf通用視覺規範]）；8 具名頁——整體情報/情態/代辦/情態牆（操作台）＋系統監控/單元編成/帳號授權/電視牆設定（維運）；list-detail/supporting-pane/feed 版型；可重用 DataTable（搜尋/排序/分頁/多選）；督核同表單角色閘控、情態牆獨立 kiosk。
- **modCore**：seed/config 對齊 MVP——u-org 戰略中心＋u-ops 系統維保；預設不內建域（殼載入機制保留，可執行期掛載示範）。
- **contract-common**：techStack 統一收進 `techStack/` 子資料夾（正源 skill 一併對齊）。
- **GATE**：modCore 48 / modWeb 42 tests；coverage modCore 98.5/93.9/96.8/98.8、modWeb 89.9/84.7/85.4/90.5（≥80）；npm audit 0×2；repoLint/docLint/uiLint PASS；方案層 smoke exit 0。

## 0.2.0 — 2026-06-27

增量 #2：依 design formatVersion 3.0 整批重建（舊雛型與新設計落差過大，砍掉重練）。

- **指管核（modCore）**：WI 單圈狀態機（待辦→進行→待核→完成＋升級/退回）、原子容量落帳（`UPDATE…WHERE remaining>0` 單一得標、不超派）、GRANTS×UNITS 隔離可視集、AUDIT_LOGS 僅可附加（seq＋雜湊鏈）、JWT＋bcrypt、**督核角色閘控（approve/reject 限組長/管理員）＋自核防呆**。
- **殼載入契約＋四域**：宣告式 DomainPack（欄位 JSON-schema 子集／容量／去重）；內建 **資安／運輸／人資／情蒐**；`POST /api/domains` 執行期掛新域，免改版/redeploy/DDL。
- **modWeb 四 surface**：情態牆（獨立 COP）、指管操作台（態勢/情資/代辦/督核/殼 UI 包）、維運台（監控＋稽核）、行動現場台（mobile）；MUI 固定版面。
- **落帳與續傳**：modData PostgreSQL schema＋pgRepo（REPO=pg）；comIntf WebSocket 續傳（seq 回放／缺口快照／heartbeat）。
- **交付**：Helm chart `sys-cepheus`（modWeb/modCore/modData）。
- 領域調整：志工域 → 人資域；新增情蒐域。

## 0.1.0 — 2026-06-25

增量 #1（issue #1）：建立通用 OODA 指管平台雛型。

- **指管核**：OODA 四階段（Observe 情資/上層目標 → Orient 情態牆/彙整 → Decide 調度分配 → Act 執行/升級上送）；單圈代辦（`WORK_ITEMS.origin`）。
- **落帳基底**：PostgreSQL 委派；容量受限分配以原子 `UPDATE…WHERE filled<capacity` 達成併發 single-winner（滿額自動關閉），CHECK 約束第二道防線。
- **共用運作基礎**：JWT 授權與資料隔離（未授權 403、可稽核）、指管單元三軸編成（任務域×層級×編成）、情態牆彙整、append-only 稽核。
- **領域殼 plugin**：資安（防護偵測/應變）、志工（需求核實去重 skill、工單滿額容量原語）、運輸（載人/載貨容量原語，擴充驗收）；新域僅靠組態掛載、不改核；離線同步冪等。
- **交付**：modWeb（React+MUI 6 頁）、modCore（Node/TS REST）、modData（PostgreSQL）；Docker images；Helm chart `sysCepheus/deploy`。
