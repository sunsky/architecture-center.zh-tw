---
title: 選擇自然語言處理技術
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.openlocfilehash: 699e01bc9905d02fc8ec1113039087189f6e8caf
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54114108"
---
# <a name="choosing-a-natural-language-processing-technology-in-azure"></a><span data-ttu-id="0ed00-102">在 Azure 中選擇自然語言處理技術</span><span class="sxs-lookup"><span data-stu-id="0ed00-102">Choosing a natural language processing technology in Azure</span></span>

<span data-ttu-id="0ed00-103">自由格式文字處理會對包含文字段落的文件執行，通常是為了支援搜尋，但是也用來執行其他自然語言處理 (NLP) 工作，例如情感分析、主題偵測、語言偵測、關鍵片語擷取和文件分類。</span><span class="sxs-lookup"><span data-stu-id="0ed00-103">Free-form text processing is performed against documents containing paragraphs of text, typically for the purpose of supporting search, but is also used to perform other natural language processing (NLP) tasks such as sentiment analysis, topic detection, language detection, key phrase extraction, and document categorization.</span></span> <span data-ttu-id="0ed00-104">本文主要說明可用來支援 NLP 工作的技術選項。</span><span class="sxs-lookup"><span data-stu-id="0ed00-104">This article focuses on the technology choices that act in support of the NLP tasks.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="what-are-your-options-when-choosing-an-nlp-service"></a><span data-ttu-id="0ed00-105">選擇 NLP 服務時有哪些選項？</span><span class="sxs-lookup"><span data-stu-id="0ed00-105">What are your options when choosing an NLP service?</span></span>

<!-- markdownlint-enable MD026 -->

<span data-ttu-id="0ed00-106">在 Azure 中，下列服務皆提供自然語言處理 (NLP) 功能：</span><span class="sxs-lookup"><span data-stu-id="0ed00-106">In Azure, the following services provide natural language processing (NLP) capabilities:</span></span>

- [<span data-ttu-id="0ed00-107">使用 Spark 和 Spark MLlib 的 Azure HDInsight</span><span class="sxs-lookup"><span data-stu-id="0ed00-107">Azure HDInsight with Spark and Spark MLlib</span></span>](/azure/hdinsight/spark/apache-spark-overview)
- [<span data-ttu-id="0ed00-108">Azure Databricks</span><span class="sxs-lookup"><span data-stu-id="0ed00-108">Azure Databricks</span></span>](/azure/azure-databricks/what-is-azure-databricks)
- [<span data-ttu-id="0ed00-109">Microsoft 認知服務</span><span class="sxs-lookup"><span data-stu-id="0ed00-109">Microsoft Cognitive Services</span></span>](/azure/cognitive-services/welcome)

## <a name="key-selection-criteria"></a><span data-ttu-id="0ed00-110">關鍵選取準則</span><span class="sxs-lookup"><span data-stu-id="0ed00-110">Key selection criteria</span></span>

<span data-ttu-id="0ed00-111">為了縮小選擇範圍，請先回答下列問題：</span><span class="sxs-lookup"><span data-stu-id="0ed00-111">To narrow the choices, start by answering these questions:</span></span>

- <span data-ttu-id="0ed00-112">您是否要使用預先建置的模型？</span><span class="sxs-lookup"><span data-stu-id="0ed00-112">Do you want to use prebuilt models?</span></span> <span data-ttu-id="0ed00-113">如果是，請考慮使用 Microsoft 認知服務所提供的 API。</span><span class="sxs-lookup"><span data-stu-id="0ed00-113">If yes, consider using the APIs offered by Microsoft Cognitive Services.</span></span>

- <span data-ttu-id="0ed00-114">您是否需要根據大量文字資料訓練自訂模型？</span><span class="sxs-lookup"><span data-stu-id="0ed00-114">Do you need to train custom models against a large corpus of text data?</span></span> <span data-ttu-id="0ed00-115">如果是，請考慮使用具有 Spark MLlib 和 Spark NLP 的 Azure HDInsight。</span><span class="sxs-lookup"><span data-stu-id="0ed00-115">If yes, consider using Azure HDInsight with Spark MLlib and Spark NLP.</span></span>

