---
title: Per app schalen voor hosting met hoge dichtheid
description: Schaal apps onafhankelijk van de App Service-abonnementen en optimaliseer de geschaalde exemplaren in uw abonnement.
author: btardif
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: f1ca4958fe2608d0c040ef5b93827a7e71a4151c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672355"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Hosting met hoge dichtheid op Azure App Service met per app-schaling

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Wanneer u App Service gebruikt, u uw apps schalen door het [App Service-abonnement](overview-hosting-plans.md) waarop ze worden uitgevoerd te schalen. Wanneer meerdere apps in hetzelfde App Service-abonnement worden uitgevoerd, worden alle apps in het abonnement uitgevoerd in elke geschaalde instantie.

Schalen per app kan worden ingeschakeld op het niveau van het App-serviceplan, zodat een app onafhankelijk van het App *Service-abonnement* kan worden geschaald. Op deze manier kan een App Service-abonnement worden geschaald naar 10 exemplaren, maar kan een app worden ingesteld op slechts vijf exemplaren.

> [!NOTE]
> Schalen per app is alleen beschikbaar voor **standaard-,** **Premium-,** **Premium-, Premium V2-** en **Geïsoleerde** prijsniveaus.
>

Apps worden toegewezen aan het beschikbare App Service-abonnement met behulp van een best effort-aanpak voor een gelijkmatige verdeling over instanties. Hoewel een gelijkmatige distributie niet is gegarandeerd, zorgt het platform ervoor dat twee exemplaren van dezelfde app niet worden gehost op hetzelfde app-abonnement.

Het platform is niet afhankelijk van statistieken om te beslissen over de toewijzing van werknemers. Toepassingen worden alleen opnieuw in evenwicht gebracht wanneer instanties worden toegevoegd of verwijderd uit het App-serviceplan.

## <a name="per-app-scaling-using-powershell"></a>Schalen per app met PowerShell

Maak een plan met per-app ```-PerSiteScaling $true``` schalen door ```New-AzAppServicePlan``` de parameter door te geven aan de cmdlet.

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Schakel per-app schalen in met een bestaand `-PerSiteScaling $true` App ```Set-AzAppServicePlan``` Service Plan door de parameter door te geven aan de cmdlet.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Configureer op app-niveau het aantal exemplaren dat de app kan gebruiken in het App Service-abonnement.

In het onderstaande voorbeeld is de app beperkt tot twee instanties, ongeacht naar hoeveel exemplaren het onderliggende app-serviceplan wordt geschaald.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers`is anders `$newapp.MaxNumberOfWorkers`dan . Per app-schaling wordt gebruikt `$newapp.SiteConfig.NumberOfWorkers` om de schaalkenmerken van de app te bepalen.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Schalen per app met Azure Resource Manager

Met de volgende sjabloon Azure Resource Manager wordt het volgende:

- Een App-serviceplan dat is opgeschaald naar 10 exemplaren
- een app die is geconfigureerd om te schalen naar maximaal vijf exemplaren.

Het App-serviceplan stelt de eigenschap `"perSiteScaling": true` **PerSiteScaling** in op true. De app stelt het **aantal werknemers** `"properties": { "numberOfWorkers": "5" }`in dat moet worden gebruikt op 5 .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Aanbevolen configuratie voor hosting met hoge dichtheid

Schalen per app is een functie die is ingeschakeld in zowel algemene Azure-regio's als [App-serviceomgevingen.](environment/app-service-app-service-environment-intro.md) De aanbevolen strategie is echter om App Service-omgevingen te gebruiken om te profiteren van hun geavanceerde functies en de grotere app-service-abonnementscapaciteit.  

Volg de volgende stappen om hosting met hoge dichtheid voor uw apps te configureren:

1. Wijs een App Service-abonnement aan als het plan met hoge dichtheid en schaal het op naar de gewenste capaciteit.
1. Stel `PerSiteScaling` de vlag in op het app-abonnement.
1. Er worden nieuwe apps gemaakt en toegewezen aan dat App-serviceplan met de eigenschap **numberOfWorkers** ingesteld op **1**.
   - Het gebruik van deze configuratie levert de hoogst mogelijke dichtheid op.
1. Het aantal werknemers kan onafhankelijk per app worden geconfigureerd om indien nodig extra resources toe te kennen. Bijvoorbeeld:
   - Een app voor veel gebruik kan **het aantal Werknemers** instellen op **3** om meer verwerkingscapaciteit voor die app te hebben.
   - Apps met weinig gebruik zouden **het aantal werknemers** instellen op **1**.

## <a name="next-steps"></a>Volgende stappen

- [Azure App Service plant diepgaand overzicht](overview-hosting-plans.md)
- [Inleiding tot de App Service-omgeving](environment/app-service-app-service-environment-intro.md)
