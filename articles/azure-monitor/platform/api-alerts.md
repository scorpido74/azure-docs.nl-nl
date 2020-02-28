---
title: Met behulp van Log Analytics Alert REST API
description: Met de Log Analytics waarschuwing REST API kunt u waarschuwingen maken en beheren in Log Analytics, die deel uitmaakt van Log Analytics.  Dit artikel bevat informatie over de API en enkele voorbeelden voor het uitvoeren van verschillende bewerkingen.
ms.subservice: logs
ms.topic: conceptual
ms.date: 07/29/2018
ms.openlocfilehash: a85dad2ba638505233e5df769e55fa5bd7b8dafd
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664997"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Maken en beheren van regels voor waarschuwingen in Log Analytics met REST-API 

De Log Analytics Alert REST API kunt u waarschuwingen in Log Analytics maken en beheren.  Dit artikel bevat informatie over de API en enkele voorbeelden voor het uitvoeren van verschillende bewerkingen.

> [!IMPORTANT]
> Zoals [eerder is aangekondigd](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/), kunnen log Analytics-werk ruimten die zijn gemaakt na *1 juni 2019* , waarschuwings regels beheren met **alleen** Azure ScheduledQueryRules [rest API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/), de [Azure Resource Manager-sjabloon](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) en de [Power shell-cmdlet](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell). Klanten kunnen eenvoudig [hun favoriete middelen van waarschuwings regel beheer](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) voor oudere werk ruimten wisselen om Azure monitor scheduledQueryRules als standaard te gebruiken en veel [nieuwe voor delen](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) te verkrijgen, zoals de mogelijkheid om systeem eigen Power shell-cmdlets uit te scha kelen, een verhoogde lookback periode in regels, het maken van regels in een afzonderlijke resource groep of abonnement en nog veel meer.

