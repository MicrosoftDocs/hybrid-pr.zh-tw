---
title: 使用 Azure 和 Azure Stack Hub，透過異地分散式應用程式進行流量導向
description: 了解如何使用 Azure 和 Azure Stack Hub，透過異地分散式應用程式解決方案將流量導向至特定端點。
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 8f2b7e48a62896acfce7293dcd4f18d5a43add01
ms.sourcegitcommit: bb3e40b210f86173568a47ba18c3cc50d4a40607
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2020
ms.locfileid: "84910165"
---
# <a name="direct-traffic-with-a-geo-distributed-app-using-azure-and-azure-stack-hub"></a><span data-ttu-id="6764a-103">使用 Azure 和 Azure Stack Hub，透過異地分散式應用程式進行流量導向</span><span class="sxs-lookup"><span data-stu-id="6764a-103">Direct traffic with a geo-distributed app using Azure and Azure Stack Hub</span></span>

<span data-ttu-id="6764a-104">了解如何根據各種計量，使用異地分散應用程式模式將流量導向至特定端點。</span><span class="sxs-lookup"><span data-stu-id="6764a-104">Learn how to direct traffic to specific endpoints based on various metrics using the geo-distributed apps pattern.</span></span> <span data-ttu-id="6764a-105">使用以地理位置為基礎的路由和端點組態來建立流量管理員設定檔，可確保資訊會根據區域需求、公司與國際法規和您的資料需求路由至端點。</span><span class="sxs-lookup"><span data-stu-id="6764a-105">Creating a Traffic Manager profile with geographic-based routing and endpoint configuration ensures information is routed to endpoints based on regional requirements, corporate and international regulation, and your data needs.</span></span>

<span data-ttu-id="6764a-106">在本解決方案中，您會建置環境範例，用以：</span><span class="sxs-lookup"><span data-stu-id="6764a-106">In this solution, you'll build a sample environment to:</span></span>

> [!div class="checklist"]
> - <span data-ttu-id="6764a-107">建立異地分散式應用程式。</span><span class="sxs-lookup"><span data-stu-id="6764a-107">Create a geo-distributed app.</span></span>
> - <span data-ttu-id="6764a-108">使用流量管理員設定應用程式的目標。</span><span class="sxs-lookup"><span data-stu-id="6764a-108">Use Traffic Manager to target your app.</span></span>

## <a name="use-the-geo-distributed-apps-pattern"></a><span data-ttu-id="6764a-109">使用異地分散應用程式模式</span><span class="sxs-lookup"><span data-stu-id="6764a-109">Use the geo-distributed apps pattern</span></span>

<span data-ttu-id="6764a-110">透過異地分散模式，您的應用程式將可跨區使用。</span><span class="sxs-lookup"><span data-stu-id="6764a-110">With the geo-distributed pattern, your app spans regions.</span></span> <span data-ttu-id="6764a-111">您可以預設為公用雲端，但您的某些使用者可能會需要將資料保留在其區域中。</span><span class="sxs-lookup"><span data-stu-id="6764a-111">You can default to the public cloud, but some of your users may require that their data remain in their region.</span></span> <span data-ttu-id="6764a-112">您可以根據使用者的需求將他們導向至最適合的雲端。</span><span class="sxs-lookup"><span data-stu-id="6764a-112">You can direct users to the most suitable cloud based on their requirements.</span></span>

### <a name="issues-and-considerations"></a><span data-ttu-id="6764a-113">問題和考量</span><span class="sxs-lookup"><span data-stu-id="6764a-113">Issues and considerations</span></span>

#### <a name="scalability-considerations"></a><span data-ttu-id="6764a-114">延展性考量</span><span class="sxs-lookup"><span data-stu-id="6764a-114">Scalability considerations</span></span>

<span data-ttu-id="6764a-115">您將在本解決方案中建置的解決方案無法處理延展性的問題。</span><span class="sxs-lookup"><span data-stu-id="6764a-115">The solution you'll build with this article isn't to accommodate scalability.</span></span> <span data-ttu-id="6764a-116">不過，如果與其他 Azure 和內部部署解決方案搭配使用，即可滿足延展性方面的需求。</span><span class="sxs-lookup"><span data-stu-id="6764a-116">However, if used in combination with other Azure and on-premises solutions, you can accommodate scalability requirements.</span></span> <span data-ttu-id="6764a-117">如需透過流量管理員使用自動調整建立混合式解決方案的相關資訊，請參閱[使用 Azure 建立跨雲端調整解決方案](solution-deployment-guide-cross-cloud-scaling.md)。</span><span class="sxs-lookup"><span data-stu-id="6764a-117">For information on creating a hybrid solution with autoscaling via traffic manager, see [Create cross-cloud scaling solutions with Azure](solution-deployment-guide-cross-cloud-scaling.md).</span></span>

#### <a name="availability-considerations"></a><span data-ttu-id="6764a-118">可用性考量</span><span class="sxs-lookup"><span data-stu-id="6764a-118">Availability considerations</span></span>

<span data-ttu-id="6764a-119">和延展性考量一樣，此解決方案無法直接處理可用性的問題。</span><span class="sxs-lookup"><span data-stu-id="6764a-119">As is the case with scalability considerations, this solution doesn't directly address availability.</span></span> <span data-ttu-id="6764a-120">但在此解決方案中可以實作 Azure 和內部部署解決方案，以確保所有相關元件皆具有高可用性。</span><span class="sxs-lookup"><span data-stu-id="6764a-120">However, Azure and on-premises solutions can be implemented within this solution to ensure high availability for all components involved.</span></span>

### <a name="when-to-use-this-pattern"></a><span data-ttu-id="6764a-121">使用此模式的時機</span><span class="sxs-lookup"><span data-stu-id="6764a-121">When to use this pattern</span></span>

- <span data-ttu-id="6764a-122">您的組織有海外分公司，且需要自訂的區域安全性和發佈原則。</span><span class="sxs-lookup"><span data-stu-id="6764a-122">Your organization has international branches requiring custom regional security and distribution policies.</span></span>

- <span data-ttu-id="6764a-123">您的每個組織辦公室皆需提取員工、商務和設備資料，因此需要各自的當地法規和時區的報告活動。</span><span class="sxs-lookup"><span data-stu-id="6764a-123">Each of your organization's offices pulls employee, business, and facility data, which requires reporting activity per local regulations and time zones.</span></span>

- <span data-ttu-id="6764a-124">只要對單一區域內和跨區域的多個應用程式部署進行應用程式的水平相應放大，即可達到高延展性需求，以處理極高的負載需求。</span><span class="sxs-lookup"><span data-stu-id="6764a-124">High-scale requirements are met by horizontally scaling out apps with multiple app deployments within a single region and across regions to handle extreme load requirements.</span></span>

### <a name="planning-the-topology"></a><span data-ttu-id="6764a-125">規劃拓樸</span><span class="sxs-lookup"><span data-stu-id="6764a-125">Planning the topology</span></span>

<span data-ttu-id="6764a-126">在建置分散式應用程式的使用量之前，最好先具備下列知識：</span><span class="sxs-lookup"><span data-stu-id="6764a-126">Before building out a distributed app footprint, it helps to know the following things:</span></span>

- <span data-ttu-id="6764a-127">**應用程式的自訂網域：** 客戶將用來存取應用程式的自訂網域名稱為何？</span><span class="sxs-lookup"><span data-stu-id="6764a-127">**Custom domain for the app:** What's the custom domain name that customers will use to access the app?</span></span> <span data-ttu-id="6764a-128">範例應用程式的自訂網域名稱是 www\.scalableasedemo.com  。</span><span class="sxs-lookup"><span data-stu-id="6764a-128">For the sample app, the custom domain name is *www\.scalableasedemo.com.*</span></span>

- <span data-ttu-id="6764a-129">**流量管理員網域：** 建立 [Azure 流量管理員設定檔](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-manage-profiles)時，必須選擇一個網域名稱。</span><span class="sxs-lookup"><span data-stu-id="6764a-129">**Traffic Manager domain:** A domain name is chosen when creating an [Azure Traffic Manager profile](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-manage-profiles).</span></span> <span data-ttu-id="6764a-130">此名稱會與 *trafficmanager.net* 尾碼結合，以註冊流量管理員所管理的網域項目。</span><span class="sxs-lookup"><span data-stu-id="6764a-130">This name is combined with the *trafficmanager.net* suffix to register a domain entry that's managed by Traffic Manager.</span></span> <span data-ttu-id="6764a-131">就範例應用程式而言，選擇的名稱是 *scalable-ase-demo*。</span><span class="sxs-lookup"><span data-stu-id="6764a-131">For the sample app, the name chosen is *scalable-ase-demo*.</span></span> <span data-ttu-id="6764a-132">因此，流量管理員所管理的完整網域名稱是 *scalable-ase-demo.trafficmanager.net*。</span><span class="sxs-lookup"><span data-stu-id="6764a-132">As a result, the full domain name that's managed by Traffic Manager is *scalable-ase-demo.trafficmanager.net*.</span></span>

- <span data-ttu-id="6764a-133">**調整應用程式使用量的策略：** 決定應用程式使用量要分散到單一區域中的多個 App Service 環境、多個區域中的多個 App Service 環境，還是兩者混用。</span><span class="sxs-lookup"><span data-stu-id="6764a-133">**Strategy for scaling the app footprint:** Decide whether the app footprint will be distributed across multiple App Service environments in a single region, multiple regions, or a mix of both approaches.</span></span> <span data-ttu-id="6764a-134">決策依據應來自於客戶流量的來源位置，以及其餘應用程式的支援後端基礎結構的可調整性。</span><span class="sxs-lookup"><span data-stu-id="6764a-134">The decision should be based on expectations of where customer traffic will originate and how well the rest of an app's supporting back-end infrastructure can scale.</span></span> <span data-ttu-id="6764a-135">例如，對於 100% 無狀態的應用程式，可以使用每一 Azure 區域多個 App Service 環境的組合，乘以跨多個 Azure 區域部署的 App Service 環境數，來大幅調整應用程式。</span><span class="sxs-lookup"><span data-stu-id="6764a-135">For example, with a 100% stateless app, an app can be massively scaled using a combination of multiple App Service environments per Azure region, multiplied by App Service environments deployed across multiple Azure regions.</span></span> <span data-ttu-id="6764a-136">由於有 15 個以上的全球 Azure 區域可供選擇，客戶將可真正建置全球性超高延展性的應用程式使用量。</span><span class="sxs-lookup"><span data-stu-id="6764a-136">With 15+ global Azure regions available to choose from, customers can truly build a world-wide hyper-scale app footprint.</span></span> <span data-ttu-id="6764a-137">在此處使用的範例應用程式中，有三個 App Service 環境建立在單一 Azure 區域 (美國中南部) 中。</span><span class="sxs-lookup"><span data-stu-id="6764a-137">For the sample app used here, three App Service environments were created in a single Azure region (South Central US).</span></span>

