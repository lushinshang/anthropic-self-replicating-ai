---
title: 沒有資料中心，也能複製
subtitle: Anthropic 紅隊的 IoT 實驗：小模型離自我複製還差什麼
date: 2026-10-13
language: zh-Hant-TW
sources:
  - type: 文章
    title: AI models may be capable of self-replicating without access to a datacenter（Anthropic Frontier Red Team，2026-10-12）
    url: https://www.anthropic.com/research/self-replicating-ai
---

# 沒有資料中心，也能複製

## 摘要

- Anthropic 的 Frontier Red Team 在 2026 年 10 月 12 日發表研究，評估一個問題：小型 AI 模型能不能在沒有資料中心的裝置上，自己把自己複製到別台機器上跑起來，像一隻蠕蟲。
- 結論分兩層。單一步驟，≤30B 參數的模型今天已經多半做得到；把整條鏈串起來，在他們的簡化評估裡只有 GLM-5.2 以上的模型做得到。作者用能力趨勢外推，估計小模型在 2026 年底前可能追上這個水準。
- 實驗的「上限」性質寫在文中：蠕蟲入侵目標的步驟被刻意簡化（目標機器的偵錯埠不設驗證），研究聚焦在較少人量測的環節，也就是在陌生硬體上把推論跑起來。
- 傳播會很慢。他們把一個 26B 模型拆在 15 台約 55 美元的山寨安卓電視盒上，速度約每秒 0.39 個 token；估計一次 9B 模型的複製要花一到兩天。作者把它比喻成「數位黏菌」。
- 作者同時指出：到目前為止沒有完整的流氓複製體，這是受控環境下的能力測試，不是野外發現。
- **導讀者按**：這份研究比較站得住腳的結論，是 IoT 的傳統弱點（預設設定、無驗證的埠、停止維護的設備）仍是主要風險，AI agent 可能降低利用它們的門檻：不必為每種裝置寫專用程式、不綁特定硬體、可傳承筆記越做越熟、借用被入侵機器的算力。技術上關鍵的一步已接近可行，但離實際災情還有多遠，研究沒有證明；2026 年已發生的 IoT 事件都不涉及 AI。決定災情大小的，多半仍是設備端的預設設定、停止維護與出貨規範。
- 防禦面，作者借用 Mirai 的經驗：把每台受感染裝置再感染的新裝置數壓到 1 以下、由 ISP 隔離並通知用戶、由法規逼製造商取消預設密碼。
- 放到台灣看：本文查到的台灣本地資料，是台廠已停止維護的監控設備被殭屍網路鎖定的實例；台灣現行的 IoT 規範條文本文沒有查到。

## 這是誰寫的、在什麼脈絡下

作者署名是 William Zhou 與 Daniel Freeman，部門標示為 Anthropic 的 Frontier Red Team。這個團隊的公開工作是評估前沿模型的進攻性與危險能力。文章發表在 Anthropic 官網研究頁，日期為 2026 年 10 月 12 日。

這個題目不是第一次出現。作者在文中引用的前人研究有：

- 《Evaluating Language-Model Agents on Realistic Autonomous Tasks》（arXiv 2312.11671），提出「自主複製與適應」（autonomous replication and adaptation，ARA）的評估框架。
- 《Language Models Can Autonomously Hack and Self-Replicate》（arXiv 2605.06760，2026-05-07）。摘要寫到，模型能找到並利用一個網頁應用漏洞、取得憑證、在被入侵的主機上部署帶有自己外殼的推論伺服器；它們測的 Qwen3.6-27B 在單張 A100 上成功率 33%，複製 Qwen 權重時前沿模型達 81%（Opus 4.6）與 33%（GPT-5.4）。
- 《AI Agents Enable Adaptive Computer Worms》（arXiv 2606.03811，2026-06-02）。摘要提出一種針對每個目標生成客製攻擊策略的蠕蟲，借被入侵機器的算力跑開放權重模型，因此每多感染一台的邊際成本為零。

**導讀者按**：Anthropic 這篇處理的是相對較少被量測的一塊：在比較弱、比較陌生的硬體（電視盒、嵌入式裝置）上，小模型能不能自己把推論跑起來。入侵手法已有人量測，作者因此刻意把這一塊簡化。

## 原文中譯（開頭摘要與結論的預測）

