---
title: 背景作業指引
description: 獨立於使用者介面之外執行的背景工作指引。
author: dragon119
ms.date: 05/24/2017
pnp.series.title: Best Practices
ms.openlocfilehash: 10c24afee4b880cfbf8ee534f4d7f945d2b046a9
ms.sourcegitcommit: 3426a9c5ed937f097725c487cf3d073ae5e2a347
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2018
---
# <a name="background-jobs"></a>背景作業作業
[!INCLUDE [header](../_includes/header.md)]

許多類型的應用程式需要執行與使用者介面 (UI) 無關的背景工作。 範例包括批次作業、大量處理的工作，以及長時間執行的處理序，例如工作流程。 背景作業可以在不需要和使用者互動的情形下執行。應用程式可以啟動此作業，然後繼續處理使用者的互動式要求。 這有助於減少應用程式 UI 的負載，如此可以改善可用性，並降低互動式回應時間。

比方說，如果產生使用者上傳的影像縮圖時需要使用應用程式，可將它作為背景作業執行，並在完成時將縮圖儲存到儲存體，使用者不需要等待處理序完成。 同樣地，下訂單的使用者可以起始背景工作流程來處理訂單，而同時 UI 能讓使用者繼續瀏覽 Web 應用程式。 當背景作業完成時，可更新儲存的訂單資料，並將電子郵件傳送給確認訂單的使用者。

當您考慮是否將工作作為背景作業執行時，主要的準則是：是否可以執行工作而不需要使用者互動，且不需要讓 UI 等待作業完成。 需要讓使用者或 UI 等待完成的工作，可能不適合作為背景作業。

## <a name="types-of-background-jobs"></a>背景工作類型
背景作業通常具有下列一個或多個的作業類型：

