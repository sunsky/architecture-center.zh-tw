---
title: 選擇自然語言處理技術
description: ''
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: dacf7bf9cf3e9efed212f34da93c1470954965cf
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2018
---
# <a name="choosing-a-natural-language-processing-technology-in-azure"></a><span data-ttu-id="ee071-102">在 Azure 中選擇自然語言處理技術</span><span class="sxs-lookup"><span data-stu-id="ee071-102">Choosing a natural language processing technology in Azure</span></span>

<span data-ttu-id="ee071-103">自由格式文字處理會對包含文字段落的文件執行，通常是為了支援搜尋，但是也用來執行其他自然語言處理 (NLP) 工作，例如情感分析、主題偵測、語言偵測、關鍵片語擷取和文件分類。</span><span class="sxs-lookup"><span data-stu-id="ee071-103">Free-form text processing is performed against documents containing paragraphs of text, typically for the purpose of supporting search, but is also used to perform other natural language processing (NLP) tasks such as sentiment analysis, topic detection, language detection, key phrase extraction, and document categorization.</span></span> <span data-ttu-id="ee071-104">本文主要說明可用來支援 NLP 工作的技術選項。</span><span class="sxs-lookup"><span data-stu-id="ee071-104">This article focuses on the technology choices that act in support of the NLP tasks.</span></span>

## <a name="what-are-your-options-when-choosing-an-nlp-service"></a><span data-ttu-id="ee071-105">選擇 NLP 服務時有哪些選項？</span><span class="sxs-lookup"><span data-stu-id="ee071-105">What are your options when choosing an NLP service?</span></span>

<span data-ttu-id="ee071-106">在 Azure 中，下列服務皆提供自然語言處理 (NLP) 功能：</span><span class="sxs-lookup"><span data-stu-id="ee071-106">In Azure, the following services provide natural language processing (NLP) capabilities:</span></span>

