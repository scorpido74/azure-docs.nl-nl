---
title: 'Quickstart: Create a search service in the portal'
titleSuffix: Azure Cognitive Search
description: In this portal quickstart, learn how to set up an Azure Cognitive Search resource in the Azure portal. Resourcegroepen, regio's en de SKU of prijscategorie kiezen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 79554429ddc9516eb2fbe9aa7b0e75c78fb17662
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406676"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Quickstart: Create an Azure Cognitive Search service in the portal

Azure Cognitive Search is a standalone resource used to plug in a search experience in custom apps. Although Azure Cognitive Search integrates easily with other Azure services, you can also use it as a standalone component, or integrate it with apps on network servers, or with software running on other cloud platforms.

In this article, learn how to create a resource in the [Azure portal](https://portal.azure.com/).

[![GIF-animatie](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Hebt u liever PowerShell? Gebruik de [servicesjabloon](https://azure.microsoft.com/resources/templates/101-azure-search-create/) van Azure Resource Manager. For help with getting started, see [Manage Azure Cognitive Search with PowerShell](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>Abonneren (gratis of betaald)

[Maak een gratis Azure-account](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) en gebruik gratis tegoed om betaalde Azure-services uit te proberen. Nadat u het tegoed hebt opgemaakt, kunt u het account houden en de gratis Azure-services, zoals Websites, blijven gebruiken. Er worden nooit kosten in rekening gebracht bij uw creditcard tenzij u de instellingen expliciet wijzigt en aangeeft dat u wilt betalen.

U kunt ook [voordelen voor MSDN-abonnees activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Als u een MSDN-abonnement hebt, ontvangt u elke maand tegoeden die u voor betaalde Azure-services kunt gebruiken. 

## <a name="find-azure-cognitive-search"></a>Find Azure Cognitive Search

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Klik op het plusteken (+ Resource maken) in de linkerbovenhoek.
3. Use the search bar to find "Azure Cognitive Search" or navigate to the resource through **Web** > **Azure Cognitive Search**.

![Create a resource in the portal](./media/search-create-service-portal/find-search3.png "Een resource maken in de portal")

## <a name="choose-a-subscription"></a>Choose a subscription

Setting the subscription ID and resource group is your first step. Als u meer dan één abonnement hebt, moet u er één kiezen dat ook services voor gegevensopslag of File Storage-services biedt. Azure Cognitive Search can autodetect Azure Table and Blob storage, SQL Database, and Azure Cosmos DB for indexing via [*indexers*](search-indexer-overview.md), but only for services under the same subscription.

## <a name="set-a-resource-group"></a>Set a resource group

A resource group is required and is useful for managing resources all-up, including costs. A resource group can consist of one service, or multiple services used together. For example, if you are using Azure Cognitive Search to index an Azure Cosmos DB database, you could make both services part of the same resource group for management purposes. 

If you aren't combining resources into a single group, or if existing resource groups are filled with resources used in unrelated solutions, create a new resource group just for your Azure Cognitive Search resource. 

![Create a new resource group](./media/search-create-service-portal/new-resource-group.png "Een nieuwe resourcegroep maken")

Over time, you can track current and projected costs all-up (as shown in the screenshot) or scroll down to view charges for individual resources. The following screenshot shows the kind of cost information you can eventually expect to see when you combine multiple resources into one group.

![Manage costs at the resource group level](./media/search-create-service-portal/resource-group-cost-management.png "Manage costs at the resource group level")

> [!TIP]
> Resource groups simplify cleanup because deleting a group also deletes the services within it. Bij prototypeprojecten die gebruikmaken van meerdere services, wordt het opschonen na afloop van het project gemakkelijker als die services zich allemaal in dezelfde resourcegroep bevinden.

## <a name="name-the-service"></a>Name the service

In Instance Details, provide a service name in the **URL** field. The name is part of the URL endpoint against which API calls are issued: `https://your-service-name.search.windows.net`. Als u bijvoorbeeld wilt dat het eindpunt `https://myservice.search.windows.net` wordt, voert u `myservice` in.

Vereisten voor servicenaam:

* De naam moet uniek zijn binnen de naamruimte search.windows.net
* tussen de 2 en 60 tekens lang zijn
* Gebruik kleine letters, cijfers of streepjes (-)
* Vermijd streepjes (-) bij de eerste 2 tekens of als het allerlaatste teken
* Zorg ervoor dat er nergens twee opeenvolgende streepjes (--) staan

> [!TIP]
> If you think you'll be using multiple services, we recommend including the region (or location) in the service name as a naming convention. Services within the same region can exchange data at no charge, so if Azure Cognitive Search is in West US, and you have other services also in West US, a name like `mysearchservice-westus` can save you a trip to the properties page when deciding how to combine or attach resources.

## <a name="choose-a-location"></a>Choose a location

As an Azure service, Azure Cognitive Search can be hosted in datacenters around the world. The list of supported regions can be found in the [pricing page](https://azure.microsoft.com/pricing/details/search/). 

You can minimize or avoid bandwidth charges by choosing the same location for multiple services. For example, if you are indexing data provided by another Azure service (Azure storage, Azure Cosmos DB, Azure SQL Database), creating your Azure Cognitive Search service in the same region avoids bandwidth charges (there are no charges for outbound data when services are in the same region).

Additionally, if you are using AI enrichment, create your service in the same region as Cognitive Services. *Co-location of Azure Cognitive Search and Cognitive Services in the same region is a requirement for AI enrichment*.

> [!Note]
> Central India is currently unavailable for new services. For services already in Central India, you can scale up with no restrictions, and your service is fully supported in that region. The restriction on this region is temporary and limited to new services only. We will remove this note when the restriction no longer applies.

## <a name="choose-a-pricing-tier-sku"></a>Choose a pricing tier (SKU)

[Azure Cognitive Search is currently offered in multiple pricing tiers](https://azure.microsoft.com/pricing/details/search/): Free, Basic, or Standard. Elke categorie heeft eigen [capaciteiten en limieten](search-limits-quotas-capacity.md). Raadpleeg [Choose a pricing tier or SKU](search-sku-tier.md) (Een prijscategorie of SKU kiezen) voor hulp.

Basic and Standard are the most common choices for production workloads, but most customers start with the Free service. Key differences among tiers is partition size and speed, and limits on the number of objects you can create.

Remember that a pricing tier cannot be changed once the service is created. Als u een hogere of lagere categorie nodig hebt, moet u de service opnieuw maken.

## <a name="create-your-service"></a>Uw service maken

After you've provided the necessary inputs, go ahead and create the service. 

![Review and create the service](./media/search-create-service-portal/new-service3.png "Review and create the service")

Your service is deployed within minutes, which you can monitor through Azure notifications. Consider pinning the service to your dashboard for easy access in the future.

![Monitor and pin the service](./media/search-create-service-portal/monitor-notifications.png "Monitor and pin the service")

## <a name="get-a-key-and-url-endpoint"></a>Een sleutel en het URL-eindpunt ophalen

Unless you are using the portal, programmatic access to your new service requires that you provide the URL endpoint and an authentication api-key.

1. On the **Overview** page, locate and copy the URL endpoint on the right side of the page.

2. On the **Keys** page, copy either one of the admin keys (they are equivalent). API-beheersleutels voor beheerders zijn vereist voor het maken, bijwerken en verwijderen van objecten in uw service. In contrast, query keys provide read-access to index content.

   ![Service overview page with URL endpoint](./media/search-create-service-portal/get-url-key.png "URL endpoint and other service details")

Een eindpunt en sleutel zijn niet nodig voor op de portal gebaseerde taken. The portal is already linked to your Azure Cognitive Search resource with admin rights. For a portal walkthrough, start with [Quickstart: Create an Azure Cognitive Search index in the portal](search-get-started-portal.md).

## <a name="scale-your-service"></a>Uw service schalen

Nadat uw service is geconfigureerd, kunt u deze schalen, zodat de service aan uw behoeften voldoet. If you chose the Standard tier for your Azure Cognitive Search service, you can scale your service in two dimensions: replicas and partitions. Als u had gekozen voor de categorie Basic, had u alleen replica's kunnen toevoegen. Als u had gekozen voor de gratis service, had u niet kunnen schalen.

***Partities*** zorgen ervoor dat uw service meer documenten bewaart en meer documenten doorzoekt.

***Replica's*** zorgen ervoor dat uw services meer zoekquery's kunnen verwerken.

Als u resources toevoegt, wordt uw maandfactuur hoger. Met de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) krijgt u inzicht in het effect van het toevoegen van resources op uw facturering. Onthoud dat u resources kunt aanpassen aan de benodigde capaciteit. Zo kunt u eerst resources toevoegen voor het maken van een volledige eerste index en later het aantal resources weer verlagen tot een niveau dat geschikt is voor incrementele indexering.

> [!Important]
> Een service moet [twee replica's hebben voor een alleen-lezen-SLA en drie replica's voor een lezen/schrijven-SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Ga naar de pagina van uw zoekservice in Azure Portal.
2. Selecteer in het navigatiedeelvenster aan de linkerkant **Instellingen** > **Schalen**.
3. Gebruik de schuifbalk om resources van een bepaald type toe te voegen.

![Add capacity](./media/search-create-service-portal/settings-scale.png "Add capacity through replicas and partitions")

> [!Note]
> Per-partition storage and speed increases at higher tiers. For more information, see [capacity and limits](search-limits-quotas-capacity.md).

## <a name="when-to-add-a-second-service"></a>Wanneer u een tweede service moet toevoegen

De meeste klanten gebruiken maar één service die geconfigureerd is voor de categorie die de [juiste balans voor resources](search-sku-tier.md) biedt. Eén service kan meerdere indexen hosten, afhankelijk van de [maximale limieten van de door u geselecteerde categorie](search-capacity-planning.md), en alle indexen zijn van elkaar geïsoleerd. In Azure Cognitive Search, requests can only be directed to one index, minimizing the chance of accidental or intentional data retrieval from other indexes in the same service.

Hoewel de meeste klanten slechts één service gebruiken, kan serviceredundantie nodig zijn als de volgende operationele vereisten gelden:

* Herstel na noodgevallen (onderbreking datacentrum). Azure Cognitive Search does not provide instant failover in the event of an outage. Raadpleeg voor aanbevelingen en richtlijnen [Servicebeheer](search-manage.md).
* Uit uw onderzoek naar multitenancymodellering blijkt dat een ontwerp met meerdere services optimaal is. Raadpleeg voor meer informatie [Ontwerp voor multitenancy](search-modeling-multitenant-saas-applications.md).
* For globally deployed applications, you might require an instance of Azure Cognitive Search in multiple regions to minimize latency of your application’s international traffic.

> [!NOTE]
> In Azure Cognitive Search, you cannot segregate indexing and querying operations; thus, you would never create multiple services for segregated workloads. Een query wordt altijd uitgevoerd op de service waarin de index is gemaakt (u kunt niet een index in een service maken en vervolgens naar een andere service kopiëren).

Een tweede service is niet vereist voor hoge beschikbaarheid. Hoge beschikbaarheid voor query's wordt bereikt wanneer u twee of meer replica's in dezelfde service gebruikt. Replica updates are sequential, which means at least one is operational when a service update is rolled out. For more information about uptime, see [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Volgende stappen

After provisioning a service, you can continue in the portal to create your first index.

> [!div class="nextstepaction"]
> [Quickstart: Create an Azure Cognitive Search index in the portal](search-get-started-portal.md)
