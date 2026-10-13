# 沒有資料中心，也能複製

Anthropic Frontier Red Team 在 2026 年 10 月 12 日發表的研究：小型 AI 模型可能在幾個月內，就有能力在智慧烤箱、山寨安卓電視盒這類 IoT 裝置上自我複製。本導讀含開頭摘要與結論預測的中文翻譯、實驗拆解、作者自述的限制、其他媒體報導，以及放到台灣看的可查證案例。

## 200字介紹

Anthropic 紅隊評估小型 AI 模型能否在沒有資料中心的裝置上自我複製。結果是：30B 以下模型已能完成許多單一步驟，端到端串起來在簡化評估裡只有 GLM-5.2 以上做到；作者依能力趨勢外推，小模型可能在年底前追上。實驗刻意簡化入侵步驟，作者稱結果是上限；傳播也會很慢，估計一次 9B 複製要一到兩天。防守思路沿用 Mirai 的經驗：壓低 R₀、ISP 隔離通知、製造商取消預設密碼。導讀把原文的流程圖與五張資料圖重繪成中文，並補上前人研究、第三方資料的核對（Mirai 清除率、Kimwolf），標明這是模型開發商自家紅隊的報告、未見獨立重現。台灣方面，本文查到台廠已停止維護的監控設備曾被殭屍網路鎖定，現行 IoT 規範條文則未查到。

## 檔案

| 檔案 | 說明 |
|---|---|
| `index.html` | 導讀網頁 |
| `anthropic-self-replicating-ai.md` | 導讀 Markdown 原稿 |
| `images/web/` | 主視覺（16:9 與 9:16 手機版）與六張依原文圖表資料重繪的中文圖（WebP） |
| `images/og_1200x630.jpg` | LINE／社群分享預覽圖 |
| `share_post.md` | 社群分享文（短版、長版） |

## 原始素材

- William Zhou、Daniel Freeman，[AI models may be capable of self-replicating without access to a datacenter](https://www.anthropic.com/research/self-replicating-ai)，Anthropic，2026-10-12

## 查證來源

- arXiv：[2312.11671](https://arxiv.org/abs/2312.11671)、[2605.06760](https://arxiv.org/abs/2605.06760)、[2606.03811](https://arxiv.org/abs/2606.03811)
- Krebs on Security，[The Kimwolf Botnet is Stalking Your Local Network](https://krebsonsecurity.com/2026/01/the-kimwolf-botnet-is-stalking-your-local-network/)
- NDSS，[Cleaning Up the Internet of Evil Things](https://www.ndss-symposium.org/ndss-paper/cleaning-up-the-internet-of-evil-things-real-world-evidence-on-isp-and-consumer-efforts-to-remove-mirai/)
- iThome，[GeoVision 漏洞報導（2024-11）](https://www.ithome.com.tw/news/166063)、[LZRD 報導（2025-05）](https://www.ithome.com.tw/news/168841)

## 狀態

導讀日期 2026-10-13。發布狀態：https://lushinshang.github.io/anthropic-self-replicating-ai/
