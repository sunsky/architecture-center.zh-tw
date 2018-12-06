---
title: 選擇機器學習技術
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.openlocfilehash: 507d343ca7bc0a3f161602c50e6b96e921276374
ms.sourcegitcommit: e7e0e0282fa93f0063da3b57128ade395a9c1ef9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2018
ms.locfileid: "52902354"
---
# <a name="choosing-a-machine-learning-technology-in-azure"></a><span data-ttu-id="6c5ff-102">在 Azure 中選擇機器學習技術</span><span class="sxs-lookup"><span data-stu-id="6c5ff-102">Choosing a machine learning technology in Azure</span></span>

<span data-ttu-id="6c5ff-103">資料科學和機器學習是一項通常由資料科學家執行的工作負載。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-103">Data science and machine learning is a workload that is usually undertaken by data scientists.</span></span> <span data-ttu-id="6c5ff-104">它需要專業工具，其中有許多是專為互動式資料探索與模型工作類型所設計，且資料科學家必須執行這些工作。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-104">It requires specialist tools, many of which are designed specifically for the type of interactive data exploration and modeling tasks that a data scientist must perform.</span></span>

<span data-ttu-id="6c5ff-105">機器學習解決方案是反覆組建的，而且擁有兩個不同的階段：</span><span class="sxs-lookup"><span data-stu-id="6c5ff-105">Machine learning solutions are built iteratively, and have two distinct phases:</span></span>
* <span data-ttu-id="6c5ff-106">資料準備與模型建立。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-106">Data preparation and modeling.</span></span>
* <span data-ttu-id="6c5ff-107">部署和使用預測服務。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-107">Deployment and consumption of predictive services.</span></span>

## <a name="tools-and-services-for-data-preparation-and-modeling"></a><span data-ttu-id="6c5ff-108">資料準備和模型建立的工具與服務</span><span class="sxs-lookup"><span data-stu-id="6c5ff-108">Tools and services for data preparation and modeling</span></span>
<span data-ttu-id="6c5ff-109">資料科學家通常想要使用 Python 或 R 所撰寫的自訂程式碼來使用資料。資料科學家通常會以互動方式使用此程式碼來查詢及探索資料、產生視覺效果和統計資料，以協助判斷與其中的關聯性。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-109">Data scientists typically prefer to work with data using custom code written in Python or R. This code is generally run interactively, with the data scientists using it to query and explore the data, generating visualizations and statistics to help determine the relationships with it.</span></span> <span data-ttu-id="6c5ff-110">有許多資料科學家可以使用的 R 和 Python 互動式環境。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-110">There are many interactive environments for R and Python that data scientists can use.</span></span> <span data-ttu-id="6c5ff-111">特別受歡迎的是提供瀏覽器型介面的 **Jupyter Notebook**，它可讓資料科學家建立包含 R 或 Python 程式碼和 Markdown 文字的「筆記本」檔案。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-111">A particular favorite is **Jupyter Notebooks** that provides a browser-based shell that enables data scientists to create *notebook* files that contain R or Python code and markdown text.</span></span> <span data-ttu-id="6c5ff-112">這是可進行共同作業的有效方式，透過共用和記錄程式碼來產生一份文件。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-112">This is an effective way to collaborate by sharing and documenting code and results in a single document.</span></span>

<span data-ttu-id="6c5ff-113">其他常用的工具包括：</span><span class="sxs-lookup"><span data-stu-id="6c5ff-113">Other commonly used tools include:</span></span>
* <span data-ttu-id="6c5ff-114">**Spyder**：隨著 Anaconda Python 發行版本提供的 Python 互動式開發環境 (IDE)。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-114">**Spyder**: The interactive development environment (IDE) for Python provided with the Anaconda Python distribution.</span></span>
* <span data-ttu-id="6c5ff-115">**R Studio**：R 程式設計語言的 IDE。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-115">**R Studio**: An IDE for the R programming language.</span></span>
* <span data-ttu-id="6c5ff-116">**Visual Studio Code**：輕量型的跨平台編碼環境，其支援 Python，通常也用於機器學習服務和 AI 開發的架構。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-116">**Visual Studio Code**: A lightweight, cross-platform coding environment that supports Python as well as commonly used frameworks for machine learning and AI development.</span></span>

