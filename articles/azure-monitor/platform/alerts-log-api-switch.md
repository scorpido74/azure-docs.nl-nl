---
title: Overschakelen van verouderde API voor Log Analytics waarschuwingen naar de nieuwe Azure Alerts-API
description: Overzicht van verouderde Log Analytics savedSearch-API en proces om waarschuwings regels te scha kelen naar een nieuwe ScheduledQueryRules-API, met details over veelvoorkomende klant problemen.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: c1ca85ba9c79f828b5ddcf0d640cfe7f8b6e097c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249436"
---
# <a name="switch-api-preference-for-log-alerts"></a>Switch-API-voor keur voor logboek waarschuwingen

> [!NOTE]
> Inhoud die alleen van toepassing is op gebruikers van Azure Public Cloud en **niet** voor Azure Government of Azure China-Cloud.  

> [!NOTE]
> Zodra een gebruiker heeft gekozen om de voor keur te wijzigen naar de nieuwe [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) , is het niet mogelijk om terug te gaan naar het gebruik van de oudere [verouderde waarschuwing](api-alerts.md)voor de API van log Analytics.

Tot onlangs hebt u de waarschuwings regels in de Microsoft Operations Management Suite Portal beheerd. De nieuwe waarschuwings ervaring is geïntegreerd met verschillende services in Microsoft Azure, waaronder Log Analytics en wij hebben gevraagd om [uw waarschuwings regels uit de OMS-Portal naar Azure uit te breiden](alerts-extend.md). Om ervoor te zorgen dat klanten zo weinig mogelijk worden onderbroken, heeft het proces de programmatische interface niet gewijzigd voor de API voor het gebruik van [log Analytics-waarschuwingen](api-alerts.md) op basis van SavedSearch.

