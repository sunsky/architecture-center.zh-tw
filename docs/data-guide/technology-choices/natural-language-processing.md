---
title: 選擇自然語言處理技術
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: 20dc51e661befcc09dd1751b031d445ff2b9fa1a
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54486483"
---
# <a name="choosing-a-natural-language-processing-technology-in-azure"></a><span data-ttu-id="970f3-102">在 Azure 中選擇自然語言處理技術</span><span class="sxs-lookup"><span data-stu-id="970f3-102">Choosing a natural language processing technology in Azure</span></span>

<span data-ttu-id="970f3-103">自由格式文字處理會對包含文字段落的文件執行，通常是為了支援搜尋，但是也用來執行其他自然語言處理 (NLP) 工作，例如情感分析、主題偵測、語言偵測、關鍵片語擷取和文件分類。</span><span class="sxs-lookup"><span data-stu-id="970f3-103">Free-form text processing is performed against documents containing paragraphs of text, typically for the purpose of supporting search, but is also used to perform other natural language processing (NLP) tasks such as sentiment analysis, topic detection, language detection, key phrase extraction, and document categorization.</span></span> <span data-ttu-id="970f3-104">本文主要說明可用來支援 NLP 工作的技術選項。</span><span class="sxs-lookup"><span data-stu-id="970f3-104">This article focuses on the technology choices that act in support of the NLP tasks.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="what-are-your-options-when-choosing-an-nlp-service"></a><span data-ttu-id="970f3-105">選擇 NLP 服務時有哪些選項？</span><span class="sxs-lookup"><span data-stu-id="970f3-105">What are your options when choosing an NLP service?</span></span>

<!-- markdownlint-enable MD026 -->

<span data-ttu-id="970f3-106">在 Azure 中，下列服務皆提供自然語言處理 (NLP) 功能：</span><span class="sxs-lookup"><span data-stu-id="970f3-106">In Azure, the following services provide natural language processing (NLP) capabilities:</span></span>

- [<span data-ttu-id="970f3-107">使用 Spark 和 Spark MLlib 的 Azure HDInsight</span><span class="sxs-lookup"><span data-stu-id="970f3-107">Azure HDInsight with Spark and Spark MLlib</span></span>](/azure/hdinsight/spark/apache-spark-overview)
- [<span data-ttu-id="970f3-108">Azure Databricks</span><span class="sxs-lookup"><span data-stu-id="970f3-108">Azure Databricks</span></span>](/azure/azure-databricks/what-is-azure-databricks)
- [<span data-ttu-id="970f3-109">Microsoft 認知服務</span><span class="sxs-lookup"><span data-stu-id="970f3-109">Microsoft Cognitive Services</span></span>](/azure/cognitive-services/welcome)

## <a name="key-selection-criteria"></a><span data-ttu-id="970f3-110">關鍵選取準則</span><span class="sxs-lookup"><span data-stu-id="970f3-110">Key selection criteria</span></span>

<span data-ttu-id="970f3-111">為了縮小選擇範圍，請先回答下列問題：</span><span class="sxs-lookup"><span data-stu-id="970f3-111">To narrow the choices, start by answering these questions:</span></span>

- <span data-ttu-id="970f3-112">您是否要使用預先建置的模型？</span><span class="sxs-lookup"><span data-stu-id="970f3-112">Do you want to use prebuilt models?</span></span> <span data-ttu-id="970f3-113">如果是，請考慮使用 Microsoft 認知服務所提供的 API。</span><span class="sxs-lookup"><span data-stu-id="970f3-113">If yes, consider using the APIs offered by Microsoft Cognitive Services.</span></span>

- <span data-ttu-id="970f3-114">您是否需要根據大量文字資料訓練自訂模型？</span><span class="sxs-lookup"><span data-stu-id="970f3-114">Do you need to train custom models against a large corpus of text data?</span></span> <span data-ttu-id="970f3-115">如果是，請考慮使用具有 Spark MLlib 和 Spark NLP 的 Azure HDInsight。</span><span class="sxs-lookup"><span data-stu-id="970f3-115">If yes, consider using Azure HDInsight with Spark MLlib and Spark NLP.</span></span>

