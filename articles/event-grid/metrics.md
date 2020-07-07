---
title: Metrische gegevens die door Azure Event Grid worden ondersteund
description: Dit artikel bevat Azure Monitor metrische gegevens die worden ondersteund door de Azure Event Grid-Service.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: spelluru
ms.openlocfilehash: 643df2f4cc6347e0fd56f9124b68f1888ab85e26
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82630133"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Metrische gegevens die door Azure Event Grid worden ondersteund
Dit artikel bevat een lijst met Event Grid metrische gegevens die door naam ruimten worden gecategoriseerd. 

## <a name="microsofteventgriddomains"></a>Micro soft. EventGrid/domeinen

|Metrisch|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
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

|Metrisch|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
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

|Metrisch|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
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

|Metrisch|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|MatchedEventCount|Overeenkomende gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|Geen|
|DeliveryAttemptFailCount|Mislukte leverings gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat niet aan dit gebeurtenis abonnement kan worden geleverd|Fout, error type|
|DeliverySuccessCount|Geleverde gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat aan dit gebeurtenis abonnement is geleverd|Geen|
|DestinationProcessingDurationInMs|Doel verwerkings duur|Milliseconden|Average|Doel verwerkings duur in milliseconden|Geen|
|DroppedEventCount|Verwijderde gebeurtenissen|Count|Totaal|Totaal aantal verloren gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|DropReason|
|DeadLetteredCount|Gebeurtenissen met onbestelbare berichten|Count|Totaal|Totaal aantal gebeurtenissen met onbestelbare berichten die overeenkomen met dit gebeurtenis abonnement|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Micro soft. EventGrid/extensionTopics

|Metrisch|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PublishSuccessCount|Gepubliceerde gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|Geen|
|PublishFailCount|Mislukte gebeurtenissen publiceren|Count|Totaal|Totaal aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|Error type, fout|
|UnmatchedEventCount|Niet-overeenkomende gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat niet overeenkomt met een van de gebeurtenis abonnementen voor dit onderwerp|Geen|
|PublishSuccessLatencyInMs|Latentie van publicatie geslaagd|Milliseconden|Totaal|Latentie van geslaagde publicatie in milliseconden|Geen|

## <a name="next-steps"></a>Volgende stappen
Raadpleeg het volgende artikel: [Diagnostische logboeken](diagnostic-logs.md)