<span data-ttu-id="6c5ff-117">除了這些工具外，資料科學家可運用 Azure 服務來簡化程式碼與模型管理。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-117">In addition to these tools, data scientists can leverage Azure services to simplify code and model management.</span></span>

### <a name="azure-notebooks"></a><span data-ttu-id="6c5ff-118">Azure Notebooks</span><span class="sxs-lookup"><span data-stu-id="6c5ff-118">Azure Notebooks</span></span>
<span data-ttu-id="6c5ff-119">Azure Notebook 是線上 Jupyter Notebook 服務，可讓資料科學家建立、執行和共用雲端型程式庫中的 Jupyter Notebook。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-119">Azure Notebooks is an online Jupyter Notebooks service that enables data scientists to create, run, and share Jupyter Notebooks in cloud-based libraries.</span></span>

<span data-ttu-id="6c5ff-120">主要優點：</span><span class="sxs-lookup"><span data-stu-id="6c5ff-120">Key benefits:</span></span>

* <span data-ttu-id="6c5ff-121">免費服務 &mdash; 不需要 Azure 訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-121">Free service&mdash;no Azure subscription required.</span></span>
* <span data-ttu-id="6c5ff-122">不需要在本機安裝 Jupyter 和支援的 R 或 Python 發行版本 &mdash; 直接使用瀏覽器即可。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-122">No need to install Jupyter and the supporting R or Python distributions locally&mdash;just use a browser.</span></span>
* <span data-ttu-id="6c5ff-123">管理您自己的線上程式庫，並從任何裝置存取它們。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-123">Manage your own online libraries and access them from any device.</span></span>
* <span data-ttu-id="6c5ff-124">與共同作業者共用您的筆記本。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-124">Share your notebooks with collaborators.</span></span>

<span data-ttu-id="6c5ff-125">考量：</span><span class="sxs-lookup"><span data-stu-id="6c5ff-125">Considerations:</span></span>

* <span data-ttu-id="6c5ff-126">您在離線時將無法存取您的筆記本。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-126">You will be unable to access your notebooks when offline.</span></span>
* <span data-ttu-id="6c5ff-127">免費筆記本服務的有限處理功能可能不足以訓練大型或複雜的模型。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-127">Limited processing capabilities of the free notebook service may not be enough to train large or complex models.</span></span>

### <a name="data-science-virtual-machine"></a><span data-ttu-id="6c5ff-128">資料科學虛擬機器</span><span class="sxs-lookup"><span data-stu-id="6c5ff-128">Data science virtual machine</span></span>
<span data-ttu-id="6c5ff-129">資料科學虛擬機器是包含工具和架構的 Azure 虛擬機器映像，通常由資料科學家使用，包括 R、Python、Jupyter Notebook、Visual Studio Code，以及適用於機器學習模型的程式庫 (如 Microsoft Cognitive Toolkit)。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-129">The data science virtual machine is an Azure virtual machine image that includes the tools and frameworks commonly used by data scientists, including R, Python, Jupyter Notebooks, Visual Studio Code, and libraries for machine learning modeling such as the Microsoft Cognitive Toolkit.</span></span> <span data-ttu-id="6c5ff-130">這些工具可能很複雜並耗用很多安裝時間，且包含許多通常會導致版本管理問題的相依性。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-130">These tools can be complex and time consuming to install, and contain many interdependencies that often lead to version management issues.</span></span> <span data-ttu-id="6c5ff-131">具有預先安裝的映像，可減少資料科學家花在疑難排解環境問題的時間，進而將焦點放在其需要執行的資料探索和模型工作。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-131">Having a preinstalled image can reduce the time data scientists spend troubleshooting environment issues, allowing them to focus on the data exploration and modeling tasks they need to perform.</span></span>