- <span data-ttu-id="970f3-116">您是否需要 Token 化、詞幹分析、詞形歸併還原和詞彙頻率/反向文件頻率 (TF/IDF) 等低階 NLP 功能？</span><span class="sxs-lookup"><span data-stu-id="970f3-116">Do you need low-level NLP capabilities like tokenization, stemming, lemmatization, and term frequency/inverse document frequency (TF/IDF)?</span></span> <span data-ttu-id="970f3-117">如果是，請考慮使用具有 Spark MLlib 和 Spark NLP 的 Azure HDInsight。</span><span class="sxs-lookup"><span data-stu-id="970f3-117">If yes, consider using Azure HDInsight with Spark MLlib and Spark NLP.</span></span>

- <span data-ttu-id="970f3-118">您是否需要簡單的高階 NLP 功能，例如實體和意圖識別、主題偵測、拼字檢查或情感分析？</span><span class="sxs-lookup"><span data-stu-id="970f3-118">Do you need simple, high-level NLP capabilities like entity and intent identification, topic detection, spell check, or sentiment analysis?</span></span> <span data-ttu-id="970f3-119">如果是，請考慮使用 Microsoft 認知服務所提供的 API。</span><span class="sxs-lookup"><span data-stu-id="970f3-119">If yes, consider using the APIs offered by Microsoft Cognitive Services.</span></span>

## <a name="capability-matrix"></a><span data-ttu-id="970f3-120">功能對照表</span><span class="sxs-lookup"><span data-stu-id="970f3-120">Capability matrix</span></span>

<span data-ttu-id="970f3-121">下表摘要列出各項功能的主要差異。</span><span class="sxs-lookup"><span data-stu-id="970f3-121">The following tables summarize the key differences in capabilities.</span></span>

### <a name="general-capabilities"></a><span data-ttu-id="970f3-122">一般功能</span><span class="sxs-lookup"><span data-stu-id="970f3-122">General capabilities</span></span>

| | <span data-ttu-id="970f3-123">Azure HDInsight</span><span class="sxs-lookup"><span data-stu-id="970f3-123">Azure HDInsight</span></span> | <span data-ttu-id="970f3-124">Microsoft 認知服務</span><span class="sxs-lookup"><span data-stu-id="970f3-124">Microsoft Cognitive Services</span></span> |
| --- | --- | --- |
| <span data-ttu-id="970f3-125">提供預先訓練的模型作為服務</span><span class="sxs-lookup"><span data-stu-id="970f3-125">Provides pretrained models as a service</span></span> | <span data-ttu-id="970f3-126">否</span><span class="sxs-lookup"><span data-stu-id="970f3-126">No</span></span> | <span data-ttu-id="970f3-127">是</span><span class="sxs-lookup"><span data-stu-id="970f3-127">Yes</span></span> |
| <span data-ttu-id="970f3-128">REST API</span><span class="sxs-lookup"><span data-stu-id="970f3-128">REST API</span></span> | <span data-ttu-id="970f3-129">是</span><span class="sxs-lookup"><span data-stu-id="970f3-129">Yes</span></span> | <span data-ttu-id="970f3-130">是</span><span class="sxs-lookup"><span data-stu-id="970f3-130">Yes</span></span> |
| <span data-ttu-id="970f3-131">可程式性</span><span class="sxs-lookup"><span data-stu-id="970f3-131">Programmability</span></span> | <span data-ttu-id="970f3-132">Python、Scala、Java</span><span class="sxs-lookup"><span data-stu-id="970f3-132">Python, Scala, Java</span></span> | <span data-ttu-id="970f3-133">C#、Java、Node.js、Python、PHP、Ruby</span><span class="sxs-lookup"><span data-stu-id="970f3-133">C#, Java, Node.js, Python, PHP, Ruby</span></span> |
| <span data-ttu-id="970f3-134">支援對巨量資料集和大型文件進行處理</span><span class="sxs-lookup"><span data-stu-id="970f3-134">Support processing of big data sets and large documents</span></span> | <span data-ttu-id="970f3-135">是</span><span class="sxs-lookup"><span data-stu-id="970f3-135">Yes</span></span> | <span data-ttu-id="970f3-136">否</span><span class="sxs-lookup"><span data-stu-id="970f3-136">No</span></span> |

