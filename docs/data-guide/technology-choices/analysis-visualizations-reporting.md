---
title: 選擇資料分析和報告技術
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.openlocfilehash: a5e793c9caf50daca4ef7e40c49e54f25f04e856
ms.sourcegitcommit: e7e0e0282fa93f0063da3b57128ade395a9c1ef9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2018
ms.locfileid: "52902411"
---
# <a name="choosing-a-data-analytics-technology-in-azure"></a>在 Azure 中選擇資料分析技術

大部分巨量資料解決方案的目標，是要透過分析和報告提供資料的深入見解。 這可能包括預先設定的報表和視覺效果，或互動式資料探索。 

## <a name="what-are-your-options-when-choosing-a-data-analytics-technology"></a>選擇資料分析技術時有哪些選項？

有幾個選項可根據您的需求在 Azure 中分析、視覺化及報告：

- [Power BI](/power-bi/)
- [Jupyter 筆記本](https://jupyter.readthedocs.io/en/latest/index.html)
- [Zeppelin Notebook](https://zeppelin.apache.org/)
- [Microsoft Azure Notebook](https://notebooks.azure.com/)

### <a name="power-bi"></a>Power BI

[Power BI](/power-bi/) 是一套商務分析工具。 它可以連線到數百個資料來源，並可用於特定分析。 請參閱[這份清單](/power-bi/desktop-data-sources)來了解目前可用的資料來源。 使用 [Power BI Embedded](https://azure.microsoft.com/services/power-bi-embedded/) 來整合您應用程式中的 Power BI (不需要任何額外授權)。

組織可使用 Power BI 產生報表，並將其發行到組織。 每個人都可以建立個人化儀表板，包含控管和[內建的安全性](/power-bi/service-admin-power-bi-security)。 Power BI 使用 [Azure Active Directory](/azure/active-directory/) (Azure AD) 來驗證登入 Power BI 服務的使用者，並在每次使用者嘗試存取需要驗證的資源時使用 Power BI 登入認證。

### <a name="jupyter-notebooks"></a>Jupyter Notebook 

[Jupyter Notebook](https://jupyter.readthedocs.io/en/latest/index.html) 提供瀏覽器型的殼層，可讓資料科學家建立「notebook」檔案，其中包含 Python、Scala 或 R 程式碼和 Markdown 文字，透過共用和記錄程式碼並產生單一文件的方式，有效率地進行協同作業。

大部分的 HDInsight 叢集類型 (例如 Spark 或 Hadoop) 皆[使用 Jupyter Notebook 進行預先設定](/azure/hdinsight/spark/apache-spark-jupyter-notebook-kernels)，以便與資料互動並提交作業進行處理。 根據您使用的 HDInsight 叢集類型，您可以取得一個或多個核心來解譯及執行您的程式碼。 例如，HDInsight 上的 Spark 叢集提供您 Spark 相關核心，您可以使用 Spark 引擎執行 Python 或 Scala 程式碼來進行選取。

Jupyter Notebook 提供優質的環境，讓您先分析、視覺化及處理您的資料，再使用 Power BI 之類的 BI/報表工具來建置更進階的視覺效果。

### <a name="zeppelin-notebooks"></a>Zeppelin Notebook

[Zeppelin Notebook](https://zeppelin.apache.org/) 是另一種瀏覽器型殼層的選項，類似 Jupyter 的功能。 某些 HDInsight 叢集[已使用 Zeppelin Notebook 進行預先設定](/azure/hdinsight/spark/apache-spark-zeppelin-notebook)。 不過，如果您使用 [HDInsight 互動式查詢](/azure/hdinsight/interactive-query/apache-interactive-query-get-started) (Hive LLAP) 叢集，則目前只能使用 [Zeppelin](/azure/hdinsight/hdinsight-connect-hive-zeppelin) 筆記本選項來執行互動式 Hive 查詢。 此外，如果使用[已加入網域的 HDInsight 叢集](/azure/hdinsight/domain-joined/apache-domain-joined-introduction)，則唯有使用 Zeppelin Notebook 類型，才能指派不同的使用者登入來控制筆記本與基礎 Hive 資料表的存取。

### <a name="microsoft-azure-notebooks"></a>Microsoft Azure Notebook

[Azure Notebook](https://notebooks.azure.com/) 是線上 Jupyter Notebook 型服務，可讓資料科學家建立、執行和共用雲端型程式庫中的 Jupyter Notebook。 Azure Notebook 提供 Python 2、Python 3、F# 和 R 的執行環境，並提供幾個圖表程式庫來視覺化資料，例如 ggplot、matplotlib、bokeh 和 seaborn。

不像 HDInsight 叢集上執行的 Jupyter Notebook 會連線到叢集的預設儲存體帳戶，Azure Notebook 不會提供任何資料。 您必須以各種不同的方式[載入資料](https://notebooks.azure.com/Microsoft/libraries/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb)，例如從線上來源下載資料、與 Azure Blob 或資料表儲存體互動、連線至 SQL 資料庫，或使用 Azure Data Factory 的複製精靈載入資料。

主要優點：

* 免費服務 &mdash; 不需要 Azure 訂用帳戶。
* 不需要在本機安裝 Jupyter 和支援的 R 或 Python 發行版本 &mdash; 直接使用瀏覽器即可。
* 管理您自己的線上程式庫，並從任何裝置存取它們。
* 與共同作業者共用您的筆記本。

考量：

* 您在離線時將無法存取您的筆記本。
* 免費筆記本服務的有限處理功能可能不足以訓練大型或複雜的模型。

## <a name="key-selection-criteria"></a>關鍵選取準則

若要縮小選項範圍，請開始回答這些問題：

- 您是否需要連線到多個資料來源，提供集中的位置，為散佈在整個網域的資料建立報表？ 如果是這樣，選擇一個可讓您連線到 100 個資料來源的選項。

- 是否要在外部網站或應用程式中內嵌動態視覺效果？ 如果是這樣，選擇一個選項來提供內嵌功能。

- 是否要在離線時設計視覺效果和報表？ 如果是，選擇包含離線功能的選項。

- 您是否需要大量的處理能力來訓練大型或複雜的 AI 模型，或使用超大型的資料集？ 如果是，請選擇可連線至巨量資料叢集的選項。

## <a name="capability-matrix"></a>相容性矩陣

下表摘要列出各項功能的主要差異。 

### <a name="general-capabilities"></a>一般功能

| | Power BI | Jupyter Notebook | Zeppelin Notebook | Microsoft Azure Notebook |
| --- | --- | --- | --- | --- |
| 連線到進階處理的巨量資料叢集 | 是 | 是 | 是 | 否 |
| 受控服務 | 是 | 是 <sup>1</sup> | 是 <sup>1</sup> | 是 |
| 連線到 100 個資料來源 | 是 | 否 | 否 | 否 |
| 離線功能 | 是 <sup>2</sup> | 否 | 否 | 否 |
| 內嵌功能 | 是 | 否 | 否 | 否 |
| 自動重新整理資料 | 是 | 否 | 否 | 否 |
| 眾多開放原始碼套件的存取權 | 否 | 是 <sup>3</sup> | 是 <sup>3</sup> | 是 <sup>4</sup> |
| 資料轉換/清理選項 | [Power Query](https://powerbi.microsoft.com/blog/getting-started-with-power-query-part-i/)、R | 40 種語言，包含 Python、R、Julia 和 Scala | 20 種以上的解譯器，包含 Python、JDBC 和 R | Python、F#、R |
| 價格 | 免費用於 Power BI Desktop (撰寫)，請參閱[定價](https://powerbi.microsoft.com/pricing/)以取得裝載選項 | 免費 | 免費 | 免費 |
| 多使用者共同作業 | [是](/power-bi/service-how-to-collaborate-distribute-dashboards-reports) | 是 (透過共用或使用多使用者伺服器，如 [JupyterHub](https://github.com/jupyterhub/jupyterhub)) | 是 | 是 (透過共用) |

[1] 作為受控 HDInsight 叢集的一部分使用時。

[2] 與 Power BI Desktop 搭配使用。

[2] 您可以搜尋 [Maven 存放庫](https://search.maven.org/)以取得社群貢獻的套件。

[3] 可使用 pip 或 conda 來安裝 Python 套件。 可以從 CRAN 或 GitHub 安裝 R 套件。 可使用 [Paket 相依性管理員](https://fsprojects.github.io/Paket/)，透過 nuget.org 以 F# 安裝套件。