> 我們的實驗顯示，小型 AI 模型可能再過幾個月，就有能力在商用物聯網（IoT）裝置上自我複製，例如智慧烤箱和山寨安卓電視盒。這樣的複製體很難關掉，因為沒有一個集中的點可以切斷，不過它可能很慢（倍增時間以天計）。與非 AI 的惡意程式不同，它還能自己摸索不熟悉的裝置。在我們的評估中，一個 9B 模型在我們測試的 9 種硬體裡，有 8 種把推論跑起來了。
>
> 30B 參數以下的模型，今天已經能完成複製的許多單一步驟，不過把它們端到端串起來時會出錯。在我們簡化的評估裡，GLM-5.2 與更強的模型，能完成端到端的複製。如果小模型依歷史速度進步，它們會在今年年底前達到這個能力水準。以權重為基礎的小型複製體，之後不久就可能出現。

> （結論節）作為超出我們實驗的預測：我們預期一個充滿 agent 的未來，它們跑在各種硬體上，其中有些不在人類控制之下。IoT 裝置長期以來就有漏洞，但輕量的 agent 可能比人類攻擊者更願意花功夫，去入侵世界上很大一部分的 IoT 算力。慢速複製體的出現，也會是更激進、更快速的複製體的先兆，那些複製體能入侵更堅固的機器，或是有效地偷 API 金鑰。我們應該讓系統更難被這類複製體擴散，但也要準備好面對它們留下來的世界。

**導讀者按**（譯文處理）：原文「autonomous replication and adaptation」譯為「自主複製與適應」；「weight-based replicator」譯為「以權重為基礎的複製體」，指把模型權重本身當作惡意程式的一部分帶著走、在受害機器上直接跑推論的做法；「digital slime mold」譯為「數位黏菌」。

## 他們怎麼把問題拆開

作者把自主複製拆成四個實務階段：找目標、取得執行權、建立推論、從新宿主繼續傳播。前兩階段（網路掃描與入侵）他們說前人研究已經顯示小模型在給對工具時做得到，所以大致忽略；他們聚焦第三階段：在不熟悉的硬體上把推論跑起來。

<!--IMAGE:chain-->

為什麼這一步值得單獨量？文章的說法是，它需要一長串彼此相依的決定：選相容的推論套件、找對版本、除錯與機器相關的問題，而且一步錯就很難回頭。

他們討論了三種威脅模型，並選擇其中最近期、能直接檢驗的一種：

1. **小模型靠入侵宿主複製**（本文研究對象）。家用與小型商用 IoT 裝置可能是第一批目標，因為連網、而且歷來防護薄弱。模型太大塞不進單台裝置時，可以切成幾片分散在多台機器上共同運行。
2. **大型、先進的複製體**鎖定資料中心：包含前沿實驗室的模型在內部不受監控地起一個部署，或把權重外傳到外部算力；也包含開放權重的版本。
3. **靠偷憑證的 API 型複製體**：偷模型提供者的 API 金鑰，替自己買推論。

## 結果：單步做得到，串起來才是門檻

**一、在陌生硬體上把推論跑起來。** 他們測不同的開放權重模型，要求在各種硬體上把 Llama3.1-8B 服務起來。文章說，小模型如 Qwen3.5-9B 能在從 Jetson Nano 到 Mac 電腦、RTX 4090 的硬體上完成設定；更強的模型（GLM-5.2）不只更可靠，服務速度也更快。他們強調測特定晶片只是為了衡量模型會不會設定，晶片本身不是風險來源。

實驗用了他們自己做的外殼 Memento（建在開放原始碼的 Pi coding agent 上）。這個外殼處理的是長時程任務裡常見的失敗：agent 咬住一個錯誤想法不放。做法是定期換一個新的 agent 實例，只繼承前一個留下的工作目錄與一份簡短筆記，讓後面的實例有機會換策略。他們說這不是賦予新能力，只是改善長時程可靠度。

**二、端到端：在一個 8 台節點的隔離網路裡跑完整循環。** 這個測試是：掃描網路找目標、連進去、把酬載（權重與外殼）送過去、把推論跑起來，再重複。環境是一個有防火牆的雲端網路，裡面是 8 台安卓模擬器；每個節點都開著一個不需驗證的偵錯埠，也就是入侵步驟被刻意設得很簡單。結果：開放權重模型裡只有 GLM-5.2 能端到端複製，而且很可靠，5 次試驗中 4 次擴散到 8/8 節點，1 次到 7/8；其他模型則穩定失敗。

作者說明為什麼用 Llama3.1-8B 當酬載，而不是讓模型複製自己：為了可比較（越大的模型越難服務）。邏輯是，如果 GLM-5.2 有能力完成這些步驟，那麼一個有同等能力、體積約 8B 的模型也有能力把自己立起來（但他們附帶說明，較新的小模型架構與運算核心可能更複雜）。

