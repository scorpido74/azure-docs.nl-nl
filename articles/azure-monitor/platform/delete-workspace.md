---
title: Azure Log Analytics-werkruimte verwijderen en herstellen | Microsoft Documenten
description: Lees hoe u uw Log Analytics-werkruimte verwijdert als u er een hebt gemaakt in een persoonlijk abonnement of uw werkruimtemodel opnieuw structureren.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/14/2020
ms.openlocfilehash: 1dceb3db4572ecdaf504745dba1099a5eccead43
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80395790"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Azure Log Analytics-werkruimte verwijderen en herstellen

In dit artikel wordt het concept van azure log analytics-werkruimte soft-delete uitgelegd en hoe verwijderde werkruimte worden hersteld. 

## <a name="considerations-when-deleting-a-workspace"></a>Overwegingen bij het verwijderen van een werkruimte

Wanneer u een Log Analytics-werkruimte verwijdert, wordt een bewerking voor softdelete uitgevoerd om het herstel van de werkruimte mogelijk te maken, inclusief de gegevens en verbonden agents binnen 14 dagen, ongeacht of de verwijdering per ongeluk of opzettelijk was. Na de periode voor het verwijderen van de soft-delete zijn de werkruimtebron en de bijbehorende gegevens niet meer te herstellen : de gegevens worden in de wachtrij geplaatst voor permanente verwijdering en worden binnen 30 dagen volledig verwijderd. De naam van de werkruimte wordt 'vrijgegeven' en u deze gebruiken om een nieuwe werkruimte te maken.