- <span data-ttu-id="0ed00-116">您是否需要 Token 化、詞幹分析、詞形歸併還原和詞彙頻率/反向文件頻率 (TF/IDF) 等低階 NLP 功能？</span><span class="sxs-lookup"><span data-stu-id="0ed00-116">Do you need low-level NLP capabilities like tokenization, stemming, lemmatization, and term frequency/inverse document frequency (TF/IDF)?</span></span> <span data-ttu-id="0ed00-117">如果是，請考慮使用具有 Spark MLlib 和 Spark NLP 的 Azure HDInsight。</span><span class="sxs-lookup"><span data-stu-id="0ed00-117">If yes, consider using Azure HDInsight with Spark MLlib and Spark NLP.</span></span>

- <span data-ttu-id="0ed00-118">您是否需要簡單的高階 NLP 功能，例如實體和意圖識別、主題偵測、拼字檢查或情感分析？</span><span class="sxs-lookup"><span data-stu-id="0ed00-118">Do you need simple, high-level NLP capabilities like entity and intent identification, topic detection, spell check, or sentiment analysis?</span></span> <span data-ttu-id="0ed00-119">如果是，請考慮使用 Microsoft 認知服務所提供的 API。</span><span class="sxs-lookup"><span data-stu-id="0ed00-119">If yes, consider using the APIs offered by Microsoft Cognitive Services.</span></span>

## <a name="capability-matrix"></a><span data-ttu-id="0ed00-120">功能對照表</span><span class="sxs-lookup"><span data-stu-id="0ed00-120">Capability matrix</span></span>

<span data-ttu-id="0ed00-121">下表摘要列出各項功能的主要差異。</span><span class="sxs-lookup"><span data-stu-id="0ed00-121">The following tables summarize the key differences in capabilities.</span></span>

### <a name="general-capabilities"></a><span data-ttu-id="0ed00-122">一般功能</span><span class="sxs-lookup"><span data-stu-id="0ed00-122">General capabilities</span></span>

| | <span data-ttu-id="0ed00-123">Azure HDInsight</span><span class="sxs-lookup"><span data-stu-id="0ed00-123">Azure HDInsight</span></span> | <span data-ttu-id="0ed00-124">Microsoft 認知服務</span><span class="sxs-lookup"><span data-stu-id="0ed00-124">Microsoft Cognitive Services</span></span> |
| --- | --- | --- |
| <span data-ttu-id="0ed00-125">提供預先訓練的模型作為服務</span><span class="sxs-lookup"><span data-stu-id="0ed00-125">Provides pretrained models as a service</span></span> | <span data-ttu-id="0ed00-126">否</span><span class="sxs-lookup"><span data-stu-id="0ed00-126">No</span></span> | <span data-ttu-id="0ed00-127">是</span><span class="sxs-lookup"><span data-stu-id="0ed00-127">Yes</span></span> |
| <span data-ttu-id="0ed00-128">REST API</span><span class="sxs-lookup"><span data-stu-id="0ed00-128">REST API</span></span> | <span data-ttu-id="0ed00-129">是</span><span class="sxs-lookup"><span data-stu-id="0ed00-129">Yes</span></span> | <span data-ttu-id="0ed00-130">是</span><span class="sxs-lookup"><span data-stu-id="0ed00-130">Yes</span></span> |
| <span data-ttu-id="0ed00-131">可程式性</span><span class="sxs-lookup"><span data-stu-id="0ed00-131">Programmability</span></span> | <span data-ttu-id="0ed00-132">Python、Scala、Java</span><span class="sxs-lookup"><span data-stu-id="0ed00-132">Python, Scala, Java</span></span> | <span data-ttu-id="0ed00-133">C#、Java、Node.js、Python、PHP、Ruby</span><span class="sxs-lookup"><span data-stu-id="0ed00-133">C#, Java, Node.js, Python, PHP, Ruby</span></span> |
| <span data-ttu-id="0ed00-134">支援對巨量資料集和大型文件進行處理</span><span class="sxs-lookup"><span data-stu-id="0ed00-134">Support processing of big data sets and large documents</span></span> | <span data-ttu-id="0ed00-135">是</span><span class="sxs-lookup"><span data-stu-id="0ed00-135">Yes</span></span> | <span data-ttu-id="0ed00-136">否</span><span class="sxs-lookup"><span data-stu-id="0ed00-136">No</span></span> |

