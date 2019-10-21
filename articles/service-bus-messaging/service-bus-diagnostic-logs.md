---
title: Diagnostische logboeken Azure Service Bus | Microsoft Docs
description: Meer informatie over het instellen van Diagnostische logboeken voor Service Bus in Azure.
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 41e0bdc1f04c9491ebe939f46b59ae4eb2bc7ab6
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592412"
---
# <a name="enable-diagnostic-logs-for-service-bus"></a>Diagnostische logboeken inschakelen voor Service Bus

Wanneer u begint met het gebruik van uw Azure Service Bus naam ruimte, wilt u wellicht controleren hoe en wanneer uw naam ruimte wordt gemaakt, verwijderd of geopend. Dit artikel bevat een overzicht van alle operationele/Diagnostische logboeken die beschikbaar zijn.

Azure Service Bus ondersteunt momenteel activiteiten/operationele logboeken voor het vastleggen van **beheer bewerkingen** die worden uitgevoerd op de Azure service bus naam ruimte. In deze logboeken wordt het bewerkings type vastgelegd, inclusief het maken van de wachtrij, de gebruikte resources en de status van de bewerking.

## <a name="operational-logs-schema"></a>Schema van operationele logboeken

Alle logboeken worden opgeslagen in de indeling JavaScript Object Notation (JSON) op de volgende twee locaties.

- **AzureActivity** : geeft logboeken weer van bewerkingen/acties die zijn uitgevoerd op uw naam ruimte op de portal of via Azure Resource Manager sjabloon implementaties.
- **AzureDiagnostics** : geeft logboeken weer van bewerkingen/acties die worden uitgevoerd op uw naam ruimte met behulp van de API of via beheer-clients in de taal-SDK.

De JSON-teken reeksen van het operationele logboek bevatten elementen die in de volgende tabel worden weer gegeven:

| Naam | Beschrijving |
| ------- | ------- |
| ActivityId | Interne ID, gebruikt om de opgegeven activiteit te identificeren |
| eventName | Naam van bewerking |
| ResourceId | Resource-ID Azure Resource Manager |
| SubscriptionId | Abonnements-id |
| EventTimeString | Bewerkings tijd |
| EventProperties | Bewerkings eigenschappen |
| Status | Bewerkingsstatus |
| Caller | Aanroeper van de bewerking (Azure Portal of Management-client) |
| Category | OperationalLogs |

Hier volgt een voor beeld van een JSON-teken reeks voor een operationeel logboek:

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="what-eventsoperations-are-captured-in-operational-logs"></a>Welke gebeurtenissen/bewerkingen worden vastgelegd in operationele logboeken?

In de bewerkings logboeken worden alle beheer bewerkingen vastgelegd die worden uitgevoerd op de naam ruimte Azure Service Bus. Gegevens bewerkingen worden niet vastgelegd vanwege de grote hoeveelheid gegevens bewerkingen die worden uitgevoerd op Azure Service Bus.

> [!NOTE]
> Als u gegevens bewerkingen beter wilt bijhouden, raden we u aan client zijde tracering te gebruiken.

De onderstaande beheer bewerkingen worden vastgelegd in operationele Logboeken. 

| Scope | Bewerking|
|-------| -------- |
| Naamruimte | <ul> <li> Naam ruimte maken</li> <li> Naam ruimte bijwerken </li> <li> Naam ruimte verwijderen </li>  </ul> | 
| Wachtrij | <ul> <li> Wachtrij maken</li> <li> Wachtrij bijwerken</li> <li> Wachtrij verwijderen </li> </ul> | 
| Onderwerp | <ul> <li> Onderwerp maken </li> <li> Onderwerp bijwerken </li> <li> Onderwerp verwijderen </li> </ul> |
| Abonnement | <ul> <li> Abonnement maken </li> <li> Abonnement bijwerken </li> <li> Abonnement verwijderen </li> </ul> |

> [!NOTE]
> Op dit moment worden **Lees** bewerkingen niet bijgehouden in de operationele Logboeken.

## <a name="how-to-enable-operational-logs"></a>Hoe kan ik operationele logboeken inschakelen?

Operationele logboeken zijn standaard uitgeschakeld. Als u Diagnostische logboeken wilt inschakelen, voert u de volgende stappen uit:

1. Navigeer in het [Azure Portal](https://portal.azure.com)naar uw Azure service bus naam ruimte en klik onder **bewaking**op **Diagnostische instellingen**.

   ![Blade navigatie naar Diagnostische logboeken](./media/service-bus-diagnostic-logs/image1.png)

2. Klik op **Diagnostische instelling toevoegen** om de diagnostische instellingen te configureren.  

   ![Diagnostische logboeken inschakelen](./media/service-bus-diagnostic-logs/image2.png)

3. De diagnostische instellingen configureren
   1. Typ een **naam** om de diagnostische instellingen te identificeren.
   2. Kies een doel voor de diagnostische gegevens.
      - Als u een **opslag account**kiest, moet u het opslag account configureren waarin de diagnostische gegevens worden opgeslagen.
      - Als u **Event hubs**selecteert, moet u de juiste Event hub configureren waar de diagnostische instellingen worden gestreamd.
      - Als u **log Analytics**kiest, moet u opgeven welk exemplaar van log Analytics de diagnostische gegevens worden verzonden.
    3. Controleer **OperationalLogs**.

       ![Diagnostische logboeken voor status wijzigen](./media/service-bus-diagnostic-logs/image3.png)

4. Klik op **Opslaan**.


Nieuwe instellingen worden in ongeveer 10 minuten van kracht. Daarna worden logboeken weer gegeven in het geconfigureerde archiverings doel op de Blade **Diagnostische logboeken** .

Zie voor meer informatie over het configureren van diagnostische gegevens het [overzicht van Azure Diagnostische logboeken](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende koppelingen voor meer informatie over Service Bus:

* [Inleiding tot Service Bus](service-bus-messaging-overview.md)
* [Aan de slag met Service Bus](service-bus-dotnet-get-started-with-queues.md)