Maar nu meldt u zich aan Log Analytics waarschuwt gebruikers een echte Azure-programmatische, [Azure monitor-SCHEDULEDQUERYRULES API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), die ook overeenkomt met uw [Azure-facturering-voor logboek waarschuwingen](alerts-unified-log.md#pricing-and-billing-of-log-alerts). Zie [logboek waarschuwingen beheren met Azure-resource sjabloon](alerts-log.md#managing-log-alerts-using-azure-resource-template) en [logboek waarschuwingen beheren met Power shell](alerts-log.md#managing-log-alerts-using-powershell)voor meer informatie over het beheren van uw logboek waarschuwingen met behulp van de API.

## <a name="benefits-of-switching-to-new-azure-api"></a>Voor delen van het overschakelen naar nieuwe Azure API

Er zijn verschillende voor delen van het maken en beheren van waarschuwingen met behulp van de [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) via de [verouderde API voor log Analytics waarschuwingen](api-alerts.md) Hieronder ziet u enkele van de belangrijkste items:

- De mogelijkheid om [logboek registratie in meerdere werk ruimten](../log-query/cross-workspace-query.md) in waarschuwings regels te doorzoeken en externe resources, zoals log Analytics werk ruimten of zelfs Application Insights apps, te beslaan
- Wanneer meerdere velden worden gebruikt om in de query te groeperen, kan met behulp van de [SCHEDULEDQUERYRULES API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) -gebruiker opgeven in welk veld moet worden geaggregeerd: in azure Portal
- Logboek waarschuwingen die zijn gemaakt met behulp van de [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) kunnen een periode hebben die maxi maal 48 uur is gedefinieerd en het ophalen van gegevens voor langere tijd dan vóór
- Waarschuwings regels in één afbeelding maken als één resource zonder dat u drie niveaus van resources hoeft te maken, net als bij een [verouderde log Analytics-waarschuwings-API](api-alerts.md)
- Eén programmatische interface voor alle varianten van op query's gebaseerde logboek waarschuwingen in Azure: de nieuwe [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) kan worden gebruikt voor het beheren van regels voor Log Analytics en Application Insights
- Uw logboek waarschuwingen beheren met [Power shell-cmdlets](alerts-log.md#managing-log-alerts-using-powershell)
- Alle nieuwe functionaliteit voor logboek waarschuwingen en toekomstige ontwikkeling is alleen beschikbaar via de nieuwe [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Proces van overschakelen van verouderde API voor logboek waarschuwingen

Gebruikers kunnen gebruikmaken van een [verouderde API voor log Analytics waarschuwingen](api-alerts.md) of de nieuwe [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Waarschuwings regels die door ofwel API zijn gemaakt, kunnen *worden beheerd met dezelfde API* , evenals uit Azure Portal. Azure Monitor wordt standaard [verouderde log Analytics waarschuwings-API](api-alerts.md) blijven gebruiken voor het maken van een nieuwe waarschuwings regel van Azure portal voor bestaande werk ruimten van log Analytics. Als [aangekondigde nieuwe logboek werkruimte die is gemaakt op of na 1 juni 2019,](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/) wordt automatisch nieuwe [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) gebruikt, met inbegrip van Azure Portal.

De gevolgen van de switch van de voor keur voor de scheduledQueryRules-API worden hieronder opgesteld:

- Alle interacties die worden uitgevoerd voor het beheer van logboek waarschuwingen via programmatische interfaces, moeten nu worden uitgevoerd met behulp van [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) . Zie voor meer informatie, voor [beeld van gebruik via een Azure-resource sjabloon](alerts-log.md#managing-log-alerts-using-azure-resource-template) en voor [beeld gebruik via Power shell](alerts-log.md#managing-log-alerts-using-powershell)
- Nieuwe waarschuwings regels voor logboeken die zijn gemaakt in Azure Portal, worden alleen gemaakt met [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) en toestaan dat gebruikers de [extra functionaliteit van nieuwe API](#benefits-of-switching-to-new-azure-api) via Azure Portal gebruiken
- De ernst van de regels voor logboek waarschuwingen verschuift van: *kritiek, waarschuwing & informatie*, naar *Ernst waarden van 0, 1 & 2*. En de optie voor het maken/bijwerken van waarschuwings regels met Ernst 3 en 4.

Door het proces van het verplaatsen van waarschuwings regels van de [verouderde log Analytics-waarschuwings-API](api-alerts.md) hoeft u de waarschuwingen definitie,-query of-configuratie op geen enkele manier te wijzigen. Uw waarschuwings regels en controle worden niet beïnvloed en de waarschuwingen worden niet gestopt of niet meer tijdens of na de switch. De enige wijzigingen zijn:

- Een wijziging in de API-voor keur en toegang tot uw regels via een nieuwe API.
- Een gewijzigde resource-URI voor een waarschuwings regel met de Id's die worden gebruikt in de [verouderde API voor log Analytics waarschuwingen](api-alerts.md) in plaats van de naam van de waarschuwings regel in deze structuur `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`. De weergave naam van de waarschuwings regel blijft ongewijzigd.

Elke klant die vrijwillig wil overschakelen naar de nieuwe [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) en het gebruik van de [verouderde log Analytics alert-API](api-alerts.md)wilt blok keren. kan dit doen door een PUT-aanroep uit te voeren op de onderstaande API om alle waarschuwings regels te wijzigen die zijn gekoppeld aan de specifieke Log Analytics-werk ruimte.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Met de aanvraag tekst die de onderstaande JSON bevat.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

De API kan ook worden geopend vanuit een Power shell-opdracht regel met behulp van [ARMClient](https://github.com/projectkudu/ARMClient), een open-source opdracht regel programma dat het aanroepen van de Azure Resource Manager-API vereenvoudigt. Zoals hieronder wordt beschreven, kunt u met behulp van de ARMclient-functie aanroepen gebruiken om alle waarschuwings regels te wijzigen die zijn gekoppeld aan de specifieke Log Analytics-werk ruimte.

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Als overschakelen van alle waarschuwings regels in de Log Analytics werk ruimte om nieuwe [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) te gebruiken is geslaagd, wordt de volgende reactie gegeven.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Gebruikers kunnen ook de huidige status van uw Log Analytics-werk ruimte controleren en zien of deze al dan niet is overgeschakeld om alleen [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) te gebruiken. Om te controleren kunnen gebruikers een GET-aanroep uitvoeren op de onderstaande API.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Zie het onderstaande voor beeld als u het bovenstaande wilt uitvoeren met behulp van de Power shell-opdracht regel met het hulp programma [ARMClient](https://github.com/projectkudu/ARMClient) .

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Als de opgegeven Log Analytics werk ruimte is overgeschakeld om alleen [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) te gebruiken; vervolgens wordt de JSON van de reactie zoals hieronder weer gegeven.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Als de opgegeven log analytic-werk ruimte nog niet is overgeschakeld om alleen [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) te gebruiken; vervolgens wordt de JSON van de reactie zoals hieronder weer gegeven.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [waarschuwingen voor Azure monitor logboeken](alerts-unified-log.md).
- Meer informatie over het maken [van logboek waarschuwingen in azure-waarschuwingen](alerts-log.md).
- Meer informatie over de [Azure Alerts-ervaring](../../azure-monitor/platform/alerts-overview.md).
