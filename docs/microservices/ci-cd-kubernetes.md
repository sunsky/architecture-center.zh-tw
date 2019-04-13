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

# <a name="building-a-cicd-pipeline-for-microservices-on-kubernetes"></a><span data-ttu-id="32178-103">在 Kubernetes 上建置微服務的 CI/CD 管線</span><span class="sxs-lookup"><span data-stu-id="32178-103">Building a CI/CD pipeline for microservices on Kubernetes</span></span>

<span data-ttu-id="32178-104">它可接受建立可靠的 CI/CD 程序，在微服務架構。</span><span class="sxs-lookup"><span data-stu-id="32178-104">It can be challenging to create a reliable CI/CD process for a microservices architecture.</span></span> <span data-ttu-id="32178-105">個別小組必須能夠快速且可靠地發行服務而不會中斷其他小組或整個應用程式造成不穩定。</span><span class="sxs-lookup"><span data-stu-id="32178-105">Individual teams must be able to release services quickly and reliably, without disrupting other teams or destabilizing the application as a whole.</span></span>

<span data-ttu-id="32178-106">本文說明將微服務部署至 Azure Kubernetes Service (AKS) 範例 CI/CD 管線。</span><span class="sxs-lookup"><span data-stu-id="32178-106">This article describes an example CI/CD pipeline for deploying microservices to Azure Kubernetes Service (AKS).</span></span> <span data-ttu-id="32178-107">每個小組和專案都不同，因此不需要這篇文章為一組 hard-and-fast 規則。</span><span class="sxs-lookup"><span data-stu-id="32178-107">Every team and project is different, so don't take this article as a set of hard-and-fast rules.</span></span> <span data-ttu-id="32178-108">相反地，它已經是用來設計您自己的 CI/CD 程序的起始點。</span><span class="sxs-lookup"><span data-stu-id="32178-108">Instead, it's meant to be a starting point for designing your own CI/CD process.</span></span>