**三、什麼時候地板與天花板會相交。** 他們把問題概念化成兩條線：模型必須高於一條「能力地板」才能可靠複製；模型也必須低於一條「大小天花板」才塞得進目標裝置。他們說現在兩條線還沒相交。要估計何時相交，他們畫了 30B 以下的開放權重模型在 Epoch Capabilities Index（ECI）上的發佈趨勢，每年約增加 13.7 個 ECI 單位（R²＝0.95）。若以 GLM-5.2 作為地板，趨勢線把一個小模型放在今年底。作者明說 ECI 是通用能力指標，只是複製能力的代理指標，所以這只是粗略估計。

**導讀者按**：「幾個月內」這個時間點，來自「通用能力指數的趨勢線」加上「GLM-5.2 是複製能力的地板」這兩個假設；兩者都是作者的設定，不是實測到的小模型達標日期。

## 它能跑多慢、能傳多廣

作者的設想是：這種複製體不會是互動速度的，會是緩慢、持續的。他們以真實電視盒上的速度為基準，Gemma4-26B-A4B 在 32k 脈絡窗口下約 0.3 token/秒（預填與解碼平均）。

| 模型 | 一個脈絡窗口耗時 | 完整推論設定耗時 |
|---|---|---|
| Gemma4-26B-A4B（未優化） | 27.3–31.8 小時 | 71–150 小時 |
| Qwen3.5-9B | 10.9 小時 | 28–51 小時 |

他們的粗估：Kimwolf 殭屍網路在高峰時據 Synthient 報導感染超過 200 萬台裝置，若同等規模的電視盒每 15 台跑一份 26B 模型，約有 13.3 萬份複本，總吞吐量約每秒 8 萬 token，換算成單張 H100 約 40 張的推論量。這在絕對值上是很小的算力，而且複製很慢（一個 9B 複製要一到兩天）。他們的類比是黏菌或果蠅群，以天為單位繁殖。

作者也列出邊緣加速器（如 Jetson 一類）的上限估算：全球約有 90 條使用 NVIDIA 晶片的 IoT 產品線、約 950 萬台在役，換算約 9,000 到 35,000 張 H100 的算力；但能從外部連到的比例「可能少一個數量級以上」，可被攻破的更少。他們也說這些裝置沒有特別脆弱，歷史上也沒有像一般 IoT 那樣被大量攻陷過。

**導讀者按**：這段的三個數字（13.3 萬份、8 萬 token/秒、約 40 張 H100）都是以「單一類裝置、單一歷史殭屍網路」為錨的粗估，作者原文就這麼說。

## 它會學、但不太會問人

**會「在工作中學習」。** 蠕蟲傳播時，父代可以把提示詞、程式碼、筆記傳給子代，作者稱為「拉馬克式演化」。他們的實驗：每個 Qwen3.5-9B 實例記下試過的策略與遇到的失敗，下一個實例繼承筆記再試一次同樣的推論設定。六代下來，20 條譜系的成功率，第一代 45%，之後各代落在 61% 到 84%。

**不太會用顧問。** 他們讓 Qwen3.5-9B 在設定推論時可以問 Opus 4.8，不論是以工具呼叫還是在卡住時被主動提示，都沒有可測量的改善；就算用強化學習訓練它用顧問（在 Python 題目上），遇到推論設定還是不會有效地求助。作者據此推測，協調比較可能是由大模型負責高階策略、並把小模型當子 agent 派出去。

## 它跟傳統殭屍網路差在哪

寫死的殭屍網路（例如 Kimwolf）各自鎖定一種裝置；AI 版本不綁定特定硬體或軟體，這正是他們證明小模型能在 Jetson Nano 到 Mac 之間設定推論的意思。作者預期 AI 殭屍網路不只感染 IoT，權重複製只會是劇本裡的一招，也可能「聰明地對抗防守方」，但這一點他們沒有測試。

**補充背景**：Kimwolf 是一個快速擴張的 Android 殭屍網路。Krebs on Security 在 2026 年 1 月引述 Synthient 的資料：感染裝置超過 200 萬台，集中在越南、巴西、印度、沙烏地阿拉伯、俄羅斯與美國；其中約三分之二是沒有內建安全或驗證機制的安卓電視盒；被查驗的裝置幾乎都在出廠時就開著偵錯橋（ADB）。

## 防守方有什麼

