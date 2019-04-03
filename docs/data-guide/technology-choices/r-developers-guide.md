---
title: Azure 的 R 開發人員指南 - R 程式設計 | Microsoft Docs
description: 本文概述各種方式，讓資料科學家能夠在 Azure 中對 R 程式設計語言運用其現有技術。 Azure 提供許多 R 開發人員可用來擴充到雲端資料科學工作負載的服務。
services: machine-learning
author: AnalyticJeremy
ms.service: machine-learning
ms.workload: data-services
ms.devlang: R
ms.topic: article
ms.date: 03/19/2019
ms.author: jepeach
ms.openlocfilehash: a14c8ce76f78baa7274f22b939eb28cb025ef87e
ms.sourcegitcommit: 1a3cc91530d56731029ea091db1f15d41ac056af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887959"
---
# <a name="r-developers-guide-to-azure"></a><span data-ttu-id="35f08-104">Azure 的 R 開發人員指南</span><span class="sxs-lookup"><span data-stu-id="35f08-104">R developer's guide to Azure</span></span>

<img src="../images/logo_r.svg" alt="R logo" align="right" width="200" />

<span data-ttu-id="35f08-105">由於要處理的資料數量不斷增加，許多資料科學家想盡辦法來利用雲端運算的能力以便完成其分析工作。</span><span class="sxs-lookup"><span data-stu-id="35f08-105">Many data scientists dealing with ever-increasing volumes of data are looking for ways to harness the power of cloud computing for their analyses.</span></span>  <span data-ttu-id="35f08-106">本文概述各種方式，讓資料科學家能夠在 Azure 中對 [R 程式設計語言](https://www.r-project.org)運用其現有技術。</span><span class="sxs-lookup"><span data-stu-id="35f08-106">This article provides an overview of the various ways that data scientists can leverage their existing skills with the [R programming language](https://www.r-project.org) in Azure.</span></span>

<span data-ttu-id="35f08-107">Microsoft 已完全採用 R 程式設計語言作為資料科學家的頂級工具。</span><span class="sxs-lookup"><span data-stu-id="35f08-107">Microsoft has fully embraced the R programming language as a first-class tool for data scientists.</span></span>  <span data-ttu-id="35f08-108">藉由提供許多不同的選項供 R 開發人員在 Azure 中執行其程式碼，該公司讓資料科學家能夠在解決大規模專案時，將其資料科學工作負載延伸到雲端。</span><span class="sxs-lookup"><span data-stu-id="35f08-108">By providing many different options for R developers to run their code in Azure, the company is enabling data scientists to extend their data science workloads into the cloud when tackling large-scale projects.</span></span>

<span data-ttu-id="35f08-109">讓我們檢查一下各種選項以及其各自最受矚目的案例。</span><span class="sxs-lookup"><span data-stu-id="35f08-109">Let's examine the various options and the most compelling scenarios for each one.</span></span>

## <a name="azure-services-with-r-language-support"></a><span data-ttu-id="35f08-110">具有 R 語言支援的 Azure 服務</span><span class="sxs-lookup"><span data-stu-id="35f08-110">Azure services with R language support</span></span>

<span data-ttu-id="35f08-111">本文涵蓋下列可支援 R 語言的 Azure 服務：</span><span class="sxs-lookup"><span data-stu-id="35f08-111">This article covers the following Azure services that support the R language:</span></span>

|<span data-ttu-id="35f08-112">服務</span><span class="sxs-lookup"><span data-stu-id="35f08-112">Service</span></span>                                                          |<span data-ttu-id="35f08-113">描述</span><span class="sxs-lookup"><span data-stu-id="35f08-113">Description</span></span>                                                                       |
|-----------------------------------------------------------------|----------------------------------------------------------------------------------|
|[<span data-ttu-id="35f08-114">資料科學虛擬機器</span><span class="sxs-lookup"><span data-stu-id="35f08-114">Data Science Virtual Machine</span></span>](#data-science-virtual-machine)    |<span data-ttu-id="35f08-115">自訂 VM，可作為資料科學工作站或作為自訂計算目標</span><span class="sxs-lookup"><span data-stu-id="35f08-115">a customized VM to use as a data science workstation or as a custom compute target</span></span>|
|[<span data-ttu-id="35f08-116">HDInsight 上的 ML 服務</span><span class="sxs-lookup"><span data-stu-id="35f08-116">ML Services on HDInsight</span></span>](#ml-services-on-hdinsight)            |<span data-ttu-id="35f08-117">叢集式系統，可對跨許多節點的大型資料集執行 R 分析</span><span class="sxs-lookup"><span data-stu-id="35f08-117">cluster-based system for running R analyses on large datasets across many nodes</span></span>   |
|[<span data-ttu-id="35f08-118">Azure Databricks</span><span class="sxs-lookup"><span data-stu-id="35f08-118">Azure Databricks</span></span>](#azure-databricks)                            |<span data-ttu-id="35f08-119">支援 R 和其他語言的共同作業 Spark 環境</span><span class="sxs-lookup"><span data-stu-id="35f08-119">collaborative Spark environment that supports R and other languages</span></span>               |
|[<span data-ttu-id="35f08-120">Azure Machine Learning Studio</span><span class="sxs-lookup"><span data-stu-id="35f08-120">Azure Machine Learning Studio</span></span>](#azure-machine-learning-studio)  |<span data-ttu-id="35f08-121">在 Azure 的機器學習實驗中執行自訂的 R 指令碼</span><span class="sxs-lookup"><span data-stu-id="35f08-121">run custom R scripts in Azure's machine learning experiments</span></span>                      |
|[<span data-ttu-id="35f08-122">Azure Batch</span><span class="sxs-lookup"><span data-stu-id="35f08-122">Azure Batch</span></span>](#azure-batch)                                      |<span data-ttu-id="35f08-123">提供各種不同選項，以便以經濟實惠的方式跨叢集中的許多節點執行 R 程式碼</span><span class="sxs-lookup"><span data-stu-id="35f08-123">offers a variety options for economically running R code across many nodes in a cluster</span></span>|
|[<span data-ttu-id="35f08-124">Azure Notebooks</span><span class="sxs-lookup"><span data-stu-id="35f08-124">Azure Notebooks</span></span>](#azure-notebooks)                              |<span data-ttu-id="35f08-125">免費的雲端式 Jupyter Notebook 版本</span><span class="sxs-lookup"><span data-stu-id="35f08-125">a no-cost cloud-based version of Jupyter notebooks</span></span>                  |
|[<span data-ttu-id="35f08-126">連接字串</span><span class="sxs-lookup"><span data-stu-id="35f08-126">Azure SQL Database</span></span>](#azure-sql-database)                        |<span data-ttu-id="35f08-127">在 SQL Server 資料庫引擎內執行 R 指令碼</span><span class="sxs-lookup"><span data-stu-id="35f08-127">run R scripts inside of the SQL Server database engine</span></span>                            |

## <a name="data-science-virtual-machine"></a><span data-ttu-id="35f08-128">資料科學虛擬機器</span><span class="sxs-lookup"><span data-stu-id="35f08-128">Data Science Virtual Machine</span></span>

<span data-ttu-id="35f08-129">[資料科學虛擬機器 (DSVM)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) 是 Microsoft Azure 雲端平台上的自訂 VM 映像，專為進行資料科學建置。</span><span class="sxs-lookup"><span data-stu-id="35f08-129">The [Data Science Virtual Machine](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) (DSVM) is a customized VM image on Microsoft’s Azure cloud platform built specifically for doing data science.</span></span> <span data-ttu-id="35f08-130">其具有許多熱門的資料科學工具，包括：</span><span class="sxs-lookup"><span data-stu-id="35f08-130">It has many popular data science tools, including:</span></span>

* [<span data-ttu-id="35f08-131">Microsoft R Open</span><span class="sxs-lookup"><span data-stu-id="35f08-131">Microsoft R Open</span></span>](https://mran.microsoft.com/open/)
* [<span data-ttu-id="35f08-132">Microsoft Machine Learning Server</span><span class="sxs-lookup"><span data-stu-id="35f08-132">Microsoft Machine Learning Server</span></span>](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)
* [<span data-ttu-id="35f08-133">RStudio Desktop</span><span class="sxs-lookup"><span data-stu-id="35f08-133">RStudio Desktop</span></span>](https://www.rstudio.com/products/rstudio/#Desktop)
* [<span data-ttu-id="35f08-134">RStudio Server</span><span class="sxs-lookup"><span data-stu-id="35f08-134">RStudio Server</span></span>](https://www.rstudio.com/products/rstudio/#Server)

<span data-ttu-id="35f08-135">DSVM 可以搭配 Windows 或 Linux 作業系統來佈建。</span><span class="sxs-lookup"><span data-stu-id="35f08-135">The DSVM can be provisioned with either Windows or Linux as the operating system.</span></span>  <span data-ttu-id="35f08-136">您可以透過兩種不同的方式使用 DSVM：作為自訂叢集的互動式工作站或計算平台。</span><span class="sxs-lookup"><span data-stu-id="35f08-136">You can use the DSVM in two different ways:  as an interactive workstation or as a compute platform for a custom cluster.</span></span>

### <a name="as-a-workstation"></a><span data-ttu-id="35f08-137">作為工作站</span><span class="sxs-lookup"><span data-stu-id="35f08-137">As a workstation</span></span>

<span data-ttu-id="35f08-138">如果您想要在雲端中快速且輕鬆地開始使用 R，這是您的最佳選擇。</span><span class="sxs-lookup"><span data-stu-id="35f08-138">If you want to get started with R in the cloud quickly and easily, this is your best bet.</span></span>  <span data-ttu-id="35f08-139">已在本機工作站上使用過 R 的人會相當熟悉此環境。</span><span class="sxs-lookup"><span data-stu-id="35f08-139">The environment will be familiar to anyone who has worked with R on a local workstation.</span></span>  <span data-ttu-id="35f08-140">不過，R 環境不會使用本機資源，而是會在雲端 VM 上執行。</span><span class="sxs-lookup"><span data-stu-id="35f08-140">However, instead of using local resources, the R environment runs on a VM in the cloud.</span></span>  <span data-ttu-id="35f08-141">如果資料已儲存在 Azure 中，其另外的好處是可讓 R 指令碼以「更接近資料」的方式執行。</span><span class="sxs-lookup"><span data-stu-id="35f08-141">If your data is already stored in Azure, this has the added benefit of allowing your R scripts to run "closer to the data."</span></span> <span data-ttu-id="35f08-142">您不必透過網際網路傳輸資料，而是可以透過 Azure 的內部網路存取資料，以提供更快的存取速度。</span><span class="sxs-lookup"><span data-stu-id="35f08-142">Instead of transferring the data across the Internet, the data can be accessed over Azure's internal network, which provides much faster access times.</span></span>

<span data-ttu-id="35f08-143">DSVM 特別適用於小型的 R 開發人員團隊。</span><span class="sxs-lookup"><span data-stu-id="35f08-143">The DSVM can be particularly useful to small teams of R developers.</span></span>  <span data-ttu-id="35f08-144">您不必為每位開發人員投資功能強大的工作站，團隊成員也不必將他們所會使用的各種軟體套件版本同步，每位開發人員都可以在需要時立即啟動 DSVM 執行個體。</span><span class="sxs-lookup"><span data-stu-id="35f08-144">Instead of investing in powerful workstations for each developer and requiring team members to synchronize on which versions of the various software packages they will use, each developer can spin up an instance of the DSVM whenever needed.</span></span>

### <a name="as-a-compute-platform"></a><span data-ttu-id="35f08-145">作為計算平台</span><span class="sxs-lookup"><span data-stu-id="35f08-145">As a compute platform</span></span>

<span data-ttu-id="35f08-146">除了作為工作站，DSVM 也可作為適用於 R 專案的可彈性調整計算平台。</span><span class="sxs-lookup"><span data-stu-id="35f08-146">In addition to being used as a workstation, the DSVM is also used as an elastically scalable compute platform for R projects.</span></span>  <span data-ttu-id="35f08-147">使用[ `AzureDSVM` ](https://github.com/Azure/AzureDSVM) R 封裝，您可以透過程式設計方式控制建立和刪除的 DSVM 執行個體。</span><span class="sxs-lookup"><span data-stu-id="35f08-147">Using the [`AzureDSVM`](https://github.com/Azure/AzureDSVM) R package, you can programmatically control the creation and deletion of DSVM instances.</span></span>  <span data-ttu-id="35f08-148">您可以將執行個體組成叢集，並部署要在雲端執行的分散式分析。</span><span class="sxs-lookup"><span data-stu-id="35f08-148">You can form the instances into a cluster and deploy a distributed analysis to be performed in the cloud.</span></span>  <span data-ttu-id="35f08-149">這整個程序可以由本機工作站上所執行的 R 程式碼來控制。</span><span class="sxs-lookup"><span data-stu-id="35f08-149">This entire process can be controlled by R code running on your local workstation.</span></span>

<span data-ttu-id="35f08-150">若要深入了解 DSVM，請參閱[Azure 資料科學虛擬機器簡介適用於 Linux 和 Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview)。</span><span class="sxs-lookup"><span data-stu-id="35f08-150">To learn more about the DSVM, see [Introduction to Azure Data Science Virtual Machine for Linux and Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview).</span></span>

## <a name="ml-services-on-hdinsight"></a><span data-ttu-id="35f08-151">HDInsight 上的 ML 服務</span><span class="sxs-lookup"><span data-stu-id="35f08-151">ML Services on HDInsight</span></span>

<span data-ttu-id="35f08-152">[Microsoft ML 服務](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-overview)可讓資料科學家、統計學家以及 R 程式設計人員隨其所需存取 HDInsight 上可調整大小的分散式分析方法。</span><span class="sxs-lookup"><span data-stu-id="35f08-152">[Microsoft ML Services](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-overview) provide data scientists, statisticians, and R programmers with on-demand access to scalable, distributed methods of analytics on HDInsight.</span></span>  <span data-ttu-id="35f08-153">此解決方案所提供的最新功能，適用於幾乎任何大小的資料集上進行之以 R 為基礎的分析，且不論資料集是載入到 Azure Blob 或 Data Lake 儲存體。</span><span class="sxs-lookup"><span data-stu-id="35f08-153">This solution provides the latest capabilities for R-based analytics on datasets of virtually any size, loaded to either Azure Blob or Data Lake storage.</span></span>

<span data-ttu-id="35f08-154">這是企業級解決方案，可讓您跨叢集調整 R 程式碼。</span><span class="sxs-lookup"><span data-stu-id="35f08-154">This is an enterprise-grade solution that allows you to scale your R code across a cluster.</span></span>  <span data-ttu-id="35f08-155">利用 Microsoft 中的函式 [`RevoScaleR`](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)</span><span class="sxs-lookup"><span data-stu-id="35f08-155">By leveraging functions in Microsoft's [`RevoScaleR`](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)</span></span>
<span data-ttu-id="35f08-156">封裝，R 指令碼在 HDInsight 上的可執行資料處理函式以平行方式跨叢集中許多節點。</span><span class="sxs-lookup"><span data-stu-id="35f08-156">package, your R scripts on HDInsight can run data processing functions in parallel across many nodes in a cluster.</span></span>  <span data-ttu-id="35f08-157">這可讓 R 處理更大規模的資料，在工作站上執行的單一執行緒 R 就做不到這一點。</span><span class="sxs-lookup"><span data-stu-id="35f08-157">This allows R to crunch data on a much larger scale than is possible with single-threaded R running on a workstation.</span></span>

<span data-ttu-id="35f08-158">這項調整能力讓 HDInsight 上的 ML 服務成為絕佳選項，可讓 R 開發人員處理大規模資料集。</span><span class="sxs-lookup"><span data-stu-id="35f08-158">This ability to scale makes ML Services on HDInsight a great option for R developers with massive data sets.</span></span>  <span data-ttu-id="35f08-159">其提供彈性且可擴充的平台供您在雲端中執行 R 指令碼。</span><span class="sxs-lookup"><span data-stu-id="35f08-159">It provides a flexible and scalable platform for running your R scripts in the cloud.</span></span>

<span data-ttu-id="35f08-160">如需建立 ML 服務叢集的逐步解說，請參閱 <<c0> [ 開始使用 Azure HDInsight 上的 ML 服務](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-get-started)。</span><span class="sxs-lookup"><span data-stu-id="35f08-160">For a walk-through on creating an ML Services cluster, see [Get started with ML Services on Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-get-started).</span></span>

## <a name="azure-databricks"></a><span data-ttu-id="35f08-161">Azure Databricks</span><span class="sxs-lookup"><span data-stu-id="35f08-161">Azure Databricks</span></span>

<span data-ttu-id="35f08-162">[Azure Databricks](https://azure.microsoft.com/services/databricks/) 是一個針對 Microsoft Azure 雲端服務平台進行最佳化的 Apache Spark 分析平台。</span><span class="sxs-lookup"><span data-stu-id="35f08-162">[Azure Databricks](https://azure.microsoft.com/services/databricks/) is an Apache Spark-based analytics platform optimized for the Microsoft Azure cloud services platform.</span></span>  <span data-ttu-id="35f08-163">Databricks 由 Apache Spark 的創立者所設計，可與 Azure 整合，提供一鍵式設定、順暢的工作流程以及互動式的工作區，可讓資料科學家、資料工程師及企業分析師共同作業。</span><span class="sxs-lookup"><span data-stu-id="35f08-163">Designed with the founders of Apache Spark, Databricks is integrated with Azure to provide one-click setup, streamlined workflows, and an interactive workspace that enables collaboration between data scientists, data engineers, and business analysts.</span></span>

<span data-ttu-id="35f08-164">平台的 Notebook 系統會啟用 Databricks 中的共同作業功能。</span><span class="sxs-lookup"><span data-stu-id="35f08-164">The collaboration in Databricks is enabled by the platform's notebook system.</span></span>  <span data-ttu-id="35f08-165">使用者可以和系統內的其他使用者建立、共用和編輯 Notebook。</span><span class="sxs-lookup"><span data-stu-id="35f08-165">Users can create, share, and edit notebooks with other users of the systems.</span></span>  <span data-ttu-id="35f08-166">這些 Notebook 可讓使用者撰寫程式碼，以針對 Databricks 環境中所管理的 Spark 叢集來執行。</span><span class="sxs-lookup"><span data-stu-id="35f08-166">These notebooks allow users to write code that executes against Spark clusters managed in the Databricks environment.</span></span>  <span data-ttu-id="35f08-167">這些 Notebook 完全支援 R，並可讓使用者透過 `SparkR` 和 `sparklyr` 套件存取 Spark。</span><span class="sxs-lookup"><span data-stu-id="35f08-167">These notebooks fully support R and give users access to Spark through both the `SparkR` and `sparklyr` packages.</span></span>

<span data-ttu-id="35f08-168">因為 Databricks 建置在 Spark 之上，而且特別著重於共同作業，所以資料科學家團隊通常會使用此平台來共同處理複雜的大型資料集分析。</span><span class="sxs-lookup"><span data-stu-id="35f08-168">Since Databricks is built on Spark and has a strong focus on collaboration, the platform is often used by teams of data scientists that work together on complex analyses of large data sets.</span></span>  <span data-ttu-id="35f08-169">由於 Databricks 中的 Notebook 不僅支援 R 也支援其他語言，所以特別適用於分析師會使用不同語言來進行其主要工作的團隊。</span><span class="sxs-lookup"><span data-stu-id="35f08-169">Because the notebooks in Databricks support other languages in addition to R, it is especially useful for teams where analysts use different languages for their primary work.</span></span>

<span data-ttu-id="35f08-170">發行項[何謂 Azure Databricks？](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks)可以提供更多詳細的平台和幫助您開始。</span><span class="sxs-lookup"><span data-stu-id="35f08-170">The article [What is Azure Databricks?](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) can provide more details about the platform and help you get started.</span></span>

## <a name="azure-machine-learning-studio"></a><span data-ttu-id="35f08-171">Azure Machine Learning Studio</span><span class="sxs-lookup"><span data-stu-id="35f08-171">Azure Machine Learning Studio</span></span>

<span data-ttu-id="35f08-172">[Azure Machine Learning Studio](https://azure.microsoft.com/services/machine-learning-studio/) 是共同作業式的拖放工具，可供您在雲端中建置、測試及部署預測性分析解決方案。</span><span class="sxs-lookup"><span data-stu-id="35f08-172">[Azure Machine Learning Studio](https://azure.microsoft.com/services/machine-learning-studio/) is a collaborative, drag-and-drop tool you can use to build, test, and deploy predictive analytics solutions in the cloud.</span></span>  <span data-ttu-id="35f08-173">它可讓新興的資料科學家建立和部署機器學習模型，而不需要撰寫龐大的程式碼。</span><span class="sxs-lookup"><span data-stu-id="35f08-173">It enables emerging data scientists to create and deploy machine learning models without the need to write much code.</span></span>

<span data-ttu-id="35f08-174">Azure Machine Learning Studio 支援 R 和 Python。</span><span class="sxs-lookup"><span data-stu-id="35f08-174">Azure Machine Learning Studio supports both R and Python.</span></span>  <span data-ttu-id="35f08-175">您可以使用 Azure Machine Learning Studio 中使用 R，有兩種。</span><span class="sxs-lookup"><span data-stu-id="35f08-175">You can use R with Azure Machine Learning Studio in two ways.</span></span>

### <a name="custom-r-scripts-in-your-experiments"></a><span data-ttu-id="35f08-176">在實驗中自訂 R 指令碼</span><span class="sxs-lookup"><span data-stu-id="35f08-176">Custom R scripts in your experiments</span></span>

<span data-ttu-id="35f08-177">首先，您可以藉由撰寫自訂 R 指令碼，擴充 ML Studio 的資料操作和機器學習功能。</span><span class="sxs-lookup"><span data-stu-id="35f08-177">First, you can extend the data manipulation and machine learning capabilities of ML Studio by writing custom R scripts.</span></span>
<span data-ttu-id="35f08-178">雖然 ML Studio 包含各種不同的資料準備和分析模組，但它所擁有的功能比不上已經成熟的語言 (例如 R)。因此，這項服務的設計，是要讓您能夠在所提供的模組不符合需求的情況下，引進您自己的自訂 R 指令碼。</span><span class="sxs-lookup"><span data-stu-id="35f08-178">Although ML Studio includes a wide variety of modules for preparing and analyzing data, it cannot match the capabilities of a mature language like R.  Therefore, the service was designed to allow you to introduce your own custom R scripts in cases where the provided modules do not meet your needs.</span></span>

<span data-ttu-id="35f08-179">若要利用這項功能，請將「執行 R 指令碼」模組拖放到實驗中。</span><span class="sxs-lookup"><span data-stu-id="35f08-179">To leverage this capability, drag and drop an "Execute R Script" module into your experiment.</span></span>  <span data-ttu-id="35f08-180">然後在 [屬性] 窗格中使用程式碼編輯器，來撰寫新的 R 指令碼或貼上現有指令碼。</span><span class="sxs-lookup"><span data-stu-id="35f08-180">Then use the code editor in the "Properties" pane to write a new R script or paste an existing script.</span></span>  <span data-ttu-id="35f08-181">在指令碼中，您可以參考外部 R 套件。</span><span class="sxs-lookup"><span data-stu-id="35f08-181">Within the script, you can reference external R packages.</span></span>  <span data-ttu-id="35f08-182">操作資料，或用來訓練複雜的 ML 模型，並不屬於標準 Azure Machine Learning Studio 模型庫，您可以使用指令碼。</span><span class="sxs-lookup"><span data-stu-id="35f08-182">You can use the script to manipulate data or to train complex ML models that are not part of the standard Azure Machine Learning Studio model library.</span></span>

<span data-ttu-id="35f08-183">如需在 ML Studio 實驗中使用 R 的完整介紹，請參閱[開始使用 R 程式語言，在 Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/r-quickstart)。</span><span class="sxs-lookup"><span data-stu-id="35f08-183">For a thorough introduction on using R within ML Studio experiments, check out [Getting started with the R programming language in Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/r-quickstart).</span></span>

### <a name="create-manage-and-deploy-experiments-from-your-local-r-environment"></a><span data-ttu-id="35f08-184">從本機 R 環境建立、管理及部署實驗</span><span class="sxs-lookup"><span data-stu-id="35f08-184">Create, manage, and deploy experiments from your local R environment</span></span>

<span data-ttu-id="35f08-185">您可以使用 Azure Machine Learning Studio 使用 R 的方式是使用[ `AzureML` ](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html)套件來監視和控制 R 程式設計環境的測試程序。</span><span class="sxs-lookup"><span data-stu-id="35f08-185">The other way that you can use R with Azure Machine Learning Studio is to use the [`AzureML`](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) package to monitor and control the experimentation process with the R programming environment.</span></span>  <span data-ttu-id="35f08-186">此套件，由 Microsoft 維護，可讓您上傳和下載資料集，以和 Azure Machine Learning Studio 中，若要查閱的實驗中，發行 R 函式為 web 服務和執行 R 資料透過現有 web 服務，並擷取輸出。</span><span class="sxs-lookup"><span data-stu-id="35f08-186">This package, which is maintained by Microsoft, allows you to upload and download datasets to and from Azure Machine Learning Studio, to interrogate experiments, to publish R functions as web services, and to run R data through existing web services and retrieve the output.</span></span>

<span data-ttu-id="35f08-187">此套件可讓您更容易做為可調整的部署平台的 Azure Machine Learning Studio 使用 R 程式碼。</span><span class="sxs-lookup"><span data-stu-id="35f08-187">This package makes it much easier to use Azure Machine Learning Studio as a scalable deployment platform for your R code.</span></span>  <span data-ttu-id="35f08-188">您不必在 UI 中按一下並拖曳，而是可以使用您已熟悉的工具自動進行整個部署程序。</span><span class="sxs-lookup"><span data-stu-id="35f08-188">Instead of clicking and dragging in the UI, you can automate the entire deployment process using tools you already know.</span></span>

## <a name="azure-batch"></a><span data-ttu-id="35f08-189">Azure Batch</span><span class="sxs-lookup"><span data-stu-id="35f08-189">Azure Batch</span></span>

<span data-ttu-id="35f08-190">對於大規模的 R 作業，您可以使用 [Azure Batch](https://azure.microsoft.com/services/batch/)。</span><span class="sxs-lookup"><span data-stu-id="35f08-190">For large-scale R jobs, you can use [Azure Batch](https://azure.microsoft.com/services/batch/).</span></span>  <span data-ttu-id="35f08-191">此服務可提供雲端級別的作業排程和計算管理，讓您可以跨數十個、數百個或數千個虛擬機器調整 R 工作負載。</span><span class="sxs-lookup"><span data-stu-id="35f08-191">This service provides cloud-scale job scheduling and compute management so you can scale your R workload across tens, hundreds, or thousands of virtual machines.</span></span>  <span data-ttu-id="35f08-192">因為它是一般化的運算平台，所以有幾個選項可供您在 Azure Batch 上執行 R 作業。</span><span class="sxs-lookup"><span data-stu-id="35f08-192">Since it is a generalized computing platform, there a few options for running R jobs on Azure Batch.</span></span>

<span data-ttu-id="35f08-193">其中一個選項是使用 Microsoft [ `doAzureParallel` ](https://github.com/Azure/doAzureParallel)封裝。</span><span class="sxs-lookup"><span data-stu-id="35f08-193">One option is to use Microsoft's [`doAzureParallel`](https://github.com/Azure/doAzureParallel) package.</span></span>  <span data-ttu-id="35f08-194">此 R 套件是 `foreach` 套件的平行後端。</span><span class="sxs-lookup"><span data-stu-id="35f08-194">This R package is a parallel backend for the `foreach` package.</span></span>  <span data-ttu-id="35f08-195">它可讓 `foreach` 迴圈的每次反覆運算在 Azure Batch 叢集內的節點上平行執行。</span><span class="sxs-lookup"><span data-stu-id="35f08-195">It allows each iteration of the `foreach` loop to run in parallel on a node within the Azure Batch cluster.</span></span>  <span data-ttu-id="35f08-196">如需封裝的簡介，請參閱部落格文章[doAzureParallel:利用 Azure 的彈性的運算，直接從您的 R 工作階段](https://azure.microsoft.com/blog/doazureparallel/)。</span><span class="sxs-lookup"><span data-stu-id="35f08-196">For an introduction to the package, see the blog post [doAzureParallel: Take advantage of Azure’s flexible compute directly from your R session](https://azure.microsoft.com/blog/doazureparallel/).</span></span>

<span data-ttu-id="35f08-197">另一個在 Azure Batch 中執行 R 指令碼的選項，是在 Azure 入口網站中將程式碼與 "RScript.exe" 組合成 Batch 應用程式。</span><span class="sxs-lookup"><span data-stu-id="35f08-197">Another option for running an R script in Azure Batch is to bundle your code with "RScript.exe" as a Batch App in the Azure portal.</span></span>  <span data-ttu-id="35f08-198">如需詳細的逐步解說，請參閱[在 Azure Batch 上的 R 工作負載](https://azure.microsoft.com/blog/r-workloads-on-azure-batch/)。</span><span class="sxs-lookup"><span data-stu-id="35f08-198">For a detailed walk-through, consult [R Workloads on Azure Batch](https://azure.microsoft.com/blog/r-workloads-on-azure-batch/).</span></span>

<span data-ttu-id="35f08-199">第三個選項是使用 [Azure 分散式資料工程工具組](https://github.com/Azure/aztk) (AZTK)，其可讓您使用 Azure Batch 中的 Docker 容器佈建隨選 Spark 叢集。</span><span class="sxs-lookup"><span data-stu-id="35f08-199">A third option is to use the [Azure Distributed Data Engineering Toolkit](https://github.com/Azure/aztk) (AZTK), which allows you to provision on-demand Spark clusters using Docker containers in Azure Batch.</span></span>  <span data-ttu-id="35f08-200">這可讓您以經濟實惠的方式在 Azure 中執行 Spark 作業。</span><span class="sxs-lookup"><span data-stu-id="35f08-200">This provides an economical way to run Spark jobs in Azure.</span></span>  <span data-ttu-id="35f08-201">藉由搭配使用 [SparklyR 與 AZTK](https://github.com/Azure/aztk/wiki/SparklyR-on-Azure-with-AZTK)，便可在雲端中以輕鬆且經濟實惠的方式相應放大 R 指令碼。</span><span class="sxs-lookup"><span data-stu-id="35f08-201">By using [SparklyR with AZTK](https://github.com/Azure/aztk/wiki/SparklyR-on-Azure-with-AZTK), your R scripts can be scaled out in the cloud easily and economically.</span></span>

## <a name="azure-notebooks"></a><span data-ttu-id="35f08-202">Azure Notebooks</span><span class="sxs-lookup"><span data-stu-id="35f08-202">Azure Notebooks</span></span>

<span data-ttu-id="35f08-203">對於偏好使用 Notebook 來將程式碼帶入 Azure 的 R 開發人員來說，[Azure Notebook](https://notebooks.azure.com) 是低成本、低摩擦的方法。</span><span class="sxs-lookup"><span data-stu-id="35f08-203">[Azure Notebooks](https://notebooks.azure.com) is a low-cost, low-friction method for R developers who prefer working with notebooks to bring their code to Azure.</span></span>  <span data-ttu-id="35f08-204">它是一項免費服務，可供任何人在瀏覽器中使用 [Jupyter](https://jupyter.org/) 來開發和執行程式碼，Jupyter 是開放原始碼專案，可將 Markdown Prose、可執行程式碼和圖形合併至單一畫布。</span><span class="sxs-lookup"><span data-stu-id="35f08-204">It is a free service for anyone to develop and run code in their browser using [Jupyter](https://jupyter.org/), which is an open-source project that enables combing markdown prose, executable code, and graphics onto a single canvas.</span></span>

<span data-ttu-id="35f08-205">Azure Notebooks 的免費服務層是適用於小規模專案的可行選項，因為它將每個 Notebook 的程序限制為 4 GB 記憶體和 1GB 資料集。</span><span class="sxs-lookup"><span data-stu-id="35f08-205">The free service tier of Azure Notebooks is a viable option for small-scale projects, as it limits each notebook's process to 4GB of memory and 1GB data sets.</span></span> <span data-ttu-id="35f08-206">不過，如果您需要超過這些限制的計算和資料功能，您可以在資料科學虛擬機器執行個體中執行 Notebook。</span><span class="sxs-lookup"><span data-stu-id="35f08-206">If you need compute and data power beyond these limitations, however, you can run notebooks in a Data Science Virtual Machine instance.</span></span> <span data-ttu-id="35f08-207">如需詳細資訊，請參閱[管理和設定 Azure Notebooks 專案 - 計算層](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier)。</span><span class="sxs-lookup"><span data-stu-id="35f08-207">For more information, see [Manage and configure Azure Notebooks projects - Compute tier](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier).</span></span>

## <a name="azure-sql-database"></a><span data-ttu-id="35f08-208">連接字串</span><span class="sxs-lookup"><span data-stu-id="35f08-208">Azure SQL Database</span></span>

<span data-ttu-id="35f08-209">[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 是 Microsoft 的智慧型、完全受控關聯式雲端資料庫服務。</span><span class="sxs-lookup"><span data-stu-id="35f08-209">[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) is Microsoft's intelligent, fully managed relational cloud database service.</span></span>  <span data-ttu-id="35f08-210">它可讓您使用 SQL Server 的完整功能，而不需要麻煩地設定基礎結構。</span><span class="sxs-lookup"><span data-stu-id="35f08-210">It allows you to use the full power of SQL Server without any hassle of setting up the infrastructure.</span></span>  <span data-ttu-id="35f08-211">這包括[SQL Server 中的 Machine Learning 服務](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning?view=sql-server-2017)，而這也是 SQL 的最近新增項目。</span><span class="sxs-lookup"><span data-stu-id="35f08-211">This includes [Machine Learning Services in SQL Server](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning?view=sql-server-2017), which is one of the more recent additions to SQL.</span></span>

<span data-ttu-id="35f08-212">這項功能可提供內嵌的預測性分析和資料科學引擎，其可在 SQL Server 資料庫內以預存程序的形式執行 R 程式碼、以 包含 R 陳述式的 T-SQL 指令碼形式執行 R 程式碼，或以包含 T-SQL 的 R 程式碼形式執行 R 程式碼。</span><span class="sxs-lookup"><span data-stu-id="35f08-212">This feature offers an embedded, predictive analytics and data science engine that can execute R code within a SQL Server database as stored procedures, as T-SQL scripts containing R statements, or as R code containing T-SQL.</span></span>  <span data-ttu-id="35f08-213">您不必從資料庫擷取資料並載入至 R 環境，而是可以將 R 程式碼直接載入到資料庫，並讓它直接與資料一起執行。</span><span class="sxs-lookup"><span data-stu-id="35f08-213">Instead of extracting data from the database and loading it into the R environment, you load your R code directly into the database and let it run right alongside the data.</span></span>

<span data-ttu-id="35f08-214">雖然機器學習服務自 2016 年起就已成為內部部署 SQL Server 的一部分，但對於 Azure SQL Database 來說，這還是相當新的功能。</span><span class="sxs-lookup"><span data-stu-id="35f08-214">While Machine Learning Services has been part of on-premises SQL Server since 2016, it is relatively new to Azure SQL Database.</span></span>  <span data-ttu-id="35f08-215">它目前是[有限預覽版](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#azure-sql-database-roadmap)，但將會持續發展。</span><span class="sxs-lookup"><span data-stu-id="35f08-215">It is currently in [limited preview](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#azure-sql-database-roadmap) but will continue to evolve.</span></span>


### <a name="next-steps"></a><span data-ttu-id="35f08-216">後續步驟</span><span class="sxs-lookup"><span data-stu-id="35f08-216">Next steps</span></span>

* [<span data-ttu-id="35f08-217">在 Azure 上執行 R 程式碼使用 mrsdeploy</span><span class="sxs-lookup"><span data-stu-id="35f08-217">Running your R code on Azure with mrsdeploy</span></span>](https://blog.revolutionanalytics.com/2017/03/running-your-r-code-azure.html)
* [<span data-ttu-id="35f08-218">機器學習服務在雲端中的伺服器</span><span class="sxs-lookup"><span data-stu-id="35f08-218">Machine Learning Server in the Cloud</span></span>](https://docs.microsoft.com/machine-learning-server/install/machine-learning-server-in-the-cloud)
* [<span data-ttu-id="35f08-219">Machine Learning Server 和 Microsoft R 的其他資源</span><span class="sxs-lookup"><span data-stu-id="35f08-219">Additional Resources for Machine Learning Server and Microsoft R</span></span>](https://docs.microsoft.com/machine-learning-server/resources-more)
* <span data-ttu-id="35f08-220">[Azure 上的 R](https://github.com/yueguoguo/r-on-azure) -概述套件、工具和搭配使用 R 與 Azure 的案例研究</span><span class="sxs-lookup"><span data-stu-id="35f08-220">[R on Azure](https://github.com/yueguoguo/r-on-azure) - an overview of packages, tools, and case studies for using R with Azure</span></span>

---

<sub><span data-ttu-id="35f08-221">R 標誌&copy;R Foundation 2016，並可依據的條款[Creative Commons Attribution-sharealike 4.0 國際授權](https://creativecommons.org/licenses/by-sa/4.0/)。</span><span class="sxs-lookup"><span data-stu-id="35f08-221">The R logo is &copy; 2016 The R Foundation and is used under the terms of the [Creative Commons Attribution-ShareAlike 4.0 International license](https://creativecommons.org/licenses/by-sa/4.0/).</span></span></sub>