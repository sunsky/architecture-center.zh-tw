---
title: 選擇機器學習技術
description: ''
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: 995349c795066ec3067b20ad2615e40b0fb152db
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2018
---
# <a name="choosing-a-machine-learning-technology-in-azure"></a>在 Azure 中選擇機器學習技術

資料科學和機器學習是一項通常由資料科學家執行的工作負載。 它需要專業工具，其中有許多是專為互動式資料探索與模型工作類型所設計，且資料科學家必須執行這些工作。

機器學習解決方案是反覆組建的，而且擁有兩個不同的階段：
* 資料準備與模型建立。
* 部署和使用預測服務。

## <a name="tools-and-services-for-data-preparation-and-modeling"></a>資料準備和模型建立的工具與服務
資料科學家通常想要使用 Python 或 R 所撰寫的自訂程式碼來使用資料。資料科學家通常會以互動方式使用此程式碼來查詢及探索資料、產生視覺效果和統計資料，以協助判斷與其中的關聯性。 有許多資料科學家可以使用的 R 和 Python 互動式環境。 特別受歡迎的是提供瀏覽器型介面的 **Jupyter Notebook**，它可讓資料科學家建立包含 R 或 Python 程式碼和 Markdown 文字的「筆記本」檔案。 這是可進行共同作業的有效方式，透過共用和記錄程式碼來產生一份文件。

其他常用的工具包括：
* **Spyder**：隨著 Anaconda Python 發行版本提供的 Python 互動式開發環境 (IDE)。
* **R Studio**：R 程式設計語言的 IDE。
* **Visual Studio Code**：輕量型的跨平台編碼環境，其支援 Python，通常也用於機器學習服務和 AI 開發的架構。

除了這些工具外，資料科學家可運用 Azure 服務來簡化程式碼與模型管理。

### <a name="azure-notebooks"></a>Azure Notebooks
Azure Notebook 是線上 Jupyter Notebook 服務，可讓資料科學家建立、執行和共用雲端型程式庫中的 Jupyter Notebook。

主要優點：

* 免費服務 &mdash; 不需要 Azure 訂用帳戶。
* 不需要在本機安裝 Jupyter 和支援的 R 或 Python 發行版本 &mdash; 直接使用瀏覽器即可。
* 管理您自己的線上程式庫，並從任何裝置存取它們。
* 與共同作業者共用您的筆記本。

考量：

* 您在離線時將無法存取您的筆記本。
* 免費筆記本服務的有限處理功能可能不足以訓練大型或複雜的模型。

### <a name="data-science-virtual-machine"></a>資料科學虛擬機器
資料科學虛擬機器是包含工具和架構的 Azure 虛擬機器映像，通常由資料科學家使用，包括 R、Python、Jupyter Notebook、Visual Studio Code，以及適用於機器學習模型的程式庫 (如 Microsoft Cognitive Toolkit)。 這些工具可能很複雜並耗用很多安裝時間，且包含許多通常會導致版本管理問題的相依性。 具有預先安裝的映像，可減少資料科學家花在疑難排解環境問題的時間，進而將焦點放在其需要執行的資料探索和模型工作。

主要優點：
* 減少資料科學工具及架構的安裝、管理和疑難排解時間。
* 包含所有常用的工具和架構的最新版本。
* 虛擬機器選項包括可高度擴充的影像，具備適用於大量資料模型化的 GPU 功能。

考量：
* 離線時無法存取虛擬機器。
* 執行虛擬機器會衍生 Azure 費用，因此您必須非常小心，讓它只在需要時執行。

### <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning 是管理機器學習實驗和模型的雲端型服務。 它包含會追蹤資料準備和模型訓練指令碼的實驗服務，可保留所有執行記錄，以便在反覆項目中比較模型效能。 名為 Azure Machine Learning Workbench 的跨平台用戶端工具提供了指令碼管理與記錄的中央介面，同時仍可讓資料科學家使用其選擇的工具 (例如 Jupyter Notebook 或 Visual Studio Code) 建立指令碼。

從 Azure Machine Learning Workbench 中，您可以使用互動式資料準備工具來簡化常見的資料轉換工作，並可設定指令碼執行環境，以便在本機、可擴充的 Docker 容器，或 Spark 中執行模型訓練指令碼。

準備好部署模型時，使用 Workbench 環境來封裝模型，並將它作為 Web 服務部署至 Docker 容器、Azure HDinsight 上的 Spark、Microsoft Machine Learning Server 或 SQL Server。 接著，Azure Machine Learning 模型管理服務可讓您追蹤和管理雲端中、邊緣裝置上，或整個企業的模型部署。

主要優點：