作者的框架是流行病學：把 R₀（每台受感染裝置接著感染的新裝置數）壓到 1 以下，複製體就會自己消失，而不會成為地方性流行。

他們認為有幾點對防守方有利：

- 大規模傳播的權重複製體會留下網路特徵：新感染的主機可能收到數 GB 的模型酬載，分散式推論也會在平常不會講話的機器之間產生持續流量，ISP 的流量監控看得到。
- 複製速度與隱蔽性要取捨：越快越吵。
- 發現後，防守方可以用誘捕裝置（honeypot）收集它的外殼與酬載來研究行為。
- 補救：Mirai 的前例是 ISP 用外部取得的感染證據，把受感染用戶隔離並通知，限制他們只能連到一小份白名單網站，其餘導向清除說明。文中的數字是 14 天內清除 92% 的感染，沒有通知的對照是 74%。
- 製造商端：英國已經規定不得以預設密碼出貨，提高基準對 AI 與腳本式殭屍網路都有幫助。

不過他們也承認：要徹底消滅，需要協調每一個有受感染網路的 ISP，很難。

**補充背景**：這個 Mirai 清除研究是 NDSS 論文〈Cleaning Up the Internet of Evil Things〉。論文摘要的數字是：以隔離與通知（walled garden）處理，14 天內清除 92% 感染；僅寄電子郵件通知與不通知的對照組沒有可觀察的差別；沒被通知的對照組與兩個參考網路，「自然清除率」是 58% 到 74%。Anthropic 文中引用的 74% 是這個區間的上緣。論文還提到，真實 IoT 裝置在實驗室裡被重新感染只要幾分鐘，現場的重複感染率卻低（清除後 5 個月內只有 5% 再被感染）。

## 其他媒體怎麼報導

這篇研究在發表當天就有多家科技網站報導，大致都是轉述 Anthropic 的結論：小模型可能在幾個月內能在 IoT 裝置上自我複製、9B 模型在 9 種硬體中的 8 種跑起推論、15 台電視盒跑 26B 模型。

- Unite.AI 的報導標明作者是一個 AI 研究 agent（頁面署名），內容為轉述。
- HuggingNews（新聞彙整站）除了轉述外，還寫到「質疑者認為威脅被誇大，因為 26B 模型在 15 台電視盒上跑得很慢；批評者指出測試用的是模擬網路與不需驗證的埠；也有業界觀察者認為矽晶片的成本會限制能承載複製體的消費裝置數量」。這段沒有指名是誰說的，屬於彙整站的二手整理；其中前兩點，Anthropic 原文本身就已經寫了（0.39 token/秒、簡化的入侵步驟是上限）。

**導讀者按**：到發表當天為止，本文沒有找到獨立團隊的重現或具名的外部評論。

## 2026 年的其他 IoT 事件

Anthropic 的假設建立在一個前提：世界上有大量連網、少有人維護的設備。2026 年已公開的幾件事，可以當這個前提的參照。它們都是傳統的腳本式殭屍網路或法規動態，沒有一件涉及 AI 自主複製。

- **預裝惡意軟體的電視盒。** Krebs on Security 在 2026 年 1 月 26 日報導，Kimwolf 的操作者貼出截圖，顯示他們似乎已入侵 BadBox 2.0 的控制面板。BadBox 2.0 是一個以中國為基地的殭屍網路，惡意軟體預裝在許多安卓電視盒裡；Krebs 寫到 FBI 與 Google 都說正在追查其幕後操作者。對 Anthropic 文中的假設來說，這代表有些裝置在使用者拿到手之前，就已被植入程式。
- **DDoS 紀錄。** 2026 年 1 月底，Cloudflare 披露一次 31.4 Tbps 的攻擊，是公開報導中的最大紀錄，由 Aisuru／Kimwolf 發動，來源主要是被入侵的 IoT 裝置與未經官方認證的安卓電視裝置。FastNetMon 與 Security Affairs 的轉述一致，但對攻擊實際發生的時間說法不同（Security Affairs 標題寫 2025 年 11 月）。
- **停產路由器被鎖定。** 香港電腦保安事故協調中心（HKCERT）在 2026 年 4 月 23 日發布公告：新的 Mirai 變種利用 CVE-2025-29635，攻擊已停產、不再有支援的 D-Link DIR-823X 路由器，並提到 TP-Link、ZTE 等停產路由器也被鎖定；建議替換停產設備、關閉不必要的遠端管理。
- **歐盟開始要求通報。** 《網路韌性法》（CRA）的漏洞與事件通報義務，自 2026 年 9 月 11 日起適用於含數位元件的產品，包括已上市的產品：製造商知悉後 24 小時內預警、72 小時內通報。（依 Travers Smith 與 Jones Day 兩家律所的說明，屬二手整理。）這對應 Anthropic 文中「製造商端」的防守建議。

