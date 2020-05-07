---
title: Azure Log Analytics-werk ruimte verwijderen en herstellen | Microsoft Docs
description: Meer informatie over het verwijderen van uw Log Analytics-werk ruimte als u deze in een persoonlijk abonnement hebt gemaakt of uw werkruimte model opnieuw hebt gestructureerd.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/30/2020
ms.openlocfilehash: 7ed01a57a4c2a55d777907a6cc14b111fb2086e3
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82731897"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Azure Log Analytics-werk ruimte verwijderen en herstellen

In dit artikel wordt uitgelegd hoe u met de procedure voor het uitvoeren van Azure Log Analytics Workspace kunt verwijderen en hoe u de verwijderde werk ruimte herstelt. 

## <a name="considerations-when-deleting-a-workspace"></a>Overwegingen bij het verwijderen van een werk ruimte

Wanneer u een Log Analytics-werk ruimte verwijdert, wordt er een tijdelijke Verwijder bewerking uitgevoerd om het herstel van de werk ruimte met inbegrip van gegevens en verbonden agents binnen 14 dagen toe te staan, of het verwijderen per ongeluk of opzettelijk is geslaagd. Na de periode voor het voorlopig verwijderen kunnen de werkruimte resource en de gegevens niet worden hersteld. de bijbehorende gegevens worden in een wachtrij geplaatst voor permanent verwijderen en binnen 30 dagen volledig verwijderd. De naam van de werk ruimte is vrijgegeven en u kunt deze gebruiken om een nieuwe werk ruimte te maken.

