# solCepheus — 可組合跨域指揮管理平台

**管理基元（Holon）＋領域包**架構的指揮管理（C2）平台：領域無關的六步驟指管核心（情資→態勢→目標→方案→決策→執行），專業能力以**一律外掛**的領域包提供——裝新領域包、單獨升級任一包，都不需要改版或重新部署核心。

> 本 repo 為 solCepheus 產品門面：提供**部署包與使用手冊**；原始碼留私有 dev repo。

## 架構特點

- **核心零域碼**：管理核心（modCore Holon API＋modWeb 管理台）不含任何領域邏輯；領域包（官方：資安、戰略綜管；示範：後勤運輸）各自獨立 image／chart 部署，經 HTTP 契約與核心互動。
- **領域 WUI 隨包自帶**：各包自帶操作介面（Micro-Frontend remote），執行期掛入管理台；包不可達時降級通用頁、不白屏。
- **不可覆寫追溯鏈**：核心資料僅可附加（append-only）、全程稽核；單位樹遞迴隔離授權。
- **同型 Objective 多視角**：上下級單位對同一目標各自視角呈現，目標下行、成效上行。
- **內建演習/展示模式**：情境載入（加情境不改碼）、SOP 樹導覽自動播放、重置封存不刪。

## 安裝（Kubernetes / Helm）

一鍵安裝核心＋官方兩包（umbrella chart，含自動登記）：

```bash
helm install cepheus oci://ghcr.io/twstellerwhale-ocean2/sol-cepheus --version 0.1.0
```

或核心與各領域包分開安裝（各自獨立 release、可單獨升級）：

```bash
helm install cepheus-core oci://ghcr.io/twstellerwhale-ocean2/sys-cepheus --version 0.14.1
helm install pack-cyber   oci://ghcr.io/twstellerwhale-ocean2/pack-cyber --version 0.1.0
helm install pack-strategy oci://ghcr.io/twstellerwhale-ocean2/pack-strategy --version 0.1.0
```

Container images：`ghcr.io/twstellerwhale-ocean2/solcepheus-modcore:0.14.1`、`…/solcepheus-modweb:0.14.1`、`…/solcepheus-pack-{cyber,strategy,logistics}:0.1.0`。

亦可自 [Releases](../../releases) 下載 chart `.tgz` 與裸網站包離線安裝（資產附 SHA256 供驗證）。

## 使用手冊

- **[solCepheus 使用手冊（PDF）](docs/solCepheus-manual.pdf)** — 四部：快速入門／產品概述與設計依據／功能與操作說明／附錄。
- 部署後系統內建同一手冊（`/manual/`，免登入、air-gapped 可用），各頁 `?` 直達對應章節；並有 SOP 樹展示導覽（自動播放）。

## 版本

| 版本 | 日期 | 摘要 |
|---|---|---|
| v0.14.1 | 2026-07-10 | 管理基元＋領域包全外掛新體系首班車（0.9.0–0.14.1 共 10 增量；含 runtime image 安檢強化） |
| v0.8.0 | 2026-07-02 | 首發：通用 OODA 指管平台（遞迴管理單位，一核＋三域） |

完整沿革見各 [Release](../../releases) notes。

## 授權與第三方元件

產品內「說明中心 → 版權·授權」與手冊附錄載明所用開源元件（19 件）之授權清單。
