---
title: Overschakelen van verouderde Api voor Logboekanalysewaarschuwingen naar nieuwe Azure Alerts API
description: Overzicht van legacy savedSearch based Log Analytics Alert API en proces om waarschuwingsregels over te schakelen naar de nieuwe ScheduledQueryRules API, met details voor het aanpakken van veelvoorkomende klantproblemen.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: c1ca85ba9c79f828b5ddcf0d640cfe7f8b6e097c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249436"
---
# <a name="switch-api-preference-for-log-alerts"></a>Api-voorkeursvoorkeur voor logboekwaarschuwingen

> [!NOTE]
> Inhoud vermeld die alleen van toepassing is op gebruikers Azure public cloud en **niet** voor Azure Government of Azure China cloud.  

> [!NOTE]
> Zodra een gebruiker ervoor kiest om de voorkeur over te schakelen naar de nieuwe [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) is het niet mogelijk om terug te keren naar het gebruik van de oudere [verouderde Log Analytics Alert API.](api-alerts.md)

Tot voor kort beheerde u waarschuwingsregels in de Microsoft Operations Management Suite-portal. De nieuwe waarschuwingservaring is geïntegreerd met verschillende services in Microsoft Azure, waaronder Log Analytics, en we hebben gevraagd om uw waarschuwingsregels uit te [breiden van OMS-portal naar Azure.](alerts-extend.md) Maar om een minimale onderbreking voor klanten te garanderen, heeft het proces de programmatische interface voor het verbruik niet gewijzigd - [Log Analytics Alert API](api-alerts.md) op basis van SavedSearch.