### <a name="low-level-natural-language-processing-capabilities"></a><span data-ttu-id="0ed00-137">低階自然語言處理功能</span><span class="sxs-lookup"><span data-stu-id="0ed00-137">Low-level natural language processing capabilities</span></span>

| | <span data-ttu-id="0ed00-138">Azure HDInsight</span><span class="sxs-lookup"><span data-stu-id="0ed00-138">Azure HDInsight</span></span> | <span data-ttu-id="0ed00-139">Microsoft 認知服務</span><span class="sxs-lookup"><span data-stu-id="0ed00-139">Microsoft Cognitive Services</span></span> |  
| --- | --- | --- |
| <span data-ttu-id="0ed00-140">權杖化工具</span><span class="sxs-lookup"><span data-stu-id="0ed00-140">Tokenizer</span></span> | <span data-ttu-id="0ed00-141">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="0ed00-141">Yes (Spark NLP)</span></span> | <span data-ttu-id="0ed00-142">是 (語言分析 API)</span><span class="sxs-lookup"><span data-stu-id="0ed00-142">Yes (Linguistic Analysis API)</span></span> |
| <span data-ttu-id="0ed00-143">詞幹分析器</span><span class="sxs-lookup"><span data-stu-id="0ed00-143">Stemmer</span></span> | <span data-ttu-id="0ed00-144">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="0ed00-144">Yes (Spark NLP)</span></span> | <span data-ttu-id="0ed00-145">否</span><span class="sxs-lookup"><span data-stu-id="0ed00-145">No</span></span> |
| <span data-ttu-id="0ed00-146">詞形分析器</span><span class="sxs-lookup"><span data-stu-id="0ed00-146">Lemmatizer</span></span> | <span data-ttu-id="0ed00-147">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="0ed00-147">Yes (Spark NLP)</span></span> | <span data-ttu-id="0ed00-148">否</span><span class="sxs-lookup"><span data-stu-id="0ed00-148">No</span></span> |
| <span data-ttu-id="0ed00-149">詞性標記</span><span class="sxs-lookup"><span data-stu-id="0ed00-149">Part of speech tagging</span></span> | <span data-ttu-id="0ed00-150">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="0ed00-150">Yes (Spark NLP)</span></span> | <span data-ttu-id="0ed00-151">是 (語言分析 API)</span><span class="sxs-lookup"><span data-stu-id="0ed00-151">Yes (Linguistic Analysis API)</span></span> |
| <span data-ttu-id="0ed00-152">詞彙頻率/反向文件頻率 (TF/IDF)</span><span class="sxs-lookup"><span data-stu-id="0ed00-152">Term frequency/inverse-document frequency (TF/IDF)</span></span> | <span data-ttu-id="0ed00-153">是 (Spark MLlib)</span><span class="sxs-lookup"><span data-stu-id="0ed00-153">Yes (Spark MLlib)</span></span> | <span data-ttu-id="0ed00-154">否</span><span class="sxs-lookup"><span data-stu-id="0ed00-154">No</span></span> |
| <span data-ttu-id="0ed00-155">字串相似度&mdash;編輯距離計算</span><span class="sxs-lookup"><span data-stu-id="0ed00-155">String similarity&mdash;edit distance calculation</span></span> | <span data-ttu-id="0ed00-156">是 (Spark MLlib)</span><span class="sxs-lookup"><span data-stu-id="0ed00-156">Yes (Spark MLlib)</span></span> | <span data-ttu-id="0ed00-157">否</span><span class="sxs-lookup"><span data-stu-id="0ed00-157">No</span></span> |
| <span data-ttu-id="0ed00-158">N-Gram 計算</span><span class="sxs-lookup"><span data-stu-id="0ed00-158">N-gram calculation</span></span> | <span data-ttu-id="0ed00-159">是 (Spark MLlib)</span><span class="sxs-lookup"><span data-stu-id="0ed00-159">Yes (Spark MLlib)</span></span> | <span data-ttu-id="0ed00-160">否</span><span class="sxs-lookup"><span data-stu-id="0ed00-160">No</span></span> |
| <span data-ttu-id="0ed00-161">停用字詞移除</span><span class="sxs-lookup"><span data-stu-id="0ed00-161">Stop word removal</span></span> | <span data-ttu-id="0ed00-162">是 (Spark MLlib)</span><span class="sxs-lookup"><span data-stu-id="0ed00-162">Yes (Spark MLlib)</span></span> | <span data-ttu-id="0ed00-163">否</span><span class="sxs-lookup"><span data-stu-id="0ed00-163">No</span></span> |

