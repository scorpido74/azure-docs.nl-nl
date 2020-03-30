---
title: Api voor waarschuwing van logboekanalyse gebruiken
description: Met de API voor log-analyses alert rest u waarschuwingen maken en beheren in Log Analytics, dat onderdeel is van Log Analytics.  In dit artikel vindt u details van de API en verschillende voorbeelden voor het uitvoeren van verschillende bewerkingen.
ms.subservice: logs
ms.topic: conceptual
ms.date: 07/29/2018
ms.openlocfilehash: a85dad2ba638505233e5df769e55fa5bd7b8dafd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664997"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Waarschuwingsregels maken en beheren in Log Analytics met REST API 

Met de API voor log-analyses alert rest u waarschuwingen maken en beheren in Log Analytics.  In dit artikel vindt u details van de API en verschillende voorbeelden voor het uitvoeren van verschillende bewerkingen.

> [!IMPORTANT]
> Zoals [eerder aangekondigd](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/), log analytics workspace(s) gemaakt na 1 juni *2019* - in staat zal zijn om waarschuwingsregels te beheren met behulp van **alleen** Azure scheduledQueryRules [REST API,](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)Azure Resource [Mananger Template](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) en [PowerShell cmdlet](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell). Klanten kunnen eenvoudig [hun favoriete manier van waarschuwingsregelbeheer](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) voor oudere werkruimten om Azure Monitor scheduledQueryRules standaard te gebruiken en veel nieuwe [voordelen](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) te behalen, zoals de mogelijkheid om native PowerShell-cmdlets te gebruiken, langere kijktijdin regels, het maken van regels in afzonderlijke resourcegroep of -abonnement en nog veel meer.

