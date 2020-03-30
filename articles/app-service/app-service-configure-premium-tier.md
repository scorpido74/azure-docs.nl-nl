---
title: PremiumV2-laag configureren
description: Lees hoe u beter presteren voor uw web-, mobiele en API-app in Azure App Service door te schalen naar de nieuwe Prijscategorie PremiumV2.
keywords: App Service, Azure App Service, schaal, schaalbaar, App Service-abonnement, kosten App Service
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 07/25/2018
ms.custom: seodec18
ms.openlocfilehash: f7f9f6d5617ad0f9be69c47ce514d395534fd892
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672214"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>PremiumV2-laag configureren voor Azure App Service

De nieuwe **PremiumV2-prijscategorie** biedt u snellere processors, SSD-opslag en verdubbelt de verhouding tussen geheugen en kern van de bestaande prijsniveaus. Met het prestatievoordeel u geld besparen door uw apps op minder exemplaren uit te voeren. In dit artikel leert u hoe u een app maakt in **premiumv2-laag** of een app opschaalt naar **premiumv2-laag.**

## <a name="prerequisites"></a>Vereisten

Als u een app wilt opschalen naar **PremiumV2,** moet u een Azure App Service-app hebben die wordt uitgevoerd in een prijscategorie die lager is dan **PremiumV2,** en moet de app worden uitgevoerd in een App Service-implementatie die PremiumV2 ondersteunt.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>PremiumV2 beschikbaarheid

De **PremiumV2-laag** is beschikbaar voor App Service op zowel _Windows_ als _Linux._

**PremiumV2** is beschikbaar in de meeste Azure-regio's. Voer de volgende opdracht Azure CLI uit in de [Azure Cloud Shell](../cloud-shell/overview.md)om te zien of deze beschikbaar is in uw regio:

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Een app maken in PremiumV2-laag

De prijscategorie van een App Service-app is gedefinieerd in het [App Service-abonnement](overview-hosting-plans.md) waarop deze wordt uitgevoerd. U zelf een App Service-abonnement maken of als onderdeel van het maken van apps.

Wanneer u het App Service-abonnement configureert in de <a href="https://portal.azure.com" target="_blank">Azure-portal,</a>selecteert u **De prijslaag**. 

Selecteer **Productie**en selecteer **Vervolgens P1V2,** **P2V2**of **P3V2**en klik op **Toepassen**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Als u **P1V2,** **P2V2**en **P3V2** niet als opties ziet of als de opties grijs zijn, is **PremiumV2** waarschijnlijk niet beschikbaar in de onderliggende App Service-implementatie die het App Service-abonnement bevat. Zie [Opschalen vanuit een niet-ondersteunde resourcegroep en regiocombinatie](#unsupported) voor meer informatie.

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Een bestaande app opschalen naar PremiumV2-laag

Voordat u een bestaande app schaalt naar **de PremiumV2-laag,** moet u ervoor zorgen dat **PremiumV2** beschikbaar is. Zie [PremiumV2 beschikbaarheid](#availability)voor informatie. Zie [Opschalen vanuit een niet-ondersteunde resourcegroep en regiocombinatie](#unsupported)als deze niet beschikbaar is.

Afhankelijk van uw hostingomgeving kan het opschalen extra stappen vereisen. 

Open de app-app-pagina in de <a href="https://portal.azure.com" target="_blank">Azure-portal.</a>

Selecteer op de linkernavigatie van de app-app-pagina van app-app **opschalen (App Service-abonnement).**

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Selecteer **Productie**en selecteer **Vervolgens P1V2,** **P2V2**of **P3V2**en klik op **Toepassen**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Als uw bewerking is voltooid, geeft de overzichtspagina van uw app aan dat deze zich nu in een **PremiumV2-laag** bevindt.

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Als u een foutmelding krijgt

Sommige App-serviceplannen kunnen niet worden opgeschaald naar de PremiumV2-laag als de onderliggende App Service-implementatie geen Ondersteuning biedt voor PremiumV2.  Zie [Opschalen vanuit een niet-ondersteunde resourcegroep en regiocombinatie](#unsupported) voor meer informatie.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Opschalen vanuit een niet-ondersteunde resourcegroep en regiocombinatie

Als uw app wordt uitgevoerd in een App Service-implementatie waarbij **PremiumV2** niet beschikbaar is of als uw app wordt uitgevoerd in een regio die momenteel geen **PremiumV2**ondersteunt, moet u uw app opnieuw implementeren om te profiteren van **PremiumV2.**  U hebt hiervoor twee opties:

- Maak een **nieuwe** resourcegroep en maak vervolgens een **nieuw** app- en App-serviceplan in de **nieuwe** resourcegroep, waarbij u de gewenste Azure-regio kiest tijdens het creatieproces.  U **moet** het **PremiumV2-abonnement** selecteren op het moment dat het nieuwe app-serviceplan wordt gemaakt.  Dit zorgt ervoor dat de combinatie van resourcegroep, App Service-abonnement en Azure-regio ertoe leidt dat het App Service-abonnement wordt gemaakt in een App Service-implementatie die **PremiumV2**ondersteunt.  Vervolgens implementeert u uw toepassingscode opnieuw in het nieuw gemaakte app- en app-serviceplan. Indien gewenst u vervolgens het App Service-abonnement van **PremiumV2** opschalen om kosten te besparen, en u in de toekomst nog steeds met succes weer opschalen met **PremiumV2.**
- Als uw app al in een bestaande **Premium-laag** wordt uitgevoerd, u uw app klonen met alle app-instellingen, verbindingstekenreeksen en implementatieconfiguratie in een nieuw app-serviceplan dat **PremiumV2**gebruikt.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    Op de pagina **Kloon-app** u een App Service-abonnement maken met **PremiumV2** in de gewenste regio en de app-instellingen en -configuratie opgeven die u wilt klonen.

## <a name="automate-with-scripts"></a>Automatiseren met scripts

U het maken van apps in de **PremiumV2-laag** automatiseren met scripts met behulp van Azure [CLI](/cli/azure/install-azure-cli) of [Azure PowerShell.](/powershell/azure/overview)

### <a name="azure-cli"></a>Azure-CLI

Met de volgende opdracht maakt u een App Service-abonnement in _P1V2_. Je het uitvoeren in de Cloud Shell. De opties `--sku` voor zijn P1V2, _P2V2_en _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Met de volgende opdracht maakt u een App Service-abonnement in _P1V2_. De opties `-WorkerSize` voor zijn _klein,_ _gemiddeld_en _groot_.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>Meer bronnen

[Een app omhoog schalen in Azure](manage-scale-up.md)  
[Het aantal exemplaren handmatig of automatisch schalen](../monitoring-and-diagnostics/insights-how-to-scale.md)
