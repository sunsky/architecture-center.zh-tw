---
title: 在 Kubernetes 上建置微服務的 CI/CD 管線
description: 描述將微服務部署至 Azure Kubernetes Service (AKS) 範例 CI/CD 管線。
author: MikeWasson
ms.date: 04/11/2019
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: microservices
ms.openlocfilehash: e7da8a1b4111fb7856b919b40d033833a4e475e0
ms.sourcegitcommit: d58e6b2b891c9c99e951c59f15fce71addcb96b1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/12/2019
ms.locfileid: "59533168"
---
<!-- markdownlint-disable MD040 -->

# <a name="building-a-cicd-pipeline-for-microservices-on-kubernetes"></a>在 Kubernetes 上建置微服務的 CI/CD 管線

它可接受建立可靠的 CI/CD 程序，在微服務架構。 個別小組必須能夠快速且可靠地發行服務而不會中斷其他小組或整個應用程式造成不穩定。

本文說明將微服務部署至 Azure Kubernetes Service (AKS) 範例 CI/CD 管線。 每個小組和專案都不同，因此不需要這篇文章為一組 hard-and-fast 規則。 相反地，它已經是用來設計您自己的 CI/CD 程序的起始點。

此處所述的範例管線建立呼叫無人機遞送應用程式，您可以找到的微服務的參考實作[GitHub][ri]。 描述應用程式案例[此處](./design/index.md#reference-implementation)。

管線的目標可以摘要如下：

- 小組可以建置和獨立部署其服務。
- 傳遞 CI 程序的程式碼變更會自動部署至類似生產的環境。
- 強制執行管線各階段的品管標準。
- 服務的新版本可以與舊版並存部署。

如需詳細背景，請參閱 <<c0> [ 微服務架構的 CI/CD](./ci-cd.md)。

## <a name="assumptions"></a>假設

基於此範例的詳細資訊，以下是一些假設開發小組和程式碼基底：

- 程式碼存放庫是 monorepo，依微服務的資料夾。
- 小組的分支策略是以[主幹型開發](https://trunkbaseddevelopment.com/)為基礎。
- 小組會使用[發行分支](/azure/devops/repos/git/git-branching-guidance?view=azure-devops#manage-releases)管理發行。 不同的版本會針對每個微服務。
- CI/CD 程序會使用[Azure 管線](/azure/devops/pipelines/?view=azure-devops)建置、 測試及部署到 AKS 的微服務。
- 每個微服務的容器映像會儲存在[Azure Container Registry](/azure/container-registry/)。
- 小組會使用 Helm 圖表來封裝每個微服務。

這些假設磁碟機有許多的 CI/CD 管線的特定詳細資料。 不過，此處所述的基本方法是適用於其他處理程序、 工具和服務，例如 Jenkins 或 Docker Hub。

## <a name="validation-builds"></a>驗證組建

假設開發人員正在努力微服務，稱為 「 遞送 」 服務。 在開發新功能的同時，開發人員會將程式碼簽入功能分支。 依照慣例，功能分支會命名為 `feature/*`。

![CI/CD 工作流程](./images/aks-cicd-1.png)

組建定義檔會包含分支名稱和來源路徑，藉此篩選的觸發程序：

```yaml
trigger:
  batch: true
  branches:
    include:
    - master
    - feature/*
    - topic/*

    exclude:
    - feature/experimental/*
    - topic/experimental/*

  paths:
     include:
     - /src/shipping/delivery/
```

&#11162;請參閱[原始程式檔](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-validation.yml)。

使用此方法，每個小組都可以有自己的組建管線。 簽入程式碼`/src/shipping/delivery`資料夾就會觸發之組建的 「 遞送 」 服務。 將認可推送至符合篩選條件的分支會觸發在 CI 組建。 此時在流程中，CI 組建會執行一些最基本的程式碼驗證：

1. 建置程式碼。
1. 執行單元測試。

目標是讓建置時間短，因此開發人員可以快速意見反應。 開發人員功能合併至 master 準備就緒後，開啟 pr。 這會觸發另一個 CI 組建，以執行一些額外的檢查：

1. 建置程式碼。
1. 執行單元測試。
1. 建置執行階段容器映像。
1. 映像上執行弱點掃描。

![CI/CD 工作流程](./images/aks-cicd-2.png)

> [!NOTE]
> 在 Azure DevOps 的存放庫，您可以定義[原則](/azure/devops/repos/git/branch-policies)保護分支。 例如，原則可能需要成功的 CI 組建再加上核准者的簽核，才能合併至主體。

## <a name="full-cicd-build"></a>完整的 CI/CD 建置

在某個時間點，小組已準備好部署新版的「遞送」服務。 發行管理員會從與這個命名模式的主機建立分支： `release/<microservice name>/<semver>`。 例如： `release/delivery/v1.0.2`。

![CI/CD 工作流程](./images/aks-cicd-3.png)

建立此分支會觸發完整的 CI 組建執行的所有加上前一個步驟：

1. 將容器映像推送至 Azure Container Registry。 映像會以取自分支名稱的版本號碼來標記。
2. 執行`helm package`來封裝服務的 Helm 圖表。 圖表也附有版本號碼。
3. Helm 套件推送至容器登錄。

假設此建置成功時，觸發的部署 (CD) 程序，使用 Azure 管線[發行管線](/azure/devops/pipelines/release/what-is-release-management)。 此管線有下列步驟：

1. 部署至 QA 環境的 Helm 圖表。
1. 將套件移到生產環境之前，核准者會先進行簽核。 請參閱[使用核准功能的發行部署控制](/azure/devops/pipelines/release/approvals/approvals)。
1. Retag 生產環境中的命名空間 Azure Container Registry 的 Docker 映像。 例如，如果目前的標記是 `myrepo.azurecr.io/delivery:v1.0.2`，則生產標記就會是 `myrepo.azurecr.io/prod/delivery:v1.0.2`。
1. 部署到生產環境的 Helm 圖表。

即使在 monorepo，這些工作可以設定為個別的微服務，以便小組可以部署具有高速度。 程序有一些手動步驟：核准 PR、建立發行分支，以及將部署核准到生產叢集中。 這些步驟都是由原則手動&mdash;它們可自動化如果組織的偏好。

## <a name="isolation-of-environments"></a>環境隔離

您部署服務時會用到多個環境，這些環境分別用於開發、煙霧測試 (Smoke Test)、整合測試、負載測試和最後的生產環境。 這些環境需要某種程度的隔離。 在 Kubernetes 中，您可以選擇實體隔離和邏輯隔離。 實體隔離表示部署到不同叢集。 邏輯隔離會使用命名空間和原則，如先前所述。

我們建議您建立專用的生產環境叢集，以及用於開發/測試環境的個別叢集。 使用邏輯隔離來區隔開發/測試叢集內的環境。 部署到開發/測試叢集的服務應一律不能存取保存商務資料的資料存放區。

## <a name="build-process"></a>建置程序

如果可能的話，請到 Docker 容器封裝建置程序。 可讓您建置您的程式碼成品使用 Docker，而不需要設定每個組建電腦上的建置環境。 容器化的組建程序輕鬆地相應放大 CI 管線藉由新增新的組建代理程式。 此外，任何小組的開發人員可以建置程式碼，只要執行組建容器。

藉由使用多階段建置在 Docker 中，您可以定義單一的 Dockerfile 中的建置環境和執行階段映像。 例如，以下是建置 ASP.NET Core 應用程式的 Dockerfile:

```
FROM microsoft/dotnet:2.2-runtime AS base
WORKDIR /app

FROM microsoft/dotnet:2.2-sdk AS build
WORKDIR /src/Fabrikam.Workflow.Service

COPY Fabrikam.Workflow.Service/Fabrikam.Workflow.Service.csproj .
RUN dotnet restore Fabrikam.Workflow.Service.csproj

COPY Fabrikam.Workflow.Service/. .
RUN dotnet build Fabrikam.Workflow.Service.csproj -c Release -o /app

FROM build AS publish
RUN dotnet publish Fabrikam.Workflow.Service.csproj -c Release -o /app

FROM base AS final
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "Fabrikam.Workflow.Service.dll"]
```

&#11162;請參閱[原始程式檔](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/workflow/Dockerfile)。

這個 Dockerfile 中定義數個建置階段。 請注意，名為階段`base`名為的階段時使用的 ASP.NET Core 執行階段中，`build`使用完整的 ASP.NET Core SDK。 `build`階段用來建置 ASP.NET Core 專案。 但最後的執行階段容器從建置`base`，其中包含執行階段，而且是比完整的 SDK 映像小得多。

### <a name="building-a-test-runner"></a>建置的測試執行器

另一個很好的作法是在容器中執行單元測試。 例如，以下是組建的測試執行器的 Docker 檔案的一部分：

```
FROM build AS testrunner
WORKDIR /src/tests

COPY Fabrikam.Workflow.Service.Tests/*.csproj .
RUN dotnet restore Fabrikam.Workflow.Service.Tests.csproj

COPY Fabrikam.Workflow.Service.Tests/. .
ENTRYPOINT ["dotnet", "test", "--logger:trx"]
```

開發人員可以使用這個 Docker 檔案在本機執行測試：

```bash
docker build . -t delivery-test:1 --target=testrunner
docker run -p 8080:8080 delivery-test:1
```

CI 管線也應該執行測試做為組建驗證步驟的一部分。

請注意，此檔案會使用 Docker`ENTRYPOINT`命令來執行測試，而不是 Docker`RUN`命令。

- 如果您使用`RUN`命令時，每次建置映像，執行測試。 使用`ENTRYPOINT`，測試是選擇性的。 只有在您明確設為目標時，才執行`testrunner`階段。
- 失敗的測試不會造成 Docker`build`命令失敗。 如此一來您可以區分容器建置失敗的測試失敗。
- 測試結果可以儲存至掛接的磁碟區。

### <a name="container-best-practices"></a>容器的最佳作法

以下是其他一些最佳作法，應考量的容器：

- 為部署到叢集 (Pod、服務等等) 的資源，定義容器標籤、版本控制和命名慣例的組織通用慣例。 這可讓您更輕鬆地診斷部署問題。

- 在開發和測試週期內，CI/CD 程序將會建立許多容器映像。 只有部分這些映像的版本中，候選項目，然後只有其中一些發行候選項目將會提升至生產環境。 有明確的版本控制策略，好讓您知道哪些映像目前已部署到生產環境，並可以回復至先前的版本如有必要。

- 一律不部署特定容器的版本標籤， `latest`。

- 使用[命名空間](/azure/container-registry/container-registry-best-practices#repository-namespaces)隔離核准用於生產環境仍正在測試的映像從映像的 Azure Container Registry 中。 不將映像移到生產命名空間，直到您準備好部署到生產環境。 如果您結合這種做法與容器映像的語意版本控制，就能盡量避免意外部署到未獲准發行的版本。

- 無權限使用者身分執行的容器，以遵循最低權限原則。 在 Kubernetes 中，您可以建立 pod 安全性原則，可防止身分執行的容器*根*。 請參閱[以根權限執行時，防止 Pod](https://docs.bitnami.com/kubernetes/how-to/secure-kubernetes-cluster-psp/)。

## <a name="helm-charts"></a>Helm 圖表

請考慮使用 Helm 來管理服務的建置和部署。 以下是 helm 的一些可協助您的 CI/CD 功能：

- 單一的微服務通常會定義多個 Kubernetes 物件。 Helm 允許這些物件會封裝成單一的 Helm 圖表。
- 圖表可以部署單一的 Helm 命令，而不是一系列的 kubectl 命令。
- 圖表是明確地建立版本。 使用 Helm 發行版本、 檢視版本中，並回復到先前的版本。 使用語意化版本控制系統來追蹤更新和修訂，並且具有能夠復原到先前版本的功能。
- Helm 圖表會使用範本，來避免重複資訊，例如標籤和選取器，在許多檔案。
- Helm 可以管理之間圖表的相依性。
- 圖表可以儲存在 Helm 存放庫，例如 Azure Container Registry，並整合到組建管線。

如需有關使用 Container Registry 作為 Helm 存放庫的詳細資訊，請參閱[使用 Azure Container Registry 作為您應用程式圖表的 Helm 存放庫](/azure/container-registry/container-registry-helm-repos)。

單一的微服務可能牽涉到多個 Kubernetes 組態檔。 更新服務可能是指觸碰所有這些檔案，以更新選取器、 標籤和映像標記。 Helm 會將這些稱為圖表的單一套件，並可讓您輕鬆地更新的 YAML 檔案使用變數。 Helm 會使用範本基礎的語言 （移至範本），讓您撰寫參數化的 YAML 組態檔。

例如，以下是定義在部署 YAML 檔案的一部分：

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "package.fullname" . | replace "." "" }}
  labels:
    app.kubernetes.io/name: {{ include "package.name" . }}
    app.kubernetes.io/instance: {{ .Release.Name }}
  annotations:
    kubernetes.io/change-cause: {{ .Values.reason }}

...

  spec:
      containers:
      - name: &package-container_name fabrikam-package
        image: {{ .Values.dockerregistry }}/{{ .Values.image.repository }}:{{ .Values.image.tag }}
        imagePullPolicy: {{ .Values.image.pullPolicy }}
        env:
        - name: LOG_LEVEL
          value: {{ .Values.log.level }}
```

&#11162;請參閱[原始程式檔](https://github.com/mspnp/microservices-reference-implementation/blob/master/charts/package/templates/package-deploy.yaml)。

您可以看到部署名稱、 標籤與容器規格所有使用範本參數，在部署期間所提供。 例如，從命令列：

```bash
helm install $HELM_CHARTS/package/ \
     --set image.tag=0.1.0 \
     --set image.repository=package \
     --set dockerregistry=$ACR_SERVER \
     --namespace backend \
     --name package-v0.1.0
```

雖然您的 CI/CD 管線無法圖表直接安裝至 Kubernetes，則建議您建立的圖表封存 （.tgz 檔案），以及將圖表推送至 Helm 存放庫，例如 Azure Container Registry。 如需詳細資訊，請參閱 <<c0> [ 封裝 Docker 為基礎的應用程式中 Azure 管線中的 Helm 圖表](/azure/devops/pipelines/languages/helm?view=azure-devops)。

請考慮將 Helm 部署到自己的命名空間，並使用角色型存取控制 (RBAC) 來限制它可以部署到哪一個命名空間。 如需詳細資訊，請參閱 <<c0> [ 角色型存取控制](https://helm.sh/docs/using_helm/#helm-and-role-based-access-control)Helm 文件中。

### <a name="revisions"></a>修訂

Helm 圖表一律有版本號碼，必須使用[語意版本設定](https://semver.org/)。 圖表也可以有`appVersion`。 此欄位是選擇性的而且不一定要關聯至圖表的版本。 有些小組可能會想要的應用程式版本分別從圖表的更新。 但更簡單的方法是使用一個版本號碼，因此圖表版本和應用程式版本之間的 1 對 1 關聯。 這樣一來，您可以儲存每個版本的一份圖表，並輕鬆地部署所需的版本：

```bash
helm install <package-chart-name> --version <desiredVersion>
```

另一個很好的做法是提供部署範本中的變更原因註釋：

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "delivery.fullname" . | replace "." "" }}
  labels:
     ...
  annotations:
    kubernetes.io/change-cause: {{ .Values.reason }}
```

這可讓您檢視每個修訂的 [變更原因] 欄位使用`kubectl rollout history`命令。 在上述範例中，變更原因被提供的 Helm 圖表參數。

```bash
$ kubectl rollout history deployments/delivery-v010 -n backend
deployment.extensions/delivery-v010
REVISION  CHANGE-CAUSE
1         Initial deployment
```

您也可以使用`helm list`命令來檢視修訂歷程記錄：

```bash
~$ helm list
NAME            REVISION    UPDATED                     STATUS        CHART            APP VERSION     NAMESPACE
delivery-v0.1.0 1           Sun Apr  7 00:25:30 2019    DEPLOYED      delivery-v0.1.0  v0.1.0          backend
```

> [!TIP]
> 使用`--history-max`初始化 Helm 時加上旗標。 此設定會限制 Tiller 儲存其歷程記錄中的修訂編號。 Tiller configmaps 儲存修訂歷程記錄。 如果您要經常發行更新，configmaps 可以成長非常大，除非您限制記錄大小。

## <a name="azure-devops-pipeline"></a>Azure DevOps Pipeline

在 Azure 的管線，管線會分為*建置管線*並*發行管線*。 建置管線執行 CI 程序，並建立組建成品。 在 Kubernetes 上的微服務架構，這些成品的容器映像和是定義每個微服務的 Helm 圖表。 發行管線會執行該 CD 程序來將微服務部署到叢集。

這篇文章稍早所述的 CI 流程為基礎，建置管線可能包含下列工作：

1. 建置測試執行器的容器。

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        arguments: '--pull --target testrunner'
        dockerFile: $(System.DefaultWorkingDirectory)/$(dockerFileName)
        imageName: '$(imageName)-test'
    ```

1. 藉由叫用測試執行器容器上執行的 docker 中執行測試。

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        command: 'run'
        containerName: testrunner
        volumes: '$(System.DefaultWorkingDirectory)/TestResults:/app/tests/TestResults'
        imageName: '$(imageName)-test'
        runInBackground: false
    ```

1. 發行測試結果。 請參閱[整合組建和測試工作](/azure/devops/pipelines/languages/docker?view=azure-devops&tabs=yaml#integrate-build-and-test-tasks)。

    ```yaml
    - task: PublishTestResults@2
      inputs:
        testResultsFormat: 'VSTest'
        testResultsFiles: 'TestResults/*.trx'
        searchFolder: '$(System.DefaultWorkingDirectory)'
        publishRunAttachments: true
    ```

1. 建置執行階段容器。

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        dockerFile: $(System.DefaultWorkingDirectory)/$(dockerFileName)
        includeLatestTag: false
        imageName: '$(imageName)'
    ```

1. 推送至 Azure Container Registry （或其他容器登錄） 的容器。

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        command: 'Push an image'
        imageName: '$(imageName)'
        includeSourceTags: false
    ```

1. 封裝 Helm 圖表。

    ```yaml
    - task: HelmDeploy@0
      inputs:
        command: package
        chartPath: $(chartPath)
        chartVersion: $(Build.SourceBranchName)
        arguments: '--app-version $(Build.SourceBranchName)'
    ```

1. Helm 套件推送至 Azure Container Registry （或其他 Helm 存放庫）。

    ```yaml
    task: AzureCLI@1
      inputs:
        azureSubscription: $(AzureSubscription)
        scriptLocation: inlineScript
        inlineScript: |
        az acr helm push $(System.ArtifactsDirectory)/$(repositoryName)-$(Build.SourceBranchName).tgz --name $(AzureContainerRegistry);
    ```

&#11162;請參閱[原始程式檔](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-ci.yml)。

CI 管線的輸出是可實際執行的容器映像和微服務更新的 Helm 圖表。 到目前為止，發行管線可以接管。 它會執行下列步驟：

- 部署至開發人員/QA/預備環境。
- 等待核准者核准或拒絕部署。
- Retag 容器映像版本
- 推送至容器登錄的版本標記。
- 升級生產環境叢集中的 Helm 圖表。

如需建立發行管線的詳細資訊，請參閱 <<c0> [ 發行管線草稿版本，並釋放選項](/azure/devops/pipelines/release/?view=azure-devops)。

下圖顯示這篇文章中的端對端的 CI/CD 程序：

![CD/CD 管線](./images/aks-cicd-flow.png)

## <a name="next-steps"></a>後續步驟

這篇文章根據您可以找到的參考實作[GitHub][ri]。

[ri]: https://github.com/mspnp/microservices-reference-implementation