<span data-ttu-id="6c5ff-132">主要優點：</span><span class="sxs-lookup"><span data-stu-id="6c5ff-132">Key benefits:</span></span>
* <span data-ttu-id="6c5ff-133">減少資料科學工具及架構的安裝、管理和疑難排解時間。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-133">Reduced time to install, manage, and troubleshoot data science tools and frameworks.</span></span>
* <span data-ttu-id="6c5ff-134">包含所有常用的工具和架構的最新版本。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-134">The latest versions of all commonly used tools and frameworks are included.</span></span>
* <span data-ttu-id="6c5ff-135">虛擬機器選項包括可高度擴充的影像，具備適用於大量資料模型化的 GPU 功能。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-135">Virtual machine options include highly scalable images with GPU capabilities for intensive data modeling.</span></span>

<span data-ttu-id="6c5ff-136">考量：</span><span class="sxs-lookup"><span data-stu-id="6c5ff-136">Considerations:</span></span>
* <span data-ttu-id="6c5ff-137">離線時無法存取虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-137">The virtual machine cannot be accessed when offline.</span></span>
* <span data-ttu-id="6c5ff-138">執行虛擬機器會衍生 Azure 費用，因此您必須非常小心，讓它只在需要時執行。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-138">Running a virtual machine incurs Azure charges, so you must be careful to have it running only when required.</span></span>

### <a name="azure-machine-learning"></a><span data-ttu-id="6c5ff-139">Azure Machine Learning</span><span class="sxs-lookup"><span data-stu-id="6c5ff-139">Azure Machine Learning</span></span>

<span data-ttu-id="6c5ff-140">Azure Machine Learning 是管理機器學習實驗和模型的雲端型服務。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-140">Azure Machine Learning is a cloud-based service for managing machine learning experiments and models.</span></span> <span data-ttu-id="6c5ff-141">它包含會追蹤資料準備和模型訓練指令碼的實驗服務，可保留所有執行記錄，以便在反覆項目中比較模型效能。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-141">It includes an experimentation service that tracks data preparation and modeling training scripts, maintaining a history of all executions so you can compare model performance across iterations.</span></span> <span data-ttu-id="6c5ff-142">名為 Azure Machine Learning Workbench 的跨平台用戶端工具提供了指令碼管理與記錄的中央介面，同時仍可讓資料科學家使用其選擇的工具 (例如 Jupyter Notebook 或 Visual Studio Code) 建立指令碼。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-142">A cross-platform client tool named Azure Machine Learning Workbench provides a central interface for script management and history, while still enabling data scientists to create scripts in their tool of choice, such as Jupyter Notebooks or Visual Studio Code.</span></span>

<span data-ttu-id="6c5ff-143">從 Azure Machine Learning Workbench 中，您可以使用互動式資料準備工具來簡化常見的資料轉換工作，並可設定指令碼執行環境，以便在本機、可擴充的 Docker 容器，或 Spark 中執行模型訓練指令碼。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-143">From Azure Machine Learning Workbench, you can use the interactive data preparation tools to simplify common data transformation tasks, and you can configure the script execution environment to run model training scripts locally, in a scalable Docker container, or in Spark.</span></span>

<span data-ttu-id="6c5ff-144">準備好部署模型時，使用 Workbench 環境來封裝模型，並將它作為 Web 服務部署至 Docker 容器、Azure HDinsight 上的 Spark、Microsoft Machine Learning Server 或 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-144">When you are ready to deploy your model, use the Workbench environment to package the model and deploy it as a web service to a Docker container, Spark on Azure HDinsight, Microsoft Machine Learning Server, or SQL Server.</span></span> <span data-ttu-id="6c5ff-145">接著，Azure Machine Learning 模型管理服務可讓您追蹤和管理雲端中、邊緣裝置上，或整個企業的模型部署。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-145">The Azure Machine Learning Model Management service then enables you to track and manage model deployments in the cloud, on edge devices, or across the enterprise.</span></span>

<span data-ttu-id="6c5ff-146">主要優點：</span><span class="sxs-lookup"><span data-stu-id="6c5ff-146">Key benefits:</span></span>