De Log Analytics Search REST-API is RESTful en zijn toegankelijk via de Azure Resource Manager REST API. In dit document vindt u voor beelden waarin de API wordt geopend vanuit een Power shell-opdracht regel met behulp van [ARMClient](https://github.com/projectkudu/ARMClient), een open-source opdracht regel programma waarmee de Azure Resource Manager-API wordt vereenvoudigd. Het gebruik van ARMClient en PowerShell is een van de vele opties voor toegang tot de Log Analytics Search-API. Met deze hulpprogramma's, kunt u gebruikmaken van de RESTful API van Azure Resource Manager om aanroepen naar Log Analytics-werkruimten en zoeken in opdrachten in deze uitvoeren. De API uitvoer zoekresultaten aan u in JSON-indeling, zodat u kunt de lijst met zoekresultaten via een programma op veel verschillende manieren gebruiken.

## <a name="prerequisites"></a>Vereisten
Waarschuwingen kunnen op dit moment alleen worden gemaakt met een opgeslagen zoekopdracht in Log Analytics.  Raadpleeg de [rest API voor zoeken in Logboeken](../../azure-monitor/log-query/log-query-overview.md) voor meer informatie.

## <a name="schedules"></a>Planningen
Een opgeslagen zoekopdracht kan een of meer schema's hebben. Het schema wordt gedefinieerd hoe vaak de zoekopdracht wordt uitgevoerd en het tijdsinterval op waarover de criteria wordt geïdentificeerd.
Schema's hebben de eigenschappen in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| Interval |Hoe vaak de zoekopdracht wordt uitgevoerd. Gemeten in minuten. |
| QueryTimeSpan |Het tijdsinterval op waarover de criteria wordt geëvalueerd. Moet gelijk zijn aan of groter zijn dan het Interval. Gemeten in minuten. |
| Version |De API-versie die wordt gebruikt.  Op dit moment moet dit altijd worden ingesteld op 1. |

Bijvoorbeeld, kunt u een gebeurtenisquery met een Interval van 15 minuten en een periode van 30 minuten. In dit geval wordt de query wordt uitgevoerd om de 15 minuten, en een waarschuwing wordt geactiveerd als de criteria verder worden omgezet naar de waarde true via een reeks van 30 minuten.

### <a name="retrieving-schedules"></a>Bij het ophalen van schema 's
De Get-methode gebruiken om op te halen van alle schema's voor een opgeslagen zoekactie.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Gebruik de Get-methode met de ID van een schema om op te halen van een bepaalde planning voor een opgeslagen zoekactie.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Hieronder volgt een voorbeeldantwoord voor een schema.

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

### <a name="creating-a-schedule"></a>Het maken van een planning
Gebruik de Put-methode met de ID van een uniek schema een nieuw schema maken.  Twee schema's kunnen niet dezelfde ID hebben, zelfs als ze zijn gekoppeld aan verschillende opgeslagen Zoek opdrachten.  Wanneer u een planning in de Log Analytics-console maakt, wordt een GUID gemaakt voor de schema-ID.

> [!NOTE]
> De naam voor alle opgeslagen zoekacties, schema's en acties die zijn gemaakt met de Log Analytics-API moet in kleine letters.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Een schema bewerken
Gebruik de put-methode met een bestaande schema-ID voor dezelfde opgeslagen zoek opdracht om dat schema te wijzigen. in het onderstaande voor beeld is het schema uitgeschakeld. De hoofd tekst van de aanvraag moet de *ETAG* van het schema bevatten.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Schema's verwijderen
Gebruik de Delete-methode met de ID van een planning om te verwijderen van een schema.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Acties
Een planning kan meerdere acties hebben. Een actie kan definiëren een of meer processen om uit te voeren, zoals een e-mail wordt verzonden of een runbook starten, of het kan een drempelwaarde waarmee wordt bepaald wanneer de resultaten van een zoekopdracht voldoen aan bepaalde criteria definiëren.  Sommige acties worden beide definiëren, zodat de processen worden uitgevoerd wanneer de drempelwaarde wordt voldaan.

Alle acties hebben de eigenschappen in de volgende tabel.  Verschillende typen waarschuwingen hebben verschillende extra eigenschappen, worden hieronder beschreven.

| Eigenschap | Beschrijving |
|:--- |:--- |
| `Type` |Het type van de actie.  De mogelijke waarden zijn momenteel waarschuwings- en Webhook. |
| `Name` |Weergavenaam voor de waarschuwing. |
| `Version` |De API-versie die wordt gebruikt.  Op dit moment moet dit altijd worden ingesteld op 1. |

### <a name="retrieving-actions"></a>Bij het ophalen van acties

De Get-methode gebruiken om op te halen van alle acties voor een schema.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Gebruik de Get-methode met de actie-ID ophalen van een bepaalde actie voor een schema.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Het maken of bewerken van acties
De Put-methode gebruiken met een actie-ID die uniek is voor de planning te maken van een nieuwe actie.  Wanneer u een actie in de Log Analytics-console maakt, wordt een GUID wordt voor de actie-ID.

> [!NOTE]
> De naam voor alle opgeslagen zoekacties, schema's en acties die zijn gemaakt met de Log Analytics-API moet in kleine letters.

Gebruik de Put-methode met een bestaande actie-ID voor de dezelfde opgeslagen zoekopdracht om te wijzigen die planning.  De hoofdtekst van de aanvraag moet de etag van het schema bevatten.

De indeling van de aanvraag voor het maken van een nieuwe actie verschilt per actietype, zodat deze voorbeelden u in de volgende secties vindt.

### <a name="deleting-actions"></a>Acties verwijderen

Gebruik de Delete-methode met de actie-ID om te verwijderen van een actie.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Waarschuwingsacties
Een planning moet slechts één actie bij waarschuwing hebben.  Waarschuwingsacties zijn een of meer van de secties in de volgende tabel.  Elk wordt nader besproken die hieronder beschreven.

| Sectie | Beschrijving | Gebruik |
|:--- |:--- |:--- |
| Drempelwaarde |Criteria voor wanneer de actie wordt uitgevoerd.| Vereist voor elke waarschuwing voordat of nadat ze zijn uitgebreid naar Azure. |
| Severity |Het label dat wordt gebruikt om te classificeren waarschuwing wanneer ze worden geactiveerd.| Vereist voor elke waarschuwing voordat of nadat ze zijn uitgebreid naar Azure. |
| Onderdrukken |Optie voor het stoppen van meldingen van de waarschuwing. | Optioneel voor elke waarschuwing voordat of nadat ze zijn uitgebreid naar Azure. |
| Actiegroepen |Id's van Azure ActionGroup waar de acties die vereist zijn opgegeven, zoals - e-mailberichten, SMSs, gesprekken, Webhooks, Automation-Runbooks, ITSM-Connectors, enzovoort.| Vereist wanneer waarschuwingen worden uitgebreid naar Azure|
| Acties aanpassen|De standaarduitvoer voor select acties op basis van ActionGroup wijzigen| Optioneel voor elke waarschuwing kan worden gebruikt nadat er waarschuwingen zijn uitgebreid naar Azure. |

### <a name="thresholds"></a>Drempelwaarden
Een actie bij waarschuwing mag slechts één drempelwaarde hebben.  Als de resultaten van een opgeslagen zoekopdracht overeenkomen met de drempelwaarde in een actie die is gekoppeld aan die zoekopdracht, worden alle andere processen in die actie uitgevoerd.  Een actie kan ook alleen een drempelwaarde bevatten, zodat deze kan worden gebruikt met de acties die van andere typen die geen drempelwaarden bevatten.

Drempelwaarden hebben de eigenschappen in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| `Operator` |Operator voor de voor drempelwaardevergelijking. <br> gt = groter dan <br> lt = minder dan |
| `Value` |De waarde voor de drempelwaarde. |

Bijvoorbeeld, kunt u een gebeurtenisquery met een Interval van 15 minuten, een periode van 30 minuten en een drempelwaarde van meer dan 10. In dit geval wordt de query wordt uitgevoerd om de 15 minuten, en een waarschuwing wordt geactiveerd als het 10 gebeurtenissen die zijn gemaakt via een reeks van 30 minuten geretourneerd.

Hieronder volgt een voorbeeldantwoord voor een actie met een drempelwaarde overschrijdt.  

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

De Put-methode gebruiken met een unieke actie-ID te maken van een nieuwe drempelwaarde-actie voor een schema.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Gebruik de Put-methode met de ID van een bestaande actie om een actie van de drempelwaarde voor een schema.  De hoofdtekst van de aanvraag moet de etag van de actie bevatten.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Severity
Log Analytics kunt u uw waarschuwingen te classificeren in categorieën, waarmee eenvoudiger beheer en sorteren. De ernst van de waarschuwing die gedefinieerd is: informatief, waarschuwing en kritiek. Deze worden toegewezen aan de ernst van genormaliseerde schaal van Azure-waarschuwingen als:

|Ernstniveau van log Analytics  |Ernstniveau van waarschuwingen van Azure  |
|---------|---------|
|`critical` |Sev 0|
|`warning` |Sev 1|
|`informational` | Sev 2|

Hieronder volgt een voorbeeldantwoord voor een actie met alleen een drempelwaarde en ernst. 

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

Gebruik de Put-methode met een unieke actie-ID om een nieuwe actie voor een schema met ernst.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Gebruik de Put-methode met de ID van een bestaande actie om een actie van de ernst voor een schema.  De hoofdtekst van de aanvraag moet de etag van de actie bevatten.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="suppress"></a>Onderdrukken
Log Analytics op basis van query waarschuwingen geactiveerd telkens als drempelwaarde is bereikt of overschreden. Op basis van de logica in de query de impliciete, dit kan leiden tot ophalen voor een reeks intervallen geactiveerde waarschuwing en kan daarom meldingen ook verstuurd voortdurend. Om te voorkomen dat dergelijke scenario, kunt een gebruiker onderdrukken optie Log Analytics dat moet worden gewacht op een bepaald hoeveelheid tijd voordat de tweede keer voor de waarschuwingsregel wordt geactiveerd door melding instellen. Dus als onderdrukken is ingesteld voor 30 minuten. waarschuwing wordt vervolgens de eerste keer wordt gestart en verzenden van meldingen geconfigureerd. Maar wacht 30 minuten voordat de melding voor de waarschuwingsregel wordt opnieuw gebruikt. Waarschuwingsregel wordt nog uitgevoerd in de tussenperiode - alleen melding door Log Analytics is onderdrukt voor de opgegeven periode, ongeacht hoe vaak de waarschuwingsregel geactiveerd in deze periode.

De eigenschap onderdrukking van Log Analytics waarschuwings regel is opgegeven met behulp van de *beperkings* waarde en de onderdrukkings periode met de waarde *DurationInMinutes* .

Hier volgt een voor beeld van een antwoord voor een actie met alleen een drempel waarde, ernst en onderdrukking-eigenschap

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

Gebruik de Put-methode met een unieke actie-ID om een nieuwe actie voor een schema met ernst.  

    $AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

Gebruik de Put-methode met de ID van een bestaande actie om een actie van de ernst voor een schema.  De hoofdtekst van de aanvraag moet de etag van de actie bevatten.

    $AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

#### <a name="action-groups"></a>Actiegroepen
Alle waarschuwingen in Azure, gebruik actiegroep als het standaardmechanisme voor het verwerken van acties. Met de actiegroep, kunt u uw acties één keer opgeven en koppel vervolgens de actie die u wilt meerdere waarschuwingen - binnen Azure. Zonder de noodzaak om te declareren dezelfde acties herhaaldelijk telkens opnieuw. Actiegroepen ondersteuning voor meerdere acties - inclusief e-mail, SMS, Spraakoproep, ITSM-verbinding, Automation-Runbook, Webhook URI en meer. 

Voor gebruikers die hun waarschuwingen hebben uitgebreid naar Azure, moeten er nu actie groeps Details worden door gegeven aan de drempel waarde, om een waarschuwing te kunnen maken. E-mail gegevens, webhook-Url's, Details van Runbook-automatisering en andere acties moeten eerst worden gedefinieerd in een actie groep naast het maken van een waarschuwing. een kan [actie groep maken op basis van Azure monitor](../../azure-monitor/platform/action-groups.md) in portal of [actie groeps-API](https://docs.microsoft.com/rest/api/monitor/actiongroups)gebruiken.

Koppeling van de actiegroep toevoegen aan een waarschuwing, geef de unieke ID voor Azure Resource Manager van de actiegroep in het definitie van de waarschuwing. Hieronder vindt u een voorbeeld van afbeelding:

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

De Put-methode gebruiken met een unieke actie-ID te koppelen van bestaande actiegroep voor een schema.  Hier volgt een voorbeeld van afbeelding van het gebruik van.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Gebruik de Put-methode met de ID van een bestaande actie om een actiegroep die is gekoppeld voor een schema.  De hoofdtekst van de aanvraag moet de etag van de actie bevatten.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': { 'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'] } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Acties aanpassen
Door standaardacties, volgt u de standaardsjabloon en de indeling voor meldingen. Maar gebruiker bepaalde acties kunt aanpassen, zelfs als ze worden beheerd door Actiegroepen. Op dit moment is aanpassing mogelijk dat het onderwerp van E-mail en Webhook-nettolading.

##### <a name="customize-e-mail-subject-for-action-group"></a>E-mailonderwerp voor actiegroep aanpassen
Standaard is het e-mail onderwerp voor waarschuwingen: waarschuwings melding `<AlertName>` voor `<WorkspaceName>`. Maar dit kan worden aangepast, zodat u kunt specifieke woorden of tags - kunt u eenvoudig gebruikmaken van filterregels in uw postvak in. De informatie aanpassen per e-mail verzenden-header moeten verzenden, samen met ActionGroup-details conform onderstaand voorbeeld.

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

De Put-methode gebruiken met een unieke actie-ID al bestaande actiegroep koppelen aan een schema aanpassen.  Hier volgt een voorbeeld van afbeelding van het gebruik van.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Gebruik de Put-methode met de ID van een bestaande actie om een actiegroep die is gekoppeld voor een schema.  De hoofdtekst van de aanvraag moet de etag van de actie bevatten.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>Webhookpayload aanpassen voor de actiegroep
De webhook is verzonden via actiegroep voor log analytics heeft standaard een vaste structuur. Maar een de JSON-nettolading kunt aanpassen met behulp van specifieke variabelen die worden ondersteund, om te voldoen aan de vereisten van de webhook-eindpunt. Zie [webhook-actie voor waarschuwings regels voor logboeken](../../azure-monitor/platform/alerts-log-webhook.md)voor meer informatie. 

De details van de webhook aanpassen hoeven in te verzenden, samen met details van ActionGroup en worden toegepast op alle Webhook URI opgegeven binnen de actiegroep; conform onderstaand voorbeeld.

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

De Put-methode gebruiken met een unieke actie-ID al bestaande actiegroep koppelen aan een schema aanpassen.  Hier volgt een voorbeeld van afbeelding van het gebruik van.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Gebruik de Put-methode met de ID van een bestaande actie om een actiegroep die is gekoppeld voor een schema.  De hoofdtekst van de aanvraag moet de etag van de actie bevatten.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson


## <a name="next-steps"></a>Volgende stappen

* Gebruik de [rest API om Zoek opdrachten](../../azure-monitor/log-query/log-query-overview.md) in het logboek in log Analytics uit te voeren.
* Meer informatie over [logboek waarschuwingen in azure monitor](../../azure-monitor/platform/alerts-unified-log.md)
* [Waarschuwings regels voor logboeken maken, bewerken of beheren in azure monitor](../../azure-monitor/platform/alerts-log.md)

