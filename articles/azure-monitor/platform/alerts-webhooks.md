---
title: Een webhook bellen met een klassieke metrische waarschuwing in Azure Monitor
description: Meer informatie over het omleiden van Azure-metrische waarschuwingen naar andere niet-Azure-systemen.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: alerts
ms.openlocfilehash: 27510871f9a022cb27c6b03b812ce1d37b47312c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248981"
---
# <a name="call-a-webhook-with-a-classic-metric-alert-in-azure-monitor"></a>Een webhook bellen met een klassieke metrische waarschuwing in Azure Monitor

U webhooks gebruiken om een Azure-waarschuwing te routeren naar andere systemen voor nabewerking of aangepaste acties. U een webhook gebruiken bij een waarschuwing om deze te routeren naar services die sms-berichten verzenden, om bugs te registreren, om een team op de hoogte te stellen via chat- of berichtenservices of voor verschillende andere acties. 

In dit artikel wordt beschreven hoe u een webhook instelt op een Azure-metrische waarschuwing. Het toont u ook wat de payload voor de HTTP POST naar een webhook eruit ziet. Zie [Een webhook op een azure-statuslogboekwaarschuwing oproepen voor](alerts-log-webhook.md)informatie over de installatie en het schema voor een azure-activiteitslogboekwaarschuwing (waarschuwing voor gebeurtenissen).

Azure-waarschuwingen gebruiken HTTP POST om de waarschuwingsinhoud in JSON-indeling te verzenden naar een webhook URI die u opgeeft wanneer u de waarschuwing maakt. Het schema wordt later in dit artikel gedefinieerd. De URI moet een geldig HTTP- of HTTPS-eindpunt zijn. Azure plaatst één vermelding per aanvraag wanneer een waarschuwing wordt geactiveerd.

