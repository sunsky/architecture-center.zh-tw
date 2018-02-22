---
title: "大規模機器學習"
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: a92060008f90f43f71869bd1ad251af150b4a9db
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2018
---
# <a name="machine-learning-at-scale"></a><span data-ttu-id="af4eb-102">大規模機器學習</span><span class="sxs-lookup"><span data-stu-id="af4eb-102">Machine learning at scale</span></span>

<span data-ttu-id="af4eb-103">機器學習 (ML) 是可根據數學演算法用來訓練預測模型的技術。</span><span class="sxs-lookup"><span data-stu-id="af4eb-103">Machine learning (ML) is a technique used to train predictive models based on mathematical algorithms.</span></span> <span data-ttu-id="af4eb-104">機器學習服務會分析資料欄位之間的關聯性，以預測未知的值。</span><span class="sxs-lookup"><span data-stu-id="af4eb-104">Machine learning analyzes the relationships between data fields to predict unknown values.</span></span>

<span data-ttu-id="af4eb-105">建立及部署機器學習模型是反覆執行的程序：</span><span class="sxs-lookup"><span data-stu-id="af4eb-105">Creating and deploying a machine learning model is an iterative process:</span></span>

* <span data-ttu-id="af4eb-106">資料科學家探索來源資料，以判斷*特性*與預測*標籤*之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="af4eb-106">Data scientists explore the source data to determine relationships between *features* and predicted *labels*.</span></span>
* <span data-ttu-id="af4eb-107">資料科學家根據適當的演算法訓練和驗證模型，以找出進行預測的最佳模型。</span><span class="sxs-lookup"><span data-stu-id="af4eb-107">The data scientists train and validate models based on appropriate algorithms to find the optimal model for prediction.</span></span>
* <span data-ttu-id="af4eb-108">最佳模型部署到生產環境中，作為 Web 服務或其他封裝函式。</span><span class="sxs-lookup"><span data-stu-id="af4eb-108">The optimal model is deployed into production, as a web service or some other encapsulated function.</span></span>
* <span data-ttu-id="af4eb-109">隨著新資料的收集，模型會定期重新訓練以提升效率。</span><span class="sxs-lookup"><span data-stu-id="af4eb-109">As new data is collected, the model is periodically retrained to improve is effectiveness.</span></span>

<span data-ttu-id="af4eb-110">大規模的機器學習可解決兩個不同的延展性問題。</span><span class="sxs-lookup"><span data-stu-id="af4eb-110">Machine learning at scale addresses two different scalability concerns.</span></span> <span data-ttu-id="af4eb-111">其一是必須根據大型資料集來訓練模型，且模型必須要有叢集的相應放大功能，才能定型。</span><span class="sxs-lookup"><span data-stu-id="af4eb-111">The first is training a model against large data sets that require the scale-out capabilities of a cluster to train.</span></span> <span data-ttu-id="af4eb-112">第二個重點是，必須以可調整以符合使用端應用程式需求的方式，將經過學習的模型操作化。</span><span class="sxs-lookup"><span data-stu-id="af4eb-112">The second centers is operationalizating the learned model in a way that can scale to meet the demands of the applications that consume it.</span></span> <span data-ttu-id="af4eb-113">將預測功能部署為後續可相應放大的 Web 服務，通常就可達成此目的。</span><span class="sxs-lookup"><span data-stu-id="af4eb-113">Typically this is accomplished by deploying the predictive capabilities as a web service that can then be scaled out.</span></span>

<span data-ttu-id="af4eb-114">大規模的機器學習具有可產生強大預測功能的優點，因為較理想的模型通常產生自較多的資料。</span><span class="sxs-lookup"><span data-stu-id="af4eb-114">Machine learning at scale has the benefit that it can produce powerful, predictive capabilities because better models typically result from more data.</span></span> <span data-ttu-id="af4eb-115">模型定型之後，可以部署為無狀態、高效能，並且可相應放大的 Web 服務。</span><span class="sxs-lookup"><span data-stu-id="af4eb-115">Once a model is trained, it can be deployed as a stateless, highly-performant, scale-out web service.</span></span> 

## <a name="model-preparation-and-training"></a><span data-ttu-id="af4eb-116">模型的準備和訓練</span><span class="sxs-lookup"><span data-stu-id="af4eb-116">Model preparation and training</span></span>

<span data-ttu-id="af4eb-117">在模型的準備和訓練階段，資料科學家會使用 Python 和 R 之類的語言以互動方式探索資料，藉以：</span><span class="sxs-lookup"><span data-stu-id="af4eb-117">During the model preparation and training phase, data scientists explore the data interactively using languages like Python and R to:</span></span>