De Log Analytics Search REST API is RESTful en is toegankelijk via de Azure Resource Manager REST API. In dit document vindt u voorbeelden waarin de API wordt geopend vanaf een PowerShell-opdrachtregel met [ARMClient](https://github.com/projectkudu/ARMClient), een open-source opdrachtregelhulpprogramma dat het inroepen van de Azure Resource Manager-API vereenvoudigt. Het gebruik van ARMClient en PowerShell is een van de vele opties om toegang te krijgen tot de Log Analytics Search API. Met deze hulpprogramma's u de RESTful Azure Resource Manager API gebruiken om aanteroepen naar Log Analytics-werkruimten en zoekopdrachten binnen deze hulpprogramma's uit te voeren. De API zal de zoekresultaten naar u uitbrengen in JSON-indeling, zodat u de zoekresultaten op veel verschillende manieren programmatisch gebruiken.

## <a name="prerequisites"></a>Vereisten
Momenteel kunnen waarschuwingen alleen worden gemaakt met een opgeslagen zoekopdracht in Log Analytics.  Voor meer informatie u verwijzen naar de [API Log Search REST.](../../azure-monitor/log-query/log-query-overview.md)

## <a name="schedules"></a>Planningen
Een opgeslagen zoekopdracht kan een of meer schema's hebben. De planning bepaalt hoe vaak de zoekopdracht wordt uitgevoerd en het tijdsinterval waarop de criteria worden geïdentificeerd.
Schema's hebben de eigenschappen in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| Interval |Hoe vaak de zoekopdracht wordt uitgevoerd. Gemeten in minuten. |
| QueryTimeSpan |Het tijdsinterval waarop de criteria worden geëvalueerd. Moet gelijk zijn aan of groter zijn dan Interval. Gemeten in minuten. |
| Versie |De API-versie die wordt gebruikt.  Momenteel moet dit altijd worden ingesteld op 1. |

Overweeg bijvoorbeeld een gebeurtenisquery met een interval van 15 minuten en een tijdspanne van 30 minuten. In dit geval wordt de query elke 15 minuten uitgevoerd en wordt een waarschuwing geactiveerd als de criteria gedurende een periode van 30 minuten blijven worden opgelost.

### <a name="retrieving-schedules"></a>Schema's ophalen
Gebruik de methode Ophalen om alle schema's voor een opgeslagen zoekopdracht op te halen.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Gebruik de methode Ophalen met een plannings-id om een bepaald schema op te halen voor een opgeslagen zoekopdracht.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Hieronder volgt een voorbeeldreactie voor een schema.

```json
{
    "value": [{
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/sampleRG/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
            "Interval": 15,
            "QueryTimeSpan": 15,
            "Enabled": true,
        }
    }]
}
```

### <a name="creating-a-schedule"></a>Een planning maken
Gebruik de methode Put met een unieke plannings-ID om een nieuw schema te maken.  Twee schema's kunnen niet dezelfde ID hebben, zelfs als ze zijn gekoppeld aan verschillende opgeslagen zoekopdrachten.  Wanneer u een planning maakt in de Log Analytics-console, wordt een GUID gemaakt voor de plannings-id.

> [!NOTE]
> De naam voor alle opgeslagen zoekopdrachten, planningen en acties die met de Api loganalytics zijn gemaakt, moet in kleine letters zijn uitgevoerd.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Een schema bewerken
Gebruik de methode Zetten met een bestaande plannings-id voor dezelfde opgeslagen zoekopdracht om dat schema te wijzigen. in voorbeeld onder het schema is uitgeschakeld. De instantie van het verzoek moet het *tijdschema* bevatten.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Schema's verwijderen
Gebruik de methode Verwijderen met een plannings-id om een planning te verwijderen.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Acties
Een planning kan meerdere acties hebben. Een actie kan een of meer processen definiëren die moeten worden uitgevoerd, zoals het verzenden van een e-mail of het starten van een runbook, of het kan een drempelwaarde definiëren die bepaalt wanneer de resultaten van een zoekopdracht overeenkomen met bepaalde criteria.  Sommige acties definiëren beide, zodat de processen worden uitgevoerd wanneer de drempel wordt bereikt.

Alle acties hebben de eigenschappen in de volgende tabel.  Verschillende soorten waarschuwingen hebben verschillende extra eigenschappen, die hieronder worden beschreven.

| Eigenschap | Beschrijving |
|:--- |:--- |
| `Type` |Type van de actie.  Momenteel zijn de mogelijke waarden Alert en Webhook. |
| `Name` |Geef de naam voor de waarschuwing weer. |
| `Version` |De API-versie die wordt gebruikt.  Momenteel moet dit altijd worden ingesteld op 1. |

### <a name="retrieving-actions"></a>Acties ophalen

Gebruik de methode Ophalen om alle acties voor een planning op te halen.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Gebruik de methode Ophalen met de actie-id om een bepaalde actie voor een planning op te halen.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Acties maken of bewerken
Gebruik de methode Put met een actie-id die uniek is voor de planning om een nieuwe actie te maken.  Wanneer u een actie maakt in de Log Analytics-console, is een GUID voor de actie-id.

> [!NOTE]
> De naam voor alle opgeslagen zoekopdrachten, planningen en acties die met de Api loganalytics zijn gemaakt, moet in kleine letters zijn uitgevoerd.

Gebruik de methode Zetten met een bestaande actie-id voor dezelfde opgeslagen zoekopdracht om dat schema te wijzigen.  De instantie van het verzoek moet het tijdschema bevatten.

De aanvraagindeling voor het maken van een nieuwe actie verschilt per actietype, zodat deze voorbeelden worden weergegeven in de onderstaande secties.

### <a name="deleting-actions"></a>Acties verwijderen

Gebruik de methode Verwijderen met de actie-id om een actie te verwijderen.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Waarschuwingsacties
Een schema moet één en slechts één waarschuwingsactie hebben.  Waarschuwingsacties hebben een of meer van de secties in de volgende tabel.  Elk wordt hieronder in meer detail beschreven.

| Sectie | Beschrijving | Gebruik |
|:--- |:--- |:--- |
| Drempelwaarde |Criteria voor wanneer de actie wordt uitgevoerd.| Vereist voor elke waarschuwing, voor of na de uitbreiding naar Azure. |
| Severity |Label dat wordt gebruikt om waarschuwing te classificeren wanneer deze wordt geactiveerd.| Vereist voor elke waarschuwing, voor of na de uitbreiding naar Azure. |
| Onderdrukken |Optie om meldingen van waarschuwingen te stoppen. | Optioneel voor elke waarschuwing, voor of na de uitbreiding naar Azure. |
| Actiegroepen |Id's van Azure ActionGroup waar vereiste acties zijn opgegeven, zoals - E-mails, KMO's, spraakoproepen, webhooks, automatiseringsrunbooks, ITSM-connectoren, enz.| Vereist zodra waarschuwingen zijn uitgebreid naar Azure|
| Acties aanpassen|De standaarduitvoer voor geselecteerde acties uit actiegroepen wijzigen| Optioneel voor elke waarschuwing, kan worden gebruikt nadat waarschuwingen zijn uitgebreid naar Azure. |

### <a name="thresholds"></a>Drempels
Een waarschuwingsactie moet één en slechts één drempelwaarde hebben.  Wanneer de resultaten van een opgeslagen zoekopdracht overeenkomen met de drempel waarde in een actie die aan die zoekopdracht is gekoppeld, worden alle andere processen in die actie uitgevoerd.  Een actie kan ook alleen een drempelwaarde bevatten, zodat deze kan worden gebruikt met acties van andere typen die geen drempelwaarden bevatten.

Drempelwaarden hebben de eigenschappen in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| `Operator` |Operator voor de drempelvergelijking. <br> gt = groter dan <br> lt = Minder dan |
| `Value` |Waarde voor de drempel. |

Overweeg bijvoorbeeld een gebeurtenisquery met een interval van 15 minuten, een tijdspanne van 30 minuten en een drempelwaarde van meer dan 10 minuten. In dit geval wordt de query elke 15 minuten uitgevoerd en wordt een waarschuwing geactiveerd als deze 10 gebeurtenissen heeft geretourneerd die gedurende een periode van 30 minuten zijn gemaakt.

Hieronder volgt een voorbeeldreactie voor een actie met alleen een drempelwaarde.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

Gebruik de methode Put met een unieke actie-id om een nieuwe drempelactie voor een planning te maken.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Gebruik de methode Put met een bestaande actie-id om een drempelactie voor een planning te wijzigen.  De instantie van het verzoek moet het etag van de actie bevatten.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Severity
Met Log Analytics u uw waarschuwingen classificeren in categorieën, zodat u eenvoudiger beheer en triage beheren. De gedefinieerde ernst van de waarschuwing is: informatief, waarschuwing en kritiek. Deze worden toegewezen aan de genormaliseerde ernstschaal van Azure Alerts als:

|Ernstniveau van Log Analytics  |Ernstniveau azure-waarschuwingen  |
|---------|---------|
|`critical` |Sev 0|
|`warning` |Sev 1|
|`informational` | Sev 2|

Hieronder volgt een voorbeeldreactie voor een actie met alleen een drempel waardedrempel en ernst. 

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Severity": "critical",
        "Version": 1    }

