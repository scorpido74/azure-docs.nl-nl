---
title: Azure Log Analytics-werk ruimte verwijderen en herstellen | Microsoft Docs
description: Meer informatie over het verwijderen van uw Log Analytics-werk ruimte als u deze in een persoonlijk abonnement hebt gemaakt of uw werkruimte model opnieuw hebt gestructureerd.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: magoedte
ms.openlocfilehash: fb6714a52a65ef5efe4725b99acb30cb67af20c3
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299276"
---
# <a name="delete-and-restore-azure-log-analytics-workspace"></a>Azure Log Analytics-werk ruimte verwijderen en herstellen

In dit artikel wordt uitgelegd hoe u met de procedure voor het uitvoeren van Azure Log Analytics Workspace kunt verwijderen en hoe u de verwijderde werk ruimte herstelt. 

## <a name="considerations-when-deleting-a-workspace"></a>Overwegingen bij het verwijderen van een werk ruimte

Wanneer u een Log Analytics-werk ruimte verwijdert, wordt er een tijdelijke Verwijder bewerking uitgevoerd om het herstel van de werk ruimte met inbegrip van gegevens en verbonden agents binnen 14 dagen toe te staan, of het verwijderen per ongeluk of opzettelijk is geslaagd. Na de periode voor het voorlopig verwijderen zijn de werk ruimte en de bijbehorende gegevens niet herstelbaar en worden ze binnen 30 dagen in de wachtrij geplaatst voor permanente verwijdering.

U wilt voorzichtig zijn wanneer u een werk ruimte verwijdert, omdat er mogelijk belang rijke gegevens en configuratie zijn die uw service bewerking negatief kunnen beïnvloeden. Bekijk welke agents, oplossingen en andere Azure-Services en-bronnen uw gegevens opslaan in Log Analytics, zoals:

* Beheeroplossingen
* Azure Automation
* Agents die worden uitgevoerd op virtuele Windows-en Linux-machines
* Agents die worden uitgevoerd op Windows-en Linux-computers in uw omgeving
* System Center Operations Manager

De bewerking voor zacht verwijderen verwijdert de werkruimte resource en de machtigingen van de bijbehorende gebruikers worden verbroken. Als gebruikers zijn gekoppeld aan andere werk ruimten, kunnen ze Log Analytics blijven gebruiken met die andere werk ruimten.

## <a name="soft-delete-behavior"></a>Gedrag bij zacht verwijderen

Met de bewerking voor het verwijderen van de werk ruimte wordt de resource manager-bron van de werk ruimte verwijderd, maar de configuratie en gegevens worden gedurende 14 dagen bewaard, terwijl het uiterlijk van de werk ruimte wordt verwijderd. Alle agents en System Center Operations Manager-beheer groepen die zijn geconfigureerd om te rapporteren aan de werk ruimte, blijven behouden in een zwevende status tijdens de tijdelijke periode voor het verwijderen. De service biedt verder een mechanisme voor het herstellen van de verwijderde werk ruimte, inclusief de bijbehorende gegevens en verbonden resources, waardoor het verwijderen in feite ongedaan wordt gemaakt.

> [!NOTE] 
> Geïnstalleerde oplossingen en gekoppelde services, zoals uw Azure Automation account, worden permanent verwijderd uit de werk ruimte tijdens het verwijderen en kunnen niet worden hersteld. Deze moeten na de herstel bewerking opnieuw worden geconfigureerd om de werk ruimte naar de eerder geconfigureerde status te brengen.

U kunt een werk ruimte verwijderen met behulp van [Power shell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), [rest API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)of in de [Azure Portal](https://portal.azure.com).

### <a name="delete-workspace-in-azure-portal"></a>Werk ruimte in Azure Portal verwijderen

1. Als u zich wilt aanmelden, gaat u naar de [Azure Portal](https://portal.azure.com). 
2. Selecteer in de Azure-portal de optie **Alle services**. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **log Analytics-werk ruimten**.
3. Selecteer een werk ruimte in de lijst met Log Analytics-werk ruimten en klik vervolgens op **verwijderen** boven in het middelste deel venster.
   ![Delete optie in deel venster werkruimte eigenschappen @ no__t-1
4. Wanneer het venster bevestigings bericht wordt weer gegeven waarin u wordt gevraagd om het verwijderen van de werk ruimte te bevestigen, klikt u op **Ja**.
   ![Confirm verwijderen van de werk ruimte @ no__t-1

## <a name="recover-workspace"></a>Werk ruimte herstellen

Als u Inzender machtigingen hebt voor het abonnement en de resource groep waaraan de werk ruimte is gekoppeld vóór de tijdelijke bewerking, kunt u deze herstellen tijdens de tijdelijke verwijderings periode, inclusief gegevens, configuratie en verbonden agents. Na de periode voor het voorlopig verwijderen is de werk ruimte niet-herstelbaar en toegewezen voor permanente verwijdering. Namen van verwijderde werk ruimten blijven behouden tijdens de tijdelijke periode en kunnen niet worden gebruikt bij het maken van een nieuwe werk ruimte.  

U kunt een werk ruimte herstellen door de werk ruimte opnieuw te maken met behulp van de werk ruimte Create Method [Power shell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) of [rest API]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) , zolang deze eigenschappen zijn gevuld met de verwijderde werkruimte Details, waaronder:

* Abonnements-id
* Naam van resource groep
* Werkruimte naam
* Regio

> [!NOTE]
> * Werkruimte herstel wordt niet ondersteund in de [Azure Portal](https://portal.azure.com). 
> * Wanneer u tijdens de tijdelijke verwijderings periode een werk ruimte opnieuw maakt, geeft u een indicatie dat deze werkruimte naam al in gebruik is. 
> 