Maar nu kondigt u voor Log Analytics gebruikers een echt Azure-programmatisch alternatief aan, [Azure Monitor - ScheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), die ook een afspiegeling is van uw [Azure-facturering - voor logboekwaarschuwingen](alerts-unified-log.md#pricing-and-billing-of-log-alerts). Zie [Logboekwaarschuwingen beheren met Azure Resource Template](alerts-log.md#managing-log-alerts-using-azure-resource-template) en [Logboekwaarschuwingen beheren met PowerShell](alerts-log.md#managing-log-alerts-using-powershell)voor meer informatie over het beheren van uw logboekwaarschuwingen met de API.

## <a name="benefits-of-switching-to-new-azure-api"></a>Voordelen van overschakelen naar nieuwe Azure API

Er zijn verschillende voordelen van het maken en beheren van waarschuwingen met behulp van [de geplande QueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) via de verouderde Log Analytics Alert [API;](api-alerts.md) we hebben hieronder enkele van de belangrijkste opgesomd:

- Mogelijkheid om [logboekzoeken](../log-query/cross-workspace-query.md) in waarschuwingsregels te vergelijken en externe bronnen zoals Log Analytics-werkruimten of zelfs Application Insights-apps te beheren
- Wanneer meerdere velden worden gebruikt om query groep te worden, kan de [API-gebruiker van scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) opgeven welk veld moet worden samengevoegd in Azure-portal
- Logboekwaarschuwingen die zijn gemaakt met [de GEPLANDEQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) kunnen tot 48 uur zijn gedefinieerd en gegevens voor langere tijd ophalen dan voorheen
- Maak waarschuwingsregels in één opname als één resource zonder dat u drie niveaus van resources hoeft te maken zoals bij [de verouderde Api voor Logboekanalysewaarschuwing](api-alerts.md)
- Eén programmatische interface voor alle varianten van op query's gebaseerde logboekwaarschuwingen in Azure - nieuwe [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) kan worden gebruikt om regels voor Log Analytics en Application Insights te beheren
- Uw logboekwaarschuwingen beheren met [Powershell-cmdlets](alerts-log.md#managing-log-alerts-using-powershell)
- Alle nieuwe log alert functionaliteit en toekomstige ontwikkeling zal alleen beschikbaar zijn via de nieuwe [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Proces van overschakelen van verouderde API logboekwaarschuwingen

Gebruikers kunnen gratis [de verouderde Log Analytics Alert API](api-alerts.md) of de nieuwe [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)gebruiken. Waarschuwingsregels die door een api zijn gemaakt, *kunnen alleen door dezelfde API worden beheerd,* evenals vanuit azure-portal. Azure Monitor blijft standaard [de verouderde Log Analytics Alert API](api-alerts.md) gebruiken voor het maken van een nieuwe waarschuwingsregel van Azure-portal voor bestaande werkruimten van Log Analytics. Zoals [aangekondigd nieuwe Log-werkruimte die op of na 1 juni 2019](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/) is gemaakt, gebruikt u automatisch standaard de nieuwe geplande Api Voor [QueryRules,](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) inclusief in azure-portal.

De effecten van de overgang van voorkeur naar scheduledQueryRules API worden hieronder gecompileerd:

- Alle interacties voor het beheren van logboekwaarschuwingen via programmatische interfaces moeten nu worden uitgevoerd met behulp van [scheduledQueryRules.](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) Zie [voorbeeldgebruik via Azure Resource Template](alerts-log.md#managing-log-alerts-using-azure-resource-template) en [voorbeeldgebruik via PowerShell](alerts-log.md#managing-log-alerts-using-powershell) voor meer informatie.
- Elke nieuwe logalertregel die is gemaakt in Azure-portal, wordt alleen gemaakt met behulp van [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) en gebruikers in staat stellen om de [extra functionaliteit van nieuwe API](#benefits-of-switching-to-new-azure-api) via Azure-portal te gebruiken
- De ernst voor logboekwaarschuwingsregels wordt verschoven van: *Kritiek, Waarschuwing & Informatie ,* naar *Ernstwaarden van 0, 1 & 2*. Samen met de optie om waarschuwingsregels te maken/bijwerken met ernst 3 en 4 ook.

Het proces van het verplaatsen van waarschuwingsregels van [de verouderde Log Analytics Alert API](api-alerts.md) houdt op geen enkele manier het wijzigen van uw waarschuwingsdefinitie, query of configuratie in. Uw waarschuwingsregels en controle worden niet beïnvloed en de waarschuwingen worden niet gestopt of worden geblokkeerd, tijdens of na de switch. De enige veranderingen zijn:

- Een wijziging in api-voorkeur en toegang tot uw regels via een nieuwe API.
- Een gewijzigde waarschuwingsregelbron URI met de id's die worden gebruikt in de verouderde `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`Log Analytics Alert [API](api-alerts.md) in plaats van de naam van de waarschuwingsregel in deze structuur . De weergavenaam van de waarschuwingsregel blijft ongewijzigd.

Elke klant die vrijwillig wil overschakelen naar de nieuwe [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) en het gebruik van de [verouderde Log Analytics Alert API](api-alerts.md)wil blokkeren; kan dit doen door een PUT-aanroep uit te voeren op de onderstaande API om alle waarschuwingsregels te schakelen die zijn gekoppeld aan de specifieke Log Analytics-werkruimte.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Met aanvraaglichaam met de onderstaande JSON.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

De API is ook toegankelijk via een PowerShell-opdrachtregel met [ARMClient,](https://github.com/projectkudu/ARMClient)een open-source opdrachtregelhulpprogramma dat het inroepen van de Azure Resource Manager API vereenvoudigt. Zoals hieronder wordt geïllustreerd, u in voorbeeld PUT-oproep met behulp van ARMclient-tool alle waarschuwingsregels schakelen die zijn gekoppeld aan de specifieke Log Analytics-werkruimte.

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Als de overstap van alle waarschuwingsregels in de werkruimte Log Analytics om nieuwe [geplandeQueryregels](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) te gebruiken is geslaagd, wordt het volgende antwoord weergegeven.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Gebruikers kunnen ook de huidige status van uw Log Analytics-werkruimte controleren en zien of deze is ingeschakeld om [alleen scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) te gebruiken. Om te controleren, kunnen gebruikers een GET-oproep uitvoeren op de onderstaande API.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Zie het onderstaande voorbeeld om het bovenstaande uit te voeren in de opdrachtregel PowerShell met behulp van het gereedschap [ARMClient.](https://github.com/projectkudu/ARMClient)

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Als de opgegeven Werkruimte Log Analytics is overgeschakeld naar [alleen scheduledQueryRules.](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) dan is het antwoord JSON zal worden zoals hieronder vermeld.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Als de opgegeven werkruimte Log-analytische nog niet is ingeschakeld om [alleen scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) te gebruiken. dan is het antwoord JSON zal worden zoals hieronder vermeld.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Azure Monitor - Logboekwaarschuwingen](alerts-unified-log.md).
- Meer informatie over het maken van [logboekwaarschuwingen in Azure Alerts](alerts-log.md).
- Meer informatie over de [Azure Alerts-ervaring](../../azure-monitor/platform/alerts-overview.md).