> [!NOTE]
> Als u het gedrag soft-delete wilt overschrijven en uw werkruimte permanent wilt verwijderen, voert u de stappen in [Permanente werkruimte verwijderen](#permanent-workspace-delete)uit.

U wilt voorzichtig zijn wanneer u een werkruimte verwijdert, omdat er belangrijke gegevens en configuratie kunnen zijn die een negatieve invloed kunnen hebben op uw servicebewerking. Bekijk welke agents, oplossingen en andere Azure-services en -bronnen hun gegevens opslaan in Log Analytics, zoals:

* Beheeroplossingen
* Azure Automation
* Agents draaien op virtuele Windows- en Linux-machines
* Agents die draaien op Windows- en Linux-computers in uw omgeving
* System Center Operations Manager

De bewerking soft-delete verwijdert de werkruimtebron en de toestemming van alle bijbehorende gebruikers wordt verbroken. Als gebruikers zijn gekoppeld aan andere werkruimten, kunnen ze Logboekanalyse blijven gebruiken met die andere werkruimten.

## <a name="soft-delete-behavior"></a>Gedrag voor soft-delete

Met de bewerking voor het verwijderen van de werkruimte wordt de resource van de werkruimteresource verwijderd, maar de configuratie en gegevens worden gedurende 14 dagen bewaard, terwijl de weergave wordt weergeven dat de werkruimte wordt verwijderd. Alle agents en System Center Operations Manager-beheergroepen die zijn geconfigureerd om aan de werkruimte te rapporteren, blijven in een verweesde status tijdens de periode voor het verwijderen van soft delete. De service biedt verder een mechanisme voor het herstellen van de verwijderde werkruimte, inclusief de gegevens en verbonden bronnen, in wezen ongedaan maken van de verwijdering.

> [!NOTE] 
> Geïnstalleerde oplossingen en gekoppelde services zoals uw Azure Automation-account worden permanent uit de werkruimte verwijderd op het moment van verwijdering en kunnen niet worden hersteld. Deze moeten na de herstelbewerking opnieuw worden geconfigureerd om de werkruimte in de eerder geconfigureerde status te brengen.

U een werkruimte verwijderen met [PowerShell,](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0) [REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)of in de [Azure-portal.](https://portal.azure.com)

### <a name="azure-portal"></a>Azure Portal

1. Als u zich wilt aanmelden, gaat u naar de [Azure-portal](https://portal.azure.com). 
2. Selecteer **alle services**in de Azure-portal . Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Logboekanalysewerkruimten**.
3. Selecteer in de lijst met Logboekanalyse-werkruimten een werkruimte en klik boven aan het middelste deelvenster op **Verwijderen.**
   ![De optie Verwijderen uit het deelvenster Eigenschappen van Werkruimte](media/delete-workspace/log-analytics-delete-workspace.png)
4. Wanneer het bevestigingsberichtvenster wordt weergegeven met de vraag om de verwijdering van de werkruimte te bevestigen, klikt u op **Ja**.
   ![Verwijdering van werkruimte bevestigen](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

### <a name="troubleshooting"></a>Problemen oplossen

U moet over machtigingen voor 'Log Analytics Contributor' beschikken om de werkruimte Log Analytics te verwijderen.<br>
Als u een foutbericht *'Deze werkruimtenaam is al in gebruik'* krijgt bij het maken van een werkruimte, kan dit zijn omdat:
* De naam van de werkruimte is niet beschikbaar en wordt gebruikt door iemand in uw organisatie of door een andere klant.
* De werkruimte is in de afgelopen 14 dagen verwijderd en de naam ervan is gereserveerd voor de periode voor het verwijderen van soft delete. Voer de volgende stappen uit om de werkruimte eerst te herstellen en de werkruimte definitief te verwijderen en deze onmiddellijk te verwijderen en een nieuwe werkruimte met dezelfde naam te maken:<br>
   1. [Herstel](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#recover-workspace) uw werkruimte.
   2. Uw werkruimte [permanent verwijderen.](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#permanent-workspace-delete)
   3. Maak een nieuwe werkruimte met dezelfde werkruimtenaam.


## <a name="permanent-workspace-delete"></a>Permanente werkruimte verwijderen
De methode voor het verwijderen van soft delete past mogelijk niet in sommige scenario's, zoals ontwikkeling en testen, waarbij u een implementatie met dezelfde instellingen en de naam van de werkruimte moet herhalen. In dergelijke gevallen u uw werkruimte permanent verwijderen en de periode voor het verwijderen van soft delete overschrijven. Met de bewerking permanente werkruimteverwijderen wordt de naam van de werkruimte vrijgegeven en u een nieuwe werkruimte maken met dezelfde naam.


> [!IMPORTANT]
> Gebruik de bewerking permanente verwijdering van werkruimtes met de nodige voorzichtigheid, omdat deze onomkeerbaar is en u uw werkruimte en de bijbehorende gegevens niet herstellen.

De permanente verwijdering van de werkruimte kan momenteel worden uitgevoerd via REST API.

> [!NOTE]
> Elke API-aanvraag moet een certificaat voor toestemming aan toonder bevatten in de aanmaningskop.
>
> U het token aanschaffen met:
> - [App-registraties](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
> - Navigeer naar Azure-portal met de console van de ontwikkelaar (F12) in de browser. Kijk in een van de **batch?** instanties voor de verificatietekenreeks onder **Kopteksten aanvragen**. Dit zal in het patroon *vergunning: Drager <token> *. Kopieer en voeg dit toe aan uw API-aanroep zoals weergegeven in de voorbeelden.
> - Navigeer naar de documentatiesite azure REST. druk **op Probeer het** op een API, kopieer het Bearer-token en voeg deze toe aan uw API-aanroep.
Als u uw werkruimte permanent wilt verwijderen, gebruikt u de API-aanroep [Voor werkruimten -]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) Rest-API-aanroep verwijderen met een forcetag:
>
> ```rst
> DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview&force=true
> Authorization: Bearer eyJ0eXAiOiJKV1Qi….
> ```
Waar 'eyJ0eXAiOiJKV1Qi...' vertegenwoordigt het volledige autorisatietoken.

## <a name="recover-workspace"></a>Werkruimte herstellen

Als u inzendermachtigingen hebt voor de abonnements- en resourcegroep waarbij de werkruimte is gekoppeld vóór de bewerking softdelete, u deze herstellen tijdens de periode voor het verwijderen van soft delete, inclusief de gegevens, de configuratie en de verbonden agents. Na de periode voor het verwijderen van soft-delete is de werkruimte niet-herstelbaar en toegewezen voor permanente verwijdering. Namen van verwijderde werkruimten blijven behouden tijdens de periode voor het verwijderen van soft en kunnen niet worden gebruikt wanneer u een nieuwe werkruimte probeert te maken.  

U een werkruimte herstellen door deze opnieuw te maken met behulp van de volgende methoden voor het maken van werkruimtes: [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) of [REST API,]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) zolang de volgende eigenschappen worden gevuld met de verwijderde werkruimtedetails:

* Abonnements-id
* Naam resourcegroep
* Naam van de werkruimte
* Regio

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

De werkruimte en alle gegevens worden na de herstelbewerking teruggebracht. Oplossingen en gekoppelde services zijn permanent uit de werkruimte verwijderd toen deze werd verwijderd en deze moeten opnieuw worden geconfigureerd om de werkruimte naar de eerder geconfigureerde status te brengen. Sommige gegevens zijn mogelijk niet beschikbaar voor query's nadat de bijbehorende oplossingen opnieuw zijn geïnstalleerd en de schema's ervan aan de werkruimte zijn toegevoegd.

> [!NOTE]
> * Herstel van werkruimtes wordt niet ondersteund in de [Azure-portal.](https://portal.azure.com) 
> * Als u een werkruimte opnieuw maakt tijdens de periode voor het verwijderen van zachte plaatsen, wordt aangegeven dat deze naam van deze werkruimte al in gebruik is. 
> 
