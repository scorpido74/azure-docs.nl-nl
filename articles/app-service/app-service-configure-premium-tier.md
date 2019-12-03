---
title: PremiumV2-laag configureren
description: Meer informatie over het verbeteren van de prestaties van uw web-, mobiele en API-app in Azure App Service door te schalen naar de nieuwe prijs categorie PremiumV2.
keywords: App Service, Azure App Service, schaal, schaalbaar, App Service-abonnement, kosten App Service
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 07/25/2018
ms.custom: seodec18
ms.openlocfilehash: f7f9f6d5617ad0f9be69c47ce514d395534fd892
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672214"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>PremiumV2-laag configureren voor Azure App Service

De nieuwe prijs categorie **PremiumV2** biedt snellere processors, SSD-opslag en verdubbelt de geheugen-naar-core-verhouding van de bestaande prijs categorieën. Met het prestatie voordeel kunt u geld besparen door uw apps op minder exemplaren uit te voeren. In dit artikel leert u hoe u een app maakt in **PremiumV2** -laag of een app omhoog schaalt naar **PremiumV2** -laag.

## <a name="prerequisites"></a>Vereisten

Als u een app wilt schalen naar **PremiumV2**, moet u beschikken over een Azure app service-app die wordt uitgevoerd in een lagere prijs categorie dan **PremiumV2**. de app moet worden uitgevoerd in een app service-implementatie die PremiumV2 ondersteunt.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>PremiumV2-Beschik baarheid

De laag **PremiumV2** is beschikbaar voor app service zowel in _Windows_ als in _Linux_.

**PremiumV2** is beschikbaar in de meeste Azure-regio's. Als u wilt zien of deze beschikbaar is in uw regio, voert u de volgende Azure CLI-opdracht uit in de [Azure Cloud shell](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Een app maken in de PremiumV2-laag

De prijs categorie van een App Service-app wordt gedefinieerd in het [app service plan](overview-hosting-plans.md) waarop het wordt uitgevoerd. U kunt een App Service plan maken op zichzelf of als onderdeel van het maken van een app.

Selecteer bij het configureren van het App Service-abonnement in de <a href="https://portal.azure.com" target="_blank">Azure Portal</a> **prijs categorie**. 

Selecteer **productie**, selecteer **P1V2**, **P2V2**of **P3V2**en klik vervolgens op **Toep assen**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Als u **P1V2**, **P2V2**en **P3V2** niet als opties ziet, of als de opties grijs worden weer gegeven, is **PremiumV2** waarschijnlijk niet beschikbaar in de onderliggende app service implementatie die het app service plan bevat. Zie [Omhoog schalen vanuit een niet-ondersteunde resource groep en regio combinatie](#unsupported) voor meer informatie.

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Een bestaande app omhoog schalen naar PremiumV2-laag

Voordat u een bestaande app naar **PremiumV2** -laag schaalt, moet u ervoor zorgen dat **PremiumV2** beschikbaar is. Zie [PremiumV2 Availability](#availability)(Engelstalig) voor meer informatie. Als deze niet beschikbaar is, raadpleegt [u omhoog schalen op basis van een niet-ondersteunde combi natie van resource groep en regio](#unsupported).

Afhankelijk van uw hostomgeving moet u mogelijk extra stappen voor het omhoog schalen. 

Open uw App Service app-pagina in de <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

Selecteer in de linkernavigatiebalk van uw App Service-app-pagina **Omhoog schalen (app service plan)** .

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Selecteer **productie**, selecteer **P1V2**, **P2V2**of **P3V2**en klik vervolgens op **Toep assen**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Als uw bewerking is voltooid, ziet u in de overzichts pagina van uw app dat deze nu in een **PremiumV2** -laag is.

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Als er een fout optreedt

Sommige App Service-abonnementen kunnen niet naar de PremiumV2-laag worden geschaald als de onderliggende App Service implementatie geen ondersteuning biedt voor PremiumV2.  Zie [Omhoog schalen vanuit een niet-ondersteunde resource groep en regio combinatie](#unsupported) voor meer informatie.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Opschalen van een niet-ondersteunde combi natie van resource groep en regio

Als uw app wordt uitgevoerd in een App Service implementatie waarbij **PremiumV2** niet beschikbaar is, of als uw app wordt uitgevoerd in een regio die momenteel geen ondersteuning biedt voor **PremiumV2**, moet u uw app opnieuw implementeren om gebruik te maken van **PremiumV2**.  U hebt hiervoor twee opties:

- Maak een **nieuwe** resource groep en maak een **nieuwe** app en app service plan in de **nieuwe** resource groep. Kies de gewenste Azure-regio tijdens het maken van het proces.  U **moet** het **PremiumV2** -abonnement selecteren op het moment dat het nieuwe app service-plan wordt gemaakt.  Dit zorgt ervoor dat de combi natie van resource groep, het App Service plan en de Azure-regio het App Service plan wordt gemaakt in een App Service-implementatie die **PremiumV2**ondersteunt.  Implementeer uw toepassings code vervolgens in het zojuist gemaakte app-en app service-plan. Indien gewenst kunt u het App Service plan vervolgens van **PremiumV2** omlaag schalen om de kosten te besparen en kunt u in de toekomst nog steeds een back-up maken met behulp van **PremiumV2**.
- Als uw app al in een bestaande **Premium** -laag wordt uitgevoerd, kunt u uw app klonen met alle app-instellingen, verbindings reeksen en implementatie configuratie in een nieuw app service-plan dat gebruikmaakt van **PremiumV2**.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    U kunt op de pagina **app klonen** een app service plan maken met behulp van **PremiumV2** in de gewenste regio en de app-instellingen en configuratie opgeven die u wilt klonen.

## <a name="automate-with-scripts"></a>Automatiseren met scripts

U kunt het maken van apps in de **PremiumV2** -laag automatiseren met scripts, met behulp van [Azure cli](/cli/azure/install-azure-cli) of [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Azure CLI

Met de volgende opdracht maakt u een App Service plan in _P1V2_. U kunt deze in de Cloud Shell uitvoeren. De opties voor `--sku` zijn P1V2, _P2V2_en _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Met de volgende opdracht maakt u een App Service plan in _P1V2_. De opties voor `-WorkerSize` zijn _klein_, _gemiddeld_en _groot_.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>Meer informatiebronnen

[Een app omhoog schalen in azure](manage-scale-up.md)  
[Het aantal exemplaren handmatig of automatisch schalen](../monitoring-and-diagnostics/insights-how-to-scale.md)
