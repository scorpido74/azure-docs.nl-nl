---
title: Verwijderde App Service apps herstellen-Azure App Service
description: Meer informatie over het herstellen van een verwijderde App Service-app met behulp van Power shell.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.service: app-service
ms.openlocfilehash: 7b3a21f3cfee806dc94353e0bc6c11e88641ea34
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827527"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Verwijderde App Service-apps herstellen met Power shell

Als u uw app per ongeluk wilt verwijderen in Azure App Service, kunt u deze herstellen met behulp van de opdrachten uit de [AZ Power shell-module](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

## <a name="list-deleted-apps"></a>Verwijderde apps weergeven

Als u de verzameling verwijderde Apps wilt ophalen, kunt u `Get-AzDeletedWebApp`gebruiken.

Voor meer informatie over een specifieke verwijderde app kunt u het volgende gebruiken:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app>
```

De gedetailleerde informatie omvat:

- **DeletedSiteId**: De unieke id voor de app die wordt gebruikt voor scenario's waarbij meerdere apps met dezelfde naam zijn verwijderd
- **SubscriptionID**: Abonnement met de verwijderde resource
- **Locatie**: Locatie van de oorspronkelijke app
- **ResourceGroupName**: Naam van de oorspronkelijke resource groep
- **Naam**: De naam van de oorspronkelijke app.
- **Sleuf**: de naam van de sleuf.
- **Verwijderings tijd**: Wanneer is de app verwijderd  

## <a name="restore-deleted-app"></a>Verwijderde app herstellen

Zodra de app die u wilt herstellen is geïdentificeerd, kunt u deze herstellen met `Restore-AzDeletedWebApp`.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

De invoer voor opdracht is:

- **Resourcegroep**: Doel resource groep waar de app wordt teruggezet
- **Naam**: De naam voor de app moet wereld wijd uniek zijn.
- **TargetAppServicePlanName**: App Service plan dat aan de app is gekoppeld

Standaard `Restore-AzDeletedWebApp` worden zowel uw app-configuratie als een inhoud hersteld. Als u alleen inhoud wilt herstellen, gebruikt u de `-RestoreContentOnly` vlag met deze commandlet.

U vindt hier de volledige naslag informatie over de commandlet: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