* 指令碼的中央管理和執行歷程記錄，讓模型版本間的比較變得簡單。
* 透過視覺化編輯器進行的互動式資料轉換。
* 輕鬆部署及管理雲端或邊緣裝置的模型。

考量：
* 需要對模型管理模型和工作台工具環境有些熟悉。

### <a name="azure-batch-ai"></a>Azure Batch AI

Azure Batch AI 可讓您平行執行您的機器學習實驗，並跨虛擬機器 (配備 GPU) 叢集執行大規模的模型訓練。 Batch AI 訓練可讓您相應放大叢集化 GPU 之間的深入學習作業，使用 Cognitive Toolkit、Caffe、Chainer 和 TensorFlow 等架構。 

您可以使用 Azure Machine Learning 模型管理來取得 Batch AI 訓練的模型，進而部署、管理和監視模型。 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio

Azure Machine Learning Studio 是雲端式視覺開發環境，用來建立資料實驗、訓練機器學習模型，並在 Azure 中將它們發佈為 Web 服務。 其視覺化的拖放介面可讓資料科學家和進階使用者快速建立機器學習解決方案，同時支援自訂的 R 和 Python 邏輯和許多已建立的統計演算法和技術 (適用於機器學習模型化工作)，以及 Jupyter Notebook 的內建支援。

主要優點：

* 互動式視覺介面可使用最少程式碼製作機器學習模型。
* 內建用於資料探索的 Jupyter Notebook。
* 直接將訓練模型部署為 Azure Web 服務。

考量：

* 有限延展性。 訓練資料集的大小上限為 10 GB。
* 線上專用。 無離線開發環境。

## <a name="tools-and-services-for-deploying-machine-learning-models"></a>部署機器學習模型的工具和服務

資料科學家建立機器學習模型之後，您通常必須部署它，然後在應用程式或其他資料流程中使用它。 機器學習模型有許多潛在的部署目標。

### <a name="spark-on-azure-hdinsight"></a>Azure HDInsight 上的 Spark

Apache Spark 包括機器學習模型的 Spark MLlib、架構和程式庫。 Spark 的 Microsoft Machine Learning 程式庫 (MMLSpark) 也提供深入的學習演算法，可支援 Spark 中的預測模型。

主要優點：

* Spark 是分散式的平台，為大量機器學習程序提供高延展性。
* 您可以從 Azure Machine Learning Workbench 將模型直接部署到 HDinsight 的 Spark，並使用 Azure Machine Learning 模型管理服務來管理模型。

考量：

* Spark 在 HDinsght 叢集中執行的整個時間會產生費用。 如果只是偶爾使用機器學習服務，這可能會導致不必要的成本。

### <a name="web-service-in-a-container"></a>容器中的 Web 服務

您可以在 Docker 容器中將機器學習模型部署為 Python Web 服務。 您可以將模型部署到 Azure 或邊緣裝置，它可在本機搭配使用其操作的資料。

主要優點：

* 容器是輕量型且通常符合成本效益的有效方法，可用來封裝及部署服務。
* 部署到邊緣裝置的能力可讓您將預測邏輯移至更貼近資料。
* 您可以從 Azure Machine Learning Workbench 直接部署到容器。

考量：

* 這種部署模型是以 Docker 容器為基礎，因此您應該先熟悉這項技術再以此方式部署 Web 服務。

### <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server

機器學習伺服器 (先前稱為 Microsoft R Server) 是 R 和 Python 程式碼的可擴充平台，特別針對機器學習案例所設計。

主要優點：

* 高延展性。
* 直接從 Azure Machine Learning Workbench 部署。

考量：

* 您需要在企業中部署和管理 Machine Learning Server。

### <a name="microsoft-sql-server"></a>連接字串

Microsoft SQL Server 本質上就支援 R 和 Python，可讓您將這些語言內建的機器學習模型封裝為資料庫中的 Transact-SQL 函式。

主要優點：

* 在資料庫函式中封裝預測邏輯，可讓函式輕鬆加入資料層邏輯中。

考量：

* 採用 SQL Server 資料庫作為應用程式的資料層。

### <a name="azure-machine-learning-web-service"></a>Azure Machine Learning Web 服務

當您使用 Azure Machine Learning Studio 建立機器學習模型時，可將其部署為 Web 服務。 接著從能夠以 HTTP 通訊的任何用戶端應用程式中，透過 REST 介面使用此服務。

主要優點：

* 簡化開發和部署。
* 使用基本監視計量的 Web 服務管理入口網站。
* 從 Azure Data Lake Analytics、Azure Data Factory 及 Azure 串流分析呼叫 Azure Machine Learning Web 服務的內建支援。

考量：

* 僅適用於使用 Azure Machine Learning Studio 建置的模型。
* 僅限 Web 型存取，訓練的模型無法在內部部署或離線時執行。