* <span data-ttu-id="af4eb-118">從大量資料存放區中擷取範例。</span><span class="sxs-lookup"><span data-stu-id="af4eb-118">Extract samples from high volume data stores.</span></span>
* <span data-ttu-id="af4eb-119">尋找並處理極端值、重複項目和遺漏值，以清理資料。</span><span class="sxs-lookup"><span data-stu-id="af4eb-119">Find and treat outliers, duplicates, and missing values to clean the data.</span></span>
* <span data-ttu-id="af4eb-120">透過統計分析和視覺效果判斷資料中的相互關聯和關聯性。</span><span class="sxs-lookup"><span data-stu-id="af4eb-120">Determine correlations and relationships in the data through statistical analysis and visualization.</span></span>
* <span data-ttu-id="af4eb-121">產生可讓統計關聯性的預測更為精準的新計算功能。</span><span class="sxs-lookup"><span data-stu-id="af4eb-121">Generate new calculated features that improve the predictiveness of statistical relationships.</span></span>
* <span data-ttu-id="af4eb-122">根據預測演算法訓練 ML 模型。</span><span class="sxs-lookup"><span data-stu-id="af4eb-122">Train ML models based on predictive algorithms.</span></span>
* <span data-ttu-id="af4eb-123">使用在訓練期間保留的資料，驗證經過訓練的模型。</span><span class="sxs-lookup"><span data-stu-id="af4eb-123">Validate trained models using data that was withheld during training.</span></span>

<span data-ttu-id="af4eb-124">為了支援此互動式分析和模型化階段，資料平台必須讓資料科學家能夠使用不同的工具來探索資料。</span><span class="sxs-lookup"><span data-stu-id="af4eb-124">To support this interactive analysis and modeling phase, the data platform must enable data scientists to explore data using a variety of tools.</span></span> <span data-ttu-id="af4eb-125">此外，要訓練複雜的機器學習模型，可能需要密集頻繁地處理大量資料，因此必須要有足夠的資源將模型訓練相應放大。</span><span class="sxs-lookup"><span data-stu-id="af4eb-125">Additionally, the training of a complex machine learning model can require a lot of intensive processing of high volumes of data, so sufficient resources for scaling out the model training is essential.</span></span>

## <a name="model-deployment-and-consumption"></a><span data-ttu-id="af4eb-126">模型的部署和使用</span><span class="sxs-lookup"><span data-stu-id="af4eb-126">Model deployment and consumption</span></span>

<span data-ttu-id="af4eb-127">模型備妥而可供部署時，可以封裝為 Web 服務並部署到雲端中，或部署到企業 ML 執行環境中。</span><span class="sxs-lookup"><span data-stu-id="af4eb-127">When a model is ready to be deployed, it can be encapsulated as a web service and deployed in the cloud, to an edge device, or within an enterprise ML execution environment.</span></span> <span data-ttu-id="af4eb-128">此部署程序稱為操作化。</span><span class="sxs-lookup"><span data-stu-id="af4eb-128">This deployment process is referred to as operationalization.</span></span>

## <a name="challenges"></a><span data-ttu-id="af4eb-129">挑戰</span><span class="sxs-lookup"><span data-stu-id="af4eb-129">Challenges</span></span>

<span data-ttu-id="af4eb-130">大規模的機器學習會產生若干挑戰：</span><span class="sxs-lookup"><span data-stu-id="af4eb-130">Machine learning at scale produces a few challenges:</span></span>

- <span data-ttu-id="af4eb-131">您通常需要以大量資料來訓練模型，特別是深度學習模型。</span><span class="sxs-lookup"><span data-stu-id="af4eb-131">You typically need a lot of data to train a model, especially for deep learning models.</span></span>
- <span data-ttu-id="af4eb-132">備妥這些巨量資料集，是您展開模型訓練的先決條件之一。</span><span class="sxs-lookup"><span data-stu-id="af4eb-132">You need to prepare these big data sets before you can even begin training your model.</span></span>
- <span data-ttu-id="af4eb-133">模型訓練階段必須存取巨量資料存放區。</span><span class="sxs-lookup"><span data-stu-id="af4eb-133">The model training phase must access the big data stores.</span></span> <span data-ttu-id="af4eb-134">使用資料準備工作所使用的相同巨量資料叢集 (例如 Spark) 來執行模型訓練，是常見的做法。</span><span class="sxs-lookup"><span data-stu-id="af4eb-134">It's common to perform the model training using the same big data cluster, such as Spark, that is used for data preparation.</span></span> 
- <span data-ttu-id="af4eb-135">對於深度學習之類的案例，您不僅需要可讓您將 CPU 相應放大的叢集，您的叢集也必須包含已啟用 GPU 的節點。</span><span class="sxs-lookup"><span data-stu-id="af4eb-135">For scenarios such as deep learning, not only will you need a cluster that can provide you scale out on CPUs, but your cluster will need to consist of GPU-enabled nodes.</span></span>