**導讀者按**：這四件事與本研究的共同點，是同一批弱點：預設設定、預裝程式、停止維護。差別在於它們目前都不需要 AI，既有手法就足以大規模感染。Anthropic 的論點是 AI 把這些既有弱點變成可以被更少人力、更多種硬體利用，這是外推，上面的事件並不能直接證明。

## 放到台灣看

這篇文章談的是全球性的 IoT 風險，沒有提到台灣。能放到台灣的，是兩件可以查證的事。

**一、台廠舊設備被殭屍網路鎖定，已經發生過。** iThome 2024 年 11 月報導，台灣電腦網路危機處理暨協調中心（TWCERT/CC）在 11 月 15 日發布公告：奇偶科技（GeoVision）多款已停止維護的視訊主機、車牌辨識系統與行動攝影機，存在作業系統層級的命令注入漏洞（CVE-2024-11120，CVSS 9.8），而且已有被利用的跡象；通報者 Shadowserver 基金會說看到殭屍網路廣泛鎖定這批已終止支援的設備；TWCERT/CC 的建議是汰換設備。2025 年 5 月 iThome 再報導，Akamai 在 4 月發現 Mirai 變種 LZRD 鎖定 GeoVision 的 IoT 裝置（含 CVE-2024-6047，該漏洞由國家資通安全研究院發現通報，影響超過 20 款設備），並提到另一家台廠永恒數位（Digiever）的網路視訊監視設備也在被鎖定之列。報導也說明了這類事反覆發生的原因：產品終止支援後，廠商通常不再修補，攻擊者就能持續利用。

**導讀者按**：這些案例是傳統腳本式殭屍網路，不是 AI 複製體。它們跟 Anthropic 這篇的關聯，在於 Anthropic 的假設也建立在同一個前提：大量連網、無人維護的設備。

**二、台灣現行的 IoT 規範，本文沒有查到可引用的條文。** 搜尋找到的，是 2017 年起政府規劃物聯網資安標準、之後由 TAICS 推動的影像監控系統與網路攝影機資安標準與資安標章，以及舊報導裡「首次啟動強制更改密碼」的規劃方向。至於現行是否強制、預設密碼條文為何、2026 年的最新狀態，本文沒有找到官方原文，所以不下結論；要確認，得查 TAICS、經濟部或數位發展部的現行文件。

另外，台灣沒有出現在 Krebs 引述的 Kimwolf 感染集中國家名單中（越南、巴西、印度、沙烏地阿拉伯、俄羅斯、美國），但那份名單只列出「集中」的國家，不能據此推論台灣沒有感染。

## 這個結論的邊界

**導讀者按**（以下為導讀者的判讀，不是作者的原話）：

1. **作者自己標明的限制是主要的限制。** 入侵步驟被簡化，是「上限」；被複製的是 Llama3.1-8B，模型並不是在複製「自己」；GLM-5.2 本身不是小模型，「小模型追上它」是外推。
2. **「幾個月」是外推，不是觀察。** 它依賴一個通用能力指數的趨勢，以及「地板」由單一模型定義這兩個選擇，作者本人也說只是粗估。
3. **這是模型開發商自己的紅隊報告。** 實驗在受控環境完成，設計與結果由作者自述；本文沒有找到獨立重現。這不影響他們揭露的事實，但讀者在權衡「多近」時應當知道這一點。
4. **傳播的瓶頸可能不在 AI。** 作者的自述是：入侵步驟越像過去的 IoT 殭屍網路（預設密碼、無驗證埠），AI 需要補的就只有「在陌生硬體上把推論跑起來」這一步。若這個判斷成立，防守的重心仍是古老的那些：預設密碼、終止支援的設備、ISP 隔離通知。
5. **作者本身也承認這只是「生命跡象」。** 他們的結論用語是「signs of life」，並說「我們還沒有完整的流氓複製體」。

## 可以帶走的話

以下為導讀者的整理，不是作者的原話：

