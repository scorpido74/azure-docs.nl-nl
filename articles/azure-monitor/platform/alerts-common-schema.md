---
title: Algemeen waarschuwingsschema voor Azure-monitorwaarschuwingen
description: Inzicht in het algemene waarschuwingsschema, waarom u het moet gebruiken en hoe u het inschakelen
ms.topic: conceptual
ms.subservice: alerts
ms.date: 03/14/2019
ms.openlocfilehash: 1445e8cf38b2694146fc8749ba5e77f2297de969
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249046"
---
# <a name="common-alert-schema"></a>Algemeen waarschuwingsschema

In dit artikel wordt beschreven wat het algemene waarschuwingsschema is, wat de voordelen zijn van het gebruik ervan en hoe u dit inschakelen.

## <a name="what-is-the-common-alert-schema"></a>Wat is het algemene waarschuwingsschema?

Het algemene waarschuwingsschema standaardiseert vandaag de dag de verbruikservaring voor waarschuwingsmeldingen in Azure. Historisch gezien hebben de drie waarschuwingstypen in Azure vandaag (metrische, logboek- en activiteitenlogboek) hun eigen e-mailsjablonen, webhook-schema's, enz. Met het algemene waarschuwingsschema u nu waarschuwingsmeldingen ontvangen met een consistent schema.

Elke waarschuwingsinstantie beschrijft **de bron die is beïnvloed** en de oorzaak van de **waarschuwing**en deze instanties worden beschreven in het algemene schema in de volgende secties:
* **Essentials**: Een set **gestandaardiseerde velden,** algemeen voor alle waarschuwingstypen, die beschrijven **op welke resource** de waarschuwing is ingeschakeld, samen met extra algemene waarschuwingsmetagegevens (bijvoorbeeld ernst of beschrijving). 
* **Waarschuwingscontext:** een reeks velden die de **oorzaak van de waarschuwing**beschrijven, met velden die variëren op basis van het **waarschuwingstype**. Een metrische waarschuwing heeft bijvoorbeeld velden zoals de metrische naam en metrische waarde in de waarschuwingscontext, terwijl een waarschuwing voor het activiteitenlogboek informatie zou bevatten over de gebeurtenis die de waarschuwing heeft gegenereerd. 

De typische integratiescenario's die we van klanten horen, omvatten de routering van de waarschuwingsinstantie naar het betrokken team op basis van een spil (bijvoorbeeld resourcegroep), waarna het verantwoordelijke team eraan begint te werken. Met het algemene waarschuwingsschema u gestandaardiseerde routeringslogica hebben voor alle waarschuwingstypen door gebruik te maken van de essentiële velden, waardoor de contextvelden zoals de betrokken teams verder kunnen worden onderzocht.

Dit betekent dat u mogelijk minder integraties hebben, waardoor het proces van beheer en onderhoud ervan een _veel_ eenvoudigere taak wordt. Bovendien komen toekomstige waarschuwingspayloadverrijkingen (bijvoorbeeld aanpassing, diagnostische verrijking, enz.) alleen in het algemene schema naar boven.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Welke verbeteringen brengt het algemene waarschuwingsschema?

Het algemene waarschuwingsschema manifesteert zich voornamelijk in uw waarschuwingsmeldingen. De verbeteringen die u ziet zijn hieronder vermeld:

| Actie | Verbeteringen |
|:---|:---|
| Sms | Een consistente sms-sjabloon voor alle waarschuwingstypen. |
| Email | Een consistente en gedetailleerde e-mailsjabloon, zodat u problemen eenvoudig in één oogopslag diagnosticeren. Ingesloten deep-links naar de waarschuwingsinstantie op de portal en de betreffende resource zorgen ervoor dat u snel in het herstelproces springen. |
| Webhook/Logic App/Azure-functie/automatiseringsrunboek | Een consistente JSON-structuur voor alle waarschuwingstypen, waarmee u eenvoudig integraties bouwen tussen de verschillende waarschuwingstypen. |