* 需要大量 CPU 處理的作業，例如數學計算或結構化模型分析。
* 進行大量 I/O 動作的作業，例如執行一系列的儲存體交易或索引檔。
* 如夜間資料更新或排程處理的批次作業。
* 長時間執行的工作流程，例如訂單履行或佈建服務和系統。
* 其中的工作移交至更安全的位置，以便進行處理的敏感性資料處理序。 例如，您可能不想在 Web 應用程式中處理敏感性資料。 相反的，您可以改用如 [閘道管理員](http://msdn.microsoft.com/library/dn589793.aspx) 的模式將資料傳送到隔離的背景處理序中，且該處理序擁有受保護儲存體的存取權。

## <a name="triggers"></a>觸發程序
可利用數個不同的方式啟動背景工作。 它們全都屬於下列類別之一：

* [**事件驅動觸發程序**](#event-driven-triggers)。 工作會啟動以回應事件，通常是使用者或工作流程中的步驟所採取的動作。
* [**排程驅動觸發程序**](#schedule-driven-triggers)。 根據計時器在排程上叫用的工作。 這可能是重複執行的排程，或指定在稍後執行的一次性叫用。

### <a name="event-driven-triggers"></a>事件驅動觸發程序
事件驅動叫用會使用觸發程序來啟動背景工作。 使用事件驅動觸發程序的範例包括：

* UI 或另一項工作可將訊息放在佇列中。 訊息包含已經執行的動作相關資料，例如下訂單的使用者。 背景工作會在此佇列上接聽，並偵測新訊息到達。 它會讀取訊息，並將其中的資料做為背景工作的輸入。
* UI 或另一項工作會儲存或更新儲存體中的值。 背景工作監視儲存體並偵測變更。 它會讀取資料，並使用它做為背景工作的輸入。
* UI 或另一項作業會要求端點 (例如 HTTPS URI)，或公開為 Web 服務的 API。 它會在要求的過程中，傳遞完成背景工作所需的資料。 端點或 Web 服務會叫用背景工作，將資料做為其輸入。

適合事件驅動叫用的工作一般範例包括影像處理、工作流程、將資訊傳送至遠端服務、傳送電子郵件訊息、在多組織用戶共享的應用程式中佈建新使用者等等。

### <a name="schedule-driven-triggers"></a>排程驅動觸發程序
排程驅動叫用會使用計時器啟動背景工作。 使用排程驅動觸發程序的範例包括：

* 在應用程式內本機執行的計時器，或作為應用程式作業系統一部分的計時器，會定期叫用背景工作。
* 在不同的應用程式中執行的計時器，或計時器服務 (例如 Azure 排程器)，會定期將要求傳送到 API 或 Web 服務。 API 或 Web 服務會叫用背景工作。
* 不同的程序或應用程式會啟動計時器，以便在指定的時間延遲後或特定的時間，叫用背景工作一次。

適合排程驅動叫用工作的一般範例包含批次處理常式 (例如根據使用者最新的行為來更新其相關的產品清單)、例行資料處理工作 (例如更新索引或產生累積結果)、每日報告的資料分析、資料保留清除、資料一致性檢查。

如果您使用必須作為單一執行個體執行的排程驅動工作，請注意下列事項：

* 如果調整正在執行排程器的計算執行個體 (例如使用 Windows 排程工作的虛擬機器)，您必須執行排程器的多個執行個體。 這些動作可能啟動工作的多個執行個體。
* 如果工作的執行時間超過排程器事件之間的期間，排程器可能在前一個工作仍在執行時啟動工作的另一個執行個體。

## <a name="returning-results"></a>傳回結果
背景作業以非同步的方式，在不同的處理序或甚至不同的位置內 (從 UI 或叫用背景工作的處理序) 中執行。 在理想的情況下，背景工作是「射後不理」(Fire and Forget) 的作業，而且其執行進度不會影響 UI 或呼叫程序。 這表示呼叫處理序不會等候工作完成。 因此，此處理序就無法自動偵測工作的結束時間。

如果您需要背景工作與呼叫工作通訊，以指出進度或完成，您必須為此實作一種機制。 部分範例如下：

* 將狀態指標值寫入至可存取 UI 或呼叫者工作的儲存體，也就是可在必要時監視或檢查此值。 可將背景工作必須傳回給呼叫端的資料，放入相同的儲存體中。
* 建立 UI 或呼叫端會接聽的回覆佇列。 背景工作可以將訊息傳送至佇列，此佇列表示出狀態和工作的完成。 可將背景工作必須傳回給呼叫端的資料放入訊息中。 如果您使用 Azure 服務匯流排，您可以使用 **ReplyTo** 和 **CorrelationId** 屬性來實作這項功能。 如需詳細資訊，請參閱 [服務匯流排代理傳訊中的相互關聯](http://www.cloudcasts.net/devguide/Default.aspx?id=13029)。
* 從 UI 或呼叫端可以存取來取得狀態資訊的背景工作中，公開 API 或端點。 可在回應中包含背景工作必須傳回給呼叫端的資料。
* 讓背景工作透過 API 回呼至 UI 或呼叫端，以表示預先定義的時間點或完成時的狀態。 這可能是透過本機引發的事件，或透過發佈和訂閱機制。 可在要求或事件裝載中，包含背景工作必須傳回給呼叫端的資料。

## <a name="hosting-environment"></a>裝載環境
您可以使用各種不同的 Azure 平台服務來裝載背景工作：

* [**Azure Web Apps 及 WebJobs**](#azure-web-apps-and-webjobs)。 您可以根據 Web 應用程式內容中各種不同類型的指令碼或可執行程式，來使用 Web 工作執行自訂工作。
* [**Azure 虛擬機器**](#azure-virtual-machines)。 如果您使用 Windows 服務，或您想要使用 Windows 工作排程器，它一般會在專用的虛擬機器內裝載您的背景工作。
* [**Azure Batch**](#azure-batch)。 Batch 是一項平台服務，可排程要在一組受控虛擬機器上執行的計算密集型工作， ，而且可以自動調整計算資源。
* [**Azure Container Service**](#azure-container-service)。 Azure Container Service (ACS) 提供 Azure 上的容器裝載環境。 
* [**Azure 雲端服務**](#azure-cloud-services)。 您可以在做為背景工作執行的角色內撰寫程式碼。

下列各節更詳細描述其中每個選項，並包含可協助您選擇適當選項的注意事項。

### <a name="azure-web-apps-and-webjobs"></a>Azure Web Apps 及 WebJobs

您可以使用 Azure WebJobs，把自訂工作當做 Azure Web 應用程式內的背景工作來執行。 Web 工作會在 Web 應用程式內容中作為連續處理序來執行。 Web 工作也會為回應 Azure 排程器的觸發事件或外部因素 (例如變更儲存體 Blob 和訊息佇列) 而執行。 工作可在要求時啟動和停止，及正常關機。 如果連續執行 WebJob 失敗，會自動重新啟動。 重試和錯誤動作皆可設定。

當您設定 Web 工作時︰

* 如果您想要讓作業回應事件驅動觸發程序，應該將其設定為 [連續執行] 。 指令碼或程式會儲存在名為 site/wwwroot/app_data/jobs/continuous 的資料夾中。
* 如果您想要作業回應排程驅動觸發程序，您應該將其設定為 [依排程執行] 。 指令碼或程式會儲存在名為 site/wwwroot/app_data/jobs/triggered 的資料夾中。
* 如果在設定作業時選擇 [視需要執行] 選項，它會執行您在啟動它時，與 [依排程執行] 選項相同的程式碼。

Azure WebJobs 在 Web 應用程式的沙箱中執行。 這代表它們可以存取環境變數，並且與 Web 應用程式分享資訊 (例如連接字串)。 作業可存取執行工作之電腦的唯一識別碼。 名為 **AzureWebJobsStorage** 的連接字串為應用程式資料提供 Azure 儲存體佇列、Blob 和資料表的存取權，以及為傳訊和通訊提供服務匯流排的存取權。 名為 **AzureWebJobsDashboard** 的連接字串工作動作記錄檔的存取權。

Azure WebJobs 具有下列特性：

* **安全性**：WebJobs 受到 Web 應用程式的部署認證所保護。
* **支援的檔案類型**：您可以使用命令指令碼 (.cmd)、批次檔 (.bat)、PowerShell 指令碼 (.ps1)、Bash 殼層指令碼 (.sh)、PHP 指令碼 (.php)、Python 指令碼 (.py)、JavaScript 程式碼 (.js) 和可執行程式 (.exe、.jar 等等) 定義 Web 工作。
* **部署**：您可以使用 [Azure 入口網站](/azure/app-service-web/web-sites-create-web-jobs)、使用 [Visual Studio](/azure/app-service-web/websites-dotnet-deploy-webjobs)、使用 [Azure WebJobs SDK](/azure/azure-webjobs-sdk)，或將它們直接複製到下列位置，來部署指令碼和可執行檔：
  * 針對觸發執行：site/wwwroot/app_data/jobs/triggered/{job name}
  * 針對連續執行：site/wwwroot/app_data/jobs/continuous/{job name}
* **記錄**：Console.Out 會被視為 (標示為) INFO。 Console.Error 會被視為 ERROR。 您可以使用 Azure 入口網站存取監視和診斷資訊。 您可以直接從網站下載記錄檔。 這些資訊會儲存在下列位置：
  * 針對觸發執行：Vfs/data/jobs/triggered/jobName
  * 針對連續執行：Vfs/data/jobs/continuous/jobName
* **設定**：您可以使用入口網站、REST API 和 PowerShell 對 Web 工作進行設定。 您可以使用和工作指令碼位於相同根目錄的設定檔 (名為 settings.job) 來提供作業的設定資訊。 例如︰
  * { "stopping_wait_time": 60 }
  * { "is_singleton": true }

#### <a name="considerations"></a>考量

* 根據預設，WebJobs 會跟據 Web 應用程式來調整自己。 不過，您可以藉由將 **is_singleton** 設定屬性設為 **true**，進而設定為在單一執行個體上執行作業。 單一執行個體 Web 工作可用於您不想要調整的工作，或作為同時進行的多重執行個體來執行，例如重新建立索引、資料分析和類似的工作。
* 若要將作業對 Web 應用程式效能的影響降到最低，請考慮在新的 App Service 方案中建立空的 Azure Web 應用程式執行個體，來裝載可能會長時間執行或耗用大量資源的 Web 工作。

### <a name="more-information"></a>詳細資訊
* [Azure WebJobs recommended resources](/azure/app-service-web/websites-webjobs-resources) 列出 Web 工作的許多有用的資源、下載及範例。

### <a name="azure-virtual-machines"></a>Azure 虛擬機器
背景工作的實作方式可能會讓它們無法部署到 Azure Web 應用程式或雲端服務，或是這些選項都不可行。 常見的範例有 Windows 服務、協力廠商公用程式和可執行程式。 另一個例子是針對某個執行環境 (和裝載應用程式的環境不同) 所撰寫的程式。 比方說，它可能是您想要從 Windows 或 .NET 應用程式執行的 Unix 或 Linux 程式。 您可以選擇各式各樣的 Azure 虛擬機器的作業系統，並在該虛擬機器上執行您的服務或可執行檔。

如要了解如何選擇使用虛擬機器的時機，請參閱 [Azure App Services、雲端服務與虛擬機器之比較](/azure/app-service-web/choose-web-site-cloud-service-vm/)。 如需虛擬機器選項的相關資訊，請參閱 [Azure 的虛擬機器和雲端服務大小](http://msdn.microsoft.com/library/azure/dn197896.aspx)。 如需虛擬機器可用之作業系統和預先建立映像的詳細資訊，請參閱 [Azure 虛擬機器 Marketplace](https://azure.microsoft.com/gallery/virtual-machines/)。

若要在個別虛擬機器中起始背景工作，您有一系列的選項可選：

* 您可以藉由將要求傳送至工作所公開的端點，以視需要直接從您的應用程式執行工作。 這會傳入工作所需的任何資料。 此端點會叫用工作。
* 您可以使用所選作業系統中的可用排程器或計時器，將工作設定為依據排程執行。 例如，在 Windows 上可以使用 Windows 工作排程器來執行指令碼和工作。 或者，如果您已在虛擬機器上安裝 SQL Server，您可以使用 SQL Server 代理程式來執行指令碼和工作。
* 您可以使用 Azure 排程器來起始工作，方法是將訊息加入至工作接聽的佇列，或將要求傳送至工作所公開的 API。

如需有關如何啟動背景工作的詳細資訊，請參閱先前的＜ [觸發程序](#triggers) ＞一節。  

#### <a name="considerations"></a>考量
當您決定是否要在 Azure 虛擬機器中部署背景工作時，請考慮下列幾點：

* 在不同的 Azure 虛擬機器中裝載背景工作提供了彈性，並可透過起始、執行、排程及資源配置以精確控制。 不過，如果必須部署虛擬機器，並只是為了執行背景工作，則會增加執行階段成本。
* 沒有任何設備可監視 Azure 入口網站中的工作，且沒有適用於失敗工作的自動重新啟動功能，但您可以監視虛擬機器的基本狀態，並使用 [Azure Resource Manager Cmdlet](https://msdn.microsoft.com/library/mt125356.aspx) 管理它。 不過，計算節點中沒有機制可用來控制程序和執行緒。 通常，使用虛擬機器將會需要額外的工作，才能在工作中實作機制，以便從檢測中收集資料，並從虛擬機器中的作業系統收集資料。 一個可能適當的解決方案是使用 [System Center Management Pack for Azure](https://www.microsoft.com/download/details.aspx?id=50013)。
* 您可以考慮建立透過 HTTP 端點公開的監視探查。 這些探查的程式碼可執行健康狀態檢查、收集操作資訊和統計資料，或自動分頁錯誤資訊，以及將它傳回給管理應用程式。 如需詳細資訊，請參閱 [健康狀態端點監控模式](http://msdn.microsoft.com/library/dn589789.aspx)。

#### <a name="more-information"></a>詳細資訊
* [虛擬機器](https://azure.microsoft.com/services/virtual-machines/) 
* [Azure 虛擬機器常見問題集](/azure/virtual-machines/virtual-machines-linux-classic-faq?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="azure-batch"></a>Azure Batch 

如果您需要跨數十、數百或數千個 VM 執行大型、平行的高效能計算 (HPC) 工作負載，請考慮 [Azure Batch](/azure/batch/)。  

Batch 服務會佈建 VM、將工作指派給 VM、執行工作，並監視進度。 Batch 可以自動相應放大 VM 來反應工作負載。 Batch 也提供作業排程。 Azure Batch 支援 Linux 和 Windows VM。

#### <a name="considerations"></a>考量 

Batch 適合執行本質平行的工作負載。 也可以執行最後有歸納步驟的平行計算，或是執行[訊息傳遞介面 (MPI) 應用程式](/azure/batch/batch-mpi) (用於需要在節點間傳遞訊息的平行工作)。 

節點 (VM) 集區上執行的 Azure Batch 作業。 其中一個做法是只在需要的時候配置集區，且在作業完成後便刪除。 這能將使用率最大化，因為節點不會閒置，但作業必須等候節點的配置。 或者，您可以事先建立集區。 這個做法可以降低作業開始所需的時間，但是會造成節點閒置。 如需詳細資訊，請參閱[集區和計算節點存留期](/azure/batch/batch-api-basics#pool-and-compute-node-lifetime)。

#### <a name="more-information"></a>詳細資訊 

* [使用 Batch 執行本質平行的工作負載](/azure/batch/batch-technical-overview) 
* [使用 Batch 開發大規模的平行運算解決方案](/azure/batch/batch-api-basics) 
* [大規模運算工作負載的 Batch 和 HPC 解決方案](/azure/batch/batch-hpc-solutions)

### <a name="azure-container-service"></a>Azure Container Service 

Azure Container Service 可讓您設定和管理 Azure 中的 VM 叢集，以執行容器化的應用程式。 它提供協調流程的選擇：Docker Swarm 、DC/OS 或 Kubernetes。 

容器適合用於執行背景作業。 以下是其中一些優點： 

- 容器支援高密度裝載。 您可以在容器中隔離背景工作，同時在每個 VM 中入放多個容器。
- 容器協調器會處理內部負載平衡、設定內部網路，以及其他設定工作。
- 視需要啟動或停止容器。 
- Azure Container Registry 可讓您註冊在 Azure 界限內註冊您的容器。 這同時具有安全性、隱私權和相近的優點。 

#### <a name="considerations"></a>考量

- 必須了解如何使用容器協調器。 這會不會是個問題，取決於您的 DevOps 小組技能。  
- 容器服務在 IaaS 環境中執行。 它會在專用 VNet 內佈建 VM 叢集。 

#### <a name="more-information"></a>詳細資訊 

* [使用 Azure Container Service 的 Docker 容器主控解決方案簡介](/azure/container-service/container-service-intro) 
* [私人 Docker 容器登錄的簡介](/azure/container-registry/container-registry-intro) 

### <a name="azure-cloud-services"></a>Azure 雲端服務 
你可以在 Web 角色或另一個背景工作角色中，執行背景工作。 當您決定是否使用背景工作角色時，應該根據考量延展性和彈性需求、工作存留期、釋放頻率、安全性、容錯、爭用、複雜度和邏輯結構。 如需詳細資訊，請參閱 [計算資源彙總模式](http://msdn.microsoft.com/library/dn589778.aspx)。

有數種方式來實作雲端服務角色中的背景工作：

* 在角色中建立 **RoleEntryPoint** 類別的實作，並使用其方法來執行背景工作。 這些工作會在 WaIISHost.exe 的內容中執行。 它們可以使用 **CloudConfigurationManager** 類別的 **GetSetting** 方法來載入組態設定。 如需詳細資訊，請參閱[生命週期](#lifecycle)。
* 應用程式啟動時，使用啟動工作來執行背景工作。 若要強制工作繼續在背景中執行，請將 **taskType** 屬性設為 **background** (如果不這麼做，應用程式啟動處理序將會停止並等待工作完成)。 如需詳細資訊，請參閱 [Run startup tasks in Azure (在 Azure 中執行啟動工作)](/azure/cloud-services/cloud-services-startup-tasks)。
* 您可以使用 WebJobs SDK，實作初始為啟動工作的背景工作 (如 Web 工作)。 如需詳細資訊，請參閱 [在 Azure App Service 中建立 .NET WebJob](/azure/app-service-web/websites-dotnet-webjobs-sdk-get-started)。
* 您可以使用啟動工作，安裝執行一或多個背景工作的 Windows 服務。 您必須將 **taskType** 屬性設為 **background**，以便在背景中執行服務。 如需詳細資訊，請參閱 [Run startup tasks in Azure (在 Azure 中執行啟動工作)](/azure/cloud-services/cloud-services-startup-tasks)。

在 Web 角色中執行背景工作的主要優點在於裝載儲存成本，因為沒有部署其他角色的需求。

在背景工作角色中執行背景工作有下列優點：

* 它可讓您為每種類型的角色個別管理調整。 例如，您可能需要 Web 角色的更多執行個體才能支援目前的負載，但僅需要較少執行背景工作的背景工作角色執行個體。 從 UI 角色調整背景工作分別計算執行個體可以減少裝載成本，同時維持可接受的效能。
* 它會卸載來自 Web 角色的背景工作的處理負擔。 提供 UI 的 Web 角色可以保持回應性，而且也可能意味只需要較少的執行個體，即可支援指定使用者的給定要求數量。
* 它可讓您實作關注的分離動作。 每種角色類型可以實作一組明確定義和相關的特定工作。 這使得設計和維護程式碼更容易，因為每個角色之間的程式碼和功能會有較少的相依關係。
* 它有助於隔離機密的程序和資料。 比方說，實作 UI 的 Web 角色不需要存取背景工作角色所管理和控制的資料。 這可以用來加強安全性，尤其是當您使用 [Gatekeeper Pattern (閘道管理員模式)](http://msdn.microsoft.com/library/dn589793.aspx)之類的模式時。  

#### <a name="considerations"></a>考量
如果使用雲端服務 Web 和背景工作角色，在選擇部署背景工作的方式和位置時，請考量下列重點：

* 在現有 Web 角色中裝載背景工作，會比為了這些工作而執行不同背景工作角色的成本更低。 不過，如果對處理順序和其他資源產生爭用的話，很可能會影響應用程式的效能和可用性。 使用不同的背景工作角色，可防止長時間執行或耗用大量資源的背景工作影響 Web 角色。
* 如果您使用 **RoleEntryPoint** 類別裝載背景工作，您可以輕鬆地將其移到另一個角色。 例如，如果您在 Web 角色中建立類別，並在稍後決定需要在背景工作角色中執行工作的話，您可以將 **RoleEntryPoint** 類別實作移入背景工作角色中。
* 啟動工作是設計來執行程式或指令碼。 將背景作業部署為可執行程式可能更困難，尤其是還需要部署相依組件時。 在您使用啟動工作時，部署和使用指令碼來定義背景作業可能更容易。
* 造成背景工作失敗的例外狀況有不同的影響，視其裝載的方式而定：
  * 如果使用 **RoleEntryPoint** 類別方法，失敗的工作會導致角色重新啟動，讓工作自動重新啟動。 這可能會影響應用程式的可用性。 若要避免這個問題，請確定在 **RoleEntryPoint** 類別及所有背景工作中加入強固的例外狀況處理。 適用時，請使用程式碼來重新啟動失敗的工作，且只有當您無法正常地從程式碼失敗中復原時，才擲回例外狀況以重新啟動角色。
  * 如果您使用啟動工作，您必須負責管理工作執行，以及檢查失敗。
* 管理和監控啟動工作比使用 **RoleEntryPoint** 類別方法更困難。 不過，Azure WebJobs SDK 包含儀表板，方便您更輕鬆管理透過啟動工作起始的 Web 工作。

#### <a name="lifecycle"></a>生命週期 
 如果您決定使用 **RoleEntryPoint** 類別，為使用 Web 和背景工作角色的雲端服務應用程式實作背景工作，請務必瞭解這個類別的生命週期以正確使用。

Web 和背景工作角色在啟動、執行和停止時會經歷一組不同的階段。 **RoleEntryPoint** 類別會公開在發生這些階段時所指示的一系列事件。 您使用這些來初始化、執行並停止您的自訂背景工作。 完整的週期是：

* Azure 會載入角色組件，並搜尋它來找出衍生自 **RoleEntryPoint**的類別。
* 如果找到此類別，它會呼叫 **RoleEntryPoint.OnStart()**。 您覆寫此方法以初始化背景工作。
* 在 **OnStart** 方法完成後，若存在應用程式的通用檔案，則 Azure 會在其中呼叫 **Application_Start()** (例如，在執行 ASP.NET 的 Web 角色中的 Global.asax)。
* Azure 在與 **OnStart()** 平行執行的新前景執行緒上呼叫 **RoleEntryPoint.Run()**。 您覆寫此方法以啟動背景工作。
* Run 方法結束時，Azure 會先在應用程式的全域檔案 (若存在) 中呼叫 **Application_End()**，然後呼叫 **RoleEntryPoint.OnStop()**。 您覆寫 **OnStop** 方法停止您的背景工作、清除資源、處置物件，並關閉工作可能已經使用的連接。
* Azure 背景工作角色主機程序已停止。 此時，角色會被回收並將重新啟動。

如需 **RoleEntryPoint** 類別的使用方法詳細資訊和範例，請參閱 [計算資源彙總模式](http://msdn.microsoft.com/library/dn589778.aspx)(英文)。

#### <a name="implementation-considerations"></a>實作考量

如果您是以 Web 或背景工作角色實作背景工作，請考慮下列幾點：

* **RoleEntryPoint** 類別中的預設 **Run** 方法實作包含呼叫 **Thread.Sleep(Timeout.Infinite)**，可保持角色無限期運作。 如果您覆寫 **Run** 方法 (這通常是執行背景工作所需要) 的話，除非您想要回收角色執行個體，否則不得允許程式碼從方法中結束。
* **Run** 方法的典型實作包含用來啟動每一個背景工作的程式碼，以及定期檢查所有背景工作狀態的迴圈建構。 它可以重新啟動任何失敗，或監視指出作業已完成的取消權杖。
* 如果背景工作擲回未處理的例外狀況，應該回收該工作，同時允許角色中的任何其他背景工作繼續執行。 不過，如果例外狀況由工作的外部物件損毀所造成，例如共用儲存體，則必須由 **RoleEntryPoint** 類別處理例外狀況，且應該取消所有工作，並允許 **Run** 方法結束。 Azure 接著會重新啟動角色。
* 使用 **OnStop** 方法來暫停或終止背景工作並清除資源。 這可能需要停止長時間執行或多步驟的工作。 請務必考慮如何完成這項動作來避免資料不一致。 如果角色執行個體因任何使用者起始關機以外的原因停止的話， **OnStop** 方法中執行的程式碼必須在五分鐘內完成，否則它將會強制終止。 請確定您的程式碼可以在這段期間內完成，或是可以容忍執行不完成。  
* 當 **RoleEntryPoint.OnStart** 方法傳回 **true** 時，Azure Load Balancer 會開始將流量導向至角色執行個體。 因此，請考慮將所有初始化程式碼置於 **OnStart** 方法中，讓未成功初始化的角色執行個體不會收到任何流量。
* 除了 **RoleEntryPoint** 類別的方法外，您還可以使用啟動工作。 您應該使用啟動工作來初始化任何需要在 Azure 負載平衡器中變更的設定，因為這些工作會在角色接收任何要求前執行。 如需詳細資訊，請參閱 [Run startup tasks in Azure (在 Azure 中執行啟動工作)](/azure/cloud-services/cloud-services-startup-tasks/)。
* 如果啟動工作中發生錯誤，它可能會強制角色持續地重新啟動。 這可能會防止您執行虛擬 IP (VIP) 位址交換回到先前預備的版本，因為交換需要角色的獨佔存取權。 這獨佔存取權無法在角色重新啟動時取得。 若要解決這個問題：
  
  * 在您的角色中，將下列程式碼新增至 **OnStart** 和 **Run** 方法的開頭：
    
    ```C#
    var freeze = CloudConfigurationManager.GetSetting("Freeze");
    if (freeze != null)
    {
     if (Boolean.Parse(freeze))
       {
         Thread.Sleep(System.Threading.Timeout.Infinite);
     }
    }
    ```
    
  * 針對角色，將 **Freeze** 設定的定義做為布林值新增至 ServiceDefinition.csdef 和 ServiceConfiguration.\*.cscfg 檔案，並將它設定為 **false**。 如果角色進入重複的重新啟動模式，您可以將設定變更為 **true** 以凍結角色執行，並允許它交換先前版本。

#### <a name="more-information"></a>詳細資訊
* [計算資源彙總模式 (英文)](http://msdn.microsoft.com/library/dn589778.aspx)
* [Get started with the Azure WebJobs SDK (開始使用 Azure WebJobs SDK)](/azure/app-service-web/websites-dotnet-webjobs-sdk-get-started/)


## <a name="partitioning"></a>分割
如果您決定讓背景工作加入現有的計算執行個體 (例如 Web 應用程式、Web 角色、現有的背景工作角色，或虛擬機器)，您必須考慮這對計算執行個體的品質屬性和背景工作本身的影響。 這些因素會幫助您決定是否要以現有的計算執行個體共置工作，或將它們分成不同的計算執行個體：

* **可用性**：背景工作可能不需要具有應用程式的其他部分所擁有的相同可用性層級，特別是直接參與使用者互動的 UI 和其他部分。 由於可將作業排入佇列，背景工作可能更容許延遲、重試的連線失敗，及影響可用性的其他因素。 不過，必須有足夠的容量以防止備份可能會封鎖佇列及影響整個應用程式的要求。
* **延展性**：背景工作對 UI 和應用程式的互動部分，可能有不同的延展性需求。 可能需要調整 UI 以符合要求的尖峰期，而未完成的背景工作可在較空閒的時間，由較少的計算執行個體數目完成。
* **恢復功能**：如果僅裝載背景工作的要求可以直到工作再次可用為止，先排入佇列或延遲，則這些工作的計算執行個體失敗可能不會嚴重影響整體應用程式。 如果計算執行個體及/或工作可以在適當的間隔內重新啟動，可能不會影響應用程式的使用者。
* **安全性**：相較於 UI 或應用程式的其他部分，背景工作可能會有不同的安全性需求或限制。 藉由使用不同的計算執行個體，您可以為工作指定不同的安全性環境。 您也可以使用模式 (例如閘道管理員)，將背景計算執行個體與 UI 隔離，以提供最大的安全性和區隔。
* **效能**：您可以選擇背景工作的計算執行個體類型，以符合工作的效能特定需求。 這可能代表當工作不需要與 UI 具有相同的處理能力的話，可使用較便宜的計算選項；或如果他們需要額外的容量和資源的話，則需要更大的執行個體。
* **可管理性**：相較於主應用程式程式碼或 UI，背景工作可能有不同的開發和部署節奏。 將它們部署到不同的計算執行個體可簡化更新與版本控制。
* **成本**：加入計算執行個體來執行背景工作會增加裝載成本。 您應該仔細考量額外的容量與這些額外的成本之間的取捨。

如需詳細資訊，請參閱 [Leader Election Pattern (前置選擇模式)](http://msdn.microsoft.com/library/dn568104.aspx) 和 [Competing Consumers Pattern (競爭取用者模式)](http://msdn.microsoft.com/library/dn568101.aspx)。

## <a name="conflicts"></a>衝突
如果您有背景作業的多個執行個體，它們就可能爭用資源和服務的存取權，例如資料庫和儲存體。 這個並行存取可能會導致資源爭用情況，其可能會造成服務可用性及儲存體中資料完整性衝突。 您可以使用悲觀鎖定方法來解決資源爭用的情況。 這可避免相互競爭的工作執行個體同時存取服務或損毀資料。

另一種解決衝突的方法是將背景工作定義為單一性，如此只有一個執行中的執行個體。 不過，這會排除多個執行個體設定可提供的可靠性和效能權益。 若 UI 可以提供足夠的工作，以讓多個背景工作保持忙碌時更是如此。

請務必確定背景工作可以自動重新啟動，而且它有足夠的容量來應付需求的尖峰期。 您可配置給計算執行個體足夠的資源、實作可儲存要求的佇列機制，供稍後需求降低時執行；或使用這些技術組合，藉此完成這個工作。

## <a name="coordination"></a>協調
背景工作可能很複雜，而且可能需要執行多個個別的工作以產生結果或滿足所有需求。 通常在這些案例中可將工作分成較小的步驟或子工作，並由多個取用者執行。 多步驟作業可以更有效率且更具彈性，因為個別步驟可能可在多個作業中重複使用。 它也能輕鬆地新增、移除或修改步驟的順序。

協調多個工作和步驟可能相當困難，但是有三種常見的模式可用來引導您的實作方案：

* **將一個工作分解成多個可重複使用的步驟**。 應用程式可能需要在其處理的資訊上，執行具有不同複雜度的各種工作。 有一項實作此應用程式簡單但具彈性的方法，此方法為將這項處理序作為整合模組執行。 不過，這種方法可能會降低重構程式碼、最佳化它，或在應用程式的其他位置需要相同處理程序的部分時，重複使用它的機會。 如需詳細資訊，請參閱 [管道與篩選模式](http://msdn.microsoft.com/library/dn568100.aspx)(英文)。
* **管理工作的步驟執行**。 應用程式可能會執行包含數個步驟 (其中有些可能會叫用遠端服務或存取遠端資源) 的工作。 個別的步驟可能會彼此獨立，但是它們會由實作工作的應用程式邏輯進行協調。 如需詳細資訊，請參閱 [排程器代理程式監督員模式](http://msdn.microsoft.com/library/dn589780.aspx)(英文)。
* **管理失敗工作步驟的復原**。 如果一個或多個步驟失敗，應用程式可能需要對執行一系列步驟的工作進行復原 (它們會共同定義最終的一致作業)。 如需詳細資訊，請參閱 [補償交易模式](http://msdn.microsoft.com/library/dn589804.aspx)(英文)。


## <a name="resiliency-considerations"></a>恢復功能考量
背景工作必須具有恢復功能，以便為應用程式提供可靠的服務。 當您規劃和設計背景工作時，請考慮下列幾點：

* 背景工作必須能正常處理角色或服務重新啟動，而不會損毀資料或導致應用程式不一致。 對於長時間執行或多步驟的工作，請考慮使用「檢查指出」  ，方法是在永續性儲存體中儲存作業狀態，或在佇列中作為訊息儲存 (如果這是適當的)。 例如，您可以在佇列的訊息中永久保存狀態資訊，並使用工作進度累加地更新此狀態資訊，以便從上次已知的良好檢查點處理工作，而不是從頭重新啟動。 使用 Azure 服務匯流排佇列時，您可以使用訊息工作階段來啟用相同的案例。 工作階段可讓您使用 [SetState](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate?view=azureservicebus-4.0.0) 和 [GetState](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate?view=azureservicebus-4.0.0) 方法來儲存和擷取應用程式處理狀態。 如需設計可靠的多步驟處理序和工作流程詳細資訊，請參閱 [Scheduler Agent Supervisor Pattern (排程器代理程式監督員模式)](http://msdn.microsoft.com/library/dn589780.aspx)。
* 使用 Web 或背景工作角色來裝載多個背景工作時，請設計 **Run** 方法的覆寫來監視失敗或已停止的工作，並重新啟動它們。 若不實用，而且您正在使用背景工作角色，請從 **Run** 方法退出，以強制重新啟動背景工作角色。
* 當您使用佇列與背景工作通訊時，佇列可在應用程式高於一般負載時，作為緩衝區來儲存傳送給工作的要求。 如此能讓工作在較空閒期間使用 UI。 這也表示回收角色不會封鎖 UI。 如需詳細資訊，請參閱 [佇列型負載調節模式](http://msdn.microsoft.com/library/dn589783.aspx)(英文)。 如果某些工作比其他工作更重要，請考慮實作 [優先順序佇列模式](http://msdn.microsoft.com/library/dn589794.aspx) (英文)，確保這些工作在較不重要的工作之前執行。
* 必須將由訊息或處理訊息初始化的背景工作設計為能夠處理不一致情況，例如以錯誤順序到達的訊息、重複導致錯誤的訊息 (通常稱為「有害訊息」 )，以及多次傳遞的訊息。 請考慮下列：
  * 必須依特定順序處理訊息，像是根據其現有值變更資料的訊息 (例如，將值加入至現有值)，可能不會以其原始的傳送順序到達。 或者，可能因每個執行個體上變動的負載，而依不同順序由背景工作的不同執行個體處理。 必須依特定順序處理的訊息應該包含序號、金鑰或其他某些指標，好讓背景工作使用以確保能依正確的順序處理這些訊息。 如果您使用 Azure 服務匯流排，您可以使用訊息工作階段來保證傳遞順序。 不過，盡可能設計處理序以讓訊息順序變得不重要，通常會更有效率。
  * 一般而言，背景工作會在佇列中查看訊息，這會暫時對其他訊息取用者隱藏這些訊息。 然後它會在成功處理完畢後刪除訊息。 如果背景工作在處理訊息時失敗，該訊息將會在查看逾時到期之後重新出現在佇列上。 它將會由工作的另一個執行個體進行處理，或在此執行個體的下一個處理週期進行處理。 如果訊息在取用者中發生相同的錯誤，它會封鎖工作、佇列，最終在佇列已滿時封鎖應用程式本身。 因此，請務必從佇列偵測並移除有害訊息。 如果您使用 Azure 服務匯流排，造成錯誤的訊息可能會被自動或手動地移至相關聯的無效信件佇列。
  * 佇列會保證「至少一次」  使用傳遞機制，但它們可能會傳遞相同的訊息多次。 此外，如果背景工作在處理訊息後、但在從佇列刪除前失敗，訊息將會變成可再次處理。 背景工作應該具有等冪性，這表示超過一次處理相同的訊息不會造成錯誤，或使應用程式的資料不一致。 某些作業自然是等冪，例如將儲存的值設為特定的新值。 不過，將值加入到現有的儲存值，而不檢查儲存值是否仍然與原先傳送的訊息相同之類的作業，可能會導致不一致情況。 Azure 服務匯流排佇列可以設定為自動移除重複的訊息。
  * 某些傳訊系統 (例如 Azure 儲存體佇列和 Azure 服務匯流排佇列) 支援 de-queue count 屬性，指出已從佇列讀取訊息的次數。 這在處理重複及有害訊息時很有用。 如需詳細資訊，請參閱[非同步傳訊入門](http://msdn.microsoft.com/library/dn589781.aspx)和[等冪性模式](http://blog.jonathanoliver.com/2010/04/idempotency-patterns/)。

## <a name="scaling-and-performance-considerations"></a>調整和效能考量
背景工作必須提供足夠的效能，確保它們不會封鎖應用程式，或不會因系統負載不足而延遲作業時導致不一致。 一般而言，藉由調整裝載背景工作的計算執行個體可提升效能。 當您規劃和設計背景工作時，請考慮下列有關延展性和效能的重點：

* Azure 會根據目前的需求和負載，或預先定義的排程，來支援 Web 應用程式、雲端服務 Web 和背景工作角色，及虛擬機器裝載部署的自動調整 (向外及往內延展都支援)。 使用此功能可確保整體應用程式具備足夠的效能，同時將執行階段成本降到最低。
* 當背景工作具有雲端服務應用程式的其他部分的不同效能時 (例如，UI 或元件，如資料存取層)，可將不同背景工作角色的背景工作裝載在一起，讓 UI 和背景工作角色進行調整而不理會負載管理。 如果多個背景工作彼此有明顯不同的效能，請考慮將它們分成不同的背景工作角色並單獨調整每個角色類型。 但請注意，相較於將所有工作結合成較少的角色，這可能會增加執行階段成本。
* 只是調整角色可能不足以防止在高負載下損失效能。 您也可能需要調整儲存體佇列和其他資源，以避免整體處理鏈的單一點成為瓶頸。 另外，請考慮其他限制，例如儲存體的最大輸送量，和應用程式和背景工作依賴的其他服務。
* 您必須針對調整設計背景工作。 比方說，他們必須能夠以動態方式偵測正在使用的儲存體佇列數目，以接聽或傳送訊息給適當的佇列。
* 根據預設，WebJobs 會根據相關聯的 Azure Web 應用程式執行個體來調整。 不過，如果您只想要將 WebJob 當作單一執行個體來執行，您可以建立包含 JSON 資料 **{ "is_singleton": true }** 的 Settings.job 檔案。 這會強制 Azure 只能執行單一 Web 工作的執行個體，即使有多個相關聯的 Web 應用程式執行個體也一樣。 這可能是針對必須執行為單一執行個體之排程作業的有用技巧。

## <a name="related-patterns"></a>相關的模式
* [非同步傳訊入門 (英文)](http://msdn.microsoft.com/library/dn589781.aspx)
* [自動調整指引 (英文)](http://msdn.microsoft.com/library/dn589774.aspx)
* [補償交易模式 (英文)](http://msdn.microsoft.com/library/dn589804.aspx)
* [競爭取用者模式 (英文)](http://msdn.microsoft.com/library/dn568101.aspx)
* [計算分割指引 (英文)](http://msdn.microsoft.com/library/dn589773.aspx)
* [計算資源彙總模式 (英文)](http://msdn.microsoft.com/library/dn589778.aspx)
* [閘道管理員模式 (英文)](http://msdn.microsoft.com/library/dn589793.aspx)
* [選出領導者模式 (英文)](http://msdn.microsoft.com/library/dn568104.aspx)
* [管道與篩選器 (英文)](http://msdn.microsoft.com/library/dn568100.aspx)
* [優先順序佇列模式 (英文)](http://msdn.microsoft.com/library/dn589794.aspx)
* [Queue-based Load Leveling Pattern (佇列型負載調節模式)](http://msdn.microsoft.com/library/dn589783.aspx)
* [排程器代理程式監督員模式 (英文)](http://msdn.microsoft.com/library/dn589780.aspx)

## <a name="more-information"></a>詳細資訊
* [調整使用背景工作角色的 Azure 應用程式 (英文)](http://msdn.microsoft.com/library/hh534484.aspx#sec8)
* [執行背景工作 (英文)](http://msdn.microsoft.com/library/ff803365.aspx)
* [Azure 角色啟動生命週期](http://blog.syntaxc4.net/post/2011/04/13/windows-azure-role-startup-life-cycle.aspx) (英文) (部落格文章)
* [Azure 雲端服務角色生命週期](http://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Windows-Azure-Cloud-Services-Role-Lifecycle) (英文) (影片)
* [什麼是 Azure WebJob SDK](https://docs.microsoft.com/azure/app-service-web/websites-dotnet-webjobs-sdk)
* [使用 WebJob 執行背景工作](https://docs.microsoft.com/azure/app-service-web/web-sites-create-web-jobs)
* [Azure 佇列和服務匯流排佇列 - 異同比較 (英文)](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted)
* [如何在雲端服務中啟用診斷](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics)