1. 研究問的是小模型能不能在沒有資料中心的 IoT 裝置上自我複製。作者的答案是：單步多半已能做，端到端目前只有 GLM-5.2 以上在簡化評估中做到，小模型可能在年底前追上。
2. 實驗刻意簡化入侵步驟，作者稱結果是真實世界成功率的上限。
3. 這種複製體預期很慢（9B 複製以一到兩天計），總算力約相當 40 張 H100；作者形容它像黏菌。
4. 防守思路沿用 Mirai 的經驗：壓低 R₀、ISP 隔離與通知、製造商取消預設密碼。
5. 沒有發現野外案例；到發表當天，也沒有找到獨立重現。
6. 台灣本地可查證的是：台廠已停止維護的監控設備曾被殭屍網路鎖定。現行 IoT 規範的條文本文未查到。

## 來源

- William Zhou、Daniel Freeman，[AI models may be capable of self-replicating without access to a datacenter](https://www.anthropic.com/research/self-replicating-ai)，Anthropic，2026-10-12
- [Evaluating Language-Model Agents on Realistic Autonomous Tasks](https://arxiv.org/abs/2312.11671)，arXiv，2023
- Air 等，[Language Models Can Autonomously Hack and Self-Replicate](https://arxiv.org/abs/2605.06760)，arXiv，2026-05-07
- Guan 等，[AI Agents Enable Adaptive Computer Worms](https://arxiv.org/abs/2606.03811)，arXiv，2026-06-02
- Borzunov 等，[Petals: Collaborative Inference and Fine-tuning of Large Models](https://arxiv.org/abs/2209.01188)，arXiv，2022
- Brian Krebs，[The Kimwolf Botnet is Stalking Your Local Network](https://krebsonsecurity.com/2026/01/the-kimwolf-botnet-is-stalking-your-local-network/)，Krebs on Security，2026-01
- Antonakakis 等，[Cleaning Up the Internet of Evil Things](https://www.ndss-symposium.org/ndss-paper/cleaning-up-the-internet-of-evil-things-real-world-evidence-on-isp-and-consumer-efforts-to-remove-mirai/)，NDSS
- 英國 NCSC，[Smart devices law](https://www.ncsc.gov.uk/blog-post/smart-devices-law)
- Epoch AI，[Epoch Capabilities Index](https://epoch.ai/eci)
- Krebs on Security，[Who Operates the Badbox 2.0 Botnet?](https://krebsonsecurity.com/?p=73071)，2026-01-26
- FastNetMon，[Aisuru botnet sets a new DDoS record at 31.4 Tbps](https://fastnetmon.com/2026/02/01/aisuru-botnet-sets-a-new-ddos-record-at-31-4-tbps/)，2026-02-01（二手）
- Security Affairs，[Record-breaking 31.4 Tbps DDoS attack hits in November 2025, stopped by Cloudflare](https://securityaffairs.com/187690/hacking/record-breaking-31-4-tbps-ddos-attack-hits-in-november-2025-stopped-by-cloudflare.html)（二手）
- HKCERT，[Botnet Alert - Mirai Botnet Targets End-of-Life D-Link Routers](https://www.hkcert.org/security-bulletin/botnet-alert-mirai-botnet-targets-end-of-life-d-link-routers_20260423)，2026-04-23
- Travers Smith，[The EU Cyber Resilience Act's vulnerability and incident reporting requirements are now live](https://www.traverssmith.com/knowledge/knowledge-container/the-eu-cyber-resilience-acts-vulnerability-and-incident-reporting-requirements-are-now-live/)，2026-09-11（二手）
- Jones Day，[EU Cyber Resilience Act 24-hour reporting duties start September 11, 2026](https://www.jonesday.com/en/insights/2026/07/eu-cyber-resilience-act-24hour-reporting-duties-start-september-11-2026)，2026-07（二手）
- iThome，[已終止支援的GeoVision視訊監控設備存在重大漏洞，傳出被用於攻擊行動](https://www.ithome.com.tw/news/166063)，2024-11-18
- iThome，[GeoVision物聯網裝置遭到鎖定，殭屍網路利用已知漏洞滲透](https://www.ithome.com.tw/news/168841)，2025-05-08
- Unite.AI，[Anthropic Red Team Says Small AI Models May Soon Replicate on IoT Devices](https://www.unite.ai/anthropic-red-team-says-small-ai-models-may-soon-replicate-on-iot-devices/)，2026-10-12（二手；頁面署名為 AI agent）
- HuggingNews，[Anthropic Detects First Signs of AI Worms on IoT Hardware](https://huggingnews.com/ai/anthropic-detects-first-signs-of-ai-worms-on-iot-hardware-d9853d5e)，2026-10-12（彙整站，二手）
