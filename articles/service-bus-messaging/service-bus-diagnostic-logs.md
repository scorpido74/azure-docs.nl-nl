---
title: Diagnostische logboeken azure servicebus | Microsoft Documenten
description: In dit artikel vindt u een overzicht van alle operationele en diagnostische logboeken die beschikbaar zijn voor Azure Service Bus.
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: a80fb97810fee04a4eb50c43178c168e66f29173
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618731"
---
# <a name="enable-diagnostics-logs-for-service-bus"></a>Diagnostische logboeken inschakelen voor Service Bus

Wanneer u de naamruimte van uw Azure Service Bus gaat gebruiken, u controleren hoe en wanneer uw naamruimte wordt gemaakt, verwijderd of geopend. Dit artikel geeft een overzicht van alle operationele en diagnostische logboeken die beschikbaar zijn.

Azure Service Bus ondersteunt momenteel activiteits- en operationele logboeken, die *beheerbewerkingen* vastleggen die worden uitgevoerd in de naamruimte van Azure Service Bus. In het bijzonder leggen deze logboeken het bewerkingstype vast, inclusief het maken van wachtrijen, gebruikte resources en de status van de bewerking.

## <a name="operational-logs-schema"></a>Schema voor operationele logboeken

Alle logboeken worden op de volgende twee locaties opgeslagen in de JSON-indeling (JavaScript Object Notation):

- **AzureActivity:** hiermee worden logboeken weergegeven van bewerkingen en acties die worden uitgevoerd tegen uw naamruimte in de Azure-portal of via azure resource manager-sjabloonimplementaties.
- **AzureDiagnostics:** hiermee worden logboeken weergegeven van bewerkingen en acties die worden uitgevoerd tegen uw naamruimte met behulp van de API of via beheerclients op de taal SDK.

Json-tekenreeksen voor operationele logboeken bevatten de elementen in de volgende tabel:

| Name | Beschrijving |
| ------- | ------- |
| ActivityId (ActivityId) | Interne ID, gebruikt om de opgegeven activiteit te identificeren |
| Gebeurtenisnaam | Naam van bewerking |
| ResourceId | Azure Resource Manager-bron-id |
| SubscriptionId | Abonnements-id |
| EventTimeString | Bewerkingstijd |
| EventProperties | Bewerkingseigenschappen |
| Status | Bewerkingsstatus |
| Caller | Beller van bewerking (de Azure-portal of beheerclient) |
| Categorie | OperationalLogs (OperationalLogs) |

Hier is een voorbeeld van een operationele log JSON-tekenreeks:

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

## <a name="events-and-operations-captured-in-operational-logs"></a>Gebeurtenissen en bewerkingen vastgelegd in operationele logboeken

Operationele logboeken leggen alle beheerbewerkingen vast die worden uitgevoerd op de naamruimte van Azure Service Bus. Gegevensbewerkingen worden niet vastgelegd vanwege het grote aantal gegevensbewerkingen dat wordt uitgevoerd op Azure Service Bus.

> [!NOTE]
> Om u te helpen gegevensbewerkingen beter bij te houden, raden we u aan om client-side tracing te gebruiken.

De volgende beheerbewerkingen worden vastgelegd in operationele logboeken: 

| Bereik | Bewerking|
|-------| -------- |
| Naamruimte | <ul> <li> Naamruimte maken</li> <li> Naamruimte bijwerken </li> <li> Naamruimte verwijderen </li> <li> Beleid voor gedeelde naamruimte bijwerken </li> </ul> | 
| Wachtrij | <ul> <li> Wachtrij maken</li> <li> Wachtrij bijwerken</li> <li> Wachtrij verwijderen </li> <li> Wachtrij automatisch verwijderen </li> </ul> | 
| Onderwerp | <ul> <li> Onderwerp maken </li> <li> Onderwerp bijwerken </li> <li> Onderwerp verwijderen </li> <li> Onderwerp automatisch verwijderen </li> </ul> |
| Abonnement | <ul> <li> Abonnement maken </li> <li> Abonnement bijwerken </li> <li> Abonnement verwijderen </li> <li> Abonnement automatisch verwijderen </li> </ul> |

> [!NOTE]
> Momenteel *worden Leesbewerkingen* niet bijgehouden in de operationele logboeken.

## <a name="enable-operational-logs"></a>Operationele logboeken inschakelen

Operationele logboeken zijn standaard uitgeschakeld. Ga als volgt te werk om diagnostische logboeken in te schakelen:

1. Ga in de [Azure-portal](https://portal.azure.com)naar de naamruimte van uw Azure Service Bus en selecteer vervolgens onder **Controle** **diagnostische instellingen**.

   ![De koppeling 'Diagnostische instellingen'](./media/service-bus-diagnostic-logs/image1.png)

1. Selecteer **diagnostische instelling toevoegen**in het deelvenster Diagnostische **instellingen** .  

   ![De koppeling Diagnostische instelling toevoegen](./media/service-bus-diagnostic-logs/image2.png)

1. Configureer de diagnostische instellingen door het volgende te doen:

   a. Voer in het vak **Naam** een naam in voor de diagnose-instellingen.  

   b. Selecteer een van de volgende drie bestemmingen voor uw diagnostische logboeken:  
   - Als u Archiveren selecteert **in een opslagaccount,** moet u het opslagaccount configureren waar de diagnostische logboeken worden opgeslagen.  
   - Als u **Streamen naar een gebeurtenishub**selecteert, moet u de gebeurtenishub configureren waarnaar u de diagnostische logboeken wilt streamen.
   - Als u **Verzenden naar logboekanalyse**selecteert, moet u opgeven naar welk exemplaar van Log Analytics de diagnose wordt verzonden.  

   c. Schakel het selectievakje **OperationalLogs** in.

    ![Het deelvenster 'Diagnostische instellingen'](./media/service-bus-diagnostic-logs/image3.png)

1. Selecteer **Opslaan**.

De nieuwe instellingen worden van kracht in ongeveer 10 minuten. De logboeken worden weergegeven in het geconfigureerde archiefdoel in het **deelvenster Logboeken diagnostische gegevens.**

Zie het [overzicht van Azure-diagnostische logboeken](../azure-monitor/platform/diagnostic-logs-overview.md)voor meer informatie over het configureren van diagnostische instellingen.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Service Bus:

* [Inleiding tot servicebus](service-bus-messaging-overview.md)
* [Aan de slag met Service Bus](service-bus-dotnet-get-started-with-queues.md)