<span data-ttu-id="32178-109">此處所述的範例管線建立呼叫無人機遞送應用程式，您可以找到的微服務的參考實作[GitHub][ri]。</span><span class="sxs-lookup"><span data-stu-id="32178-109">The example pipeline described here was created for a microservices reference implementation called the Drone Delivery application, which you can find on [GitHub][ri].</span></span> <span data-ttu-id="32178-110">描述應用程式案例[此處](./design/index.md#reference-implementation)。</span><span class="sxs-lookup"><span data-stu-id="32178-110">The application scenario is described [here](./design/index.md#reference-implementation).</span></span>

<span data-ttu-id="32178-111">管線的目標可以摘要如下：</span><span class="sxs-lookup"><span data-stu-id="32178-111">The goals of the pipeline can be summarized as follows:</span></span>

- <span data-ttu-id="32178-112">小組可以建置和獨立部署其服務。</span><span class="sxs-lookup"><span data-stu-id="32178-112">Teams can build and deploy their services independently.</span></span>
- <span data-ttu-id="32178-113">傳遞 CI 程序的程式碼變更會自動部署至類似生產的環境。</span><span class="sxs-lookup"><span data-stu-id="32178-113">Code changes that pass the CI process are automatically deployed to a production-like environment.</span></span>
- <span data-ttu-id="32178-114">強制執行管線各階段的品管標準。</span><span class="sxs-lookup"><span data-stu-id="32178-114">Quality gates are enforced at each stage of the pipeline.</span></span>
- <span data-ttu-id="32178-115">服務的新版本可以與舊版並存部署。</span><span class="sxs-lookup"><span data-stu-id="32178-115">A new version of a service can be deployed side by side with the previous version.</span></span>

<span data-ttu-id="32178-116">如需詳細背景，請參閱 <<c0> [ 微服務架構的 CI/CD](./ci-cd.md)。</span><span class="sxs-lookup"><span data-stu-id="32178-116">For more background, see [CI/CD for microservices architectures](./ci-cd.md).</span></span>

## <a name="assumptions"></a><span data-ttu-id="32178-117">假設</span><span class="sxs-lookup"><span data-stu-id="32178-117">Assumptions</span></span>

<span data-ttu-id="32178-118">基於此範例的詳細資訊，以下是一些假設開發小組和程式碼基底：</span><span class="sxs-lookup"><span data-stu-id="32178-118">For purposes of this example, here are some assumptions about the development team and the code base:</span></span>

- <span data-ttu-id="32178-119">程式碼存放庫是 monorepo，依微服務的資料夾。</span><span class="sxs-lookup"><span data-stu-id="32178-119">The code repository is a monorepo, with folders organized by microservice.</span></span>
- <span data-ttu-id="32178-120">小組的分支策略是以[主幹型開發](https://trunkbaseddevelopment.com/)為基礎。</span><span class="sxs-lookup"><span data-stu-id="32178-120">The team's branching strategy is based on [trunk-based development](https://trunkbaseddevelopment.com/).</span></span>
- <span data-ttu-id="32178-121">小組會使用[發行分支](/azure/devops/repos/git/git-branching-guidance?view=azure-devops#manage-releases)管理發行。</span><span class="sxs-lookup"><span data-stu-id="32178-121">The team uses [release branches](/azure/devops/repos/git/git-branching-guidance?view=azure-devops#manage-releases) to manage releases.</span></span> <span data-ttu-id="32178-122">不同的版本會針對每個微服務。</span><span class="sxs-lookup"><span data-stu-id="32178-122">Separate releases are created for each microservice.</span></span>
- <span data-ttu-id="32178-123">CI/CD 程序會使用[Azure 管線](/azure/devops/pipelines/?view=azure-devops)建置、 測試及部署到 AKS 的微服務。</span><span class="sxs-lookup"><span data-stu-id="32178-123">The CI/CD process uses [Azure Pipelines](/azure/devops/pipelines/?view=azure-devops) to build, test, and deploy the microservices to AKS.</span></span>
- <span data-ttu-id="32178-124">每個微服務的容器映像會儲存在[Azure Container Registry](/azure/container-registry/)。</span><span class="sxs-lookup"><span data-stu-id="32178-124">The container images for each microservice are stored in [Azure Container Registry](/azure/container-registry/).</span></span>
- <span data-ttu-id="32178-125">小組會使用 Helm 圖表來封裝每個微服務。</span><span class="sxs-lookup"><span data-stu-id="32178-125">The team uses Helm charts to package each microservice.</span></span>

<span data-ttu-id="32178-126">這些假設磁碟機有許多的 CI/CD 管線的特定詳細資料。</span><span class="sxs-lookup"><span data-stu-id="32178-126">These assumptions drive many of the specific details of the CI/CD pipeline.</span></span> <span data-ttu-id="32178-127">不過，此處所述的基本方法是適用於其他處理程序、 工具和服務，例如 Jenkins 或 Docker Hub。</span><span class="sxs-lookup"><span data-stu-id="32178-127">However, the basic approach described here be adapted for other processes, tools, and services, such as Jenkins or Docker Hub.</span></span>

## <a name="validation-builds"></a><span data-ttu-id="32178-128">驗證組建</span><span class="sxs-lookup"><span data-stu-id="32178-128">Validation builds</span></span>

<span data-ttu-id="32178-129">假設開發人員正在努力微服務，稱為 「 遞送 」 服務。</span><span class="sxs-lookup"><span data-stu-id="32178-129">Suppose that a developer is working on a microservice called the Delivery Service.</span></span> <span data-ttu-id="32178-130">在開發新功能的同時，開發人員會將程式碼簽入功能分支。</span><span class="sxs-lookup"><span data-stu-id="32178-130">While developing a new feature, the developer checks code into a feature branch.</span></span> <span data-ttu-id="32178-131">依照慣例，功能分支會命名為 `feature/*`。</span><span class="sxs-lookup"><span data-stu-id="32178-131">By convention, feature branches are named `feature/*`.</span></span>

![CI/CD 工作流程](./images/aks-cicd-1.png)

<span data-ttu-id="32178-133">組建定義檔會包含分支名稱和來源路徑，藉此篩選的觸發程序：</span><span class="sxs-lookup"><span data-stu-id="32178-133">The build definition file includes a trigger that filters by the branch name and the source path:</span></span>

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

<span data-ttu-id="32178-134">&#11162;請參閱[原始程式檔](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-validation.yml)。</span><span class="sxs-lookup"><span data-stu-id="32178-134">&#11162; See the [source file](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-validation.yml).</span></span>

<span data-ttu-id="32178-135">使用此方法，每個小組都可以有自己的組建管線。</span><span class="sxs-lookup"><span data-stu-id="32178-135">Using this approach, each team can have its own build pipeline.</span></span> <span data-ttu-id="32178-136">簽入程式碼`/src/shipping/delivery`資料夾就會觸發之組建的 「 遞送 」 服務。</span><span class="sxs-lookup"><span data-stu-id="32178-136">Only code that is checked into the `/src/shipping/delivery` folder triggers a build of the Delivery Service.</span></span> <span data-ttu-id="32178-137">將認可推送至符合篩選條件的分支會觸發在 CI 組建。</span><span class="sxs-lookup"><span data-stu-id="32178-137">Pushing commits to a branch that matches the filter triggers a CI build.</span></span> <span data-ttu-id="32178-138">此時在流程中，CI 組建會執行一些最基本的程式碼驗證：</span><span class="sxs-lookup"><span data-stu-id="32178-138">At this point in the workflow, the CI build runs some minimal code verification:</span></span>

1. <span data-ttu-id="32178-139">建置程式碼。</span><span class="sxs-lookup"><span data-stu-id="32178-139">Build the code.</span></span>
1. <span data-ttu-id="32178-140">執行單元測試。</span><span class="sxs-lookup"><span data-stu-id="32178-140">Run unit tests.</span></span>

<span data-ttu-id="32178-141">目標是讓建置時間短，因此開發人員可以快速意見反應。</span><span class="sxs-lookup"><span data-stu-id="32178-141">The goal is to keep build times short, so the developer can get quick feedback.</span></span> <span data-ttu-id="32178-142">開發人員功能合併至 master 準備就緒後，開啟 pr。</span><span class="sxs-lookup"><span data-stu-id="32178-142">Once the feature is ready to merge into master, the developer opens a PR.</span></span> <span data-ttu-id="32178-143">這會觸發另一個 CI 組建，以執行一些額外的檢查：</span><span class="sxs-lookup"><span data-stu-id="32178-143">This triggers another CI build that performs some additional checks:</span></span>

1. <span data-ttu-id="32178-144">建置程式碼。</span><span class="sxs-lookup"><span data-stu-id="32178-144">Build the code.</span></span>
1. <span data-ttu-id="32178-145">執行單元測試。</span><span class="sxs-lookup"><span data-stu-id="32178-145">Run unit tests.</span></span>
1. <span data-ttu-id="32178-146">建置執行階段容器映像。</span><span class="sxs-lookup"><span data-stu-id="32178-146">Build the runtime container image.</span></span>
1. <span data-ttu-id="32178-147">映像上執行弱點掃描。</span><span class="sxs-lookup"><span data-stu-id="32178-147">Run vulnerability scans on the image.</span></span>

![CI/CD 工作流程](./images/aks-cicd-2.png)

> [!NOTE]
> <span data-ttu-id="32178-149">在 Azure DevOps 的存放庫，您可以定義[原則](/azure/devops/repos/git/branch-policies)保護分支。</span><span class="sxs-lookup"><span data-stu-id="32178-149">In Azure DevOps Repos, you can define [policies](/azure/devops/repos/git/branch-policies) to protect branches.</span></span> <span data-ttu-id="32178-150">例如，原則可能需要成功的 CI 組建再加上核准者的簽核，才能合併至主體。</span><span class="sxs-lookup"><span data-stu-id="32178-150">For example, the policy could require a successful CI build plus a sign-off from an approver in order to merge into master.</span></span>

## <a name="full-cicd-build"></a><span data-ttu-id="32178-151">完整的 CI/CD 建置</span><span class="sxs-lookup"><span data-stu-id="32178-151">Full CI/CD build</span></span>

<span data-ttu-id="32178-152">在某個時間點，小組已準備好部署新版的「遞送」服務。</span><span class="sxs-lookup"><span data-stu-id="32178-152">At some point, the team is ready to deploy a new version of the Delivery service.</span></span> <span data-ttu-id="32178-153">發行管理員會從與這個命名模式的主機建立分支： `release/<microservice name>/<semver>`。</span><span class="sxs-lookup"><span data-stu-id="32178-153">The release manager creates a branch from master with this naming pattern: `release/<microservice name>/<semver>`.</span></span> <span data-ttu-id="32178-154">例如： `release/delivery/v1.0.2`。</span><span class="sxs-lookup"><span data-stu-id="32178-154">For example, `release/delivery/v1.0.2`.</span></span>

![CI/CD 工作流程](./images/aks-cicd-3.png)

<span data-ttu-id="32178-156">建立此分支會觸發完整的 CI 組建執行的所有加上前一個步驟：</span><span class="sxs-lookup"><span data-stu-id="32178-156">Creation of this branch triggers a full CI build that runs all of the previous steps plus:</span></span>

1. <span data-ttu-id="32178-157">將容器映像推送至 Azure Container Registry。</span><span class="sxs-lookup"><span data-stu-id="32178-157">Push the container image to Azure Container Registry.</span></span> <span data-ttu-id="32178-158">映像會以取自分支名稱的版本號碼來標記。</span><span class="sxs-lookup"><span data-stu-id="32178-158">The image is tagged with the version number taken from the branch name.</span></span>
2. <span data-ttu-id="32178-159">執行`helm package`來封裝服務的 Helm 圖表。</span><span class="sxs-lookup"><span data-stu-id="32178-159">Run `helm package` to package the Helm chart for the service.</span></span> <span data-ttu-id="32178-160">圖表也附有版本號碼。</span><span class="sxs-lookup"><span data-stu-id="32178-160">The chart is also tagged with a version number.</span></span>
3. <span data-ttu-id="32178-161">Helm 套件推送至容器登錄。</span><span class="sxs-lookup"><span data-stu-id="32178-161">Push the Helm package to Container Registry.</span></span>

<span data-ttu-id="32178-162">假設此建置成功時，觸發的部署 (CD) 程序，使用 Azure 管線[發行管線](/azure/devops/pipelines/release/what-is-release-management)。</span><span class="sxs-lookup"><span data-stu-id="32178-162">Assuming this build succeeds, it triggers a deployment (CD) process using an Azure Pipelines [release pipeline](/azure/devops/pipelines/release/what-is-release-management).</span></span> <span data-ttu-id="32178-163">此管線有下列步驟：</span><span class="sxs-lookup"><span data-stu-id="32178-163">This pipeline has the following steps:</span></span>

1. <span data-ttu-id="32178-164">部署至 QA 環境的 Helm 圖表。</span><span class="sxs-lookup"><span data-stu-id="32178-164">Deploy the Helm chart to a QA environment.</span></span>
1. <span data-ttu-id="32178-165">將套件移到生產環境之前，核准者會先進行簽核。</span><span class="sxs-lookup"><span data-stu-id="32178-165">An approver signs off before the package moves to production.</span></span> <span data-ttu-id="32178-166">請參閱[使用核准功能的發行部署控制](/azure/devops/pipelines/release/approvals/approvals)。</span><span class="sxs-lookup"><span data-stu-id="32178-166">See [Release deployment control using approvals](/azure/devops/pipelines/release/approvals/approvals).</span></span>
1. <span data-ttu-id="32178-167">Retag 生產環境中的命名空間 Azure Container Registry 的 Docker 映像。</span><span class="sxs-lookup"><span data-stu-id="32178-167">Retag the Docker image for the production namespace in Azure Container Registry.</span></span> <span data-ttu-id="32178-168">例如，如果目前的標記是 `myrepo.azurecr.io/delivery:v1.0.2`，則生產標記就會是 `myrepo.azurecr.io/prod/delivery:v1.0.2`。</span><span class="sxs-lookup"><span data-stu-id="32178-168">For example, if the current tag is `myrepo.azurecr.io/delivery:v1.0.2`, the production tag is `myrepo.azurecr.io/prod/delivery:v1.0.2`.</span></span>
1. <span data-ttu-id="32178-169">部署到生產環境的 Helm 圖表。</span><span class="sxs-lookup"><span data-stu-id="32178-169">Deploy the Helm chart to the production environment.</span></span>

<span data-ttu-id="32178-170">即使在 monorepo，這些工作可以設定為個別的微服務，以便小組可以部署具有高速度。</span><span class="sxs-lookup"><span data-stu-id="32178-170">Even in a monorepo, these tasks can be scoped to individual microservices, so that teams can deploy with high velocity.</span></span> <span data-ttu-id="32178-171">程序有一些手動步驟：核准 PR、建立發行分支，以及將部署核准到生產叢集中。</span><span class="sxs-lookup"><span data-stu-id="32178-171">The process has some manual steps: Approving PRs, creating release branches, and approving deployments into the production cluster.</span></span> <span data-ttu-id="32178-172">這些步驟都是由原則手動&mdash;它們可自動化如果組織的偏好。</span><span class="sxs-lookup"><span data-stu-id="32178-172">These steps are manual by policy &mdash; they could be automated if the organization prefers.</span></span>

## <a name="isolation-of-environments"></a><span data-ttu-id="32178-173">環境隔離</span><span class="sxs-lookup"><span data-stu-id="32178-173">Isolation of environments</span></span>

<span data-ttu-id="32178-174">您部署服務時會用到多個環境，這些環境分別用於開發、煙霧測試 (Smoke Test)、整合測試、負載測試和最後的生產環境。</span><span class="sxs-lookup"><span data-stu-id="32178-174">You will have multiple environments where you deploy services, including environments for development, smoke testing, integration testing, load testing, and finally production.</span></span> <span data-ttu-id="32178-175">這些環境需要某種程度的隔離。</span><span class="sxs-lookup"><span data-stu-id="32178-175">These environments need some level of isolation.</span></span> <span data-ttu-id="32178-176">在 Kubernetes 中，您可以選擇實體隔離和邏輯隔離。</span><span class="sxs-lookup"><span data-stu-id="32178-176">In Kubernetes, you have a choice between physical isolation and logical isolation.</span></span> <span data-ttu-id="32178-177">實體隔離表示部署到不同叢集。</span><span class="sxs-lookup"><span data-stu-id="32178-177">Physical isolation means deploying to separate clusters.</span></span> <span data-ttu-id="32178-178">邏輯隔離會使用命名空間和原則，如先前所述。</span><span class="sxs-lookup"><span data-stu-id="32178-178">Logical isolation makes use of namespaces and policies, as described earlier.</span></span>

<span data-ttu-id="32178-179">我們建議您建立專用的生產環境叢集，以及用於開發/測試環境的個別叢集。</span><span class="sxs-lookup"><span data-stu-id="32178-179">Our recommendation is to create a dedicated production cluster along with a separate cluster for your dev/test environments.</span></span> <span data-ttu-id="32178-180">使用邏輯隔離來區隔開發/測試叢集內的環境。</span><span class="sxs-lookup"><span data-stu-id="32178-180">Use logical isolation to separate environments within the dev/test cluster.</span></span> <span data-ttu-id="32178-181">部署到開發/測試叢集的服務應一律不能存取保存商務資料的資料存放區。</span><span class="sxs-lookup"><span data-stu-id="32178-181">Services deployed to the dev/test cluster should never have access to data stores that hold business data.</span></span>

## <a name="build-process"></a><span data-ttu-id="32178-182">建置程序</span><span class="sxs-lookup"><span data-stu-id="32178-182">Build process</span></span>

<span data-ttu-id="32178-183">如果可能的話，請到 Docker 容器封裝建置程序。</span><span class="sxs-lookup"><span data-stu-id="32178-183">When possible, package your build process into a Docker container.</span></span> <span data-ttu-id="32178-184">可讓您建置您的程式碼成品使用 Docker，而不需要設定每個組建電腦上的建置環境。</span><span class="sxs-lookup"><span data-stu-id="32178-184">That allows you to build your code artifacts using Docker, without needing to configure the build environment on each build machine.</span></span> <span data-ttu-id="32178-185">容器化的組建程序輕鬆地相應放大 CI 管線藉由新增新的組建代理程式。</span><span class="sxs-lookup"><span data-stu-id="32178-185">A containerized build process makes it easy to scale out the CI pipeline by adding new build agents.</span></span> <span data-ttu-id="32178-186">此外，任何小組的開發人員可以建置程式碼，只要執行組建容器。</span><span class="sxs-lookup"><span data-stu-id="32178-186">Also, any developer on the team can build the code simply by running the build container.</span></span>

<span data-ttu-id="32178-187">藉由使用多階段建置在 Docker 中，您可以定義單一的 Dockerfile 中的建置環境和執行階段映像。</span><span class="sxs-lookup"><span data-stu-id="32178-187">By using multi-stage builds in Docker, you can define the build environment and the runtime image in a single Dockerfile.</span></span> <span data-ttu-id="32178-188">例如，以下是建置 ASP.NET Core 應用程式的 Dockerfile:</span><span class="sxs-lookup"><span data-stu-id="32178-188">For example, here's a Dockerfile that builds an ASP.NET Core application:</span></span>

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

<span data-ttu-id="32178-189">&#11162;請參閱[原始程式檔](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/workflow/Dockerfile)。</span><span class="sxs-lookup"><span data-stu-id="32178-189">&#11162; See the [source file](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/workflow/Dockerfile).</span></span>

<span data-ttu-id="32178-190">這個 Dockerfile 中定義數個建置階段。</span><span class="sxs-lookup"><span data-stu-id="32178-190">This Dockerfile defines several build stages.</span></span> <span data-ttu-id="32178-191">請注意，名為階段`base`名為的階段時使用的 ASP.NET Core 執行階段中，`build`使用完整的 ASP.NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="32178-191">Notice that the stage named `base` uses the ASP.NET Core runtime, while the stage named `build` uses the full ASP.NET Core SDK.</span></span> <span data-ttu-id="32178-192">`build`階段用來建置 ASP.NET Core 專案。</span><span class="sxs-lookup"><span data-stu-id="32178-192">The `build` stage is used to build the ASP.NET Core project.</span></span> <span data-ttu-id="32178-193">但最後的執行階段容器從建置`base`，其中包含執行階段，而且是比完整的 SDK 映像小得多。</span><span class="sxs-lookup"><span data-stu-id="32178-193">But the final runtime container is built from `base`, which contains just the runtime and is significantly smaller than the full SDK image.</span></span>

### <a name="building-a-test-runner"></a><span data-ttu-id="32178-194">建置的測試執行器</span><span class="sxs-lookup"><span data-stu-id="32178-194">Building a test runner</span></span>

<span data-ttu-id="32178-195">另一個很好的作法是在容器中執行單元測試。</span><span class="sxs-lookup"><span data-stu-id="32178-195">Another good practice is to run unit tests in the container.</span></span> <span data-ttu-id="32178-196">例如，以下是組建的測試執行器的 Docker 檔案的一部分：</span><span class="sxs-lookup"><span data-stu-id="32178-196">For example, here is part of a Docker file that builds a test runner:</span></span>

```
FROM build AS testrunner
WORKDIR /src/tests

COPY Fabrikam.Workflow.Service.Tests/*.csproj .
RUN dotnet restore Fabrikam.Workflow.Service.Tests.csproj

COPY Fabrikam.Workflow.Service.Tests/. .
ENTRYPOINT ["dotnet", "test", "--logger:trx"]
```

<span data-ttu-id="32178-197">開發人員可以使用這個 Docker 檔案在本機執行測試：</span><span class="sxs-lookup"><span data-stu-id="32178-197">A developer can use this Docker file to run the tests locally:</span></span>

```bash
docker build . -t delivery-test:1 --target=testrunner
docker run -p 8080:8080 delivery-test:1
```

<span data-ttu-id="32178-198">CI 管線也應該執行測試做為組建驗證步驟的一部分。</span><span class="sxs-lookup"><span data-stu-id="32178-198">The CI pipeline should also run the tests as part of the build verification step.</span></span>

<span data-ttu-id="32178-199">請注意，此檔案會使用 Docker`ENTRYPOINT`命令來執行測試，而不是 Docker`RUN`命令。</span><span class="sxs-lookup"><span data-stu-id="32178-199">Note that this file uses the Docker `ENTRYPOINT` command to run the tests, not the Docker `RUN` command.</span></span>

- <span data-ttu-id="32178-200">如果您使用`RUN`命令時，每次建置映像，執行測試。</span><span class="sxs-lookup"><span data-stu-id="32178-200">If you use the `RUN` command, the tests run every time you build the image.</span></span> <span data-ttu-id="32178-201">使用`ENTRYPOINT`，測試是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="32178-201">By using `ENTRYPOINT`, the tests are opt-in.</span></span> <span data-ttu-id="32178-202">只有在您明確設為目標時，才執行`testrunner`階段。</span><span class="sxs-lookup"><span data-stu-id="32178-202">They run only when you explicitly target the `testrunner` stage.</span></span>
- <span data-ttu-id="32178-203">失敗的測試不會造成 Docker`build`命令失敗。</span><span class="sxs-lookup"><span data-stu-id="32178-203">A failing test doesn't cause the Docker `build` command to fail.</span></span> <span data-ttu-id="32178-204">如此一來您可以區分容器建置失敗的測試失敗。</span><span class="sxs-lookup"><span data-stu-id="32178-204">That way you can distinguish container build failures from test failures.</span></span>
- <span data-ttu-id="32178-205">測試結果可以儲存至掛接的磁碟區。</span><span class="sxs-lookup"><span data-stu-id="32178-205">Test results can be saved to a mounted volume.</span></span>

### <a name="container-best-practices"></a><span data-ttu-id="32178-206">容器的最佳作法</span><span class="sxs-lookup"><span data-stu-id="32178-206">Container best practices</span></span>

<span data-ttu-id="32178-207">以下是其他一些最佳作法，應考量的容器：</span><span class="sxs-lookup"><span data-stu-id="32178-207">Here are some other best practices to consider for containers:</span></span>

- <span data-ttu-id="32178-208">為部署到叢集 (Pod、服務等等) 的資源，定義容器標籤、版本控制和命名慣例的組織通用慣例。</span><span class="sxs-lookup"><span data-stu-id="32178-208">Define organization-wide conventions for container tags, versioning, and naming conventions for resources deployed to the cluster (pods, services, and so on).</span></span> <span data-ttu-id="32178-209">這可讓您更輕鬆地診斷部署問題。</span><span class="sxs-lookup"><span data-stu-id="32178-209">That can make it easier to diagnose deployment issues.</span></span>

- <span data-ttu-id="32178-210">在開發和測試週期內，CI/CD 程序將會建立許多容器映像。</span><span class="sxs-lookup"><span data-stu-id="32178-210">During the development and test cycle, the CI/CD process will build many container images.</span></span> <span data-ttu-id="32178-211">只有部分這些映像的版本中，候選項目，然後只有其中一些發行候選項目將會提升至生產環境。</span><span class="sxs-lookup"><span data-stu-id="32178-211">Only some of those images are candidates for release, and then only some of those release candidates will get promoted to production.</span></span> <span data-ttu-id="32178-212">有明確的版本控制策略，好讓您知道哪些映像目前已部署到生產環境，並可以回復至先前的版本如有必要。</span><span class="sxs-lookup"><span data-stu-id="32178-212">Have a clear versioning strategy, so that you know which images are currently deployed to production, and can roll back to a previous version if necessary.</span></span>

- <span data-ttu-id="32178-213">一律不部署特定容器的版本標籤， `latest`。</span><span class="sxs-lookup"><span data-stu-id="32178-213">Always deploy specific container version tags, not `latest`.</span></span>

- <span data-ttu-id="32178-214">使用[命名空間](/azure/container-registry/container-registry-best-practices#repository-namespaces)隔離核准用於生產環境仍正在測試的映像從映像的 Azure Container Registry 中。</span><span class="sxs-lookup"><span data-stu-id="32178-214">Use [namespaces](/azure/container-registry/container-registry-best-practices#repository-namespaces) in Azure Container Registry to isolate images that are approved for production from images that are still being tested.</span></span> <span data-ttu-id="32178-215">不將映像移到生產命名空間，直到您準備好部署到生產環境。</span><span class="sxs-lookup"><span data-stu-id="32178-215">Don't move an image into the production namespace until you're ready to deploy it into production.</span></span> <span data-ttu-id="32178-216">如果您結合這種做法與容器映像的語意版本控制，就能盡量避免意外部署到未獲准發行的版本。</span><span class="sxs-lookup"><span data-stu-id="32178-216">If you combine this practice with semantic versioning of container images, it can reduce the chance of accidentally deploying a version that wasn't approved for release.</span></span>

- <span data-ttu-id="32178-217">無權限使用者身分執行的容器，以遵循最低權限原則。</span><span class="sxs-lookup"><span data-stu-id="32178-217">Follow the principle of least privilege by running containers as a nonprivileged user.</span></span> <span data-ttu-id="32178-218">在 Kubernetes 中，您可以建立 pod 安全性原則，可防止身分執行的容器*根*。</span><span class="sxs-lookup"><span data-stu-id="32178-218">In Kubernetes, you can create a pod security policy that prevents containers from running as *root*.</span></span> <span data-ttu-id="32178-219">請參閱[以根權限執行時，防止 Pod](https://docs.bitnami.com/kubernetes/how-to/secure-kubernetes-cluster-psp/)。</span><span class="sxs-lookup"><span data-stu-id="32178-219">See [Prevent Pods From Running With Root Privileges](https://docs.bitnami.com/kubernetes/how-to/secure-kubernetes-cluster-psp/).</span></span>

## <a name="helm-charts"></a><span data-ttu-id="32178-220">Helm 圖表</span><span class="sxs-lookup"><span data-stu-id="32178-220">Helm charts</span></span>

<span data-ttu-id="32178-221">請考慮使用 Helm 來管理服務的建置和部署。</span><span class="sxs-lookup"><span data-stu-id="32178-221">Consider using Helm to manage building and deploying services.</span></span> <span data-ttu-id="32178-222">以下是 helm 的一些可協助您的 CI/CD 功能：</span><span class="sxs-lookup"><span data-stu-id="32178-222">Here are some of the features of Helm that help with CI/CD:</span></span>

- <span data-ttu-id="32178-223">單一的微服務通常會定義多個 Kubernetes 物件。</span><span class="sxs-lookup"><span data-stu-id="32178-223">Often a single microservice is defined by multiple Kubernetes objects.</span></span> <span data-ttu-id="32178-224">Helm 允許這些物件會封裝成單一的 Helm 圖表。</span><span class="sxs-lookup"><span data-stu-id="32178-224">Helm allows these objects to be packaged into a single Helm chart.</span></span>
- <span data-ttu-id="32178-225">圖表可以部署單一的 Helm 命令，而不是一系列的 kubectl 命令。</span><span class="sxs-lookup"><span data-stu-id="32178-225">A chart can be deployed with a single Helm command, rather than a series of kubectl commands.</span></span>
- <span data-ttu-id="32178-226">圖表是明確地建立版本。</span><span class="sxs-lookup"><span data-stu-id="32178-226">Charts are explicitly versioned.</span></span> <span data-ttu-id="32178-227">使用 Helm 發行版本、 檢視版本中，並回復到先前的版本。</span><span class="sxs-lookup"><span data-stu-id="32178-227">Use Helm to release a version, view releases, and roll back to a previous version.</span></span> <span data-ttu-id="32178-228">使用語意化版本控制系統來追蹤更新和修訂，並且具有能夠復原到先前版本的功能。</span><span class="sxs-lookup"><span data-stu-id="32178-228">Tracking updates and revisions, using semantic versioning, along with the ability to roll back to a previous version.</span></span>
- <span data-ttu-id="32178-229">Helm 圖表會使用範本，來避免重複資訊，例如標籤和選取器，在許多檔案。</span><span class="sxs-lookup"><span data-stu-id="32178-229">Helm charts use templates to avoid duplicating information, such as labels and selectors, across many files.</span></span>
- <span data-ttu-id="32178-230">Helm 可以管理之間圖表的相依性。</span><span class="sxs-lookup"><span data-stu-id="32178-230">Helm can manage dependencies between charts.</span></span>
- <span data-ttu-id="32178-231">圖表可以儲存在 Helm 存放庫，例如 Azure Container Registry，並整合到組建管線。</span><span class="sxs-lookup"><span data-stu-id="32178-231">Charts can be stored in a Helm repository, such as Azure Container Registry, and integrated into the build pipeline.</span></span>

<span data-ttu-id="32178-232">如需有關使用 Container Registry 作為 Helm 存放庫的詳細資訊，請參閱[使用 Azure Container Registry 作為您應用程式圖表的 Helm 存放庫](/azure/container-registry/container-registry-helm-repos)。</span><span class="sxs-lookup"><span data-stu-id="32178-232">For more information about using Container Registry as a Helm repository, see [Use Azure Container Registry as a Helm repository for your application charts](/azure/container-registry/container-registry-helm-repos).</span></span>

<span data-ttu-id="32178-233">單一的微服務可能牽涉到多個 Kubernetes 組態檔。</span><span class="sxs-lookup"><span data-stu-id="32178-233">A single microservice may involve multiple Kubernetes configuration files.</span></span> <span data-ttu-id="32178-234">更新服務可能是指觸碰所有這些檔案，以更新選取器、 標籤和映像標記。</span><span class="sxs-lookup"><span data-stu-id="32178-234">Updating a service can mean touching all of these files to update selectors, labels, and image tags.</span></span> <span data-ttu-id="32178-235">Helm 會將這些稱為圖表的單一套件，並可讓您輕鬆地更新的 YAML 檔案使用變數。</span><span class="sxs-lookup"><span data-stu-id="32178-235">Helm treats these as a single package called a chart and allows you to easily update the YAML files by using variables.</span></span> <span data-ttu-id="32178-236">Helm 會使用範本基礎的語言 （移至範本），讓您撰寫參數化的 YAML 組態檔。</span><span class="sxs-lookup"><span data-stu-id="32178-236">Helm uses a template language (based on Go templates) to let you write parameterized YAML configuration files.</span></span>

<span data-ttu-id="32178-237">例如，以下是定義在部署 YAML 檔案的一部分：</span><span class="sxs-lookup"><span data-stu-id="32178-237">For example, here's part of a YAML file that defines a deployment:</span></span>

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

<span data-ttu-id="32178-238">&#11162;請參閱[原始程式檔](https://github.com/mspnp/microservices-reference-implementation/blob/master/charts/package/templates/package-deploy.yaml)。</span><span class="sxs-lookup"><span data-stu-id="32178-238">&#11162; See the [source file](https://github.com/mspnp/microservices-reference-implementation/blob/master/charts/package/templates/package-deploy.yaml).</span></span>

<span data-ttu-id="32178-239">您可以看到部署名稱、 標籤與容器規格所有使用範本參數，在部署期間所提供。</span><span class="sxs-lookup"><span data-stu-id="32178-239">You can see that the deployment name, labels, and container spec all use template parameters, which are provided at deployment time.</span></span> <span data-ttu-id="32178-240">例如，從命令列：</span><span class="sxs-lookup"><span data-stu-id="32178-240">For example, from the command line:</span></span>

```bash
helm install $HELM_CHARTS/package/ \
     --set image.tag=0.1.0 \
     --set image.repository=package \
     --set dockerregistry=$ACR_SERVER \
     --namespace backend \
     --name package-v0.1.0
```

<span data-ttu-id="32178-241">雖然您的 CI/CD 管線無法圖表直接安裝至 Kubernetes，則建議您建立的圖表封存 （.tgz 檔案），以及將圖表推送至 Helm 存放庫，例如 Azure Container Registry。</span><span class="sxs-lookup"><span data-stu-id="32178-241">Although your CI/CD pipeline could install a chart directly to Kubernetes, we recommend creating a chart archive (.tgz file) and pushing the chart to a Helm repository such as Azure Container Registry.</span></span> <span data-ttu-id="32178-242">如需詳細資訊，請參閱 <<c0> [ 封裝 Docker 為基礎的應用程式中 Azure 管線中的 Helm 圖表](/azure/devops/pipelines/languages/helm?view=azure-devops)。</span><span class="sxs-lookup"><span data-stu-id="32178-242">For more information, see [Package Docker-based apps in Helm charts in Azure Pipelines](/azure/devops/pipelines/languages/helm?view=azure-devops).</span></span>

<span data-ttu-id="32178-243">請考慮將 Helm 部署到自己的命名空間，並使用角色型存取控制 (RBAC) 來限制它可以部署到哪一個命名空間。</span><span class="sxs-lookup"><span data-stu-id="32178-243">Consider deploying Helm to its own namespace and using role-based access control (RBAC) to restrict which namespaces it can deploy to.</span></span> <span data-ttu-id="32178-244">如需詳細資訊，請參閱 <<c0> [ 角色型存取控制](https://helm.sh/docs/using_helm/#helm-and-role-based-access-control)Helm 文件中。</span><span class="sxs-lookup"><span data-stu-id="32178-244">For more information, see [Role-based Access Control](https://helm.sh/docs/using_helm/#helm-and-role-based-access-control) in the Helm documentation.</span></span>

### <a name="revisions"></a><span data-ttu-id="32178-245">修訂</span><span class="sxs-lookup"><span data-stu-id="32178-245">Revisions</span></span>

<span data-ttu-id="32178-246">Helm 圖表一律有版本號碼，必須使用[語意版本設定](https://semver.org/)。</span><span class="sxs-lookup"><span data-stu-id="32178-246">Helm charts always have a version number, which must use [semantic versioning](https://semver.org/).</span></span> <span data-ttu-id="32178-247">圖表也可以有`appVersion`。</span><span class="sxs-lookup"><span data-stu-id="32178-247">A chart can also have an `appVersion`.</span></span> <span data-ttu-id="32178-248">此欄位是選擇性的而且不一定要關聯至圖表的版本。</span><span class="sxs-lookup"><span data-stu-id="32178-248">This field is optional, and doesn't have to be related to the chart version.</span></span> <span data-ttu-id="32178-249">有些小組可能會想要的應用程式版本分別從圖表的更新。</span><span class="sxs-lookup"><span data-stu-id="32178-249">Some teams might want to application versions separately from updates to the charts.</span></span> <span data-ttu-id="32178-250">但更簡單的方法是使用一個版本號碼，因此圖表版本和應用程式版本之間的 1 對 1 關聯。</span><span class="sxs-lookup"><span data-stu-id="32178-250">But a simpler approach is to use one version number, so there's a 1:1 relation between chart version and application version.</span></span> <span data-ttu-id="32178-251">這樣一來，您可以儲存每個版本的一份圖表，並輕鬆地部署所需的版本：</span><span class="sxs-lookup"><span data-stu-id="32178-251">That way, you can store one chart per release and easily deploy the desired release:</span></span>

```bash
helm install <package-chart-name> --version <desiredVersion>
```

<span data-ttu-id="32178-252">另一個很好的做法是提供部署範本中的變更原因註釋：</span><span class="sxs-lookup"><span data-stu-id="32178-252">Another good practice is to provide a change-cause annotation in the deployment template:</span></span>

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

<span data-ttu-id="32178-253">這可讓您檢視每個修訂的 [變更原因] 欄位使用`kubectl rollout history`命令。</span><span class="sxs-lookup"><span data-stu-id="32178-253">This lets you view the change-cause field for each revision, using the `kubectl rollout history` command.</span></span> <span data-ttu-id="32178-254">在上述範例中，變更原因被提供的 Helm 圖表參數。</span><span class="sxs-lookup"><span data-stu-id="32178-254">In the previous example, the change-cause is provided as a Helm chart parameter.</span></span>

```bash
$ kubectl rollout history deployments/delivery-v010 -n backend
deployment.extensions/delivery-v010
REVISION  CHANGE-CAUSE
1         Initial deployment
```

<span data-ttu-id="32178-255">您也可以使用`helm list`命令來檢視修訂歷程記錄：</span><span class="sxs-lookup"><span data-stu-id="32178-255">You can also use the `helm list` command to view the revision history:</span></span>

```bash
~$ helm list
NAME            REVISION    UPDATED                     STATUS        CHART            APP VERSION     NAMESPACE
delivery-v0.1.0 1           Sun Apr  7 00:25:30 2019    DEPLOYED      delivery-v0.1.0  v0.1.0          backend
```

> [!TIP]
> <span data-ttu-id="32178-256">使用`--history-max`初始化 Helm 時加上旗標。</span><span class="sxs-lookup"><span data-stu-id="32178-256">Use the `--history-max` flag when initializing Helm.</span></span> <span data-ttu-id="32178-257">此設定會限制 Tiller 儲存其歷程記錄中的修訂編號。</span><span class="sxs-lookup"><span data-stu-id="32178-257">This setting limits the number of revisions that Tiller saves in its history.</span></span> <span data-ttu-id="32178-258">Tiller configmaps 儲存修訂歷程記錄。</span><span class="sxs-lookup"><span data-stu-id="32178-258">Tiller stores revision history in configmaps.</span></span> <span data-ttu-id="32178-259">如果您要經常發行更新，configmaps 可以成長非常大，除非您限制記錄大小。</span><span class="sxs-lookup"><span data-stu-id="32178-259">If you're releasing updates frequently, the configmaps can grow very large unless you limit the history size.</span></span>

## <a name="azure-devops-pipeline"></a><span data-ttu-id="32178-260">Azure DevOps Pipeline</span><span class="sxs-lookup"><span data-stu-id="32178-260">Azure DevOps Pipeline</span></span>

<span data-ttu-id="32178-261">在 Azure 的管線，管線會分為*建置管線*並*發行管線*。</span><span class="sxs-lookup"><span data-stu-id="32178-261">In Azure Pipelines, pipelines are divided into *build pipelines* and *release pipelines*.</span></span> <span data-ttu-id="32178-262">建置管線執行 CI 程序，並建立組建成品。</span><span class="sxs-lookup"><span data-stu-id="32178-262">The build pipeline runs the CI process and creates build artifacts.</span></span> <span data-ttu-id="32178-263">在 Kubernetes 上的微服務架構，這些成品的容器映像和是定義每個微服務的 Helm 圖表。</span><span class="sxs-lookup"><span data-stu-id="32178-263">For a microservices architecture on Kubernetes, these artifacts are the container images and Helm charts that define each microservice.</span></span> <span data-ttu-id="32178-264">發行管線會執行該 CD 程序來將微服務部署到叢集。</span><span class="sxs-lookup"><span data-stu-id="32178-264">The release pipeline runs that CD process that deploys a microservice into a cluster.</span></span>

<span data-ttu-id="32178-265">這篇文章稍早所述的 CI 流程為基礎，建置管線可能包含下列工作：</span><span class="sxs-lookup"><span data-stu-id="32178-265">Based on the CI flow described earlier in this article, a build pipeline might consist of the following tasks:</span></span>

1. <span data-ttu-id="32178-266">建置測試執行器的容器。</span><span class="sxs-lookup"><span data-stu-id="32178-266">Build the test runner container.</span></span>

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        arguments: '--pull --target testrunner'
        dockerFile: $(System.DefaultWorkingDirectory)/$(dockerFileName)
        imageName: '$(imageName)-test'
    ```

1. <span data-ttu-id="32178-267">藉由叫用測試執行器容器上執行的 docker 中執行測試。</span><span class="sxs-lookup"><span data-stu-id="32178-267">Run the tests, by invoking docker run against the test runner container.</span></span>

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

1. <span data-ttu-id="32178-268">發行測試結果。</span><span class="sxs-lookup"><span data-stu-id="32178-268">Publish the test results.</span></span> <span data-ttu-id="32178-269">請參閱[整合組建和測試工作](/azure/devops/pipelines/languages/docker?view=azure-devops&tabs=yaml#integrate-build-and-test-tasks)。</span><span class="sxs-lookup"><span data-stu-id="32178-269">See [Integrate build and test tasks](/azure/devops/pipelines/languages/docker?view=azure-devops&tabs=yaml#integrate-build-and-test-tasks).</span></span>

    ```yaml
    - task: PublishTestResults@2
      inputs:
        testResultsFormat: 'VSTest'
        testResultsFiles: 'TestResults/*.trx'
        searchFolder: '$(System.DefaultWorkingDirectory)'
        publishRunAttachments: true
    ```

1. <span data-ttu-id="32178-270">建置執行階段容器。</span><span class="sxs-lookup"><span data-stu-id="32178-270">Build the runtime container.</span></span>

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        dockerFile: $(System.DefaultWorkingDirectory)/$(dockerFileName)
        includeLatestTag: false
        imageName: '$(imageName)'
    ```

1. <span data-ttu-id="32178-271">推送至 Azure Container Registry （或其他容器登錄） 的容器。</span><span class="sxs-lookup"><span data-stu-id="32178-271">Push to the container to Azure Container Registry (or other container registry).</span></span>

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        command: 'Push an image'
        imageName: '$(imageName)'
        includeSourceTags: false
    ```

1. <span data-ttu-id="32178-272">封裝 Helm 圖表。</span><span class="sxs-lookup"><span data-stu-id="32178-272">Package the Helm chart.</span></span>

    ```yaml
    - task: HelmDeploy@0
      inputs:
        command: package
        chartPath: $(chartPath)
        chartVersion: $(Build.SourceBranchName)
        arguments: '--app-version $(Build.SourceBranchName)'
    ```

1. <span data-ttu-id="32178-273">Helm 套件推送至 Azure Container Registry （或其他 Helm 存放庫）。</span><span class="sxs-lookup"><span data-stu-id="32178-273">Push the Helm package to Azure Container Registry (or other Helm repository).</span></span>

    ```yaml
    task: AzureCLI@1
      inputs:
        azureSubscription: $(AzureSubscription)
        scriptLocation: inlineScript
        inlineScript: |
        az acr helm push $(System.ArtifactsDirectory)/$(repositoryName)-$(Build.SourceBranchName).tgz --name $(AzureContainerRegistry);
    ```

<span data-ttu-id="32178-274">&#11162;請參閱[原始程式檔](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-ci.yml)。</span><span class="sxs-lookup"><span data-stu-id="32178-274">&#11162; See the [source file](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-ci.yml).</span></span>

<span data-ttu-id="32178-275">CI 管線的輸出是可實際執行的容器映像和微服務更新的 Helm 圖表。</span><span class="sxs-lookup"><span data-stu-id="32178-275">The output from the CI pipeline is a production-ready container image and an updated Helm chart for the microservice.</span></span> <span data-ttu-id="32178-276">到目前為止，發行管線可以接管。</span><span class="sxs-lookup"><span data-stu-id="32178-276">At this point, the release pipeline can take over.</span></span> <span data-ttu-id="32178-277">它會執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="32178-277">It performs the following steps:</span></span>

- <span data-ttu-id="32178-278">部署至開發人員/QA/預備環境。</span><span class="sxs-lookup"><span data-stu-id="32178-278">Deploy to dev/QA/staging environments.</span></span>
- <span data-ttu-id="32178-279">等待核准者核准或拒絕部署。</span><span class="sxs-lookup"><span data-stu-id="32178-279">Wait for an approver to approve or reject the deployment.</span></span>
- <span data-ttu-id="32178-280">Retag 容器映像版本</span><span class="sxs-lookup"><span data-stu-id="32178-280">Retag the container image for release</span></span>
- <span data-ttu-id="32178-281">推送至容器登錄的版本標記。</span><span class="sxs-lookup"><span data-stu-id="32178-281">Push the release tag to the container registry.</span></span>
- <span data-ttu-id="32178-282">升級生產環境叢集中的 Helm 圖表。</span><span class="sxs-lookup"><span data-stu-id="32178-282">Upgrade the Helm chart in the production cluster.</span></span>

<span data-ttu-id="32178-283">如需建立發行管線的詳細資訊，請參閱 <<c0> [ 發行管線草稿版本，並釋放選項](/azure/devops/pipelines/release/?view=azure-devops)。</span><span class="sxs-lookup"><span data-stu-id="32178-283">For more information about creating a release pipeline, see [Release pipelines, draft releases, and release options](/azure/devops/pipelines/release/?view=azure-devops).</span></span>

<span data-ttu-id="32178-284">下圖顯示這篇文章中的端對端的 CI/CD 程序：</span><span class="sxs-lookup"><span data-stu-id="32178-284">The following diagram shows the end-to-end CI/CD process described in this article:</span></span>

![CD/CD 管線](./images/aks-cicd-flow.png)

## <a name="next-steps"></a><span data-ttu-id="32178-286">後續步驟</span><span class="sxs-lookup"><span data-stu-id="32178-286">Next steps</span></span>

<span data-ttu-id="32178-287">這篇文章根據您可以找到的參考實作[GitHub][ri]。</span><span class="sxs-lookup"><span data-stu-id="32178-287">This article was based on a reference implementation that you can find on [GitHub][ri].</span></span>

[ri]: https://github.com/mspnp/microservices-reference-implementation