Het nieuwe schema maakt ook een rijkere ervaring met waarschuwingsverbruik mogelijk in zowel de Azure-portal als de mobiele Azure-app in de nabije toekomst. 

[Meer informatie over de schemadefinities voor Webhooks/Logic Apps/Azure Functions/Automation Runbooks.](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> De volgende acties ondersteunen niet het algemene waarschuwingsschema: ITSM Connector.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Hoe schakel ik het algemene waarschuwingsschema in?

U zich aanmelden voor het algemene waarschuwingsschema via actiegroepen, zowel op de portal als via de REST API. De schakelschakelaar om over te schakelen naar het nieuwe schema bestaat op actieniveau. U moet zich bijvoorbeeld afzonderlijk aanmelden voor een e-mailactie en een webhook-actie.

> [!NOTE]
> 1. De volgende waarschuwingstypen ondersteunen standaard het algemene schema (geen opt-in vereist):
>     * Slimme detectiewaarschuwingen
> 1. De volgende waarschuwingstypen ondersteunen momenteel het algemene schema niet:
>     * Waarschuwingen gegenereerd door [Azure Monitor voor VM's](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)
>     * Waarschuwingen gegenereerd door [Azure Cost Management](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario)

### <a name="through-the-azure-portal"></a>Via de Azure-portal

![Opt-in van het algemene waarschuwingsschema](media/alerts-common-schema/portal-opt-in.png)

1. Open een bestaande of een nieuwe actie in een actiegroep. 
1. Selecteer 'Ja' voor de wisselknop om het algemene waarschuwingsschema in te schakelen zoals weergegeven.

### <a name="through-the-action-groups-rest-api"></a>Via de API voor actiegroepen REST

U de [API voor actiegroepen](https://docs.microsoft.com/rest/api/monitor/actiongroups) ook gebruiken om u aan te melden voor het algemene waarschuwingsschema. Tijdens het [maken of bijwerken](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) van de REST API-aanroep u de vlag 'useCommonAlertSchema' instellen op 'true' (om u aan te melden) of 'false' (om u af te melden) voor een van de volgende acties - e-mail/webhook/logic app/Azure Function/automation runbook.

De volgende aanvraaginstantie die is gemaakt naar de [API voor het maken of bijwerken van](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) rest, gaat bijvoorbeeld als volgt te werk:

* Het algemene waarschuwingsschema inschakelen voor de e-mailactie 'De e-mail van John Doe'
* Schakel het algemene waarschuwingsschema voor de e-mailactie 'E-mail van Jane Smith' uit
* Het algemene waarschuwingsschema inschakelen voor de webhook-actie 'Voorbeeld webhook'

```json
{
  "properties": {
    "groupShortName": "sample",
    "enabled": true,
    "emailReceivers": [
      {
        "name": "John Doe's email",
        "emailAddress": "johndoe@email.com",
        "useCommonAlertSchema": true
      },
      {
        "name": "Jane Smith's email",
        "emailAddress": "janesmith@email.com",
        "useCommonAlertSchema": false
      }
    ],
    "smsReceivers": [
      {
        "name": "John Doe's mobile",
        "countryCode": "1",
        "phoneNumber": "1234567890"
      },
      {
        "name": "Jane Smith's mobile",
        "countryCode": "1",
        "phoneNumber": "0987654321"
      }
    ],
    "webhookReceivers": [
      {
        "name": "Sample webhook",
        "serviceUri": "http://www.example.com/webhook",
        "useCommonAlertSchema": true
      }
    ]
  },
  "location": "Global",
  "tags": {}
}
```





## <a name="next-steps"></a>Volgende stappen

- [Algemene waarschuwingsschemadefinities voor Webhooks/Logic Apps/Azure Functions/Automation Runbooks.](https://aka.ms/commonAlertSchemaDefinitions)
- [Meer informatie over het maken van een logische app die gebruikmaakt van het algemene waarschuwingsschema om al uw waarschuwingen te verwerken.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-integrations) 