Gebruik de methode Put met een unieke actie-id om een nieuwe actie te maken voor een schema met ernst.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Gebruik de methode Put met een bestaande actie-id om een ernstactie voor een planning te wijzigen.  De instantie van het verzoek moet het etag van de actie bevatten.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="suppress"></a>Onderdrukken
Op log analytics gebaseerde querywaarschuwingen worden elke keer dat de drempel waarde wordt bereikt of overschreden, afgenomen. Op basis van de logica die in de query wordt geïmpliceerd, kan dit ertoe leiden dat waarschuwingen worden afgevuurd voor een reeks intervallen en dus meldingen die ook voortdurend worden verzonden. Om een dergelijk scenario te voorkomen, kan een gebruiker de optie Onderdrukken instellen door Log Analytics te instrueren om een bepaalde tijd te wachten voordat de melding de tweede keer wordt geactiveerd voor de waarschuwingsregel. Dus als onderdrukken is ingesteld voor 30 minuten; vervolgens wordt de eerste keer geactiveerd en worden meldingen geconfigureerd verzonden. Maar wacht dan 30 minuten, voordat de melding voor de waarschuwingsregel opnieuw wordt gebruikt. In de tussentijdse periode blijft de waarschuwingsregel worden uitgevoerd: alleen meldingen worden door Log Analytics voor bepaalde tijd onderdrukt, ongeacht hoe vaak de waarschuwingsregel in deze periode is geactiveerd.

De eigenschap Onderdrukken van de waarschuwingregel Log Analytics wordt opgegeven met behulp van de *waarde Beperking* en de onderdrukkingsperiode met de waarde *DurationInMinutes.*

Hieronder volgt een voorbeeldreactie voor een actie met alleen een drempelwaarde, ernst en onderdrukking

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Throttling": {
          "DurationInMinutes": 30
        },
        "Severity": "critical",
        "Version": 1    }

Gebruik de methode Put met een unieke actie-id om een nieuwe actie te maken voor een schema met ernst.  

    $AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

Gebruik de methode Put met een bestaande actie-id om een ernstactie voor een planning te wijzigen.  De instantie van het verzoek moet het etag van de actie bevatten.

    $AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

