---
title: Metrische gegevens die door Azure Event Grid worden ondersteund
description: Dit artikel bevat Azure Monitor metrische gegevens die worden ondersteund door de Azure Event Grid-Service.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 3b22beafc9f88d2d95b25fd7ad2f2308a4df9097
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116417"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Metrische gegevens die door Azure Event Grid worden ondersteund
Dit artikel bevat een lijst met Event Grid metrische gegevens die door naam ruimten worden gecategoriseerd. 

## <a name="microsofteventgriddomains"></a>Micro soft. EventGrid/domeinen

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|
|PublishSuccessCount|Gepubliceerde gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|Onderwerp|
|PublishFailCount|Mislukte gebeurtenissen publiceren|Count|Totaal|Totaal aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|Onderwerp, error type, fout|
|PublishSuccessLatencyInMs|Latentie van publicatie geslaagd|Milliseconden|Totaal|Latentie van geslaagde publicatie in milliseconden|Geen|
|MatchedEventCount|Overeenkomende gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Mislukte leverings gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat niet aan dit gebeurtenis abonnement kan worden geleverd|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName, fout, error type|
|DeliverySuccessCount|Geleverde gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat aan dit gebeurtenis abonnement is geleverd|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Doel verwerkings duur|Milliseconden|Average|Doel verwerkings duur in milliseconden|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Verwijderde gebeurtenissen|Count|Totaal|Totaal aantal verloren gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|DeadLetteredCount|Gebeurtenissen met onbestelbare berichten|Count|Totaal|Totaal aantal gebeurtenissen met onbestelbare berichten die overeenkomen met dit gebeurtenis abonnement|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Micro soft. EventGrid/topics

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|
|PublishSuccessCount|Gepubliceerde gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|Geen|
|PublishFailCount|Mislukte gebeurtenissen publiceren|Count|Totaal|Totaal aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|Error type, fout|
|UnmatchedEventCount|Niet-overeenkomende gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat niet overeenkomt met een van de gebeurtenis abonnementen voor dit onderwerp|Geen|
|PublishSuccessLatencyInMs|Latentie van publicatie geslaagd|Milliseconden|Totaal|Latentie van geslaagde publicatie in milliseconden|Geen|
|MatchedEventCount|Overeenkomende gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|EventSubscriptionName|
|DeliveryAttemptFailCount|Mislukte leverings gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat niet aan dit gebeurtenis abonnement kan worden geleverd|Fout, error type, EventSubscriptionName|
|DeliverySuccessCount|Geleverde gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat aan dit gebeurtenis abonnement is geleverd|EventSubscriptionName|
|DestinationProcessingDurationInMs|Doel verwerkings duur|Milliseconden|Average|Doel verwerkings duur in milliseconden|EventSubscriptionName|
|DroppedEventCount|Verwijderde gebeurtenissen|Count|Totaal|Totaal aantal verloren gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|DropReason, EventSubscriptionName|
|DeadLetteredCount|Gebeurtenissen met onbestelbare berichten|Count|Totaal|Totaal aantal gebeurtenissen met onbestelbare berichten die overeenkomen met dit gebeurtenis abonnement|DeadLetterReason, EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Micro soft. EventGrid/systemTopics

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|
|PublishSuccessCount|Gepubliceerde gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|Geen|
|PublishFailCount|Mislukte gebeurtenissen publiceren|Count|Totaal|Totaal aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|Error type, fout|
|UnmatchedEventCount|Niet-overeenkomende gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat niet overeenkomt met een van de gebeurtenis abonnementen voor dit onderwerp|Geen|
|PublishSuccessLatencyInMs|Latentie van publicatie geslaagd|Milliseconden|Totaal|Latentie van geslaagde publicatie in milliseconden|Geen|
|MatchedEventCount|Overeenkomende gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|EventSubscriptionName|
|DeliveryAttemptFailCount|Mislukte leverings gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat niet aan dit gebeurtenis abonnement kan worden geleverd|Fout, error type, EventSubscriptionName|
|DeliverySuccessCount|Geleverde gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat aan dit gebeurtenis abonnement is geleverd|EventSubscriptionName|
|DestinationProcessingDurationInMs|Doel verwerkings duur|Milliseconden|Average|Doel verwerkings duur in milliseconden|EventSubscriptionName|
|DroppedEventCount|Verwijderde gebeurtenissen|Count|Totaal|Totaal aantal verloren gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|DropReason, EventSubscriptionName|
|DeadLetteredCount|Gebeurtenissen met onbestelbare berichten|Count|Totaal|Totaal aantal gebeurtenissen met onbestelbare berichten die overeenkomen met dit gebeurtenis abonnement|DeadLetterReason, EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Micro soft. EventGrid/eventSubscriptions

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|
|MatchedEventCount|Overeenkomende gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|Geen|
|DeliveryAttemptFailCount|Mislukte leverings gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat niet aan dit gebeurtenis abonnement kan worden geleverd|Fout, error type|
|DeliverySuccessCount|Geleverde gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat aan dit gebeurtenis abonnement is geleverd|Geen|
|DestinationProcessingDurationInMs|Doel verwerkings duur|Milliseconden|Average|Doel verwerkings duur in milliseconden|Geen|
|DroppedEventCount|Verwijderde gebeurtenissen|Count|Totaal|Totaal aantal verloren gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|DropReason|
|DeadLetteredCount|Gebeurtenissen met onbestelbare berichten|Count|Totaal|Totaal aantal gebeurtenissen met onbestelbare berichten die overeenkomen met dit gebeurtenis abonnement|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Micro soft. EventGrid/extensionTopics

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|
|PublishSuccessCount|Gepubliceerde gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|Geen|
|PublishFailCount|Mislukte gebeurtenissen publiceren|Count|Totaal|Totaal aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|Error type, fout|
|UnmatchedEventCount|Niet-overeenkomende gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat niet overeenkomt met een van de gebeurtenis abonnementen voor dit onderwerp|Geen|
|PublishSuccessLatencyInMs|Latentie van publicatie geslaagd|Milliseconden|Totaal|Latentie van geslaagde publicatie in milliseconden|Geen|

## <a name="next-steps"></a>Volgende stappen
Raadpleeg het volgende artikel: [Diagnostische logboeken](diagnostic-logs.md)