* <span data-ttu-id="6c5ff-147">指令碼的中央管理和執行歷程記錄，讓模型版本間的比較變得簡單。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-147">Central management of scripts and run history, making it easy to compare model versions.</span></span>
* <span data-ttu-id="6c5ff-148">透過視覺化編輯器進行的互動式資料轉換。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-148">Interactive data transformation through a visual editor.</span></span>
* <span data-ttu-id="6c5ff-149">輕鬆部署及管理雲端或邊緣裝置的模型。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-149">Easy deployment and management of models to the cloud or edge devices.</span></span>

<span data-ttu-id="6c5ff-150">考量：</span><span class="sxs-lookup"><span data-stu-id="6c5ff-150">Considerations:</span></span>
* <span data-ttu-id="6c5ff-151">需要對模型管理模型和工作台工具環境有些熟悉。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-151">Requires some familiarity with the model management model and Workbench tool environment.</span></span>

### <a name="azure-batch-ai"></a><span data-ttu-id="6c5ff-152">Azure Batch AI</span><span class="sxs-lookup"><span data-stu-id="6c5ff-152">Azure Batch AI</span></span>

<span data-ttu-id="6c5ff-153">Azure Batch AI 可讓您平行執行您的機器學習實驗，並跨虛擬機器 (配備 GPU) 叢集執行大規模的模型訓練。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-153">Azure Batch AI enables you to run your machine learning experiments in parallel, and perform model training at scale across a cluster of virtual machines with GPUs.</span></span> <span data-ttu-id="6c5ff-154">Batch AI 訓練可讓您相應放大叢集化 GPU 之間的深入學習作業，使用 Cognitive Toolkit、Caffe、Chainer 和 TensorFlow 等架構。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-154">Batch AI training enables you to scale out deep learning jobs across clustered GPUs, using frameworks such as Cognitive Toolkit, Caffe, Chainer, and TensorFlow.</span></span> 

<span data-ttu-id="6c5ff-155">您可以使用 Azure Machine Learning 模型管理來取得 Batch AI 訓練的模型，進而部署、管理和監視模型。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-155">Azure Machine Learning Model Management can be used to take models from Batch AI training to deploy, manage, and monitor them.</span></span> 

### <a name="azure-machine-learning-studio"></a><span data-ttu-id="6c5ff-156">Azure Machine Learning Studio</span><span class="sxs-lookup"><span data-stu-id="6c5ff-156">Azure Machine Learning Studio</span></span>

<span data-ttu-id="6c5ff-157">Azure Machine Learning Studio 是雲端式視覺開發環境，用來建立資料實驗、訓練機器學習模型，並在 Azure 中將它們發佈為 Web 服務。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-157">Azure Machine Learning Studio is a cloud-based, visual development environment for creating data experiments, training machine learning models, and publishing them as web services in Azure.</span></span> <span data-ttu-id="6c5ff-158">其視覺化的拖放介面可讓資料科學家和進階使用者快速建立機器學習解決方案，同時支援自訂的 R 和 Python 邏輯和許多已建立的統計演算法和技術 (適用於機器學習模型化工作)，以及 Jupyter Notebook 的內建支援。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-158">Its visual drag-and-drop interface lets data scientists and power users create machine learning solutions quickly, while supporting custom R and Python logic, a wide range of established statistical algorithms and techniques for machine learning modeling tasks, and built-in support for Jupyter Notebooks.</span></span>

<span data-ttu-id="6c5ff-159">主要優點：</span><span class="sxs-lookup"><span data-stu-id="6c5ff-159">Key benefits:</span></span>

* <span data-ttu-id="6c5ff-160">互動式視覺介面可使用最少程式碼製作機器學習模型。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-160">Interactive visual interface enables machine learning modeling with minimal code.</span></span>
* <span data-ttu-id="6c5ff-161">內建用於資料探索的 Jupyter Notebook。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-161">Built-in Jupyter Notebooks for data exploration.</span></span>
* <span data-ttu-id="6c5ff-162">直接將訓練模型部署為 Azure Web 服務。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-162">Direct deployment of trained models as Azure web services.</span></span>

