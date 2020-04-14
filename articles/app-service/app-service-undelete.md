---
title: Verwijderde apps herstellen
description: Meer informatie over het herstellen van een verwijderde app in Azure App Service. Vermijd de hoofdpijn van een per ongeluk verwijderde app.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: 296c8e2dfe99e3b0aea66f364ac6f6d9b2f60a1a
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272488"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Verwijderde App Service-apps herstellen met PowerShell

Als u uw app per ongeluk hebt verwijderd in Azure App Service, u deze herstellen met behulp van de opdrachten uit de [Az PowerShell-module.](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0)

> [!NOTE]
> Verwijderde apps worden 30 dagen na de eerste verwijdering uit het systeem verwijderd. Zodra een app is verwijderd, kan deze niet meer worden hersteld.
>

## <a name="re-register-app-service-resource-provider"></a>App-serviceprovider opnieuw registreren
Sommige klanten kunnen een probleem tegenkomen waarbij het ophalen van de lijst met verwijderde apps mislukt. Voer de volgende opdracht uit om het probleem op te lossen:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Verwijderde apps weergeven

Als u de verzameling verwijderde apps `Get-AzDeletedWebApp`wilt ophalen, u.

Voor meer informatie over een specifieke verwijderde app u het gewenste aantal gebruiken:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

De gedetailleerde informatie omvat:

- **VerwijderdSiteId**: Unieke id voor de app, gebruikt voor scenario's waarin meerdere apps met dezelfde naam zijn verwijderd
- **Abonnement-id**: Abonnement met de verwijderde bron
- **Locatie**: Locatie van de oorspronkelijke app
- **ResourceGroupName:** Naam van de oorspronkelijke resourcegroep
- **Naam**: Naam van de oorspronkelijke app.
- **Slot**: de naam van de sleuf.
- **Verwijderingstijd**: Wanneer is de app verwijderd  

## <a name="restore-deleted-app"></a>Verwijderde app herstellen

Zodra de app die u wilt herstellen is geïdentificeerd, u deze herstellen met behulp van `Restore-AzDeletedWebApp`.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> Implementatiesleuven worden niet hersteld als onderdeel van uw app. Als u een faseringssleuf `-Slot <slot-name>` moet herstellen, gebruikt u de vlag.
>

De ingangen voor opdracht zijn:

- **Resourcegroep**: doelgroepgroep waarbij de app wordt hersteld
- **Naam:** Naam voor de app, moet wereldwijd uniek zijn.
- **TargetAppServicePlanName**: App Service-abonnement gekoppeld aan de app

Standaard `Restore-AzDeletedWebApp` wordt zowel uw app-configuratie als een inhoud hersteld. Als u alleen inhoud wilt herstellen, gebruikt u de `-RestoreContentOnly` vlag met deze commandlet.

> [!NOTE]
> Als de app is gehost op en vervolgens is verwijderd uit een App Service-omgeving, kan deze alleen worden hersteld als de bijbehorende App-serviceomgeving nog bestaat.
>

U vindt de volledige commandlet referentie hier: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