> [!NOTE]
> Als u het gedrag van zacht verwijderen wilt negeren en uw werk ruimte permanent wilt verwijderen, volgt u de stappen in [permanente werk ruimte verwijderen](#permanent-workspace-delete).

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

### <a name="azure-portal"></a>Azure Portal

1. Als u zich wilt aanmelden, gaat u naar de [Azure Portal](https://portal.azure.com). 
2. Selecteer in het Azure Portal **alle services**. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **log Analytics-werk ruimten**.
3. Selecteer een werk ruimte in de lijst met Log Analytics-werk ruimten en klik vervolgens op **verwijderen** boven in het middelste deel venster.
   ![Optie verwijderen in deel venster Eigenschappen van werk ruimte](media/delete-workspace/log-analytics-delete-workspace.png)
4. Wanneer het venster bevestigings bericht wordt weer gegeven waarin u wordt gevraagd om het verwijderen van de werk ruimte te bevestigen, klikt u op **Ja**.
   ![Verwijderen van werk ruimte bevestigen](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

### <a name="troubleshooting"></a>Problemen oplossen

U moet ten minste *log Analytics Inzender* machtigingen hebben om een werk ruimte te verwijderen.<br>
Als er een fout bericht wordt weer gegeven, *is de naam van de werk ruimte al in gebruik* of *conflict* bij het maken van een werk ruimte, kan dit sinds:
* De naam van de werk ruimte is niet beschikbaar en wordt gebruikt door iemand in uw organisatie of door een andere klant.
* De werk ruimte is in de afgelopen 14 dagen verwijderd en de naam is gereserveerd voor de tijdelijke periode voor het verwijderen. Volg deze stappen om de werk ruimte eerst te herstellen en permanent verwijderen uit te voeren om de tijdelijke verwijdering te onderdrukken en uw werk ruimte permanent te verwijderen om een nieuwe werk ruimte met dezelfde naam te maken.<br>
   1. [Herstel](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#recover-workspace) uw werk ruimte.
   2. Uw werk ruimte [permanent verwijderen](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#permanent-workspace-delete) .
   3. Maak een nieuwe werk ruimte met dezelfde naam voor de werk ruimte.

## <a name="permanent-workspace-delete"></a>Permanente werk ruimte verwijderen
De methode voor het zacht verwijderen past mogelijk niet in sommige scenario's zoals ontwikkelen en testen, waarbij u een implementatie met dezelfde instellingen en werkruimte naam moet herhalen. In dergelijke gevallen kunt u uw werk ruimte permanent verwijderen en de periode voor voorlopig verwijderen negeren. Met de bewerking permanent verwijderen van werk ruimte wordt de naam van de werk ruimte vrijgegeven en kunt u een nieuwe werk ruimte maken met dezelfde naam.


> [!IMPORTANT]
> Gebruik de permanente bewerking voor het verwijderen van werk ruimten met een waarschuwing omdat het onomkeerbaar is en u de werk ruimte en de gegevens niet kunt herstellen.

Het permanent verwijderen van de werk ruimte kan momenteel worden uitgevoerd via REST API.

> [!NOTE]
> Elke API-aanvraag moet een Bearer-autorisatie token in de aanvraag header bevatten.
>
> U kunt het token verkrijgen met behulp van:
> - [App-registraties](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
> - Ga in de browser naar Azure Portal met behulp van de console van de ontwikkelaar (F12). Zoek in een van de **batch?** instanties voor de verificatie reeks onder **aanvraag headers**. Dit is de patroon *autorisatie: Bearer <token> *. Kopieer en voeg dit toe aan uw API-oproep, zoals wordt weer gegeven in de voor beelden.
> - Ga naar de site van de Azure REST-documentatie. Druk op **try it** op een API, kopieer het Bearer-token en voeg dit toe aan uw API-aanroep.
Als u uw werk ruimte permanent wilt verwijderen, gebruikt u de [werk ruimten-rest-API-aanroep verwijderen]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) met een Force-tag:
>
> ```rst
> DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview&force=true
> Authorization: Bearer eyJ0eXAiOiJKV1Qi….
> ```
Waarbij ' eyJ0eXAiOiJKV1Qi... ' vertegenwoordigt het volledige verificatie token.

## <a name="recover-workspace"></a>Werk ruimte herstellen

Als u Inzender machtigingen hebt voor het abonnement en de resource groep waaraan de werk ruimte is gekoppeld vóór de tijdelijke bewerking, kunt u deze herstellen tijdens de tijdelijke verwijderings periode, inclusief gegevens, configuratie en verbonden agents. Na de periode voor het voorlopig verwijderen is de werk ruimte niet-herstelbaar en toegewezen voor permanente verwijdering. Namen van verwijderde werk ruimten blijven behouden tijdens de tijdelijke periode en kunnen niet worden gebruikt bij het maken van een nieuwe werk ruimte.  

U kunt uw werk ruimte herstellen door een werk ruimte te maken met de details van de verwijderde werk ruimte. Dit zijn onder andere *abonnements-id*, *naam van resource groep*, naam en *regio*van de *werk* ruimte. Als uw resource groep ook is verwijderd en niet bestaat, maakt u een resource groep met dezelfde naam die werd gebruikt vóór het verwijderen en maakt u vervolgens een werk ruimte met een van deze methoden: [Azure Portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace), [power shell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) of [rest API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate).

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

De werk ruimte en alle bijbehorende gegevens worden teruggezet na de herstel bewerking. Oplossingen en gekoppelde services zijn permanent verwijderd uit de werk ruimte toen ze werd verwijderd en moeten opnieuw worden geconfigureerd om de werk ruimte naar de eerder geconfigureerde status te brengen. Sommige gegevens zijn mogelijk niet beschikbaar voor de query nadat de werk ruimte is hersteld en de bijbehorende schema's worden toegevoegd aan de werk ruimte.

> [!NOTE]
> * Werkruimte herstel wordt niet ondersteund in de [Azure Portal](https://portal.azure.com). 
> * Wanneer u tijdens de tijdelijke verwijderings periode een werk ruimte opnieuw maakt, geeft u een indicatie dat deze werkruimte naam al in gebruik is. 
> 