### <a name="low-level-natural-language-processing-capabilities"></a><span data-ttu-id="970f3-137">低階自然語言處理功能</span><span class="sxs-lookup"><span data-stu-id="970f3-137">Low-level natural language processing capabilities</span></span>

| | <span data-ttu-id="970f3-138">Azure HDInsight</span><span class="sxs-lookup"><span data-stu-id="970f3-138">Azure HDInsight</span></span> | <span data-ttu-id="970f3-139">Microsoft 認知服務</span><span class="sxs-lookup"><span data-stu-id="970f3-139">Microsoft Cognitive Services</span></span> |  
| --- | --- | --- |
| <span data-ttu-id="970f3-140">權杖化工具</span><span class="sxs-lookup"><span data-stu-id="970f3-140">Tokenizer</span></span> | <span data-ttu-id="970f3-141">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="970f3-141">Yes (Spark NLP)</span></span> | <span data-ttu-id="970f3-142">是 (語言分析 API)</span><span class="sxs-lookup"><span data-stu-id="970f3-142">Yes (Linguistic Analysis API)</span></span> |
| <span data-ttu-id="970f3-143">詞幹分析器</span><span class="sxs-lookup"><span data-stu-id="970f3-143">Stemmer</span></span> | <span data-ttu-id="970f3-144">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="970f3-144">Yes (Spark NLP)</span></span> | <span data-ttu-id="970f3-145">否</span><span class="sxs-lookup"><span data-stu-id="970f3-145">No</span></span> |
| <span data-ttu-id="970f3-146">詞形分析器</span><span class="sxs-lookup"><span data-stu-id="970f3-146">Lemmatizer</span></span> | <span data-ttu-id="970f3-147">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="970f3-147">Yes (Spark NLP)</span></span> | <span data-ttu-id="970f3-148">否</span><span class="sxs-lookup"><span data-stu-id="970f3-148">No</span></span> |
| <span data-ttu-id="970f3-149">詞性標記</span><span class="sxs-lookup"><span data-stu-id="970f3-149">Part of speech tagging</span></span> | <span data-ttu-id="970f3-150">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="970f3-150">Yes (Spark NLP)</span></span> | <span data-ttu-id="970f3-151">是 (語言分析 API)</span><span class="sxs-lookup"><span data-stu-id="970f3-151">Yes (Linguistic Analysis API)</span></span> |
| <span data-ttu-id="970f3-152">詞彙頻率/反向文件頻率 (TF/IDF)</span><span class="sxs-lookup"><span data-stu-id="970f3-152">Term frequency/inverse-document frequency (TF/IDF)</span></span> | <span data-ttu-id="970f3-153">是 (Spark MLlib)</span><span class="sxs-lookup"><span data-stu-id="970f3-153">Yes (Spark MLlib)</span></span> | <span data-ttu-id="970f3-154">否</span><span class="sxs-lookup"><span data-stu-id="970f3-154">No</span></span> |
| <span data-ttu-id="970f3-155">字串相似度&mdash;編輯距離計算</span><span class="sxs-lookup"><span data-stu-id="970f3-155">String similarity&mdash;edit distance calculation</span></span> | <span data-ttu-id="970f3-156">是 (Spark MLlib)</span><span class="sxs-lookup"><span data-stu-id="970f3-156">Yes (Spark MLlib)</span></span> | <span data-ttu-id="970f3-157">否</span><span class="sxs-lookup"><span data-stu-id="970f3-157">No</span></span> |
| <span data-ttu-id="970f3-158">N-Gram 計算</span><span class="sxs-lookup"><span data-stu-id="970f3-158">N-gram calculation</span></span> | <span data-ttu-id="970f3-159">是 (Spark MLlib)</span><span class="sxs-lookup"><span data-stu-id="970f3-159">Yes (Spark MLlib)</span></span> | <span data-ttu-id="970f3-160">否</span><span class="sxs-lookup"><span data-stu-id="970f3-160">No</span></span> |
| <span data-ttu-id="970f3-161">停用字詞移除</span><span class="sxs-lookup"><span data-stu-id="970f3-161">Stop word removal</span></span> | <span data-ttu-id="970f3-162">是 (Spark MLlib)</span><span class="sxs-lookup"><span data-stu-id="970f3-162">Yes (Spark MLlib)</span></span> | <span data-ttu-id="970f3-163">否</span><span class="sxs-lookup"><span data-stu-id="970f3-163">No</span></span> |

