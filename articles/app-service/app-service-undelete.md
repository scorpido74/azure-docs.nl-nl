---
title: Verwijderde App Service apps herstellen-Azure App Service
description: Meer informatie over het herstellen van een verwijderde App Service-app met behulp van Power shell.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.service: app-service
ms.openlocfilehash: 6a3a62053a488f95e22cae13ef9d0714a7b5dd05
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173743"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Verwijderde App Service-apps herstellen met Power shell

Als u uw app per ongeluk wilt verwijderen in Azure App Service, kunt u deze herstellen met behulp van de opdrachten uit de [AZ Power shell-module](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

## <a name="re-register-app-service-resource-provider"></a>App Service Resource provider opnieuw registreren
Sommige klanten kunnen een probleem ondervinden waarbij het ophalen van de lijst met verwijderde apps mislukt. Voer de volgende opdracht uit om het probleem op te lossen:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Verwijderde apps weergeven

Als u de verzameling verwijderde Apps wilt ophalen, kunt u `Get-AzDeletedWebApp`gebruiken.

Voor meer informatie over een specifieke verwijderde app kunt u het volgende gebruiken:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

De gedetailleerde informatie omvat:

- **DeletedSiteId**: de unieke id voor de app die wordt gebruikt voor scenario's waarbij meerdere apps met dezelfde naam zijn verwijderd
- **SubscriptionID**: abonnement met de verwijderde resource
- **Locatie**: locatie van de oorspronkelijke app
- **ResourceGroupName**: naam van de oorspronkelijke resource groep
- **Naam**: naam van de oorspronkelijke app.
- **Sleuf**: de naam van de sleuf.
- **Verwijderings tijd**: wanneer is de app verwijderd  

## <a name="restore-deleted-app"></a>Verwijderde app herstellen

Zodra de app die u wilt herstellen is geïdentificeerd, kunt u deze herstellen met behulp van `Restore-AzDeletedWebApp`.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

De invoer voor opdracht is:

- **Resource groep**: doel resource groep waar de app wordt teruggezet
- **Naam**: de naam voor de app moet wereld wijd uniek zijn.
- **TargetAppServicePlanName**: app service plan dat aan de app is gekoppeld

Standaard worden door `Restore-AzDeletedWebApp` zowel de configuratie van de app als de inhoud hersteld. Als u alleen inhoud wilt herstellen, gebruikt u de vlag `-RestoreContentOnly` met deze commandlet.

> [!NOTE]
> Als de app is gehost op en vervolgens wordt verwijderd uit een App Service Environment, kan deze alleen worden hersteld als de bijbehorende App Service Environment nog bestaan.
>

U vindt hier de volledige naslag informatie over de commandlet: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