<span data-ttu-id="6c5ff-163">考量：</span><span class="sxs-lookup"><span data-stu-id="6c5ff-163">Considerations:</span></span>

* <span data-ttu-id="6c5ff-164">有限延展性。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-164">Limited scalability.</span></span> <span data-ttu-id="6c5ff-165">訓練資料集的大小上限為 10 GB。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-165">The maximum size of a training dataset is 10 GB.</span></span>
* <span data-ttu-id="6c5ff-166">線上專用。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-166">Online only.</span></span> <span data-ttu-id="6c5ff-167">無離線開發環境。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-167">No offline development environment.</span></span>

## <a name="tools-and-services-for-deploying-machine-learning-models"></a><span data-ttu-id="6c5ff-168">部署機器學習模型的工具和服務</span><span class="sxs-lookup"><span data-stu-id="6c5ff-168">Tools and services for deploying machine learning models</span></span>

<span data-ttu-id="6c5ff-169">資料科學家建立機器學習模型之後，您通常必須部署它，然後在應用程式或其他資料流程中使用它。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-169">After a data scientist has created a machine learning model, you will typically need to deploy it and consume it from applications or in other data flows.</span></span> <span data-ttu-id="6c5ff-170">機器學習模型有許多潛在的部署目標。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-170">There are a number of potential deployment targets for machine learning models.</span></span>

### <a name="spark-on-azure-hdinsight"></a><span data-ttu-id="6c5ff-171">Azure HDInsight 上的 Spark</span><span class="sxs-lookup"><span data-stu-id="6c5ff-171">Spark on Azure HDInsight</span></span>

<span data-ttu-id="6c5ff-172">Apache Spark 包括機器學習模型的 Spark MLlib、架構和程式庫。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-172">Apache Spark includes Spark MLlib, a framework and library for machine learning models.</span></span> <span data-ttu-id="6c5ff-173">Spark 的 Microsoft Machine Learning 程式庫 (MMLSpark) 也提供深入的學習演算法，可支援 Spark 中的預測模型。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-173">The Microsoft Machine Learning library for Spark (MMLSpark) also provides deep learning algorithm support for predictive models in Spark.</span></span>

<span data-ttu-id="6c5ff-174">主要優點：</span><span class="sxs-lookup"><span data-stu-id="6c5ff-174">Key benefits:</span></span>

* <span data-ttu-id="6c5ff-175">Spark 是分散式的平台，為大量機器學習程序提供高延展性。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-175">Spark is a distributed platform that offers high scalability for high-volume machine learning processes.</span></span>
* <span data-ttu-id="6c5ff-176">您可以從 Azure Machine Learning Workbench 將模型直接部署到 HDinsight 的 Spark，並使用 Azure Machine Learning 模型管理服務來管理模型。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-176">You can deploy models directly to Spark in HDinsight from Azure Machine Learning Workbench, and manage them using the Azure Machine Learning Model Management service.</span></span>

<span data-ttu-id="6c5ff-177">考量：</span><span class="sxs-lookup"><span data-stu-id="6c5ff-177">Considerations:</span></span>

* <span data-ttu-id="6c5ff-178">Spark 在 HDinsght 叢集中執行的整個時間會產生費用。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-178">Spark runs in an HDinsght cluster that incurs charges the whole time it is running.</span></span> <span data-ttu-id="6c5ff-179">如果只是偶爾使用機器學習服務，這可能會導致不必要的成本。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-179">If the machine learning service will only be used occasionally, this may result in unnecessary costs.</span></span>

### <a name="azure-databricks"></a><span data-ttu-id="6c5ff-180">Azure Databricks</span><span class="sxs-lookup"><span data-stu-id="6c5ff-180">Azure Databricks</span></span>

