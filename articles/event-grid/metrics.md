---
title: Metrische gegevens die door Azure Event Grid worden ondersteund
description: Dit artikel bevat Azure Monitor metrische gegevens die worden ondersteund door de Azure Event Grid-Service.
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 06c43b1990efc977cae33ced3f66f02e2de0b9c4
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225169"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Metrische gegevens die door Azure Event Grid worden ondersteund
Dit artikel bevat een lijst met Event Grid metrische gegevens die door naam ruimten worden gecategoriseerd. 

## <a name="microsofteventgriddomains"></a>Micro soft. EventGrid/domeinen

|Gegevens|Exporteerbaar via Diagnostische instellingen?|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Ja|Gebeurtenissen met onbestelbare berichten|Aantal|Totaal|Totaal aantal gebeurtenissen met onbestelbare berichten die overeenkomen met dit gebeurtenis abonnement|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|Nee|Mislukte leverings gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet aan dit gebeurtenis abonnement kan worden geleverd|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName, fout, error type|
|DeliverySuccessCount|Ja|Geleverde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat aan dit gebeurtenis abonnement is geleverd|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Nee|Doel verwerkings duur|Milliseconden|Gemiddeld|Doel verwerkings duur in milliseconden|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Ja|Verwijderde gebeurtenissen|Aantal|Totaal|Totaal aantal verloren gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Ja|Overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Ja|Mislukte gebeurtenissen publiceren|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|Onderwerp, error type, fout|
|PublishSuccessCount|Ja|Gepubliceerde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|Onderwerp|
|PublishSuccessLatencyInMs|Ja|Latentie van publicatie geslaagd|Milliseconden|Totaal|Latentie van geslaagde publicatie in milliseconden|Geen dimensies|


## <a name="microsofteventgrideventsubscriptions"></a>Micro soft. EventGrid/eventSubscriptions

|Gegevens|Exporteerbaar via Diagnostische instellingen?|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Ja|Gebeurtenissen met onbestelbare berichten|Aantal|Totaal|Totaal aantal gebeurtenissen met onbestelbare berichten die overeenkomen met dit gebeurtenis abonnement|DeadLetterReason|
|DeliveryAttemptFailCount|Nee|Mislukte leverings gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet aan dit gebeurtenis abonnement kan worden geleverd|Fout, error type|
|DeliverySuccessCount|Ja|Geleverde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat aan dit gebeurtenis abonnement is geleverd|Geen dimensies|
|DestinationProcessingDurationInMs|Nee|Doel verwerkings duur|Milliseconden|Gemiddeld|Doel verwerkings duur in milliseconden|Geen dimensies|
|DroppedEventCount|Ja|Verwijderde gebeurtenissen|Aantal|Totaal|Totaal aantal verloren gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|DropReason|
|MatchedEventCount|Ja|Overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|Geen dimensies|


## <a name="microsofteventgridextensiontopics"></a>Micro soft. EventGrid/extensionTopics

|Gegevens|Exporteerbaar via Diagnostische instellingen?|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|---|
|PublishFailCount|Ja|Mislukte gebeurtenissen publiceren|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|Error type, fout|
|PublishSuccessCount|Ja|Gepubliceerde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|Geen dimensies|
|PublishSuccessLatencyInMs|Ja|Latentie van publicatie geslaagd|Milliseconden|Totaal|Latentie van geslaagde publicatie in milliseconden|Geen dimensies|
|UnmatchedEventCount|Ja|Niet-overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet overeenkomt met een van de gebeurtenis abonnementen voor dit onderwerp|Geen dimensies|


## <a name="microsofteventgridsystemtopics"></a>Micro soft. EventGrid/systemTopics

|Gegevens|Exporteerbaar via Diagnostische instellingen?|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Ja|Gebeurtenissen met onbestelbare berichten|Aantal|Totaal|Totaal aantal gebeurtenissen met onbestelbare berichten die overeenkomen met dit gebeurtenis abonnement|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nee|Mislukte leverings gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet aan dit gebeurtenis abonnement kan worden geleverd|Fout, error type, EventSubscriptionName|
|DeliverySuccessCount|Ja|Geleverde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat aan dit gebeurtenis abonnement is geleverd|EventSubscriptionName|
|DestinationProcessingDurationInMs|Nee|Doel verwerkings duur|Milliseconden|Gemiddeld|Doel verwerkings duur in milliseconden|EventSubscriptionName|
|DroppedEventCount|Ja|Verwijderde gebeurtenissen|Aantal|Totaal|Totaal aantal verloren gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|DropReason, EventSubscriptionName|
|MatchedEventCount|Ja|Overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|EventSubscriptionName|
|PublishFailCount|Ja|Mislukte gebeurtenissen publiceren|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|Error type, fout|
|PublishSuccessCount|Ja|Gepubliceerde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|Geen dimensies|
|PublishSuccessLatencyInMs|Ja|Latentie van publicatie geslaagd|Milliseconden|Totaal|Latentie van geslaagde publicatie in milliseconden|Geen dimensies|
|UnmatchedEventCount|Ja|Niet-overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet overeenkomt met een van de gebeurtenis abonnementen voor dit onderwerp|Geen dimensies|


## <a name="microsofteventgridtopics"></a>Micro soft. EventGrid/topics

|Gegevens|Exporteerbaar via Diagnostische instellingen?|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Ja|Gebeurtenissen met onbestelbare berichten|Aantal|Totaal|Totaal aantal gebeurtenissen met onbestelbare berichten die overeenkomen met dit gebeurtenis abonnement|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nee|Mislukte leverings gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet aan dit gebeurtenis abonnement kan worden geleverd|Fout, error type, EventSubscriptionName|
|DeliverySuccessCount|Ja|Geleverde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat aan dit gebeurtenis abonnement is geleverd|EventSubscriptionName|
|DestinationProcessingDurationInMs|Nee|Doel verwerkings duur|Milliseconden|Gemiddeld|Doel verwerkings duur in milliseconden|EventSubscriptionName|
|DroppedEventCount|Ja|Verwijderde gebeurtenissen|Aantal|Totaal|Totaal aantal verloren gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|DropReason, EventSubscriptionName|
|MatchedEventCount|Ja|Overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|EventSubscriptionName|
|PublishFailCount|Ja|Mislukte gebeurtenissen publiceren|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|Error type, fout|
|PublishSuccessCount|Ja|Gepubliceerde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|Geen dimensies|
|PublishSuccessLatencyInMs|Ja|Latentie van publicatie geslaagd|Milliseconden|Totaal|Latentie van geslaagde publicatie in milliseconden|Geen dimensies|
|UnmatchedEventCount|Ja|Niet-overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet overeenkomt met een van de gebeurtenis abonnementen voor dit onderwerp|Geen dimensies|

## <a name="next-steps"></a>Volgende stappen
Raadpleeg het volgende artikel: [Diagnostische logboeken](diagnostic-logs.md)