#### <a name="action-groups"></a>Actiegroepen
Alle waarschuwingen in Azure gebruiken actiegroepen als het standaardmechanisme voor het afhandelen van acties. Met Actiegroep u uw acties één keer opgeven en de actiegroep vervolgens koppelen aan meerdere waarschuwingen in Azure. Zonder de noodzaak, herhaaldelijk dezelfde acties te verklaren over en weer. Actiegroepen ondersteunen meerdere acties - waaronder e-mail, SMS, Voice Call, ITSM Connection, Automation Runbook, Webhook URI en meer. 

Voor gebruikers die hun waarschuwingen hebben uitgebreid naar Azure - een planning moet nu de details van de actiegroep hebben doorgegeven, samen met drempelwaarde, om een waarschuwing te kunnen maken. E-mailgegevens, Webhook-URL's, details over runbook automation en andere acties moeten eerst in een groep naast elkaar worden gedefinieerd voordat een waarschuwing wordt gegenereerd; Men kan [actiegroepen](../../azure-monitor/platform/action-groups.md) maken vanuit Azure Monitor in Portal of [action group API](https://docs.microsoft.com/rest/api/monitor/actiongroups)gebruiken.

Als u de koppeling van de actiegroep wilt toevoegen aan een waarschuwing, geeft u de unieke Azure Resource Manager-id van de actiegroep op in de waarschuwingsdefinitie. Hieronder vindt u een voorbeeldillustratie:

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
        },
        "Severity": "critical",
        "Version": 1
      },

Gebruik de methode Put met een unieke actie-id om reeds bestaande actiegroepen te koppelen aan een planning.  Het volgende is een voorbeeld illustratie van het gebruik.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Gebruik de methode Zetten met een bestaande actie-id om een actiegroep te wijzigen die is gekoppeld aan een planning.  De instantie van het verzoek moet het etag van de actie bevatten.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': { 'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'] } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Acties aanpassen
Volg standaardacties de standaardsjabloon en -indeling voor meldingen. Maar de gebruiker kan sommige acties aanpassen, zelfs als ze worden gecontroleerd door actiegroepen. Momenteel is maatwerk mogelijk voor E-mail onderwerp en Webhook Payload.

##### <a name="customize-e-mail-subject-for-action-group"></a>E-mailonderwerp aanpassen voor actiegroep
Standaard is het e-mailonderwerp voor `<AlertName>` `<WorkspaceName>`waarschuwingen: Waarschuwing melding voor . Maar dit kan worden aangepast, zodat u specifieke woorden of tags - zodat u eenvoudig filterregels in uw Postvak IN gebruiken. De details van de e-mailheader moeten samen met actiongroup-gegevens worden verzonden, zoals in onderstaand voorbeeld.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ],
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Gebruik de methode Put met een unieke actie-id om reeds bestaande actiegroepte te koppelen aan aanpassing voor een planning.  Het volgende is een voorbeeld illustratie van het gebruik.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Gebruik de methode Zetten met een bestaande actie-id om een actiegroep te wijzigen die is gekoppeld aan een planning.  De instantie van het verzoek moet het etag van de actie bevatten.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>Webhook-payload aanpassen voor actiegroep
Standaard heeft de webhook die via Action Group voor loganalytics wordt verzonden een vaste structuur. Maar men kan de JSON payload aanpassen met behulp van specifieke variabelen ondersteund, om te voldoen aan de eisen van de webhook eindpunt. Zie [Webhook-actie voor logboekwaarschuwingsregels voor](../../azure-monitor/platform/alerts-log-webhook.md)meer informatie . 

De webhook-details moeten samen met actiongroup-gegevens worden verzonden en worden toegepast op alle Webhook URI die in de actiegroep is opgegeven; zoals in onderstaand voorbeeld.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ],
          "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Gebruik de methode Put met een unieke actie-id om reeds bestaande actiegroepte te koppelen aan aanpassing voor een planning.  Het volgende is een voorbeeld illustratie van het gebruik.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Gebruik de methode Zetten met een bestaande actie-id om een actiegroep te wijzigen die is gekoppeld aan een planning.  De instantie van het verzoek moet het etag van de actie bevatten.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson


## <a name="next-steps"></a>Volgende stappen

* Gebruik de [REST API om logboekzoekopdrachten uit te voeren](../../azure-monitor/log-query/log-query-overview.md) in Log Analytics.
* Meer informatie over [logboekwaarschuwingen in Azure-monitor](../../azure-monitor/platform/alerts-unified-log.md)
* [Logboekwaarschuwingsregels maken, bewerken of beheren in Azure-monitor](../../azure-monitor/platform/alerts-log.md)