## <a name="machine-learning-at-scale-in-azure"></a><span data-ttu-id="af4eb-136">Azure 中的大規模機器學習</span><span class="sxs-lookup"><span data-stu-id="af4eb-136">Machine learning at scale in Azure</span></span>

<span data-ttu-id="af4eb-137">在決定要將哪些 ML 服務用於訓練和操作化之前，請考量您是需要從頭訓練模型，還是有預先建置的模型可以滿足您的需求。</span><span class="sxs-lookup"><span data-stu-id="af4eb-137">Before deciding which ML services to use in training and operationalization, consider whether you need to train a model at all, or if a prebuilt model can meet your requirements.</span></span> <span data-ttu-id="af4eb-138">在許多情況下，只要呼叫 Web 服務，或使用 ML 程式庫載入現有的模型，即可使用預先建置的模型。</span><span class="sxs-lookup"><span data-stu-id="af4eb-138">In many cases, using a prebuilt model is just a matter of calling a web service or using an ML library to load an existing model.</span></span> <span data-ttu-id="af4eb-139">選項包括︰</span><span class="sxs-lookup"><span data-stu-id="af4eb-139">Some options include:</span></span> 

- <span data-ttu-id="af4eb-140">使用 Microsoft 認知服務所提供的 Web 服務。</span><span class="sxs-lookup"><span data-stu-id="af4eb-140">Use the web services provided by Microsoft Cognitive Services.</span></span>
- <span data-ttu-id="af4eb-141">使用認知工具組所提供的預先定型類神經網路模型。</span><span class="sxs-lookup"><span data-stu-id="af4eb-141">Use the pretrained neural network models provided by Cognitive Toolkit.</span></span>
- <span data-ttu-id="af4eb-142">內嵌 iOS 應用程式的核心 ML 所提供的序列化模型。</span><span class="sxs-lookup"><span data-stu-id="af4eb-142">Embed the serialized models provided by Core ML for an iOS apps.</span></span> 

<span data-ttu-id="af4eb-143">如果預先建置的模型不適用於您的資料或案例，您可以使用 Azure 中的選項，包括 Azure Machine Learning、使用 Spark MLlib 和 MMLSpark 的 HDInsight、認知工具組，以及 SQL 機器學習服務。</span><span class="sxs-lookup"><span data-stu-id="af4eb-143">If a prebuilt model does not fit your data or your scenario, options in Azure include Azure Machine Learning, HDInsight with Spark MLlib and MMLSpark, Cognitive Toolkit, and SQL Machine Learning Services.</span></span> <span data-ttu-id="af4eb-144">如果您決定使用自訂模型，您必須設計包含模型訓練和操作化的管線。</span><span class="sxs-lookup"><span data-stu-id="af4eb-144">If you decide to use a custom model, you must design a pipeline that includes model training and operationalization.</span></span> 

![Azure 中的模型選項](./images/machine-learning-model-training-and-deployment.png)

<span data-ttu-id="af4eb-146">如需 Azure 中的 ML 適用的技術選項清單，請參閱下列主題：</span><span class="sxs-lookup"><span data-stu-id="af4eb-146">For a list of technology choices for ML in Azure, see the following topics:</span></span>

- [<span data-ttu-id="af4eb-147">選擇認知服務技術</span><span class="sxs-lookup"><span data-stu-id="af4eb-147">Choosing a cognitive services technology</span></span>](../technology-choices/cognitive-services.md)
- [<span data-ttu-id="af4eb-148">選擇機器學習技術</span><span class="sxs-lookup"><span data-stu-id="af4eb-148">Choosing a machine learning technology</span></span>](../technology-choices/data-science-and-machine-learning.md)
- [<span data-ttu-id="af4eb-149">選擇自然語言處理技術</span><span class="sxs-lookup"><span data-stu-id="af4eb-149">Choosing a natural language processing technology</span></span>](../technology-choices/natural-language-processing.md)