### <a name="high-level-natural-language-processing-capabilities"></a><span data-ttu-id="970f3-164">高階自然語言處理功能</span><span class="sxs-lookup"><span data-stu-id="970f3-164">High-level natural language processing capabilities</span></span>

| | <span data-ttu-id="970f3-165">Azure HDInsight</span><span class="sxs-lookup"><span data-stu-id="970f3-165">Azure HDInsight</span></span> | <span data-ttu-id="970f3-166">Microsoft 認知服務</span><span class="sxs-lookup"><span data-stu-id="970f3-166">Microsoft Cognitive Services</span></span> |
| --- | --- | --- |
| <span data-ttu-id="970f3-167">實體/意圖識別與擷取</span><span class="sxs-lookup"><span data-stu-id="970f3-167">Entity/intent identification & extraction</span></span> | <span data-ttu-id="970f3-168">否</span><span class="sxs-lookup"><span data-stu-id="970f3-168">No</span></span> | <span data-ttu-id="970f3-169">是 (Language Understanding Intelligent Service (LUIS) API)</span><span class="sxs-lookup"><span data-stu-id="970f3-169">Yes (Language Understanding Intelligent Service (LUIS) API)</span></span> |
| <span data-ttu-id="970f3-170">主題偵測</span><span class="sxs-lookup"><span data-stu-id="970f3-170">Topic detection</span></span> | <span data-ttu-id="970f3-171">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="970f3-171">Yes (Spark NLP)</span></span> | <span data-ttu-id="970f3-172">是 (文字分析 API)</span><span class="sxs-lookup"><span data-stu-id="970f3-172">Yes (Text Analytics API)</span></span> |
| <span data-ttu-id="970f3-173">拼字檢查</span><span class="sxs-lookup"><span data-stu-id="970f3-173">Spell checking</span></span> | <span data-ttu-id="970f3-174">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="970f3-174">Yes (Spark NLP)</span></span> | <span data-ttu-id="970f3-175">是 (Bing 拼字檢查 API)</span><span class="sxs-lookup"><span data-stu-id="970f3-175">Yes (Bing Spell Check API)</span></span> |
| <span data-ttu-id="970f3-176">情感分析</span><span class="sxs-lookup"><span data-stu-id="970f3-176">Sentiment analysis</span></span> | <span data-ttu-id="970f3-177">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="970f3-177">Yes (Spark NLP)</span></span> | <span data-ttu-id="970f3-178">是 (文字分析 API)</span><span class="sxs-lookup"><span data-stu-id="970f3-178">Yes (Text Analytics API)</span></span> |
| <span data-ttu-id="970f3-179">語言偵測</span><span class="sxs-lookup"><span data-stu-id="970f3-179">Language detection</span></span> | <span data-ttu-id="970f3-180">否</span><span class="sxs-lookup"><span data-stu-id="970f3-180">No</span></span> | <span data-ttu-id="970f3-181">是 (文字分析 API)</span><span class="sxs-lookup"><span data-stu-id="970f3-181">Yes (Text Analytics API)</span></span> |
| <span data-ttu-id="970f3-182">支援英文以外的多種語言</span><span class="sxs-lookup"><span data-stu-id="970f3-182">Supports multiple languages besides English</span></span> | <span data-ttu-id="970f3-183">否</span><span class="sxs-lookup"><span data-stu-id="970f3-183">No</span></span> | <span data-ttu-id="970f3-184">是 (隨 API 而不同)</span><span class="sxs-lookup"><span data-stu-id="970f3-184">Yes (varies by API)</span></span> |

## <a name="see-also"></a><span data-ttu-id="970f3-185">另請參閱</span><span class="sxs-lookup"><span data-stu-id="970f3-185">See also</span></span>

[<span data-ttu-id="970f3-186">自然語言處理</span><span class="sxs-lookup"><span data-stu-id="970f3-186">Natural language processing</span></span>](../scenarios/natural-language-processing.md)
