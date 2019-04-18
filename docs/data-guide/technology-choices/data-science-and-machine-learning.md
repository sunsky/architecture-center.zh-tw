---
title: 選擇機器學習技術
description: 比較建置、部署及管理機器學習模型的選項。 決定您要為解決方案選擇哪些 Microsoft 產品。
author: MikeWasson
ms.date: 03/06/2019
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: e4c81add1a97254f427d67584ff7e2a4799f84a9
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640901"
---
# <a name="what-are-the-machine-learning-products-at-microsoft"></a>Microsoft 有哪些機器學習產品？

機器學習是一項資料科學技術，可讓電腦使用現有資料來預測未來的行為、結果和趨勢。 使用機器學習，電腦不需要明確進行程式設計就能學習。

機器學習解決方案反覆組建的並有不同的階段：

- 準備資料
- 實驗和定型模型
- 將定型的模型部署
- 管理部署模型

Microsoft 提供各種不同的產品選項，以準備、 建置、 部署及管理您的機器學習模型。 請比較這些產品，然後選擇可讓您最有效率地開發機器學習解決方案的產品。

## <a name="cloud-based-options"></a>雲端架構的選項

下列選項適用於 Azure 雲端中的機器學習。

| 雲端&nbsp;選項 | 內容 | 產品用途 |
|-|-|-|
| [Azure Machine Learning 服務](#azure-machine-learning-service) | 機器學習服務的受管理的雲端服務  | 使用 Python 和 CLI 在 Azure 中訓練、部署及管理模型 |
| [Azure Machine Learning Studio](#azure-machine-learning-studio) | 拖曳&ndash;和&ndash;拖放視覺化介面，適用於 machine learning | 使用預先設定的演算法來建置、實驗及部署模型 |

如果您想要使用預先建置的 AI 和機器學習服務模型[Azure 認知服務](#azure-cognitive-services)可讓您輕鬆地將智慧型功能新增至您的應用程式。

## <a name="on-premises-options"></a>在內部部署選項

下列選項適用於內部部署環境的機器學習。 內部部署伺服器也可以在雲端的虛擬機器中執行。

| 內部部署&nbsp;選項 | 內容 | 產品用途 |
|-|-|-|
| [SQL Server Machine Learning 服務](#sql-server-machine-learning-services) | 內嵌在 SQL 中的分析引擎 | 在 SQL Server 內建置及部署模型 |
| [Microsoft Machine Learning Server](#microsoft-machine-learning-server) | 適用於預測分析的獨立企業伺服器 | 建置及部署預先處理的資料模型 |

## <a name="development-platforms-and-tools"></a>開發平台和工具

下列開發平台和工具可供機器學習服務。

| 平台/工具 | 內容 | 產品用途 |
|-|-|-|
| [Azure 資料科學虛擬機器](#azure-data-science-virtual-machine) | 預先安裝了資料科學工具的虛擬機器 | 開發在預先設定的環境中的機器學習解決方案 |
| [Azure Databricks](#azure-databricks) | 以 Spark 為基礎的分析平台 | 建置及部署模型和資料工作流程 |
| [ML.NET](#mlnet) | 開放原始碼、 跨平台的機器學習服務 SDK | 開發.NET 應用程式的機器學習解決方案 |
| [Windows ML](#windows-ml) | Windows 10 機器學習平台 | 在 Windows 10 裝置上評估已定型的模型 |

## <a name="azure-machine-learning-service"></a>Azure Machine Learning 服務

[Azure Machine Learning 服務](/azure/machine-learning/service/overview-what-is-azure-ml.md)是用來訓練、 部署及管理大規模的機器學習服務模型的完全受控的雲端服務。 此服務可完整支援開放原始碼技術，因此您可以使用數以萬計的開放原始碼 Python 套件，例如 TensorFlow、PyTorch 與 scikit-learn。 此外也有齊備的工具 (例如 [Azure Notebooks](https://notebooks.azure.com/)、[Jupyter Notebooks](http://jupyter.org) 或[適用於 Visual Studio Code 的 Azure Machine Learning](https://aka.ms/vscodetoolsforai) 擴充功能) 可供您輕鬆地瀏覽和轉換資料，然後訓練和部署模型。 Azure Machine Learning 服務包含自動產生模型的功能，並可讓您輕鬆、有效率且正確地進行調整。

使用 Azure Machine Learning 服務來訓練、 部署及管理雲端規模使用 Python 和 CLI 的機器學習服務模型。

試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

|||
|-|-|
|**類型**                   |雲端式機器學習服務解決方案|
|**支援的語言**    |Python|
|**機器學習階段**|資料準備<br>模型訓練<br>部署<br>管理性|
|**主要優點**           |指令碼的中央管理和執行歷程記錄，讓模型版本間的比較變得簡單。<br/><br/>輕鬆部署及管理雲端或邊緣裝置的模型。|
|**考量**         |需要對模型管理模型有些熟悉。|

## <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio

[Azure Machine Learning Studio](/azure/machine-learning/studio/) 提供互動式的視覺化工作區，可讓您使用預先建置的機器學習演算法輕鬆快速地建置、測試和部署模型。 Machine Learning Studio 會以 Web 服務方式發佈模型，讓自訂應用程式或 BI 工具 (例如 Excel) 都能夠很容易地使用。
無需任何程式設計 - 您可以連接互動式畫布上的資料集和分析模組以建構機器學習服務模型，然後輕鬆按幾下滑鼠加以部署。

如果您想要直接開發及部署模型而不使用程式碼，請使用 Machine Learning Studio。

請嘗試付費選項或免費選項中提供的 [Azure Machine Learning Studio](https://studio.azureml.net/?selectAccess=true&o=2&target=_blank)。

|||
|-|-|
|**類型**                   |雲端式的拖放的機器學習服務解決方案|
|**支援的語言**    |Python、R|
|**機器學習階段**|資料準備<br>模型訓練<br>部署<br>管理性|
|**主要優點**           |互動式視覺介面可使用最少程式碼製作機器學習模型。<br/><br/>內建用於資料探索的 Jupyter Notebook。<br/><br/>直接將訓練模型部署為 Azure Web 服務。|
|**考量**         |有限延展性。 訓練資料集的大小上限為 10 GB。<br/><br/>線上專用。 無離線開發環境。|

## <a name="azure-cognitive-services"></a>Azure 認知服務

[Azure 認知服務](/azure/cognitive-services/welcome)是一組 API，可讓您建置使用自然通訊方法的應用程式。 只要幾行程式碼，這些 API 即讓您的應用程式查看、聽取、說出、了解並解譯使用者的需求。 輕鬆地將智慧型功能新增至您的應用程式，例如：

- 情緒和人氣偵測
- 願景與語音辨識
- Language Understanding (LUIS)
- 知識和搜尋

使用認知服務可開發跨裝置及平台的應用程式。 API 會持續改進，且易於設定。

|||
|-|-|
|**類型**                   |建置智慧型應用程式的 Api|
|**支援的語言**    |根據服務的許多選項|
|**機器學習階段**|部署|
|**主要優點**           |將應用程式中使用預先定型的模型的機器學習服務功能。<br/><br/>各種自然溝通方法與視覺與語音模型。|
|**考量**         |已預先定型模型，並不是可自訂。|

## <a name="sql-server-machine-learning-services"></a>SQL Server Machine Learning 服務

[SQL Server Microsoft Machine Learning 服務](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)針對 SQL Server 資料庫中的關聯式資料新增了採用 R 和 Python 的統計分析、資料視覺效果和預測分析。 Microsoft R 和 Python 程式庫會包含進階模型化和機器學習服務演算法，可以執行且大規模地以平行方式，在 SQL Server。

如果您在 SQL Server 中的關聯式資料需要內建的 AI 和預測分析，請使用 SQL Server Machine Learning 服務。

|||
|-|-|
|**類型**                   |在內部部署關聯式資料的預測性分析|
|**支援的語言**    |Python、R|
|**機器學習階段**|資料準備<br>模型訓練<br>部署|
|**主要優點**           |在資料庫函式中封裝預測邏輯，可讓函式輕鬆加入資料層邏輯中。|
|**考量**         |採用 SQL Server 資料庫作為應用程式的資料層。|

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server

[Microsoft Machine Learning 伺服器](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)是企業伺服器，用來裝載及管理 R 及 Python 處理程序的並行和分散式工作負載。 Microsoft Machine Learning 伺服器可在 Linux、Windows、Hadoop 和 Apache Spark 上執行，也可在 [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/) 中使用。 它可為使用 [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)、[revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) 和 [MicrosoftML 套件](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)建置的解決方案提供執行引擎，並藉由對高效能分析、統計分析、機器學習和大型資料集的支援，來擴充開放原始碼 R 和 Python。 此功能會透過隨著伺服器安裝的專屬套件而提供。 對於開發，您可以使用 IDE，例如 [Visual Studio R 工具](https://www.visualstudio.com/vs/rtvs/)和[適用於 Visual Studio 的 Python 工具](https://www.visualstudio.com/vs/python/)。

如果您需要建置及運算化在伺服器上使用 R 和 Python 建置的模型，或是在 Hadoop 或 Spark 叢集上大規模散發 R 和 Python 訓練，請使用 Microsoft Machine Learning 伺服器。

|||
|-|-|
|**類型**                   |預測性分析的內部部署企業伺服器|
|**支援的語言**    |Python、R|
|**機器學習階段**|模型訓練<br>部署|
|**主要優點**           |高延展性。|
|**考量**         |您需要在企業中部署和管理 Machine Learning Server。|

## <a name="azure-data-science-virtual-machine"></a>Azure 資料科學虛擬機器

[Azure 資料科學虛擬機器](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview)是 Microsoft Azure 雲端上的自訂虛擬機器環境，專為進行資料科學建置。 它已預先安裝和預先設定許多常用的資料科學和其他工具，以開始建置智慧應用程式進行進階分析。

目前支援以資料科學虛擬機器作為 Azure Machine Learning 服務的目標。
這適用於 Windows 和 Linux Ubuntu 的版本 (Linux CentOS 不支援 Azure Machine Learning 服務)。
如需特定版本資訊及其內含項目的清單，請參閱 [Azure 資料科學虛擬機器簡介](/azure/machine-learning/data-science-virtual-machine/overview.md)。

當您需要在單一節點上執行或裝載您的作業時，請使用資料科學 VM。 或者，如果您需要從遠端相應增加在單一機器上的處理。

|||
|-|-|
|**類型**                   |適用於資料科學的自訂的虛擬機器環境|
|**主要優點**           |減少資料科學工具及架構的安裝、管理和疑難排解時間。<br/><br/>包含所有常用的工具和架構的最新版本。<br/><br/>虛擬機器選項包括可高度擴充的影像，具備適用於大量資料模型化的 GPU 功能。|
|**考量**         |離線時無法存取虛擬機器。<br/><br/>執行虛擬機器會衍生 Azure 費用，因此您必須非常小心，讓它只在需要時執行。|

## <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/azure-databricks/what-is-azure-databricks) 是一個針對 Microsoft Azure 雲端服務平台進行最佳化的 Apache Spark 分析平台。 Databricks 可與 Azure 整合，提供一鍵式設定、順暢的工作流程以及互動式的工作區，可讓資料科學家、資料工程師及企業分析師共同作業。
您可以在 Web 型 Notebook 中使用 Python、R、Scala 和 SQL 程式碼，來查詢、視覺化和模型化資料。

如果您想要在 Apache Spark 上共同建置機器學習解決方案，請使用 Databricks。

|||
|-|-|
|**類型**                   |以 Apache Spark 為基礎的分析平台|
|**支援的語言**    |Python, R, Scala, SQL|
|**機器學習階段**|資料查詢<br>模型訓練|

## <a name="mlnet"></a>ML.NET

[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/) 是免費、開放原始碼且跨平台的機器學習架構，可讓您建立自訂的機器學習解決方案，並將其整合到您的 .NET 應用程式中。

如果您想要將機器學習解決方案整合到您的 .NET 應用程式中，請使用 ML.NET。

|||
|-|-|
|**類型**                   |開發自訂的機器學習應用程式的開放原始碼架構|
|**支援的語言**    |.NET|

## <a name="windows-ml"></a>Windows ML

[Windows ML](https://docs.microsoft.com/windows/uwp/machine-learning/)推斷引擎，可讓您使用定型的機器學習服務應用程式中的模型、 評估 Windows 10 裝置上，在本機定型的模型。

如果您想要在 Windows 應用程式中使用定型的機器學習模型，請使用 Windows ML。

|||
|-|-|
|**類型**                   |在 Windows 裝置的定型模型的推斷引擎|
|**支援的語言**    |C#/C++，JavaScript|

## <a name="next-steps"></a>後續步驟

- 若要深入了解所有人工智慧 (AI) 開發產品可從 Microsoft 取得，請參閱[Microsoft AI 平台](https://www.microsoft.com/ai)
- 如需有關於開發 AI 解決方案的教學，請參閱 [Microsoft AI School](https://aischool.microsoft.com/learning-paths)