- <span data-ttu-id="6764a-138">**App Service 環境的命名慣例：** 每個 App Service 環境都需要一個唯一的名稱。</span><span class="sxs-lookup"><span data-stu-id="6764a-138">**Naming convention for the App Service environments:** Each App Service environment requires a unique name.</span></span> <span data-ttu-id="6764a-139">有兩個或更多 App Service 環境時，命名慣例將有助於識別每個 App Service 環境。</span><span class="sxs-lookup"><span data-stu-id="6764a-139">Beyond one or two App Service environments, it's helpful to have a naming convention to help identify each App Service environment.</span></span> <span data-ttu-id="6764a-140">在此使用的範例應用程式會以簡單的命名慣例命名。</span><span class="sxs-lookup"><span data-stu-id="6764a-140">For the sample app used here, a simple naming convention was used.</span></span> <span data-ttu-id="6764a-141">三個 App Service 環境的名稱分別是 fe1ase  、fe2ase  和 fe3ase  。</span><span class="sxs-lookup"><span data-stu-id="6764a-141">The names of the three App Service environments are *fe1ase*, *fe2ase*, and *fe3ase*.</span></span>

- <span data-ttu-id="6764a-142">**應用程式的命名慣例：** 由於將會部署多個應用程式執行個體，因此每個部署的應用程式執行個體都需要一個名稱。</span><span class="sxs-lookup"><span data-stu-id="6764a-142">**Naming convention for the apps:** Since multiple instances of the app will be deployed, a name is needed for each instance of the deployed app.</span></span> <span data-ttu-id="6764a-143">使用 Power Apps 的 App Service 環境時，多個環境可以使用相同的應用程式名稱。</span><span class="sxs-lookup"><span data-stu-id="6764a-143">With App Service Environment for Power Apps, the same app name can be used across multiple environments.</span></span> <span data-ttu-id="6764a-144">由於每個 App Service 環境都有唯一的網域尾碼，開發人員可以選擇在每個環境中重複使用相同的應用程式名稱。</span><span class="sxs-lookup"><span data-stu-id="6764a-144">Since each App Service environment has a unique domain suffix, developers can choose to reuse the exact same app name in each environment.</span></span> <span data-ttu-id="6764a-145">例如，開發人員可以將應用程式命名如下：myapp.foo1.p.azurewebsites.net  、myapp.foo2.p.azurewebsites.net  、myapp.foo3.p.azurewebsites.net  ，依此類推。</span><span class="sxs-lookup"><span data-stu-id="6764a-145">For example, a developer could have apps named as follows: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*, and so on.</span></span> <span data-ttu-id="6764a-146">就此處使用的應用程式，每個應用程式執行個體都有唯一名稱。</span><span class="sxs-lookup"><span data-stu-id="6764a-146">For the app used here, each app instance has a unique name.</span></span> <span data-ttu-id="6764a-147">所使用的應用程式執行個體名稱是 webfrontend1  、webfrontend2  和 webfrontend3  。</span><span class="sxs-lookup"><span data-stu-id="6764a-147">The app instance names used are *webfrontend1*, *webfrontend2*, and *webfrontend3*.</span></span>

> [!Tip]  
> <span data-ttu-id="6764a-148">![hybrid-pillars.png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)</span><span class="sxs-lookup"><span data-stu-id="6764a-148">![hybrid-pillars.png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)</span></span>  
> <span data-ttu-id="6764a-149">Microsoft Azure Stack Hub 是 Azure 的延伸模組。</span><span class="sxs-lookup"><span data-stu-id="6764a-149">Microsoft Azure Stack Hub is an extension of Azure.</span></span> <span data-ttu-id="6764a-150">Azure Stack Hub 可將雲端運算的靈活性與創新能力導入您的內部部署環境中，並啟用獨特的混合式雲端，讓您能夠隨處建置及部署混合式應用程式。</span><span class="sxs-lookup"><span data-stu-id="6764a-150">Azure Stack Hub brings the agility and innovation of cloud computing to your on-premises environment, enabling the only hybrid cloud that allows you to build and deploy hybrid apps anywhere.</span></span>  
> 
> <span data-ttu-id="6764a-151">[混合式應用程式設計考量](overview-app-design-considerations.md)一文檢閱了設計、部署和操作混合式應用程式時的軟體品質要素 (放置、延展性、可用性、復原、管理性和安全性)。</span><span class="sxs-lookup"><span data-stu-id="6764a-151">The article [Hybrid app design considerations](overview-app-design-considerations.md) reviews pillars of software quality (placement, scalability, availability, resiliency, manageability, and security) for designing, deploying, and operating hybrid apps.</span></span> <span data-ttu-id="6764a-152">這些設計考量有助於您設計出最佳的混合式應用程式，減少生產環境可能會遇到的挑戰。</span><span class="sxs-lookup"><span data-stu-id="6764a-152">The design considerations assist in optimizing hybrid app design, minimizing challenges in production environments.</span></span>

## <a name="part-1-create-a-geo-distributed-app"></a><span data-ttu-id="6764a-153">第 1 部分：建立異地分散應用程式</span><span class="sxs-lookup"><span data-stu-id="6764a-153">Part 1: Create a geo-distributed app</span></span>

<span data-ttu-id="6764a-154">在此部分中，您將建立 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="6764a-154">In this part, you'll create a web app.</span></span>

> [!div class="checklist"]
> - <span data-ttu-id="6764a-155">建立 Web 應用程式並發佈。</span><span class="sxs-lookup"><span data-stu-id="6764a-155">Create web apps and publish.</span></span>
> - <span data-ttu-id="6764a-156">將程式碼新增至 Azure Repos。</span><span class="sxs-lookup"><span data-stu-id="6764a-156">Add code to Azure Repos.</span></span>
> - <span data-ttu-id="6764a-157">將應用程式組建指向多個雲端目標。</span><span class="sxs-lookup"><span data-stu-id="6764a-157">Point the app build to multiple cloud targets.</span></span>
> - <span data-ttu-id="6764a-158">管理和設定 CD 流程。</span><span class="sxs-lookup"><span data-stu-id="6764a-158">Manage and configure the CD process.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="6764a-159">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="6764a-159">Prerequisites</span></span>

<span data-ttu-id="6764a-160">需要 Azure 訂閱並安裝 Azure Stack Hub。</span><span class="sxs-lookup"><span data-stu-id="6764a-160">An Azure subscription and Azure Stack Hub installation are required.</span></span>

### <a name="geo-distributed-app-steps"></a><span data-ttu-id="6764a-161">異地分散應用程式的步驟</span><span class="sxs-lookup"><span data-stu-id="6764a-161">Geo-distributed app steps</span></span>

### <a name="obtain-a-custom-domain-and-configure-dns"></a><span data-ttu-id="6764a-162">取得自訂網域並設定 DNS</span><span class="sxs-lookup"><span data-stu-id="6764a-162">Obtain a custom domain and configure DNS</span></span>

<span data-ttu-id="6764a-163">更新網域的 DNS 區域檔案。</span><span class="sxs-lookup"><span data-stu-id="6764a-163">Update the DNS zone file for the domain.</span></span> <span data-ttu-id="6764a-164">然後，Azure AD 會驗證自訂網域名稱的擁有權。</span><span class="sxs-lookup"><span data-stu-id="6764a-164">Azure AD can then verify ownership of the custom domain name.</span></span> <span data-ttu-id="6764a-165">對於 Azure 中的 Azure/Office 365/外部 DNS 記錄使用 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal)，或在[不同的 DNS 註冊機構](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)新增 DNS 項目。</span><span class="sxs-lookup"><span data-stu-id="6764a-165">Use [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) for Azure/Office 365/external DNS records within Azure, or add the DNS entry at [a different DNS registrar](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).</span></span>

1. <span data-ttu-id="6764a-166">向公用註冊機構註冊自訂網域。</span><span class="sxs-lookup"><span data-stu-id="6764a-166">Register a custom domain with a public registrar.</span></span>

2. <span data-ttu-id="6764a-167">登入網域的網域名稱註冊機構。</span><span class="sxs-lookup"><span data-stu-id="6764a-167">Sign in to the domain name registrar for the domain.</span></span> <span data-ttu-id="6764a-168">已核准的系統管理員可能需要進行 DNS 更新。</span><span class="sxs-lookup"><span data-stu-id="6764a-168">An approved admin may be required to make the DNS updates.</span></span>

3. <span data-ttu-id="6764a-169">透過新增 Azure AD 提供的 DNS 項目來更新網域的 DNS 區域檔案。</span><span class="sxs-lookup"><span data-stu-id="6764a-169">Update the DNS zone file for the domain by adding the DNS entry provided by Azure AD.</span></span> <span data-ttu-id="6764a-170">DNS 項目不會變更郵件路由或 Web 裝載等行為。</span><span class="sxs-lookup"><span data-stu-id="6764a-170">The DNS entry doesn't change behaviors such as mail routing or web hosting.</span></span>

### <a name="create-web-apps-and-publish"></a><span data-ttu-id="6764a-171">建立 Web 應用程式並發佈</span><span class="sxs-lookup"><span data-stu-id="6764a-171">Create web apps and publish</span></span>

<span data-ttu-id="6764a-172">設定混合式持續整合/持續交付 (CI/CD)，以將 Web 應用程式部署至 Azure 和 Azure Stack Hub，並自動將變更推送至這兩個雲端。</span><span class="sxs-lookup"><span data-stu-id="6764a-172">Set up Hybrid Continuous Integration/Continuous Delivery (CI/CD) to deploy Web App to Azure and Azure Stack Hub, and auto push changes to both clouds.</span></span>

> [!Note]  
> <span data-ttu-id="6764a-173">需要適當映像摘要整合執行的 Azure Stack Hub (Windows Server 和 SQL) 及 App Service 部署。</span><span class="sxs-lookup"><span data-stu-id="6764a-173">Azure Stack Hub with proper images syndicated to run (Windows Server and SQL) and App Service deployment are required.</span></span> <span data-ttu-id="6764a-174">如需詳細資訊，請參閱[部署 Azure Stack Hub 上的 App Service 必要條件](/azure-stack/operator/azure-stack-app-service-before-you-get-started.md)。</span><span class="sxs-lookup"><span data-stu-id="6764a-174">For more information, see [Prerequisites for deploying App Service on Azure Stack Hub](/azure-stack/operator/azure-stack-app-service-before-you-get-started.md).</span></span>

#### <a name="add-code-to-azure-repos"></a><span data-ttu-id="6764a-175">將程式碼新增至 Azure Repos</span><span class="sxs-lookup"><span data-stu-id="6764a-175">Add Code to Azure Repos</span></span>