### <a name="high-level-natural-language-processing-capabilities"></a><span data-ttu-id="0ed00-164">高階自然語言處理功能</span><span class="sxs-lookup"><span data-stu-id="0ed00-164">High-level natural language processing capabilities</span></span>

| | <span data-ttu-id="0ed00-165">Azure HDInsight</span><span class="sxs-lookup"><span data-stu-id="0ed00-165">Azure HDInsight</span></span> | <span data-ttu-id="0ed00-166">Microsoft 認知服務</span><span class="sxs-lookup"><span data-stu-id="0ed00-166">Microsoft Cognitive Services</span></span> |
| --- | --- | --- |
| <span data-ttu-id="0ed00-167">實體/意圖識別與擷取</span><span class="sxs-lookup"><span data-stu-id="0ed00-167">Entity/intent identification & extraction</span></span> | <span data-ttu-id="0ed00-168">否</span><span class="sxs-lookup"><span data-stu-id="0ed00-168">No</span></span> | <span data-ttu-id="0ed00-169">是 (Language Understanding Intelligent Service (LUIS) API)</span><span class="sxs-lookup"><span data-stu-id="0ed00-169">Yes (Language Understanding Intelligent Service (LUIS) API)</span></span> |
| <span data-ttu-id="0ed00-170">主題偵測</span><span class="sxs-lookup"><span data-stu-id="0ed00-170">Topic detection</span></span> | <span data-ttu-id="0ed00-171">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="0ed00-171">Yes (Spark NLP)</span></span> | <span data-ttu-id="0ed00-172">是 (文字分析 API)</span><span class="sxs-lookup"><span data-stu-id="0ed00-172">Yes (Text Analytics API)</span></span> |
| <span data-ttu-id="0ed00-173">拼字檢查</span><span class="sxs-lookup"><span data-stu-id="0ed00-173">Spell checking</span></span> | <span data-ttu-id="0ed00-174">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="0ed00-174">Yes (Spark NLP)</span></span> | <span data-ttu-id="0ed00-175">是 (Bing 拼字檢查 API)</span><span class="sxs-lookup"><span data-stu-id="0ed00-175">Yes (Bing Spell Check API)</span></span> |
| <span data-ttu-id="0ed00-176">情感分析</span><span class="sxs-lookup"><span data-stu-id="0ed00-176">Sentiment analysis</span></span> | <span data-ttu-id="0ed00-177">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="0ed00-177">Yes (Spark NLP)</span></span> | <span data-ttu-id="0ed00-178">是 (文字分析 API)</span><span class="sxs-lookup"><span data-stu-id="0ed00-178">Yes (Text Analytics API)</span></span> |
| <span data-ttu-id="0ed00-179">語言偵測</span><span class="sxs-lookup"><span data-stu-id="0ed00-179">Language detection</span></span> | <span data-ttu-id="0ed00-180">否</span><span class="sxs-lookup"><span data-stu-id="0ed00-180">No</span></span> | <span data-ttu-id="0ed00-181">是 (文字分析 API)</span><span class="sxs-lookup"><span data-stu-id="0ed00-181">Yes (Text Analytics API)</span></span> |
| <span data-ttu-id="0ed00-182">支援英文以外的多種語言</span><span class="sxs-lookup"><span data-stu-id="0ed00-182">Supports multiple languages besides English</span></span> | <span data-ttu-id="0ed00-183">否</span><span class="sxs-lookup"><span data-stu-id="0ed00-183">No</span></span> | <span data-ttu-id="0ed00-184">是 (隨 API 而不同)</span><span class="sxs-lookup"><span data-stu-id="0ed00-184">Yes (varies by API)</span></span> |

## <a name="see-also"></a><span data-ttu-id="0ed00-185">另請參閱</span><span class="sxs-lookup"><span data-stu-id="0ed00-185">See also</span></span>

[<span data-ttu-id="0ed00-186">自然語言處理</span><span class="sxs-lookup"><span data-stu-id="0ed00-186">Natural language processing</span></span>](../scenarios/natural-language-processing.md)