- [<span data-ttu-id="ee071-107">使用 Spark 和 Spark MLlib 的 Azure HDInsight</span><span class="sxs-lookup"><span data-stu-id="ee071-107">Azure HDInsight with Spark and Spark MLlib</span></span>](/azure/hdinsight/spark/apache-spark-overview)
- [<span data-ttu-id="ee071-108">Microsoft 認知服務</span><span class="sxs-lookup"><span data-stu-id="ee071-108">Microsoft Cognitive Services</span></span>](/azure/#pivot=products&panel=cognitive)

## <a name="key-selection-criteria"></a><span data-ttu-id="ee071-109">關鍵選取準則</span><span class="sxs-lookup"><span data-stu-id="ee071-109">Key selection criteria</span></span>

<span data-ttu-id="ee071-110">為了縮小選擇範圍，請先回答下列問題：</span><span class="sxs-lookup"><span data-stu-id="ee071-110">To narrow the choices, start by answering these questions:</span></span>

- <span data-ttu-id="ee071-111">您是否要使用預先建置的模型？</span><span class="sxs-lookup"><span data-stu-id="ee071-111">Do you want to use prebuilt models?</span></span> <span data-ttu-id="ee071-112">如果是，請考慮使用 Microsoft 認知服務所提供的 API。</span><span class="sxs-lookup"><span data-stu-id="ee071-112">If yes, consider using the APIs offered by Microsoft Cognitive Services.</span></span>

- <span data-ttu-id="ee071-113">您是否需要根據大量文字資料訓練自訂模型？</span><span class="sxs-lookup"><span data-stu-id="ee071-113">Do you need to train custom models against a large corpus of text data?</span></span> <span data-ttu-id="ee071-114">如果是，請考慮使用具有 Spark MLlib 和 Spark NLP 的 Azure HDInsight。</span><span class="sxs-lookup"><span data-stu-id="ee071-114">If yes, consider using Azure HDInsight with Spark MLlib and Spark NLP.</span></span>

- <span data-ttu-id="ee071-115">您是否需要 Token 化、詞幹分析、詞形歸併還原和詞彙頻率/反向文件頻率 (TF/IDF) 等低階 NLP 功能？</span><span class="sxs-lookup"><span data-stu-id="ee071-115">Do you need low-level NLP capabilities like tokenization, stemming, lemmatization, and term frequency/inverse document frequency (TF/IDF)?</span></span> <span data-ttu-id="ee071-116">如果是，請考慮使用具有 Spark MLlib 和 Spark NLP 的 Azure HDInsight。</span><span class="sxs-lookup"><span data-stu-id="ee071-116">If yes, consider using Azure HDInsight with Spark MLlib and Spark NLP.</span></span>

- <span data-ttu-id="ee071-117">您是否需要簡單的高階 NLP 功能，例如實體和意圖識別、主題偵測、拼字檢查或情感分析？</span><span class="sxs-lookup"><span data-stu-id="ee071-117">Do you need simple, high-level NLP capabilities like entity and intent identification, topic detection, spell check, or sentiment analysis?</span></span> <span data-ttu-id="ee071-118">如果是，請考慮使用 Microsoft 認知服務所提供的 API。</span><span class="sxs-lookup"><span data-stu-id="ee071-118">If yes, consider using the APIs offered by Microsoft Cognitive Services.</span></span>

## <a name="capability-matrix"></a><span data-ttu-id="ee071-119">功能對照表</span><span class="sxs-lookup"><span data-stu-id="ee071-119">Capability matrix</span></span>

<span data-ttu-id="ee071-120">下表摘錄主要的功能差異。</span><span class="sxs-lookup"><span data-stu-id="ee071-120">The following tables summarize the key differences in capabilities.</span></span>  

### <a name="general-capabilities"></a><span data-ttu-id="ee071-121">一般功能</span><span class="sxs-lookup"><span data-stu-id="ee071-121">General capabilities</span></span>

| | <span data-ttu-id="ee071-122">Azure HDInsight</span><span class="sxs-lookup"><span data-stu-id="ee071-122">Azure HDInsight</span></span> | <span data-ttu-id="ee071-123">Microsoft 認知服務</span><span class="sxs-lookup"><span data-stu-id="ee071-123">Microsoft Cognitive Services</span></span> |
| --- | --- | --- |
| <span data-ttu-id="ee071-124">提供預先訓練的模型作為服務</span><span class="sxs-lookup"><span data-stu-id="ee071-124">Provides pretrained models as a service</span></span> | <span data-ttu-id="ee071-125">否</span><span class="sxs-lookup"><span data-stu-id="ee071-125">No</span></span> | <span data-ttu-id="ee071-126">yes</span><span class="sxs-lookup"><span data-stu-id="ee071-126">Yes</span></span> |
| <span data-ttu-id="ee071-127">REST API</span><span class="sxs-lookup"><span data-stu-id="ee071-127">REST API</span></span> | <span data-ttu-id="ee071-128">yes</span><span class="sxs-lookup"><span data-stu-id="ee071-128">Yes</span></span> | <span data-ttu-id="ee071-129">yes</span><span class="sxs-lookup"><span data-stu-id="ee071-129">Yes</span></span> |
| <span data-ttu-id="ee071-130">可程式性</span><span class="sxs-lookup"><span data-stu-id="ee071-130">Programmability</span></span> | <span data-ttu-id="ee071-131">Python、Scala、Java</span><span class="sxs-lookup"><span data-stu-id="ee071-131">Python, Scala, Java</span></span> | <span data-ttu-id="ee071-132">C#、Java、Node.js、Python、PHP、Ruby</span><span class="sxs-lookup"><span data-stu-id="ee071-132">C#, Java, Node.js, Python, PHP, Ruby</span></span> |
| <span data-ttu-id="ee071-133">支援對巨量資料集和大型文件進行處理</span><span class="sxs-lookup"><span data-stu-id="ee071-133">Support processing of big data sets and large documents</span></span> | <span data-ttu-id="ee071-134">yes</span><span class="sxs-lookup"><span data-stu-id="ee071-134">Yes</span></span> | <span data-ttu-id="ee071-135">否</span><span class="sxs-lookup"><span data-stu-id="ee071-135">No</span></span> |

### <a name="low-level-natural-language-processing-capabilities"></a><span data-ttu-id="ee071-136">低階自然語言處理功能</span><span class="sxs-lookup"><span data-stu-id="ee071-136">Low-level natural language processing capabilities</span></span>

| | <span data-ttu-id="ee071-137">Azure HDInsight</span><span class="sxs-lookup"><span data-stu-id="ee071-137">Azure HDInsight</span></span> | <span data-ttu-id="ee071-138">Microsoft 認知服務</span><span class="sxs-lookup"><span data-stu-id="ee071-138">Microsoft Cognitive Services</span></span> |  
| --- | --- | --- | 
| <span data-ttu-id="ee071-139">權杖化工具</span><span class="sxs-lookup"><span data-stu-id="ee071-139">Tokenizer</span></span> | <span data-ttu-id="ee071-140">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="ee071-140">Yes (Spark NLP)</span></span> | <span data-ttu-id="ee071-141">是 (語言分析 API)</span><span class="sxs-lookup"><span data-stu-id="ee071-141">Yes (Linguistic Analysis API)</span></span> |
| <span data-ttu-id="ee071-142">詞幹分析器</span><span class="sxs-lookup"><span data-stu-id="ee071-142">Stemmer</span></span> | <span data-ttu-id="ee071-143">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="ee071-143">Yes (Spark NLP)</span></span> | <span data-ttu-id="ee071-144">否</span><span class="sxs-lookup"><span data-stu-id="ee071-144">No</span></span> |
| <span data-ttu-id="ee071-145">詞形分析器</span><span class="sxs-lookup"><span data-stu-id="ee071-145">Lemmatizer</span></span> | <span data-ttu-id="ee071-146">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="ee071-146">Yes (Spark NLP)</span></span> | <span data-ttu-id="ee071-147">否</span><span class="sxs-lookup"><span data-stu-id="ee071-147">No</span></span> |
| <span data-ttu-id="ee071-148">詞性標記</span><span class="sxs-lookup"><span data-stu-id="ee071-148">Part of speech tagging</span></span> | <span data-ttu-id="ee071-149">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="ee071-149">Yes (Spark NLP)</span></span> | <span data-ttu-id="ee071-150">是 (語言分析 API)</span><span class="sxs-lookup"><span data-stu-id="ee071-150">Yes (Linguistic Analysis API)</span></span> |
| <span data-ttu-id="ee071-151">詞彙頻率/反向文件頻率 (TF/IDF)</span><span class="sxs-lookup"><span data-stu-id="ee071-151">Term frequency/inverse-document frequency (TF/IDF)</span></span> | <span data-ttu-id="ee071-152">是 (Spark MLlib)</span><span class="sxs-lookup"><span data-stu-id="ee071-152">Yes (Spark MLlib)</span></span> | <span data-ttu-id="ee071-153">否</span><span class="sxs-lookup"><span data-stu-id="ee071-153">No</span></span> |
| <span data-ttu-id="ee071-154">字串相似度&mdash;編輯距離計算</span><span class="sxs-lookup"><span data-stu-id="ee071-154">String similarity&mdash;edit distance calculation</span></span> | <span data-ttu-id="ee071-155">是 (Spark MLlib)</span><span class="sxs-lookup"><span data-stu-id="ee071-155">Yes (Spark MLlib)</span></span> | <span data-ttu-id="ee071-156">否</span><span class="sxs-lookup"><span data-stu-id="ee071-156">No</span></span> |
| <span data-ttu-id="ee071-157">N-Gram 計算</span><span class="sxs-lookup"><span data-stu-id="ee071-157">N-gram calculation</span></span> | <span data-ttu-id="ee071-158">是 (Spark MLlib)</span><span class="sxs-lookup"><span data-stu-id="ee071-158">Yes (Spark MLlib)</span></span> | <span data-ttu-id="ee071-159">否</span><span class="sxs-lookup"><span data-stu-id="ee071-159">No</span></span> |
| <span data-ttu-id="ee071-160">停用字詞移除</span><span class="sxs-lookup"><span data-stu-id="ee071-160">Stop word removal</span></span> | <span data-ttu-id="ee071-161">是 (Spark MLlib)</span><span class="sxs-lookup"><span data-stu-id="ee071-161">Yes (Spark MLlib)</span></span> | <span data-ttu-id="ee071-162">否</span><span class="sxs-lookup"><span data-stu-id="ee071-162">No</span></span> |

### <a name="high-level-natural-language-processing-capabilities"></a><span data-ttu-id="ee071-163">高階自然語言處理功能</span><span class="sxs-lookup"><span data-stu-id="ee071-163">High-level natural language processing capabilities</span></span>

| | <span data-ttu-id="ee071-164">Azure HDInsight</span><span class="sxs-lookup"><span data-stu-id="ee071-164">Azure HDInsight</span></span> | <span data-ttu-id="ee071-165">Microsoft 認知服務</span><span class="sxs-lookup"><span data-stu-id="ee071-165">Microsoft Cognitive Services</span></span> |
| --- | --- | --- | 
| <span data-ttu-id="ee071-166">實體/意圖識別與擷取</span><span class="sxs-lookup"><span data-stu-id="ee071-166">Entity/intent identification & extraction</span></span> | <span data-ttu-id="ee071-167">否</span><span class="sxs-lookup"><span data-stu-id="ee071-167">No</span></span> | <span data-ttu-id="ee071-168">是 (Language Understanding Intelligent Service (LUIS) API)</span><span class="sxs-lookup"><span data-stu-id="ee071-168">Yes (Language Understanding Intelligent Service (LUIS) API)</span></span> |    
| <span data-ttu-id="ee071-169">主題偵測</span><span class="sxs-lookup"><span data-stu-id="ee071-169">Topic detection</span></span> | <span data-ttu-id="ee071-170">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="ee071-170">Yes (Spark NLP)</span></span> | <span data-ttu-id="ee071-171">是 (文字分析 API)</span><span class="sxs-lookup"><span data-stu-id="ee071-171">Yes (Text Analytics API)</span></span> |
| <span data-ttu-id="ee071-172">拼字檢查</span><span class="sxs-lookup"><span data-stu-id="ee071-172">Spell checking</span></span> | <span data-ttu-id="ee071-173">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="ee071-173">Yes (Spark NLP)</span></span> | <span data-ttu-id="ee071-174">是 (Bing 拼字檢查 API)</span><span class="sxs-lookup"><span data-stu-id="ee071-174">Yes (Bing Spell Check API)</span></span> |
| <span data-ttu-id="ee071-175">情感分析</span><span class="sxs-lookup"><span data-stu-id="ee071-175">Sentiment analysis</span></span> | <span data-ttu-id="ee071-176">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="ee071-176">Yes (Spark NLP)</span></span> | <span data-ttu-id="ee071-177">是 (文字分析 API)</span><span class="sxs-lookup"><span data-stu-id="ee071-177">Yes (Text Analytics API)</span></span> |
| <span data-ttu-id="ee071-178">語言偵測</span><span class="sxs-lookup"><span data-stu-id="ee071-178">Language detection</span></span> | <span data-ttu-id="ee071-179">否</span><span class="sxs-lookup"><span data-stu-id="ee071-179">No</span></span> | <span data-ttu-id="ee071-180">是 (文字分析 API)</span><span class="sxs-lookup"><span data-stu-id="ee071-180">Yes (Text Analytics API)</span></span> |
| <span data-ttu-id="ee071-181">支援英文以外的多種語言</span><span class="sxs-lookup"><span data-stu-id="ee071-181">Supports multiple languages besides English</span></span> | <span data-ttu-id="ee071-182">否</span><span class="sxs-lookup"><span data-stu-id="ee071-182">No</span></span> | <span data-ttu-id="ee071-183">是 (隨 API 而不同)</span><span class="sxs-lookup"><span data-stu-id="ee071-183">Yes (varies by API)</span></span> |

## <a name="see-also"></a><span data-ttu-id="ee071-184">另請參閱</span><span class="sxs-lookup"><span data-stu-id="ee071-184">See also</span></span>

[<span data-ttu-id="ee071-185">自然語言處理</span><span class="sxs-lookup"><span data-stu-id="ee071-185">Natural language processing</span></span>](../scenarios/natural-language-processing.md)