1. <span data-ttu-id="6764a-176">使用在 Azure Repos 上具有專案建立權限的**帳戶**登入 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="6764a-176">Sign in to Visual Studio with an **account that has project creation rights** on Azure Repos.</span></span>

    <span data-ttu-id="6764a-177">CI/CD 可同時套用至應用程式程式碼和基礎結構程式碼。</span><span class="sxs-lookup"><span data-stu-id="6764a-177">CI/CD can apply to both app code and infrastructure code.</span></span> <span data-ttu-id="6764a-178">使用 [Azure Resource Manager 範本](https://azure.microsoft.com/resources/templates/)進行私用與託管的雲端開發。</span><span class="sxs-lookup"><span data-stu-id="6764a-178">Use [Azure Resource Manager templates](https://azure.microsoft.com/resources/templates/) for both private and hosted cloud development.</span></span>

    ![在 Visual Studio 中連線至專案](media/solution-deployment-guide-geo-distributed/image1.JPG)

2. <span data-ttu-id="6764a-180">建立並開啟預設 Web 應用程式以**複製存放庫**。</span><span class="sxs-lookup"><span data-stu-id="6764a-180">**Clone the repository** by creating and opening the default web app.</span></span>

    ![在 Visual Studio 中複製存放庫](media/solution-deployment-guide-geo-distributed/image2.png)

### <a name="create-web-app-deployment-in-both-clouds"></a><span data-ttu-id="6764a-182">在這兩個雲端中建立 Web 應用程式部署</span><span class="sxs-lookup"><span data-stu-id="6764a-182">Create web app deployment in both clouds</span></span>

1. <span data-ttu-id="6764a-183">編輯 **WebApplication.csproj**檔案：選取 `Runtimeidentifier` 並新增 `win10-x64`。</span><span class="sxs-lookup"><span data-stu-id="6764a-183">Edit the **WebApplication.csproj** file: Select `Runtimeidentifier` and add `win10-x64`.</span></span> <span data-ttu-id="6764a-184">(請參閱[獨立式部署](https://docs.microsoft.com/dotnet/core/deploying/deploy-with-vs#simpleSelf)文件。)</span><span class="sxs-lookup"><span data-stu-id="6764a-184">(See [Self-contained Deployment](https://docs.microsoft.com/dotnet/core/deploying/deploy-with-vs#simpleSelf) documentation.)</span></span>

    ![在 Visual Studio 中編輯 Web 應用程式專案檔](media/solution-deployment-guide-geo-distributed/image3.png)

2. <span data-ttu-id="6764a-186">使用 Team Explorer **將程式碼簽入 Azure Repos 中**。</span><span class="sxs-lookup"><span data-stu-id="6764a-186">**Check in the code to Azure Repos** using Team Explorer.</span></span>

3. <span data-ttu-id="6764a-187">確認**應用程式程式碼**已簽入 Azure Repos 中。</span><span class="sxs-lookup"><span data-stu-id="6764a-187">Confirm that the **application code** has been checked into Azure Repos.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="6764a-188">建立組建定義</span><span class="sxs-lookup"><span data-stu-id="6764a-188">Create the build definition</span></span>

1. <span data-ttu-id="6764a-189">**登入 Azure Pipelines** 以確認能夠建立組建定義。</span><span class="sxs-lookup"><span data-stu-id="6764a-189">**Sign in to Azure Pipelines** to confirm ability to create build definitions.</span></span>

2. <span data-ttu-id="6764a-190">新增 `-r win10-x64` 程式碼。</span><span class="sxs-lookup"><span data-stu-id="6764a-190">Add `-r win10-x64` code.</span></span> <span data-ttu-id="6764a-191">新增的部分為觸發 .NNE Core 獨立部署所需的程式碼。</span><span class="sxs-lookup"><span data-stu-id="6764a-191">This addition is necessary to trigger a self-contained deployment with .NET Core.</span></span>

    ![將程式碼新增至 Azure Pipelines 中的組建定義](media/solution-deployment-guide-geo-distributed/image4.png)

3. <span data-ttu-id="6764a-193">**執行組建**。</span><span class="sxs-lookup"><span data-stu-id="6764a-193">**Run the build**.</span></span> <span data-ttu-id="6764a-194">[獨立式部署組建](https://docs.microsoft.com/dotnet/core/deploying/deploy-with-vs#simpleSelf)程序將會發佈可在 Azure 與 Azure Stack Hub 上執行的成品。</span><span class="sxs-lookup"><span data-stu-id="6764a-194">The [self-contained deployment build](https://docs.microsoft.com/dotnet/core/deploying/deploy-with-vs#simpleSelf) process will publish artifacts that can run on Azure and Azure Stack Hub.</span></span>

#### <a name="using-an-azure-hosted-agent"></a><span data-ttu-id="6764a-195">使用 Azure 託管的代理程式</span><span class="sxs-lookup"><span data-stu-id="6764a-195">Using an Azure Hosted Agent</span></span>

<span data-ttu-id="6764a-196">在 Azure Pipelines 中使用託管的代理程式，是建置及部署 Web 應用程式的便利選項。</span><span class="sxs-lookup"><span data-stu-id="6764a-196">Using a hosted agent in Azure Pipelines is a convenient option to build and deploy web apps.</span></span> <span data-ttu-id="6764a-197">Microsoft Azure 會自動執行維護和升級，以支援不間斷的開發、測試和部署。</span><span class="sxs-lookup"><span data-stu-id="6764a-197">Maintenance and upgrades are automatically performed by Microsoft Azure, which enables uninterrupted development, testing, and deployment.</span></span>

### <a name="manage-and-configure-the-cd-process"></a><span data-ttu-id="6764a-198">管理和設定 CD 程序</span><span class="sxs-lookup"><span data-stu-id="6764a-198">Manage and configure the CD process</span></span>

<span data-ttu-id="6764a-199">Azure DevOps Services 提供具有高度設定和管理能力的管線，可用於對多個環境的發行 (例如開發、暫存、QA 和生產環境)；包括特定階段需要核准。</span><span class="sxs-lookup"><span data-stu-id="6764a-199">Azure DevOps Services provide a highly configurable and manageable pipeline for releases to multiple environments such as development, staging, QA, and production environments; including requiring approvals at specific stages.</span></span>

## <a name="create-release-definition"></a><span data-ttu-id="6764a-200">建立發行定義</span><span class="sxs-lookup"><span data-stu-id="6764a-200">Create release definition</span></span>

1. <span data-ttu-id="6764a-201">在 Azure DevOps Services 的 [建置及發行]  區段的 [發行]  索引標籤下選取 [加號]  按鈕，以新增發行。</span><span class="sxs-lookup"><span data-stu-id="6764a-201">Select the **plus** button to add a new release under the **Releases** tab in the **Build and Release** section of Azure DevOps Services.</span></span>

    ![在 Azure DevOps Services 中建立發行定義](media/solution-deployment-guide-geo-distributed/image5.png)

2. <span data-ttu-id="6764a-203">套用 Azure App Service 部署範本。</span><span class="sxs-lookup"><span data-stu-id="6764a-203">Apply the Azure App Service Deployment template.</span></span>

   ![在 Azure DevOps Services 中套用 Azure App Service 部署範本](media/solution-deployment-guide-geo-distributed/image6.png)

3. <span data-ttu-id="6764a-205">在 [新增成品]  下，為 Azure 雲端建置應用程式新增成品。</span><span class="sxs-lookup"><span data-stu-id="6764a-205">Under **Add artifact**, add the artifact for the Azure Cloud build app.</span></span>

   ![在 Azure DevOps Services 中將成品新增至 Azure 雲端組建](media/solution-deployment-guide-geo-distributed/image7.png)

4. <span data-ttu-id="6764a-207">在 [管線] 索引標籤下選取環境的 [階段]、[工作]  連結，並設定 Azure 雲端環境值。</span><span class="sxs-lookup"><span data-stu-id="6764a-207">Under Pipeline tab, select the **Phase, Task** link of the environment and set the Azure cloud environment values.</span></span>

   ![在 Azure DevOps Services 中設定 Azure 雲端環境值](media/solution-deployment-guide-geo-distributed/image8.png)

5. <span data-ttu-id="6764a-209">設定 [環境名稱]  ，並選取 Azure 雲端端點的 **Azure訂用帳戶**。</span><span class="sxs-lookup"><span data-stu-id="6764a-209">Set the **environment name** and select the **Azure subscription** for the Azure Cloud endpoint.</span></span>

      ![在 Azure DevOps Services 中選取 Azure 雲端端點的 Azure 訂用帳戶](media/solution-deployment-guide-geo-distributed/image9.png)

6. <span data-ttu-id="6764a-211">在 [應用程式服務名稱]  下設定所需的 Azure 應用程式服務名稱。</span><span class="sxs-lookup"><span data-stu-id="6764a-211">Under **App service name**, set the required Azure app service name.</span></span>

      ![在 Azure DevOps Services 中設定 Azure App Service 名稱](media/solution-deployment-guide-geo-distributed/image10.png)

7. <span data-ttu-id="6764a-213">在 Azure 雲端託管環境的**代理程式佇列**下輸入 "Hosted VS2017"。</span><span class="sxs-lookup"><span data-stu-id="6764a-213">Enter "Hosted VS2017" under **Agent queue** for Azure cloud hosted environment.</span></span>

      ![在 Azure DevOps Services 中為 Azure 雲端裝載環境設定代理程式佇列](media/solution-deployment-guide-geo-distributed/image11.png)

8. <span data-ttu-id="6764a-215">在 [部署 Azure App Service] 功能表中，為環境選取有效的**套件或資料夾**。</span><span class="sxs-lookup"><span data-stu-id="6764a-215">In Deploy Azure App Service menu, select the valid **Package or Folder** for the environment.</span></span> <span data-ttu-id="6764a-216">對**資料夾位置**選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-216">Select **OK** to **folder location**.</span></span>
  
      ![在 Azure DevOps Services 中選取適用於 Azure App Service 環境的套件或資料夾](media/solution-deployment-guide-geo-distributed/image12.png)

      ![在 Azure DevOps Services 中選取適用於 Azure App Service 環境的套件或資料夾](media/solution-deployment-guide-geo-distributed/image13.png)

9. <span data-ttu-id="6764a-219">儲存所有變更，並返回**發行管線**。</span><span class="sxs-lookup"><span data-stu-id="6764a-219">Save all changes and go back to **release pipeline**.</span></span>

    ![在 Azure DevOps Services 中儲存發行管線中的變更](media/solution-deployment-guide-geo-distributed/image14.png)

10. <span data-ttu-id="6764a-221">選取 Azure Stack Hub 應用程式的組建，以新增成品。</span><span class="sxs-lookup"><span data-stu-id="6764a-221">Add a new artifact selecting the build for the Azure Stack Hub app.</span></span>

    ![在 Azure DevOps Services 中為 Azure Stack Hub 應用程式新增成品](media/solution-deployment-guide-geo-distributed/image15.png)


11. <span data-ttu-id="6764a-223">套用 Azure App Service 部署，以便再新增一個環境。</span><span class="sxs-lookup"><span data-stu-id="6764a-223">Add one more environment by applying the Azure App Service Deployment.</span></span>

    ![在 Azure DevOps Services 中將環境新增至 Azure App Service 部署](media/solution-deployment-guide-geo-distributed/image16.png)

12. <span data-ttu-id="6764a-225">將新環境命名為 Azure Stack Hub。</span><span class="sxs-lookup"><span data-stu-id="6764a-225">Name the new environment Azure Stack Hub.</span></span>

    ![在 Azure DevOps Services 中為 Azure App Service 部署中的環境命名](media/solution-deployment-guide-geo-distributed/image17.png)

13. <span data-ttu-id="6764a-227">在 [工作]  索引標籤下找出 Azure Stack Hub 環境。</span><span class="sxs-lookup"><span data-stu-id="6764a-227">Find the Azure Stack Hub environment under **Task** tab.</span></span>

    ![在 Azure DevOps Services 中的 Azure Stack Hub 環境](media/solution-deployment-guide-geo-distributed/image18.png)

14. <span data-ttu-id="6764a-229">選取 Azure Stack Hub 端點的訂閱。</span><span class="sxs-lookup"><span data-stu-id="6764a-229">Select the subscription for the Azure Stack Hub endpoint.</span></span>

    ![在 Azure DevOps Services 中選取 Azure Stack Hub 端點的訂用帳戶](media/solution-deployment-guide-geo-distributed/image19.png)

15. <span data-ttu-id="6764a-231">將 Azure Stack Hub Web 應用程式名稱設定為 App Service 名稱。</span><span class="sxs-lookup"><span data-stu-id="6764a-231">Set the Azure Stack Hub web app name as the App service name.</span></span>

    ![在 Azure DevOps Services 中設定 Azure Stack Hub Web 應用程式名稱](media/solution-deployment-guide-geo-distributed/image20.png)

16. <span data-ttu-id="6764a-233">選取 Azure Stack Hub 代理程式。</span><span class="sxs-lookup"><span data-stu-id="6764a-233">Select the Azure Stack Hub agent.</span></span>

    ![在 Azure DevOps Services 中選取 Azure Stack Hub 代理程式](media/solution-deployment-guide-geo-distributed/image21.png)

17. <span data-ttu-id="6764a-235">在 [部署 Azure App Service] 區段下，為環境選取有效的**套件或資料夾**。</span><span class="sxs-lookup"><span data-stu-id="6764a-235">Under the Deploy Azure App Service section, select the valid **Package or Folder** for the environment.</span></span> <span data-ttu-id="6764a-236">對資料夾位置選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-236">Select **OK** to folder location.</span></span>

    ![在 Azure DevOps Services 中為 Azure App Service 部署選取資料夾](media/solution-deployment-guide-geo-distributed/image22.png)

    ![在 Azure DevOps Services 中為 Azure App Service 部署選取資料夾](media/solution-deployment-guide-geo-distributed/image23.png)

18. <span data-ttu-id="6764a-239">在 [變數] 索引標籤下新增名為 `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS` 的變數，並將其值設定為 **true**，範圍設定為 Azure Stack Hub。</span><span class="sxs-lookup"><span data-stu-id="6764a-239">Under Variable tab add a variable named `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`, set its value as **true**, and scope to Azure Stack Hub.</span></span>

    ![在 Azure DevOps Services 中將變數新增至 Azure 應用程式部署](media/solution-deployment-guide-geo-distributed/image24.png)

19. <span data-ttu-id="6764a-241">選取兩個成品中的 [持續部署觸發程序]  圖示，並啟用**持續**部署觸發程序。</span><span class="sxs-lookup"><span data-stu-id="6764a-241">Select the **Continuous** deployment trigger icon in both artifacts and enable the **Continues** deployment trigger.</span></span>

    ![在 Azure DevOps Services 中選取持續部署觸發程序](media/solution-deployment-guide-geo-distributed/image25.png)

20. <span data-ttu-id="6764a-243">選取 Azure Stack Hub 環境中的 [預先部署]  條件圖示，並將觸發程序設定為 [發行之後]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-243">Select the **Pre-deployment** conditions icon in the Azure Stack Hub environment and set the trigger to **After release.**</span></span>

    ![在 Azure DevOps Services 中選取部署前的條件](media/solution-deployment-guide-geo-distributed/image26.png)

21. <span data-ttu-id="6764a-245">儲存所有變更。</span><span class="sxs-lookup"><span data-stu-id="6764a-245">Save all changes.</span></span>

> [!Note]  
> <span data-ttu-id="6764a-246">工作的某些設定可能已在從範本建立發行定義時自動定義為[環境變數](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables)。</span><span class="sxs-lookup"><span data-stu-id="6764a-246">Some settings for the tasks may have been automatically defined as [environment variables](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) when creating a release definition from a template.</span></span> <span data-ttu-id="6764a-247">這些設定無法在工作設定中修改；而是必須選取父環境項目才能編輯這些設定。</span><span class="sxs-lookup"><span data-stu-id="6764a-247">These settings can't be modified in the task settings; instead, the parent environment item must be selected to edit these settings.</span></span>

## <a name="part-2-update-web-app-options"></a><span data-ttu-id="6764a-248">第 2 部分：更新 Web 應用程式選項</span><span class="sxs-lookup"><span data-stu-id="6764a-248">Part 2: Update web app options</span></span>

<span data-ttu-id="6764a-249">[Azure App Service](https://docs.microsoft.com/azure/app-service/overview) 可提供可高度擴充、自我修復的 Web 主控服務。</span><span class="sxs-lookup"><span data-stu-id="6764a-249">[Azure App Service](https://docs.microsoft.com/azure/app-service/overview) provides a highly scalable, self-patching web hosting service.</span></span>

![Azure App Service](media/solution-deployment-guide-geo-distributed/image27.png)

> [!div class="checklist"]
> - <span data-ttu-id="6764a-251">將現有的自訂 DNS 名稱對應至 Azure Web Apps。</span><span class="sxs-lookup"><span data-stu-id="6764a-251">Map an existing custom DNS name to Azure Web Apps.</span></span>
> - <span data-ttu-id="6764a-252">使用 **CNAME 記錄** **A 記錄**將自訂 DNS 名稱對應至 App Service。</span><span class="sxs-lookup"><span data-stu-id="6764a-252">Use a **CNAME record** and an **A record** to map a custom DNS name to App Service.</span></span>

### <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a><span data-ttu-id="6764a-253">將現有的自訂 DNS 名稱對應至 Azure Web Apps</span><span class="sxs-lookup"><span data-stu-id="6764a-253">Map an existing custom DNS name to Azure Web Apps</span></span>

> [!Note]  
> <span data-ttu-id="6764a-254">請對所有自訂 DNS 名稱使用 CNAME，但根網域除外 (例如 northwind.com)。</span><span class="sxs-lookup"><span data-stu-id="6764a-254">Use a CNAME for all custom DNS names except a root domain (for example, northwind.com).</span></span>

<span data-ttu-id="6764a-255">若要將即時網站及其 DNS 網域名稱移轉至 App Service，請參閱[將作用中的 DNS 名稱移轉至 Azure App Service](https://docs.microsoft.com/azure/app-service/manage-custom-dns-migrate-domain)。</span><span class="sxs-lookup"><span data-stu-id="6764a-255">To migrate a live site and its DNS domain name to App Service, see [Migrate an active DNS name to Azure App Service](https://docs.microsoft.com/azure/app-service/manage-custom-dns-migrate-domain).</span></span>

### <a name="prerequisites"></a><span data-ttu-id="6764a-256">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="6764a-256">Prerequisites</span></span>

<span data-ttu-id="6764a-257">若要完成此解決方案：</span><span class="sxs-lookup"><span data-stu-id="6764a-257">To complete this solution:</span></span>

- <span data-ttu-id="6764a-258">[建立 App Service 應用程式](https://docs.microsoft.com/azure/app-service/)，或使用您為另一個解決方案建立的應用程式。</span><span class="sxs-lookup"><span data-stu-id="6764a-258">[Create an App Service app](https://docs.microsoft.com/azure/app-service/), or use an app created for another  solution.</span></span>

- <span data-ttu-id="6764a-259">購買網域名稱，並確定您可存取網域提供者的 DNS 登錄。</span><span class="sxs-lookup"><span data-stu-id="6764a-259">Purchase a domain name and ensure access to the DNS registry for the domain provider.</span></span>

<span data-ttu-id="6764a-260">更新網域的 DNS 區域檔案。</span><span class="sxs-lookup"><span data-stu-id="6764a-260">Update the DNS zone file for the domain.</span></span> <span data-ttu-id="6764a-261">Azure AD 會驗證自訂網域名稱的擁有權。</span><span class="sxs-lookup"><span data-stu-id="6764a-261">Azure AD will verify ownership of the custom domain name.</span></span> <span data-ttu-id="6764a-262">對於 Azure 中的 Azure/Office 365/外部 DNS 記錄使用 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal)，或在[不同的 DNS 註冊機構](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)新增 DNS 項目。</span><span class="sxs-lookup"><span data-stu-id="6764a-262">Use [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) for Azure/Office 365/external DNS records within Azure, or add the DNS entry at [a different DNS registrar](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).</span></span>

- <span data-ttu-id="6764a-263">向公用註冊機構註冊自訂網域。</span><span class="sxs-lookup"><span data-stu-id="6764a-263">Register a custom domain with a public registrar.</span></span>

- <span data-ttu-id="6764a-264">登入網域的網域名稱註冊機構。</span><span class="sxs-lookup"><span data-stu-id="6764a-264">Sign in to the domain name registrar for the domain.</span></span> <span data-ttu-id="6764a-265">(已核准的系統管理員可能需要進行 DNS 更新。)</span><span class="sxs-lookup"><span data-stu-id="6764a-265">(An approved admin may be required to make DNS updates.)</span></span>

- <span data-ttu-id="6764a-266">透過新增 Azure AD 提供的 DNS 項目來更新網域的 DNS 區域檔案。</span><span class="sxs-lookup"><span data-stu-id="6764a-266">Update the DNS zone file for the domain by adding the DNS entry provided by Azure AD.</span></span>

<span data-ttu-id="6764a-267">例如，若要對 northwindcloud.com 和 www\.northwindcloud.com 新增 DNS 項目，請設定 northwindcloud.com 根網域的 DNS 設定。</span><span class="sxs-lookup"><span data-stu-id="6764a-267">For example, to add DNS entries for northwindcloud.com and www\.northwindcloud.com, configure DNS settings for the northwindcloud.com root domain.</span></span>

> [!Note]  
> <span data-ttu-id="6764a-268">網域名稱可使用 [Azure 入口網站](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain)來購買。</span><span class="sxs-lookup"><span data-stu-id="6764a-268">A domain name may be purchased using the [Azure portal](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain).</span></span> <span data-ttu-id="6764a-269">若要將自訂 DNS 名稱對應至 Web 應用程式，Web 應用程式的 [App Service 方案](https://azure.microsoft.com/pricing/details/app-service/)必須是付費層 (**共用**、**基本**、**標準**或**進階**)。</span><span class="sxs-lookup"><span data-stu-id="6764a-269">To map a custom DNS name to a web app, the web app's [App Service plan](https://azure.microsoft.com/pricing/details/app-service/) must be a paid tier (**Shared**, **Basic**, **Standard**, or **Premium**).</span></span>

### <a name="create-and-map-cname-and-a-records"></a><span data-ttu-id="6764a-270">建立和對應 CNAME 與 A 記錄</span><span class="sxs-lookup"><span data-stu-id="6764a-270">Create and map CNAME and A records</span></span>

#### <a name="access-dns-records-with-domain-provider"></a><span data-ttu-id="6764a-271">存取網域提供者中的 DNS 記錄</span><span class="sxs-lookup"><span data-stu-id="6764a-271">Access DNS records with domain provider</span></span>

> [!Note]  
>  <span data-ttu-id="6764a-272">請使用 Azure DNS 來設定 Azure Web Apps 的自訂 DNS 名稱。</span><span class="sxs-lookup"><span data-stu-id="6764a-272">Use Azure DNS to configure a custom DNS name for Azure Web Apps.</span></span> <span data-ttu-id="6764a-273">如需詳細資訊，請參閱[使用 Azure DNS 為 Azure 服務提供自訂網域設定](https://docs.microsoft.com/azure/dns/dns-custom-domain)。</span><span class="sxs-lookup"><span data-stu-id="6764a-273">For more information, see [Use Azure DNS to provide custom domain settings for an Azure service](https://docs.microsoft.com/azure/dns/dns-custom-domain).</span></span>

1. <span data-ttu-id="6764a-274">登入主要網域提供者的網站。</span><span class="sxs-lookup"><span data-stu-id="6764a-274">Sign in to the website of the main provider.</span></span>

2. <span data-ttu-id="6764a-275">尋找管理 DNS 記錄的頁面。</span><span class="sxs-lookup"><span data-stu-id="6764a-275">Find the page for managing DNS records.</span></span> <span data-ttu-id="6764a-276">每個網域提供者有自己的 DNS 記錄介面。</span><span class="sxs-lookup"><span data-stu-id="6764a-276">Every domain provider has its own DNS records interface.</span></span> <span data-ttu-id="6764a-277">在網站中尋找標示為 [網域名稱]  、[DNS]  或 [名稱伺服器管理]  的區域。</span><span class="sxs-lookup"><span data-stu-id="6764a-277">Look for areas of the site labeled **Domain Name**, **DNS**, or **Name Server Management**.</span></span>

<span data-ttu-id="6764a-278">您可以在 [我的網域]  中檢視 DNS 記錄頁面。</span><span class="sxs-lookup"><span data-stu-id="6764a-278">DNS records page can be viewed in **My domains**.</span></span> <span data-ttu-id="6764a-279">尋找名為 [區域檔案]  、[DNS 記錄]  或 [進階設定]  的連結。</span><span class="sxs-lookup"><span data-stu-id="6764a-279">Find the link named **Zone file**, **DNS Records**, or **Advanced configuration**.</span></span>

<span data-ttu-id="6764a-280">下列螢幕擷取畫面是 DNS 記錄頁面的範例：</span><span class="sxs-lookup"><span data-stu-id="6764a-280">The following screenshot is an example of a DNS records page:</span></span>

![DNS 記錄頁面範例](media/solution-deployment-guide-geo-distributed/image28.png)

1. <span data-ttu-id="6764a-282">在 [網域名稱註冊機構] 中，選取 [新增或建立]  以建立記錄。</span><span class="sxs-lookup"><span data-stu-id="6764a-282">In Domain Name Registrar, select **Add or Create** to create a record.</span></span> <span data-ttu-id="6764a-283">某些提供者有不同的連結來新增其他記錄類型。</span><span class="sxs-lookup"><span data-stu-id="6764a-283">Some providers have different links to add different record types.</span></span> <span data-ttu-id="6764a-284">請參閱提供者的文件。</span><span class="sxs-lookup"><span data-stu-id="6764a-284">Consult the provider's documentation.</span></span>

2. <span data-ttu-id="6764a-285">新增 CNAME 記錄以將子網域對應至應用程式的預設主機名稱。</span><span class="sxs-lookup"><span data-stu-id="6764a-285">Add a CNAME record to map a subdomain to the app's default hostname.</span></span>

   <span data-ttu-id="6764a-286">在 www\.northwindcloud.com 網域範例中，請新增將名稱對應至 `<app_name>.azurewebsites.net` 的 CNAME 記錄。</span><span class="sxs-lookup"><span data-stu-id="6764a-286">For the www\.northwindcloud.com domain example, add a CNAME record that maps the name to `<app_name>.azurewebsites.net`.</span></span>

<span data-ttu-id="6764a-287">新增 CNAME 之後，DNS 記錄分頁看起來如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="6764a-287">After adding the CNAME, the DNS records page looks like the following example:</span></span>

![入口網站瀏覽至 Azure 應用程式](media/solution-deployment-guide-geo-distributed/image29.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a><span data-ttu-id="6764a-289">在 Azure 中啟用 CNAME 記錄對應</span><span class="sxs-lookup"><span data-stu-id="6764a-289">Enable the CNAME record mapping in Azure</span></span>

1. <span data-ttu-id="6764a-290">在新的索引標籤中，登入 Azure 入口網站。</span><span class="sxs-lookup"><span data-stu-id="6764a-290">In a new tab, sign in to the Azure portal.</span></span>

2. <span data-ttu-id="6764a-291">移至 [App Service]。</span><span class="sxs-lookup"><span data-stu-id="6764a-291">Go to App Services.</span></span>

3. <span data-ttu-id="6764a-292">選取 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="6764a-292">Select web app.</span></span>

4. <span data-ttu-id="6764a-293">在 Azure 入口網站之應用程式分頁的左側導覽中，選取 [自訂網域]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-293">In the left navigation of the app page in the Azure portal, select **Custom domains**.</span></span>

5. <span data-ttu-id="6764a-294">選取 [新增主機名稱]  旁的 **+** 圖示。</span><span class="sxs-lookup"><span data-stu-id="6764a-294">Select the **+** icon next to **Add hostname**.</span></span>

6. <span data-ttu-id="6764a-295">輸入完整網域名稱，例如 `www.northwindcloud.com`。</span><span class="sxs-lookup"><span data-stu-id="6764a-295">Type the fully qualified domain name, like `www.northwindcloud.com`.</span></span>

7. <span data-ttu-id="6764a-296">選取 [驗證]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-296">Select **Validate**.</span></span>

8. <span data-ttu-id="6764a-297">如有指示，請再將其他類型的記錄 (`A` 或 `TXT`) 新增至網域名稱註冊機構 DNS 記錄。</span><span class="sxs-lookup"><span data-stu-id="6764a-297">If indicated, add additional records of other types (`A` or `TXT`) to the domain name registrars DNS records.</span></span> <span data-ttu-id="6764a-298">Azure 會提供這些記錄的類型與值：</span><span class="sxs-lookup"><span data-stu-id="6764a-298">Azure will provide the values and types of these records:</span></span>

   <span data-ttu-id="6764a-299">a.</span><span class="sxs-lookup"><span data-stu-id="6764a-299">a.</span></span>  <span data-ttu-id="6764a-300">**A** 記錄對應至應用程式的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="6764a-300">An **A** record to map to the app's IP address.</span></span>

   <span data-ttu-id="6764a-301">b.</span><span class="sxs-lookup"><span data-stu-id="6764a-301">b.</span></span>  <span data-ttu-id="6764a-302">**TXT** 記錄對應至應用程式的預設主機名稱 `<app_name>.azurewebsites.net`。</span><span class="sxs-lookup"><span data-stu-id="6764a-302">A **TXT** record to map to the app's default hostname `<app_name>.azurewebsites.net`.</span></span> <span data-ttu-id="6764a-303">App Service 只會在設定時使用此記錄，以確認自訂網域擁有權。</span><span class="sxs-lookup"><span data-stu-id="6764a-303">App Service uses this record only at configuration time to verify custom domain ownership.</span></span> <span data-ttu-id="6764a-304">確認之後，請刪除 TXT 記錄。</span><span class="sxs-lookup"><span data-stu-id="6764a-304">After verification, delete the TXT record.</span></span>

9. <span data-ttu-id="6764a-305">在網域註冊機構索引標籤中完成這項工作並重新驗證，直到 [新增主機名稱]  按鈕啟動為止。</span><span class="sxs-lookup"><span data-stu-id="6764a-305">Complete this task in the domain registrar tab and revalidate until the **Add hostname** button is activated.</span></span>

10. <span data-ttu-id="6764a-306">確定 [主機名稱記錄類型]  設為 **CNAME** (www.example.com 或任何子網域)。</span><span class="sxs-lookup"><span data-stu-id="6764a-306">Make sure that **Hostname record type** is set to **CNAME** (www.example.com or any subdomain).</span></span>

11. <span data-ttu-id="6764a-307">選取 [新增主機名稱]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-307">Select **Add hostname**.</span></span>

12. <span data-ttu-id="6764a-308">輸入完整網域名稱，例如 `northwindcloud.com`。</span><span class="sxs-lookup"><span data-stu-id="6764a-308">Type the fully qualified domain name, like `northwindcloud.com`.</span></span>

13. <span data-ttu-id="6764a-309">選取 [驗證]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-309">Select **Validate**.</span></span> <span data-ttu-id="6764a-310">[新增]  已啟動。</span><span class="sxs-lookup"><span data-stu-id="6764a-310">The **Add** is activated.</span></span>

14. <span data-ttu-id="6764a-311">確定 [主機名稱記錄類型]  設為 **A 記錄** (example.com)。</span><span class="sxs-lookup"><span data-stu-id="6764a-311">Make sure that **Hostname record type** is set to **A record** (example.com).</span></span>

15. <span data-ttu-id="6764a-312">**新增主機名稱**。</span><span class="sxs-lookup"><span data-stu-id="6764a-312">**Add hostname**.</span></span>

    <span data-ttu-id="6764a-313">可能需要一些時間，新的主機名稱才會反映在應用程式的 [自訂網域]  頁面中。</span><span class="sxs-lookup"><span data-stu-id="6764a-313">It might take some time for the new hostnames to be reflected in the app's **Custom domains** page.</span></span> <span data-ttu-id="6764a-314">嘗試重新整理瀏覽器以更新資料。</span><span class="sxs-lookup"><span data-stu-id="6764a-314">Try refreshing the browser to update the data.</span></span>
  
    ![自訂網域](media/solution-deployment-guide-geo-distributed/image31.png) 
  
    <span data-ttu-id="6764a-316">如果發生錯誤，頁面底部會出現驗證錯誤通知。</span><span class="sxs-lookup"><span data-stu-id="6764a-316">If there's an error, a verification error notification will appear at the bottom of the page.</span></span> ![網域驗證錯誤](media/solution-deployment-guide-geo-distributed/image32.png)

> [!Note]  
>  <span data-ttu-id="6764a-318">您可以重複執行上述步驟，以對應萬用字元網域 (\*.northwindcloud.com)。</span><span class="sxs-lookup"><span data-stu-id="6764a-318">The above steps may be repeated to map a wildcard domain (\*.northwindcloud.com).</span></span> <span data-ttu-id="6764a-319">如此即可直接將其他子網域新增至此應用程式服務，而無須為每個子網域建立個別的 CNAME 記錄。</span><span class="sxs-lookup"><span data-stu-id="6764a-319">This allows the addition of any additional subdomains to this app service without having to create a separate CNAME record for each one.</span></span> <span data-ttu-id="6764a-320">請依照註冊機構的指示進行此設定。</span><span class="sxs-lookup"><span data-stu-id="6764a-320">Follow the registrar instructions to configure this setting.</span></span>

#### <a name="test-in-a-browser"></a><span data-ttu-id="6764a-321">在瀏覽器中測試</span><span class="sxs-lookup"><span data-stu-id="6764a-321">Test in a browser</span></span>

<span data-ttu-id="6764a-322">瀏覽至稍早設定的 DNS 名稱 (例如 `northwindcloud.com` 或 `www.northwindcloud.com`)。</span><span class="sxs-lookup"><span data-stu-id="6764a-322">Browse to the DNS name(s) configured earlier (for example, `northwindcloud.com` or `www.northwindcloud.com`).</span></span>

## <a name="part-3-bind-a-custom-ssl-cert"></a><span data-ttu-id="6764a-323">第 3 部分：繫結自訂 SSL 憑證</span><span class="sxs-lookup"><span data-stu-id="6764a-323">Part 3: Bind a custom SSL cert</span></span>

<span data-ttu-id="6764a-324">在此部分中，我們會：</span><span class="sxs-lookup"><span data-stu-id="6764a-324">In this part, we will:</span></span>

> [!div class="checklist"]
> - <span data-ttu-id="6764a-325">將自訂 SSL 憑證繫結至 App Service。</span><span class="sxs-lookup"><span data-stu-id="6764a-325">Bind the custom SSL certificate to App Service.</span></span>
> - <span data-ttu-id="6764a-326">為應用程式強制使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="6764a-326">Enforce HTTPS for the app.</span></span>
> - <span data-ttu-id="6764a-327">使用指令碼來自動繫結 SSL 憑證。</span><span class="sxs-lookup"><span data-stu-id="6764a-327">Automate SSL certificate binding with scripts.</span></span>

> [!Note]  
> <span data-ttu-id="6764a-328">如有需要，請在 Azure 入口網站中取得客戶的 SSL 憑證，並將其繫結至 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="6764a-328">If needed, obtain a customer SSL certificate in the Azure portal and bind it to the web app.</span></span> <span data-ttu-id="6764a-329">如需詳細資訊，請參閱 [App Service 憑證教學課程](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site)。</span><span class="sxs-lookup"><span data-stu-id="6764a-329">For more information, see the [App Service Certificates tutorial](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site).</span></span>

### <a name="prerequisites"></a><span data-ttu-id="6764a-330">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="6764a-330">Prerequisites</span></span>

<span data-ttu-id="6764a-331">若要完成此解決方案：</span><span class="sxs-lookup"><span data-stu-id="6764a-331">To complete this  solution:</span></span>

- [<span data-ttu-id="6764a-332">建立 App Service 應用程式。</span><span class="sxs-lookup"><span data-stu-id="6764a-332">Create an App Service app.</span></span>](https://docs.microsoft.com/azure/app-service/)
- [<span data-ttu-id="6764a-333">將自訂 DNS 名稱對應至 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="6764a-333">Map a custom DNS name to your web app.</span></span>](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)
- <span data-ttu-id="6764a-334">取得受信任憑證授權單位所核發的 SSL 憑證，並使用金鑰簽署要求。</span><span class="sxs-lookup"><span data-stu-id="6764a-334">Acquire an SSL certificate from a trusted certificate authority and use the key to sign the request.</span></span>

### <a name="requirements-for-your-ssl-certificate"></a><span data-ttu-id="6764a-335">SSL 憑證的需求</span><span class="sxs-lookup"><span data-stu-id="6764a-335">Requirements for your SSL certificate</span></span>

<span data-ttu-id="6764a-336">若要在 App Service 中使用憑證，憑證必須符合以下所有需求︰</span><span class="sxs-lookup"><span data-stu-id="6764a-336">To use a certificate in App Service, the certificate must meet all the following requirements:</span></span>

- <span data-ttu-id="6764a-337">由受信任的憑證授權單位簽署。</span><span class="sxs-lookup"><span data-stu-id="6764a-337">Signed by a trusted certificate authority.</span></span>

- <span data-ttu-id="6764a-338">以受密碼保護的 PFX 檔案形式匯出。</span><span class="sxs-lookup"><span data-stu-id="6764a-338">Exported as a password-protected PFX file.</span></span>

- <span data-ttu-id="6764a-339">包含長度至少為 2048 位元的私密金鑰。</span><span class="sxs-lookup"><span data-stu-id="6764a-339">Contains private key at least 2048 bits long.</span></span>

- <span data-ttu-id="6764a-340">包含憑證鏈結中的所有中繼憑證。</span><span class="sxs-lookup"><span data-stu-id="6764a-340">Contains all intermediate certificates in the certificate chain.</span></span>

> [!Note]  
> <span data-ttu-id="6764a-341">**橢圓曲線密碼編譯 (ECC) 憑證**可與 App Service 搭配使用，但不在本指南的討論範圍內。</span><span class="sxs-lookup"><span data-stu-id="6764a-341">**Elliptic Curve Cryptography (ECC) certificates** work with App Service but aren't included in this guide.</span></span> <span data-ttu-id="6764a-342">如需建立 ECC 憑證方面的協助，請洽詢憑證授權單位。</span><span class="sxs-lookup"><span data-stu-id="6764a-342">Consult a certificate authority for assistance in creating ECC certificates.</span></span>

#### <a name="prepare-the-web-app"></a><span data-ttu-id="6764a-343">準備 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="6764a-343">Prepare the web app</span></span>

<span data-ttu-id="6764a-344">若要將自訂 SSL 憑證繫結至 Web 應用程式，[App Service 方案](https://azure.microsoft.com/pricing/details/app-service/)必須為**基本**、**標準**或**進階**層。</span><span class="sxs-lookup"><span data-stu-id="6764a-344">To bind a custom SSL certificate to the web app, the [App Service plan](https://azure.microsoft.com/pricing/details/app-service/) must be in the **Basic**, **Standard**, or **Premium** tier.</span></span>

#### <a name="sign-in-to-azure"></a><span data-ttu-id="6764a-345">登入 Azure</span><span class="sxs-lookup"><span data-stu-id="6764a-345">Sign in to Azure</span></span>

1. <span data-ttu-id="6764a-346">開啟 [Azure 入口網站](https://portal.azure.com/)，然後移至 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="6764a-346">Open the [Azure portal](https://portal.azure.com/) and go to the web app.</span></span>

2. <span data-ttu-id="6764a-347">從左側功能表中選取 [App Service]  ，然後選取 Web 應用程式名稱。</span><span class="sxs-lookup"><span data-stu-id="6764a-347">From the left menu, select **App Services**, and then select the web app name.</span></span>

![在 Azure 入口網站中選取 Web 應用程式](media/solution-deployment-guide-geo-distributed/image33.png)

#### <a name="check-the-pricing-tier"></a><span data-ttu-id="6764a-349">檢查定價層</span><span class="sxs-lookup"><span data-stu-id="6764a-349">Check the pricing tier</span></span>

1. <span data-ttu-id="6764a-350">在 Web 應用程式頁面的左側導覽中，捲動到 [設定]  區段，然後選取 [擴大 (App Service 方案)]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-350">In the left-hand navigation of the web app page, scroll to the **Settings** section and select **Scale up (App Service plan)**.</span></span>

    ![Web 應用程式中的相應增加功能表](media/solution-deployment-guide-geo-distributed/image34.png)

1. <span data-ttu-id="6764a-352">確定 Web 應用程式不在**免費**或**共用**服務層級中。</span><span class="sxs-lookup"><span data-stu-id="6764a-352">Ensure the web app isn't in the **Free** or **Shared** tier.</span></span> <span data-ttu-id="6764a-353">系統會以深藍色方塊醒目顯示 Web 應用程式目前的層。</span><span class="sxs-lookup"><span data-stu-id="6764a-353">The web app's current tier is highlighted in a dark blue box.</span></span>

    ![檢查 Web 應用程式中的定價層](media/solution-deployment-guide-geo-distributed/image35.png)

<span data-ttu-id="6764a-355">**免費**和**共用**服務層級中不支援自訂 SSL。</span><span class="sxs-lookup"><span data-stu-id="6764a-355">Custom SSL isn't supported in the **Free** or **Shared** tier.</span></span> <span data-ttu-id="6764a-356">若要升級，請依照下一節中的步驟操作，或**選擇您的定價層**頁面，然後跳至[上傳並繫結 SSL 憑證](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)。</span><span class="sxs-lookup"><span data-stu-id="6764a-356">To upscale, follow the steps in the next section or the **Choose your pricing tier** page and skip to [Upload and bind your SSL certificate](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

#### <a name="scale-up-your-app-service-plan"></a><span data-ttu-id="6764a-357">擴大您的 App Service 方案</span><span class="sxs-lookup"><span data-stu-id="6764a-357">Scale up your App Service plan</span></span>

1. <span data-ttu-id="6764a-358">選取**基本** **標準**或**高階**層的其中一個。</span><span class="sxs-lookup"><span data-stu-id="6764a-358">Select one of the **Basic**, **Standard**, or **Premium** tiers.</span></span>

2. <span data-ttu-id="6764a-359">選取 [選取]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-359">Select **Select**.</span></span>

![選擇 Web 應用程式的定價層](media/solution-deployment-guide-geo-distributed/image36.png)

<span data-ttu-id="6764a-361">顯示通知時，表示調整作業已完成。</span><span class="sxs-lookup"><span data-stu-id="6764a-361">The scale operation is complete when notification is displayed.</span></span>

![擴大通知](media/solution-deployment-guide-geo-distributed/image37.png)

#### <a name="bind-your-ssl-certificate-and-merge-intermediate-certificates"></a><span data-ttu-id="6764a-363">繫結您的 SSL 憑證，並合併中繼憑證</span><span class="sxs-lookup"><span data-stu-id="6764a-363">Bind your SSL certificate and merge intermediate certificates</span></span>

<span data-ttu-id="6764a-364">將多個憑證合併到鏈結中。</span><span class="sxs-lookup"><span data-stu-id="6764a-364">Merge multiple certificates in the chain.</span></span>

1. <span data-ttu-id="6764a-365">在文字編輯器中**開啟您收到的每個憑證**。</span><span class="sxs-lookup"><span data-stu-id="6764a-365">**Open each certificate** you received in a text editor.</span></span>

2. <span data-ttu-id="6764a-366">為合併的憑證建立一個檔案，並命名為 *mergedcertificate.crt*。</span><span class="sxs-lookup"><span data-stu-id="6764a-366">Create a file for the merged certificate called *mergedcertificate.crt*.</span></span> <span data-ttu-id="6764a-367">在文字編輯器中，將每個憑證的內容複製到這個檔案中。</span><span class="sxs-lookup"><span data-stu-id="6764a-367">In a text editor, copy the content of each certificate into this file.</span></span> <span data-ttu-id="6764a-368">憑證的順序應該遵循在憑證鏈結中的順序，開頭為您的憑證，以及結尾為根憑證。</span><span class="sxs-lookup"><span data-stu-id="6764a-368">The order of your certificates should follow the order in the certificate chain, beginning with your certificate and ending with the root certificate.</span></span> <span data-ttu-id="6764a-369">看起來會像下列範例：</span><span class="sxs-lookup"><span data-stu-id="6764a-369">It looks like the following example:</span></span>

    ```Text

    -----BEGIN CERTIFICATE-----

    <your entire Base64 encoded SSL certificate>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 1>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 2>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded root certificate>

    -----END CERTIFICATE-----
    ```

#### <a name="export-certificate-to-pfx"></a><span data-ttu-id="6764a-370">將憑證匯出為 PFX</span><span class="sxs-lookup"><span data-stu-id="6764a-370">Export certificate to PFX</span></span>

<span data-ttu-id="6764a-371">使用憑證所產生的私密金鑰，將合併的 SSL 憑證匯出。</span><span class="sxs-lookup"><span data-stu-id="6764a-371">Export the merged SSL certificate with the private key generated by the certificate.</span></span>

<span data-ttu-id="6764a-372">系統會透過 OpenSSL 建立私密金鑰檔案。</span><span class="sxs-lookup"><span data-stu-id="6764a-372">A private key file is created via OpenSSL.</span></span> <span data-ttu-id="6764a-373">若要將憑證匯出為 PFX，請執行下列命令，並將預留位置 `<private-key-file>` 和 `<merged-certificate-file>` 取代為私密金鑰路徑與合併的憑證檔案：</span><span class="sxs-lookup"><span data-stu-id="6764a-373">To export the certificate to PFX, run the following command and replace the placeholders `<private-key-file>` and `<merged-certificate-file>` with the private key path and the merged certificate file:</span></span>

```powershell
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

<span data-ttu-id="6764a-374">出現提示時，請定義您之後將 SSL 憑證上傳至 App Service 時所使用的密碼。</span><span class="sxs-lookup"><span data-stu-id="6764a-374">When prompted, define an export password for uploading your SSL certificate to App Service later.</span></span>

<span data-ttu-id="6764a-375">如果您使用 IIS 或 **Certreq.exe** 產生憑證要求，請將憑證安裝至本機電腦，然後[將憑證匯出為 PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx)。</span><span class="sxs-lookup"><span data-stu-id="6764a-375">When IIS or **Certreq.exe** are used to generate the certificate request, install the certificate to a local machine and then [export the certificate to PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).</span></span>

#### <a name="upload-the-ssl-certificate"></a><span data-ttu-id="6764a-376">上傳 SSL 憑證</span><span class="sxs-lookup"><span data-stu-id="6764a-376">Upload the SSL certificate</span></span>

1. <span data-ttu-id="6764a-377">在 Web 應用程式的左側導覽中選取 [SSL 設定]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-377">Select **SSL settings** in the left navigation of the web app.</span></span>

2. <span data-ttu-id="6764a-378">選取 [上傳憑證]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-378">Select **Upload Certificate**.</span></span>

3. <span data-ttu-id="6764a-379">在 [PFX 憑證檔案]  中，選取 PFX 檔案。</span><span class="sxs-lookup"><span data-stu-id="6764a-379">In **PFX Certificate File**, select PFX file.</span></span>

4. <span data-ttu-id="6764a-380">在 [憑證密碼]  中，輸入匯出 PFX 檔案時所建立的密碼。</span><span class="sxs-lookup"><span data-stu-id="6764a-380">In **Certificate password**, type the password created when exporting the PFX file.</span></span>

5. <span data-ttu-id="6764a-381">選取 [上傳]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-381">Select **Upload**.</span></span>

    ![上傳 SSL 憑證](media/solution-deployment-guide-geo-distributed/image38.png)

<span data-ttu-id="6764a-383">當 App Service 完成憑證上傳作業時，憑證會出現在 [SSL 設定]  頁面中。</span><span class="sxs-lookup"><span data-stu-id="6764a-383">When App Service finishes uploading the certificate, it appears in the **SSL settings** page.</span></span>

![SSL 設定](media/solution-deployment-guide-geo-distributed/image39.png)

#### <a name="bind-your-ssl-certificate"></a><span data-ttu-id="6764a-385">繫結 SSL 憑證</span><span class="sxs-lookup"><span data-stu-id="6764a-385">Bind your SSL certificate</span></span>

1. <span data-ttu-id="6764a-386">在 [SSL 繫結]  區段中，選取 [新增繫結]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-386">In the **SSL bindings** section, select **Add binding**.</span></span>

    > [!Note]  
    >  <span data-ttu-id="6764a-387">如果憑證已上傳，但未出現在 [主機名稱]  下拉式清單的網域名稱中，請嘗試重新整理瀏覽器頁面。</span><span class="sxs-lookup"><span data-stu-id="6764a-387">If the certificate has been uploaded, but doesn't appear in domain name(s) in the **Hostname** dropdown, try refreshing the browser page.</span></span>

2. <span data-ttu-id="6764a-388">在 [新增 SSL 繫結]  頁面中，使用下拉式清單選取要保護的網域名稱，以及要使用的憑證。</span><span class="sxs-lookup"><span data-stu-id="6764a-388">In the **Add SSL Binding** page, use the drop downs to select the domain name to secure and the certificate to use.</span></span>

3. <span data-ttu-id="6764a-389">在 **SSL 類型**中，選擇使用 [**伺服器名稱指示 (SNI)** ](https://en.wikipedia.org/wiki/Server_Name_Indication)還是以 IP 為基礎的 SSL。</span><span class="sxs-lookup"><span data-stu-id="6764a-389">In **SSL Type**, select whether to use [**Server Name Indication (SNI)**](https://en.wikipedia.org/wiki/Server_Name_Indication) or IP-based SSL.</span></span>

    - <span data-ttu-id="6764a-390">**以 SNI 為基礎的 SSL**：可能會新增多個以 SNI 為基礎的 SSL 繫結。</span><span class="sxs-lookup"><span data-stu-id="6764a-390">**SNI-based SSL**: Multiple SNI-based SSL bindings may be added.</span></span> <span data-ttu-id="6764a-391">此選項可允許多個 SSL 憑證保護同一個 IP 位址上的多個網域。</span><span class="sxs-lookup"><span data-stu-id="6764a-391">This option allows multiple SSL certificates to secure multiple domains on the same IP address.</span></span> <span data-ttu-id="6764a-392">大多數現代化的瀏覽器 (包括 Internet Explorer、Chrome、Firefox 和 Opera) 都支援 SNI (可在[伺服器名稱指示](https://wikipedia.org/wiki/Server_Name_Indication)找到更完整的瀏覽器支援資訊)。</span><span class="sxs-lookup"><span data-stu-id="6764a-392">Most modern browsers (including Internet Explorer, Chrome, Firefox, and Opera) support SNI (find more comprehensive browser support information at [Server Name Indication](https://wikipedia.org/wiki/Server_Name_Indication)).</span></span>

    - <span data-ttu-id="6764a-393">**以 IP 為基礎的 SSL**：可能只會新增一個以 IP 為基礎的 SSL 繫結。</span><span class="sxs-lookup"><span data-stu-id="6764a-393">**IP-based SSL**: Only one IP-based SSL binding may be added.</span></span> <span data-ttu-id="6764a-394">此選項只允許一個 SSL 憑證保護專用的公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="6764a-394">This option allows only one SSL certificate to secure a dedicated public IP address.</span></span> <span data-ttu-id="6764a-395">若要保護多個網域，請全部使用相同的 SSL 憑證來保護它們。</span><span class="sxs-lookup"><span data-stu-id="6764a-395">To secure multiple domains, secure them all using the same SSL certificate.</span></span> <span data-ttu-id="6764a-396">以 IP 為基礎的 SSL 是 SSL 繫結的傳統選項。</span><span class="sxs-lookup"><span data-stu-id="6764a-396">IP-based SSL is the traditional option for SSL binding.</span></span>

4. <span data-ttu-id="6764a-397">選取 [新增繫結]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-397">Select **Add Binding**.</span></span>

    ![新增 SSL 繫結](media/solution-deployment-guide-geo-distributed/image40.png)

<span data-ttu-id="6764a-399">當 App Service 完成憑證的上傳時，憑證會出現在 [SSL 繫結]  區段中。</span><span class="sxs-lookup"><span data-stu-id="6764a-399">When App Service finishes uploading the certificate, it appears in the **SSL bindings** sections.</span></span>

![SSL 繫結已完成上傳](media/solution-deployment-guide-geo-distributed/image41.png)

#### <a name="remap-the-a-record-for-ip-ssl"></a><span data-ttu-id="6764a-401">將 IP SSL 的 A 記錄重新對應</span><span class="sxs-lookup"><span data-stu-id="6764a-401">Remap the A record for IP SSL</span></span>

<span data-ttu-id="6764a-402">如果不在 Web 應用程式中使用以 IP 為基礎的 SSL，請跳至[測試自訂網域的 HTTPS](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)。</span><span class="sxs-lookup"><span data-stu-id="6764a-402">If IP-based SSL isn't used in the web app, skip to [Test HTTPS for your custom domain](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

<span data-ttu-id="6764a-403">根據預設，Web 應用程式會使用共用的公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="6764a-403">By default, the web app uses a shared public IP address.</span></span> <span data-ttu-id="6764a-404">當憑證與以 IP 為基礎的 SSL 繫結時，App Service 會為 Web 應用程式建立新的專用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="6764a-404">When the certificate is bound with IP-based SSL, App Service creates a new and dedicated IP address for the web app.</span></span>

<span data-ttu-id="6764a-405">當 A 記錄對應至 Web 應用程式時，必須使用專用的 IP 位址更新網域登錄。</span><span class="sxs-lookup"><span data-stu-id="6764a-405">When an A record is mapped to the web app, the domain registry must be updated with the dedicated IP address.</span></span>

<span data-ttu-id="6764a-406">[自訂網域]  頁面會以新的專用 IP 位址進行更新。</span><span class="sxs-lookup"><span data-stu-id="6764a-406">The **Custom domain** page is updated with the new, dedicated IP address.</span></span> <span data-ttu-id="6764a-407">請複製此 [IP 位址](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)，然後將 [A 記錄](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)重新對應至這個新的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="6764a-407">Copy this [IP address](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain), then remap the [A record](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain) to this new IP address.</span></span>

#### <a name="test-https"></a><span data-ttu-id="6764a-408">測試 HTTPS</span><span class="sxs-lookup"><span data-stu-id="6764a-408">Test HTTPS</span></span>

<span data-ttu-id="6764a-409">在不同的瀏覽器中，移至 `https://<your.custom.domain>` 確認 Web 應用程式已提供。</span><span class="sxs-lookup"><span data-stu-id="6764a-409">In different browsers, go to `https://<your.custom.domain>` to ensure the web app is served.</span></span>

![瀏覽至 Web 應用程式](media/solution-deployment-guide-geo-distributed/image42.png)

> [!Note]  
> <span data-ttu-id="6764a-411">如果發生憑證驗證錯誤，這可能是自我簽署的憑證所致，或是在匯出至 PFX 檔案時遺漏了中繼憑證。</span><span class="sxs-lookup"><span data-stu-id="6764a-411">If certificate validation errors occur, a self-signed certificate may be the cause, or intermediate certificates may have been left off when exporting to the PFX file.</span></span>

#### <a name="enforce-https"></a><span data-ttu-id="6764a-412">強制使用 HTTPS</span><span class="sxs-lookup"><span data-stu-id="6764a-412">Enforce HTTPS</span></span>

<span data-ttu-id="6764a-413">根據預設，任何人皆可使用 HTTP 存取 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="6764a-413">By default, anyone can access the web app using HTTP.</span></span> <span data-ttu-id="6764a-414">所有 HTTP 要求都可重新都導向至 HTTPS 連接埠。</span><span class="sxs-lookup"><span data-stu-id="6764a-414">All HTTP requests to the HTTPS port may be redirected.</span></span>

<span data-ttu-id="6764a-415">在 [Web 應用程式] 頁面中，選取 [SSL 設定]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-415">In the web app page, select **SL settings**.</span></span> <span data-ttu-id="6764a-416">然後，在 [僅限 HTTPS]  中選取 [開啟]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-416">Then, in **HTTPS Only**, select **On**.</span></span>

![強制使用 HTTPS](media/solution-deployment-guide-geo-distributed/image43.png)

<span data-ttu-id="6764a-418">在作業完成後，移至指向應用程式的任何 HTTP URL。</span><span class="sxs-lookup"><span data-stu-id="6764a-418">When the operation is complete, go to any of the HTTP URLs that point to the app.</span></span> <span data-ttu-id="6764a-419">例如：</span><span class="sxs-lookup"><span data-stu-id="6764a-419">For example:</span></span>

- <span data-ttu-id="6764a-420">https://<app_name>.azurewebsites.net</span><span class="sxs-lookup"><span data-stu-id="6764a-420">https://<app_name>.azurewebsites.net</span></span>
- `https://northwindcloud.com`
- `https://www.northwindcloud.com`

#### <a name="enforce-tls-1112"></a><span data-ttu-id="6764a-421">強制使用 TLS 1.1/1.2</span><span class="sxs-lookup"><span data-stu-id="6764a-421">Enforce TLS 1.1/1.2</span></span>

<span data-ttu-id="6764a-422">應用程式依預設會允許 [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0，但業界標準 (例如 [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)) 已不再將其視為安全的版本。</span><span class="sxs-lookup"><span data-stu-id="6764a-422">The app allows [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 by default, which is no longer considered secure by industry standards (like [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)).</span></span> <span data-ttu-id="6764a-423">若要強制使用更高的 TLS 版本，請遵循下列步驟：</span><span class="sxs-lookup"><span data-stu-id="6764a-423">To enforce higher TLS versions, follow these steps:</span></span>

1. <span data-ttu-id="6764a-424">在 Web 應用程式頁面的左側導覽中，選取 [SSL 設定]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-424">In the web app page, in the left navigation, select **SSL settings**.</span></span>

2. <span data-ttu-id="6764a-425">在 [TLS 版本]  中，選取最低的 TLS 版本。</span><span class="sxs-lookup"><span data-stu-id="6764a-425">In **TLS version**, select the minimum TLS version.</span></span>

    ![強制使用 TLS 1.1 或 1.2](media/solution-deployment-guide-geo-distributed/image44.png)

### <a name="create-a-traffic-manager-profile"></a><span data-ttu-id="6764a-427">建立流量管理員設定檔</span><span class="sxs-lookup"><span data-stu-id="6764a-427">Create a Traffic Manager profile</span></span>

1. <span data-ttu-id="6764a-428">選取 [建立資源]   > [網路]   > [流量管理員設定檔]   > [建立]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-428">Select **Create a resource** > **Networking** > **Traffic Manager profile** > **Create**.</span></span>

2. <span data-ttu-id="6764a-429">在 [建立流量管理員設定檔]  中，如下所示操作：</span><span class="sxs-lookup"><span data-stu-id="6764a-429">In the **Create Traffic Manager profile**, complete as follows:</span></span>

    1. <span data-ttu-id="6764a-430">在 [名稱]  中，提供設定檔的名稱。</span><span class="sxs-lookup"><span data-stu-id="6764a-430">In **Name**, provide a name for the profile.</span></span> <span data-ttu-id="6764a-431">此名稱在 trafficmanager.net 區域內必須是唯一的，而且會產生 DNS 名稱 trafficmanager.net，用以存取流量管理員設定檔。</span><span class="sxs-lookup"><span data-stu-id="6764a-431">This name needs to be unique within the traffic manager.net zone and results in the DNS name, trafficmanager.net, which is used to access the Traffic Manager profile.</span></span>

    2. <span data-ttu-id="6764a-432">在 [路由方法]  中，選取 [地理路由方法]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-432">In **Routing method**, select the **Geographic routing method**.</span></span>

    3. <span data-ttu-id="6764a-433">在 [訂用帳戶]  中，選取要用來建立此設定檔的訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="6764a-433">In **Subscription**, select the subscription under which to create this profile.</span></span>

    4. <span data-ttu-id="6764a-434">在 [資源群組]  中，建立新的資源群組來放置此設定檔。</span><span class="sxs-lookup"><span data-stu-id="6764a-434">In **Resource Group**, create a new resource group to place this profile under.</span></span>

    5. <span data-ttu-id="6764a-435">在 [資源群組位置]  中，選取資源群組的位置。</span><span class="sxs-lookup"><span data-stu-id="6764a-435">In **Resource group location**, select the location of the resource group.</span></span> <span data-ttu-id="6764a-436">這項設定是指資源群組的位置，完全不影響將部署到全球的流量管理員設定檔。</span><span class="sxs-lookup"><span data-stu-id="6764a-436">This setting refers to the location of the resource group and has no impact on the Traffic Manager profile deployed globally.</span></span>

    6. <span data-ttu-id="6764a-437">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-437">Select **Create**.</span></span>

    7. <span data-ttu-id="6764a-438">當流量管理員設定檔的全球部署完成時，它會列為個別資源群組的其中一個資源。</span><span class="sxs-lookup"><span data-stu-id="6764a-438">When the global deployment of the Traffic Manager profile is complete, it's listed in the respective resource group as one of the resources.</span></span>

        ![建立流量管理原設定檔中的資源群組](media/solution-deployment-guide-geo-distributed/image45.png)

### <a name="add-traffic-manager-endpoints"></a><span data-ttu-id="6764a-440">新增流量管理員端點</span><span class="sxs-lookup"><span data-stu-id="6764a-440">Add Traffic Manager endpoints</span></span>

1. <span data-ttu-id="6764a-441">在入口網站的搜尋列中，搜尋您在上一節建立的**流量管理員設定檔**名稱，然後在顯示的結果中選取流量管理員設定檔。</span><span class="sxs-lookup"><span data-stu-id="6764a-441">In the portal search bar, search for the **Traffic Manager profile** name created in the preceding section and select the traffic manager profile in the displayed results.</span></span>

2. <span data-ttu-id="6764a-442">在 [流量管理員設定檔]  的 [設定]  區段中，選取 [端點]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-442">In **Traffic Manager profile**, in the **Settings** section, select **Endpoints**.</span></span>

3. <span data-ttu-id="6764a-443">選取 [新增]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-443">Select **Add**.</span></span>

4. <span data-ttu-id="6764a-444">新增 Azure Stack Hub 端點。</span><span class="sxs-lookup"><span data-stu-id="6764a-444">Adding the Azure Stack Hub Endpoint.</span></span>

5. <span data-ttu-id="6764a-445">針對 [類型]  ，選取 [外部端點]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-445">For **Type**, select **External endpoint**.</span></span>

6. <span data-ttu-id="6764a-446">提供此端點的 [名稱]  ，最好是 Azure Stack Hub 的名稱。</span><span class="sxs-lookup"><span data-stu-id="6764a-446">Provide a **Name** for this endpoint, ideally the name of the Azure Stack Hub.</span></span>

7. <span data-ttu-id="6764a-447">針對完整網域名稱 (**FQDN**)，請使用 Azure Stack Hub Web 應用程式的外部 URL。</span><span class="sxs-lookup"><span data-stu-id="6764a-447">For fully qualified domain name (**FQDN**), use the external URL for the Azure Stack Hub Web App.</span></span>

8. <span data-ttu-id="6764a-448">在 [地區對應] 下方，選取資源所在的區域/洲別。</span><span class="sxs-lookup"><span data-stu-id="6764a-448">Under Geo-mapping, select a region/continent where the resource is located.</span></span> <span data-ttu-id="6764a-449">例如**歐洲**。</span><span class="sxs-lookup"><span data-stu-id="6764a-449">For example, **Europe.**</span></span>

9. <span data-ttu-id="6764a-450">在顯示的 [國家/區域] 下拉式清單下方，選取將套用到此端點的國家/地區。</span><span class="sxs-lookup"><span data-stu-id="6764a-450">Under the Country/Region drop-down that appears, select the country that applies to this endpoint.</span></span> <span data-ttu-id="6764a-451">例如**德國**。</span><span class="sxs-lookup"><span data-stu-id="6764a-451">For example, **Germany**.</span></span>

10. <span data-ttu-id="6764a-452">維持不勾選 [新增為已停用]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-452">Keep **Add as disabled** unchecked.</span></span>

11. <span data-ttu-id="6764a-453">選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-453">Select **OK**.</span></span>

12. <span data-ttu-id="6764a-454">新增 Azure 端點：</span><span class="sxs-lookup"><span data-stu-id="6764a-454">Adding the Azure Endpoint:</span></span>

    1. <span data-ttu-id="6764a-455">針對 [類型]  ，選取 [Azure 端點]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-455">For **Type**, select **Azure endpoint**.</span></span>

    2. <span data-ttu-id="6764a-456">提供端點**名稱**。</span><span class="sxs-lookup"><span data-stu-id="6764a-456">Provide a **Name** for the endpoint.</span></span>

    3. <span data-ttu-id="6764a-457">針對 [目標資源類型]  ，選取 [App Service]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-457">For **Target resource type**, select **App Service**.</span></span>

    4. <span data-ttu-id="6764a-458">針對 [目標資源]  ，選取 [選擇應用程式服務]  ，以顯示相同訂用帳戶下的 Web Apps 清單。</span><span class="sxs-lookup"><span data-stu-id="6764a-458">For **Target resource**, select **Choose an app service** to show the listing of the Web Apps under the same subscription.</span></span> <span data-ttu-id="6764a-459">在 [資源]  中，挑選要作為第一個端點的應用程式服務。</span><span class="sxs-lookup"><span data-stu-id="6764a-459">In **Resource**, pick the App service used as the first endpoint.</span></span>

13. <span data-ttu-id="6764a-460">在 [地區對應] 下方，選取資源所在的區域/洲別。</span><span class="sxs-lookup"><span data-stu-id="6764a-460">Under Geo-mapping, select a region/continent where the resource is located.</span></span> <span data-ttu-id="6764a-461">例如**北美洲/中美洲/加勒比海**。</span><span class="sxs-lookup"><span data-stu-id="6764a-461">For example, **North America/Central America/Caribbean.**</span></span>

14. <span data-ttu-id="6764a-462">在顯示的 [國家/區域] 下拉式清單下方，將此位置保留為空白，以選取上述所有的區域群組。</span><span class="sxs-lookup"><span data-stu-id="6764a-462">Under the Country/Region drop-down that appears, leave this spot blank to select all of the above regional grouping.</span></span>

15. <span data-ttu-id="6764a-463">維持不勾選 [新增為已停用]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-463">Keep **Add as disabled** unchecked.</span></span>

16. <span data-ttu-id="6764a-464">選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="6764a-464">Select **OK**.</span></span>

    > [!Note]  
    >  <span data-ttu-id="6764a-465">建立至少一個地理範圍為 [全部 (全球)] 的端點，作為資源的預設端點。</span><span class="sxs-lookup"><span data-stu-id="6764a-465">Create at least one endpoint with a geographic scope of All (World) to serve as the default endpoint for the resource.</span></span>

17. <span data-ttu-id="6764a-466">這兩個端點新增完畢後，它們會顯示在 [流量管理員設定檔]  中，而且監視狀態是**線上**。</span><span class="sxs-lookup"><span data-stu-id="6764a-466">When the addition of both endpoints is complete, they're displayed in **Traffic Manager profile** along with their monitoring status as **Online**.</span></span>

    ![流量管理員設定檔端點狀態](media/solution-deployment-guide-geo-distributed/image46.png)

#### <a name="global-enterprise-relies-on-azure-geo-distribution-capabilities"></a><span data-ttu-id="6764a-468">國際企業需依賴 Azure 異地分散功能</span><span class="sxs-lookup"><span data-stu-id="6764a-468">Global Enterprise relies on Azure geo-distribution capabilities</span></span>

<span data-ttu-id="6764a-469">透過 Azure 流量管理員和地理位置專屬端點導向資料流量，全球化企業得以遵循地區法規，並且維持資料的合規性和安全性，這是對於本地企業的發展和遠端企業地點而言都至關重要。</span><span class="sxs-lookup"><span data-stu-id="6764a-469">Directing data traffic via Azure Traffic Manager and geography-specific endpoints enables global enterprises to adhere to regional regulations and keep data compliant and secure, which is crucial to the success of local and remote business locations.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6764a-470">後續步驟</span><span class="sxs-lookup"><span data-stu-id="6764a-470">Next steps</span></span>

- <span data-ttu-id="6764a-471">若要深入了解 Azure 雲端模式，請參閱[雲端設計模式](https://docs.microsoft.com/azure/architecture/patterns)。</span><span class="sxs-lookup"><span data-stu-id="6764a-471">To learn more about Azure Cloud Patterns, see [Cloud Design Patterns](https://docs.microsoft.com/azure/architecture/patterns).</span></span>