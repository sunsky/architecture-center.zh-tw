---
title: 選擇認知服務技術
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: AI
ms.openlocfilehash: e8bbdf4874d5fc669e1c08add8cf212217d176a4
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54482641"
---
# <a name="choosing-a-microsoft-cognitive-services-technology"></a>選擇 Microsoft 認知服務技術

Microsoft 認知服務是雲端式 API，可用於人工智慧 (AI) 應用程式和資料流程。 它們會為您提供已可在應用程式中使用的預先定型模型，讓您不需要提供任何資料，也不需進行模型定型。 認知服務是由 Microsoft 的 AI 和研究小組所開發，並利用最新的深度學習演算法。 它們可透過 HTTP REST 介面來取用。 此外，SDK 也可供許多常見的應用程式開發架構使用。

認知服務包括：

- 文字分析
- 電腦視覺
- 視訊分析
- 語音辨識和產生
- 自然語言理解
- 智慧型搜尋

主要優點：

- 適用於最新 AI 服務的最少開發工作。
- 透過 HTTP REST 介面輕鬆整合到應用程式。
- 在 Azure Data Lake Analytics 中內建認知服務的取用支援。

考量：

- 只能透過 Web 取得。 通常需要網際網路連線。 自訂視覺服務是例外，您可以匯出其已定型的模型，以在裝置和 IoT Edge 上進行預測。

- 雖然支援大量自訂，但可用的服務可能不適合所有的預測性分析需求。

<!-- markdownlint-disable MD026 -->

## <a name="what-are-your-options-when-choosing-amongst-the-cognitive-services"></a>您在選擇認知服務時有哪些選項可用？

<!-- markdownlint-disable MD026 -->

Azure 中有數十個可用的認知服務。 這些服務的目前清單可以在依其支援之功能區域所分類的目錄中找到：

- [視覺](https://azure.microsoft.com/services/cognitive-services/directory/vision/)
- [語音](https://azure.microsoft.com/services/cognitive-services/directory/speech/)
- [知識](https://azure.microsoft.com/services/cognitive-services/directory/know/)
- [搜尋](https://azure.microsoft.com/services/cognitive-services/directory/search/)
- [語言](https://azure.microsoft.com/services/cognitive-services/directory/lang/)

## <a name="key-selection-criteria"></a>重要選取準則

若要縮小選項範圍，請從回答下列問題來開始：

- 您正在處理何種資料類型？ 請根據您正在處理的輸入資料類型來縮小選項。 例如，如果輸入文字，則從輸入類型為文字的服務中選取。

- 您是否有用來將模型定型的資料？ 如果是，請考慮自訂服務，這些服務可讓您以提供的資料將其基礎模型定型，以改進精確度和效能。

## <a name="capability-matrix"></a>功能對照表

下表摘要列出各項功能的主要差異。

### <a name="uses-prebuilt-models"></a>使用預先建置的模型

|                                                   |             輸入類型              |                                                                                主要優點                                                                                |
|---------------------------------------------------|-------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|                文字分析 API                 |                文字                 |                                                       解讀意見與話題，從而了解使用者的需求。                                                        |
|                實體連結 API                 |                文字                 |                                               透過具名實體辨識和去除混淆，來增強您的應用程式資料連結。                                               |
| 語意辨識智慧服務 (LUIS) |                文字                 |                                                          教導您的應用程式理解使用者發出的命令。                                                          |
|                 QnA Maker 服務                 |                文字                 |                                             將常見問題集格式的資訊整理成易於導覽的交談式回答。                                              |
|              語言分析 API              |                文字                 |                                                            簡化複雜的語言概念及剖析文字。                                                             |
|           知識探索服務           |                文字                 |                                          啟用透過自然語言輸入對結構化資料進行互動式搜尋的功能。                                          |
|              Web 語言模型 API               |                文字                 |                                                         運用以 Web 規模資料所定型的預測語言模型。                                                         |
|              Academic Knowledge API               |                文字                 |                                        充分利用 Bing 所填入之 Microsoft Academic Graph 中豐富的學術內容。                                         |
|               Bing 自動建議 API                |                文字                 |                                                        將搜尋用的智慧型自動建議選項提供給您的應用程式。                                                        |
|               Bing 拼字檢查 API                |                文字                 |                                                             偵測並校正您應用程式中的拼字錯誤。                                                             |
|                Translator Text API                |                文字                 |                                                                           機器翻譯。                                                                            |
|                建議 API                |                文字                 |                                                             預測並建議客戶想要的商品。                                                              |
|              Bing 實體搜尋 API               |       文字 (Web 搜尋查詢)       |                                                           識別並加強來自 Web 的實體資訊。                                                           |
|               Bing 影像搜尋 API               |       文字 (Web 搜尋查詢)       |                                                                            搜尋影像。                                                                             |
|               Bing 新聞搜尋 API                |       文字 (Web 搜尋查詢)       |                                                                             搜尋新聞。                                                                              |
|               Bing 影片搜尋 API               |       文字 (Web 搜尋查詢)       |                                                                            搜尋影片。                                                                             |
|                Bing Web 搜尋 API                |       文字 (Web 搜尋查詢)       |                                                        從數以十億計的 Web 文件取得增強的搜尋詳細資料。                                                        |
|                  Bing 語音 API                  |           文字或語音            |                                                                  雙向轉換語音與文字。                                                                   |
|              說話者辨識 API              |               語音                |                                                       使用語音來辨識及驗證各個說話者。                                                        |
|               Translator Speech API               |               語音                |                                                                   執行即時語音翻譯。                                                                   |
|                Computer Vision API                |    影像 (或影片的畫面格)    | 從影像擷取可操作的資訊、自動建立相片的描述、衍生標記、辨識名人、擷取文字，並建立精確縮圖。 |
|                 內容仲裁                 |        文字、影像或影片        |                                                               自動審核影像、文字及影片。                                                                |
|                    Emotion API                    | 影像 (含人物主體的相片) |                                                              識別人物主體的範圍情感。                                                               |
|                     人臉識別 API                      | 影像 (含人物主體的相片) |                                                       偵測、識別、分析、組織和標記相片中的臉孔。                                                       |
|                   影片索引子                   |                影片                |                        人氣、文字記錄語音、平移語音、辨識臉孔和情緒，和擷取關鍵字等影片深入解析。                         |

### <a name="trained-with-custom-data-you-provide"></a>使用您提供的自訂資料加以定型

| | 輸入類型 | 主要優點 |
| --- | --- | --- |
| 自訂辨識服務 | 影像 (或影片的畫面格) | 自訂您自己的電腦視覺模型。 |
| 自訂語音服務 | 語音 | 克服像是語音模式、背景雜音以及詞彙等語音辨識的阻礙。 |
| 自訂決策服務 | Web 內容 (例如，RSS 摘要) | 使用機器學習來自動選取首頁的適當內容 |
| Bing 自訂搜尋 API | 文字 (Web 搜尋查詢) | 商業級搜尋工具。 |