<span data-ttu-id="6c5ff-181">[Azure Databricks](/azure/azure-databricks/) 是 Apache Spark 型分析平台。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-181">[Azure Databricks](/azure/azure-databricks/) is an Apache Spark-based analytics platform.</span></span> <span data-ttu-id="6c5ff-182">您可以將它視為「Spark 即服務」。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-182">You can think of it as "Spark as a service."</span></span> <span data-ttu-id="6c5ff-183">它是在 Azure 平台上使用 Spark 的最簡單方式。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-183">It's the easiest way to use Spark on the Azure platform.</span></span> <span data-ttu-id="6c5ff-184">對於機器學習，您可以使用 [MLFlow](https://www.mlflow.org/)、[Databricks Runtime ML](https://docs.azuredatabricks.net/user-guide/clusters/mlruntime.html)、Apache Spark MLlib 和其他。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-184">For machine learning, you can use [MLFlow](https://www.mlflow.org/), [Databricks Runtime ML](https://docs.azuredatabricks.net/user-guide/clusters/mlruntime.html), Apache Spark MLlib, and others.</span></span> <span data-ttu-id="6c5ff-185">如需詳細資訊，請參閱 [Azure Databricks：Machine Learning](https://docs.azuredatabricks.net/spark/latest/mllib/index.html)。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-185">For more information, see [Azure Databricks: Machine Learning](https://docs.azuredatabricks.net/spark/latest/mllib/index.html).</span></span> 

### <a name="web-service-in-a-container"></a><span data-ttu-id="6c5ff-186">容器中的 Web 服務</span><span class="sxs-lookup"><span data-stu-id="6c5ff-186">Web service in a container</span></span>

<span data-ttu-id="6c5ff-187">您可以在 Docker 容器中將機器學習模型部署為 Python Web 服務。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-187">You can deploy a machine learning model as a Python web service in a Docker container.</span></span> <span data-ttu-id="6c5ff-188">您可以將模型部署到 Azure 或邊緣裝置，它可在本機搭配使用其操作的資料。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-188">You can deploy the model to Azure or to an edge device, where it can be used locally with the data on which it operates.</span></span>

<span data-ttu-id="6c5ff-189">主要優點：</span><span class="sxs-lookup"><span data-stu-id="6c5ff-189">Key Benefits:</span></span>

* <span data-ttu-id="6c5ff-190">容器是輕量型且通常符合成本效益的有效方法，可用來封裝及部署服務。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-190">Containers are a lightweight and generally cost effective way to package and deploy services.</span></span>
* <span data-ttu-id="6c5ff-191">部署到邊緣裝置的能力可讓您將預測邏輯移至更貼近資料。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-191">The ability to deploy to an edge device enables you to move your predictive logic closer to the data.</span></span>
* <span data-ttu-id="6c5ff-192">您可以從 Azure Machine Learning Workbench 直接部署到容器。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-192">You can deploy to a container directly from Azure Machine Learning Workbench.</span></span>

<span data-ttu-id="6c5ff-193">考量：</span><span class="sxs-lookup"><span data-stu-id="6c5ff-193">Considerations:</span></span>

* <span data-ttu-id="6c5ff-194">這種部署模型是以 Docker 容器為基礎，因此您應該先熟悉這項技術再以此方式部署 Web 服務。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-194">This deployment model is based on Docker containers, so you should be familiar with this technology before deploying a web service this way.</span></span>

### <a name="microsoft-machine-learning-server"></a><span data-ttu-id="6c5ff-195">Microsoft Machine Learning Server</span><span class="sxs-lookup"><span data-stu-id="6c5ff-195">Microsoft Machine Learning Server</span></span>

<span data-ttu-id="6c5ff-196">機器學習伺服器 (先前稱為 Microsoft R Server) 是 R 和 Python 程式碼的可擴充平台，特別針對機器學習案例所設計。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-196">Machine Learning Server (formerly Microsoft R Server) is a scalable platform for R and Python code, specifically designed for machine learning scenarios.</span></span>

<span data-ttu-id="6c5ff-197">主要優點：</span><span class="sxs-lookup"><span data-stu-id="6c5ff-197">Key benefits:</span></span>

* <span data-ttu-id="6c5ff-198">高延展性。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-198">High scalability.</span></span>
* <span data-ttu-id="6c5ff-199">直接從 Azure Machine Learning Workbench 部署。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-199">Direct deployment from Azure Machine Learning Workbench.</span></span>

<span data-ttu-id="6c5ff-200">考量：</span><span class="sxs-lookup"><span data-stu-id="6c5ff-200">Considerations:</span></span>

* <span data-ttu-id="6c5ff-201">您需要在企業中部署和管理 Machine Learning Server。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-201">You need to deploy and manage Machine Learning Server in your enterprise.</span></span>

### <a name="microsoft-sql-server"></a><span data-ttu-id="6c5ff-202">連接字串</span><span class="sxs-lookup"><span data-stu-id="6c5ff-202">Microsoft SQL Server</span></span>

<span data-ttu-id="6c5ff-203">Microsoft SQL Server 本質上就支援 R 和 Python，可讓您將這些語言內建的機器學習模型封裝為資料庫中的 Transact-SQL 函式。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-203">Microsoft SQL Server supports R and Python natively, enabling you to encapsulate machine learning models built in these languages as Transact-SQL functions in a database.</span></span>

<span data-ttu-id="6c5ff-204">主要優點：</span><span class="sxs-lookup"><span data-stu-id="6c5ff-204">Key benefits:</span></span>

* <span data-ttu-id="6c5ff-205">在資料庫函式中封裝預測邏輯，可讓函式輕鬆加入資料層邏輯中。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-205">Encapsulate predictive logic in a database function, making it easy to include in data-tier logic.</span></span>

<span data-ttu-id="6c5ff-206">考量：</span><span class="sxs-lookup"><span data-stu-id="6c5ff-206">Considerations:</span></span>

* <span data-ttu-id="6c5ff-207">採用 SQL Server 資料庫作為應用程式的資料層。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-207">Assumes a SQL Server database as the data tier for your application.</span></span>

### <a name="azure-machine-learning-web-service"></a><span data-ttu-id="6c5ff-208">Azure Machine Learning Web 服務</span><span class="sxs-lookup"><span data-stu-id="6c5ff-208">Azure Machine Learning web service</span></span>

<span data-ttu-id="6c5ff-209">當您使用 Azure Machine Learning Studio 建立機器學習模型時，可將其部署為 Web 服務。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-209">When you create a machine learning model using Azure Machine Learning Studio, you can deploy it as a web service.</span></span> <span data-ttu-id="6c5ff-210">接著從能夠以 HTTP 通訊的任何用戶端應用程式中，透過 REST 介面使用此服務。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-210">This can then be consumed through a REST interface from any client applications capable of communicating by HTTP.</span></span>

<span data-ttu-id="6c5ff-211">主要優點：</span><span class="sxs-lookup"><span data-stu-id="6c5ff-211">Key benefits:</span></span>

* <span data-ttu-id="6c5ff-212">簡化開發和部署。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-212">Ease of development and deployment.</span></span>
* <span data-ttu-id="6c5ff-213">使用基本監視計量的 Web 服務管理入口網站。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-213">Web service management portal with basic monitoring metrics.</span></span>
* <span data-ttu-id="6c5ff-214">從 Azure Data Lake Analytics、Azure Data Factory 及 Azure 串流分析呼叫 Azure Machine Learning Web 服務的內建支援。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-214">Built-in support for calling Azure Machine Learning web services from Azure Data Lake Analytics, Azure Data Factory, and Azure Stream Analytics.</span></span>

<span data-ttu-id="6c5ff-215">考量：</span><span class="sxs-lookup"><span data-stu-id="6c5ff-215">Considerations:</span></span>

* <span data-ttu-id="6c5ff-216">僅適用於使用 Azure Machine Learning Studio 建置的模型。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-216">Only available for models built using Azure Machine Learning Studio.</span></span>
* <span data-ttu-id="6c5ff-217">僅限 Web 型存取，訓練的模型無法在內部部署或離線時執行。</span><span class="sxs-lookup"><span data-stu-id="6c5ff-217">Web-based access only, trained models cannot run on-premises or offline.</span></span>

