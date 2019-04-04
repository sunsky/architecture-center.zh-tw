---
title: Azure 資料架構指南
description: ''
author: zoinerTejada
ms.date: 02/12/2018
layout: LandingPage
ms.topic: landing-page
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: 5fb74c6323f8dc571d827eb9a4c65a6c87d0ae36
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58344422"
---
# <a name="azure-data-architecture-guide"></a>Azure 資料架構指南

本指南提供結構化的方式，可供您在 Microsoft Azure 上設計以資料為中心的解決方案。 它是以我們與客戶合作後所得到的實證做法為基礎。

## <a name="introduction"></a>簡介

雲端正在改變應用程式的設計方式，包括資料的處理和儲存方式。 「polyglot persistence」解決方案不會使用一個可處理所有解決方案資料的一般用途資料庫，而是會使用多個專門的資料存放區，每個都會經過最佳化以提供特定功能。 您對於解決方案中資料的看法也會因此改變。 您不會再使用多層式的商務邏輯來讀取和寫入至單一資料層。 相反地，您會圍繞「資料管線」來設計解決方案，資料管線可描述資料如何流經解決方案、處理的位置、儲存在何處，以及管線的下一個元件如何使用它。

## <a name="how-this-guide-is-structured"></a>指南結構

本指南的結構是以資料解決方案的兩個一般類別為主：傳統 RDBMS 工作負載和巨量資料解決方案。

**[傳統 RDBMS 工作負載](./relational-data/index.md)**。 這些工作負載包括線上交易處理 (OLTP) 和線上分析處理 (OLAP)。 OLTP 系統中的資料通常是關聯式資料，它有一個預先定義的結構描述和一組用來維護參考完整性的條件約束。 通常，組織中多個來源的資料可能會合併到資料倉儲，並使用 ETL 程序來移動和轉換來源資料。

![傳統的 RDBMS 工作負載](./images/guide-rdbms.svg)

**[巨量資料解決方案](./big-data/index.md)**。 巨量資料架構的設計，可處理對於傳統資料庫系統而言太大或太複雜之資料的擷取、處理和分析。 資料可能會以批次或即時方式處理。 巨量資料解決方案通常牽涉到大量非關聯式資料，例如索引鍵-值資料、JSON 文件或時間序列資料。 傳統 RDBMS 系統通常不適合用來存放這類資料。 NoSQL 一詞是指設計用來保存非關聯式資料的一系列資料庫。 (該詞不完全精確，因為有許多非關聯式資料存放區支援 SQL 相容查詢。)

![巨量資料解決方案](./images/guide-big-data.svg)

這兩個類別不會互斥，且兩者間有重疊，但我們覺得這是很實用的構思討論方式。 本指南討論每個類別中的**常見案例**，包括案例適用的相關 Azure 服務和適當架構。 此外，本指南會比較 Azure 中資料解決方案的**技術選擇**，其中包括開放原始碼選項。 在每個類別中，我們會描述重要選取準則和功能對照表，以便協助您選擇適合您案例的技術。

本指南不打算告訴您資料科學或資料庫理論 &mdash; 您可以找到關於這些主題的完整書籍。 相反地，本指南的目標是要協助您選取適合您案例的資料架構或資料管線，然後選取最適合您需求的 Azure 服務和技術。 如果您心裡已經有關於架構的想法，則可以直接跳到技術選項。