## <a name="configure-webhooks-via-the-azure-portal"></a>Webhooks configureren via de Azure-portal
Als u de webhook URI wilt toevoegen of bijwerken, gaat u in de [Azure-portal](https://portal.azure.com/)naar **Waarschuwingen maken/bijwerken**.

![Een waarschuwingsregelvenster toevoegen](./media/alerts-webhooks/Alertwebhook.png)

U ook een waarschuwing configureren om te posten op een webhook URI met [Azure PowerShell-cmdlets,](../../azure-monitor/platform/powershell-quickstart-samples.md#create-metric-alerts)een [cross-platform CLI](../../azure-monitor/platform/cli-samples.md#work-with-alerts)of [Azure Monitor REST API's.](https://msdn.microsoft.com/library/azure/dn933805.aspx)

## <a name="authenticate-the-webhook"></a>De webhook verifiëren
De webhook kan verifiëren met behulp van token-gebaseerde autorisatie. De webhook URI wordt opgeslagen met een token-ID. Bijvoorbeeld: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Payload-schema
De functie POST bevat de volgende JSON-payload en -schema voor alle waarschuwingen op basis van metrische gegevens:

```JSON
{
    "status": "Activated",
    "context": {
        "timestamp": "2015-08-14T22:26:41.9975398Z",
        "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
        "name": "ruleName1",
        "description": "some description",
        "conditionType": "Metric",
        "condition": {
            "metricName": "Requests",
            "metricUnit": "Count",
            "metricValue": "10",
            "threshold": "10",
            "windowSize": "15",
            "timeAggregation": "Average",
            "operator": "GreaterThanOrEqual"
        },
        "subscriptionId": "s1",
        "resourceGroupName": "useast",
        "resourceName": "mysite1",
        "resourceType": "microsoft.foo/sites",
        "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
        "resourceRegion": "centralus",
        "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
    },
    "properties": {
        "key1": "value1",
        "key2": "value2"
    }
}
```


| Veld | Verplicht | Vaste set waarden | Opmerkingen |
|:--- |:--- |:--- |:--- |
| status |J |Geactiveerd, opgelost |De status voor de waarschuwing op basis van de voorwaarden die u instelt. |
| context |J | |De waarschuwingscontext. |
| tijdstempel |J | |Het tijdstip waarop de waarschuwing is geactiveerd. |
| id |J | |Elke waarschuwingsregel heeft een unieke ID. |
| name |J | |De waarschuwingsnaam. |
| description |J | |Een beschrijving van de waarschuwing. |
| voorwaardeType |J |Metric, Gebeurtenis |Er worden twee soorten waarschuwingen ondersteund: metrische en gebeurtenis. Metrische waarschuwingen zijn gebaseerd op een metrische voorwaarde. Gebeurteniswaarschuwingen zijn gebaseerd op een gebeurtenis in het activiteitenlogboek. Gebruik deze waarde om te controleren of de waarschuwing is gebaseerd op een statistiek of op een gebeurtenis. |
| Voorwaarde |J | |De specifieke velden die u wilt controleren op basis van de **waarde conditionType.** |
| metricName |Voor metrische waarschuwingen | |De naam van de statistiek die definieert wat de regel bewaakt. |
| metricUnit |Voor metrische waarschuwingen |Bytes, Bytesperseconde, Telling, Countpersecond, Procent, Seconden |De eenheid is toegestaan in de statistiek. Zie [toegestane waarden](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metrische waarde |Voor metrische waarschuwingen | |De werkelijke waarde van de statistiek die de waarschuwing heeft veroorzaakt. |
| threshold |Voor metrische waarschuwingen | |De drempelwaarde waartegen de waarschuwing wordt geactiveerd. |
| windowSize |Voor metrische waarschuwingen | |De periode die wordt gebruikt om waarschuwingsactiviteiten te controleren op basis van de drempelwaarde. De waarde moet tussen de 5 minuten en 1 dag liggen. De waarde moet in de duurnotatie ISO 8601 staan. |
| timeAggregation |Voor metrische waarschuwingen |Gemiddelde, laatste, maximum, minimum, geen, totaal |Hoe de verzamelde gegevens in de loop van de tijd moeten worden gecombineerd. De standaardwaarde is Gemiddeld. Zie [toegestane waarden](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| operator |Voor metrische waarschuwingen | |De operator die wordt gebruikt om de huidige metrische gegevens te vergelijken met de ingestelde drempelwaarde. |
| subscriptionId |J | |De Azure-abonnements-ID. |
| resourceGroupName |J | |De naam van de resourcegroep voor de betreffende resource. |
| resourceName |J | |De resourcenaam van de getroffen resource. |
| resourceType |J | |Het resourcetype van de betreffende resource. |
| resourceId |J | |De resource-id van de betreffende resource. |
| resourceRegio |J | |De regio of locatie van de getroffen resource. |
| portalLink portalLink |J | |Een directe link naar de overzichtspagina van de portalbron. |
| properties |N |Optioneel |Een set sleutel-/waardeparen met details over de gebeurtenis. Bijvoorbeeld `Dictionary<String, String>`. Het veld Eigenschappen is optioneel. In een aangepaste ui- of logische app-gebaseerde workflow kunnen gebruikers sleutel-/waardeparen invoeren die via de payload kunnen worden doorgegeven. Een alternatieve manier om aangepaste eigenschappen terug te geven aan de webhook is via de webhook URI zelf (als queryparameters). |

> [!NOTE]
> U **het** eigenschappenveld alleen instellen met [Azure Monitor REST API's](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over Azure-waarschuwingen en webhooks in de video [Azure-waarschuwingen integreren met PagerDuty](https://go.microsoft.com/fwlink/?LinkId=627080).
* Meer informatie over het [uitvoeren van Azure Automation-scripts (runbooks) op Azure-waarschuwingen](https://go.microsoft.com/fwlink/?LinkId=627081).
* Meer informatie over het [gebruik van een logische app om een sms-bericht via Twilio te verzenden vanuit een Azure-waarschuwing.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
* Meer informatie over het [gebruik van een logische app om een Slack-bericht te verzenden vanuit een Azure-waarschuwing](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Meer informatie over het [gebruik van een logische app om een bericht naar een Azure-wachtrij te verzenden vanuit een Azure-waarschuwing](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).

