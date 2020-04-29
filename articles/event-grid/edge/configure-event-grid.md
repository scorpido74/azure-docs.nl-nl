---
title: Configuratie-Azure Event Grid IoT Edge | Microsoft Docs
description: Configuratie in Event Grid op IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 841b5092775353bbe3340dbbd55610026f998a15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76846466"
---
# <a name="event-grid-configuration"></a>Event Grid configuratie

Event Grid biedt veel configuraties die per omgeving kunnen worden gewijzigd. De volgende sectie bevat een verwijzing naar alle beschik bare opties en de standaard waarden.

## <a name="tls-configuration"></a>TLS-configuratie

Zie [beveiliging en verificatie](security-authentication.md)voor meer informatie over client verificatie in het algemeen. Voor beelden van het gebruik vindt u in [dit artikel](configure-api-protocol.md).

| Naam van eigenschap | Beschrijving |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| TLS-beleid van de module Event Grid. De standaard waarde is alleen HTTPS.
|`inbound__serverAuth__serverCert__source`| Bron van server certificaat dat wordt gebruikt door de module Event Grid voor de TLS-configuratie. De standaard waarde is IoT Edge.

## <a name="incoming-client-authentication"></a>Verificatie van binnenkomende clients

Zie [beveiliging en verificatie](security-authentication.md)voor meer informatie over client verificatie in het algemeen. Voor beelden vindt u in [dit artikel](configure-client-auth.md).

| Naam van eigenschap | Beschrijving |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| Client authenticatie op basis van certificaten in-of uitschakelen. De standaard waarde is True.
|`inbound__clientAuth__clientCert__source`| Bron voor het valideren van client certificaten. De standaard waarde is IoT Edge.
|`inbound__clientAuth__clientCert__allowUnknownCA`| Beleid om een zelfondertekend client certificaat toe te staan. De standaard waarde is True.
|`inbound__clientAuth__sasKeys__enabled`| Client authenticatie op basis van SAS-sleutel in-of uitschakelen. De standaard waarde is uitgeschakeld.
|`inbound__clientAuth__sasKeys__key1`| Een van de waarden voor het valideren van binnenkomende aanvragen.
|`inbound__clientAuth__sasKeys__key2`| Optionele tweede waarde voor het valideren van binnenkomende aanvragen.

## <a name="outgoing-client-authentication"></a>Verificatie van uitgaande clients
Zie [beveiliging en verificatie](security-authentication.md)voor meer informatie over client verificatie in het algemeen. Voor beelden vindt u in [dit artikel](configure-identity-auth.md).

| Naam van eigenschap | Beschrijving |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| Het koppelen van een identiteits certificaat voor uitgaande aanvragen in-of uitschakelen. De standaard waarde is True.
|`outbound__clientAuth__clientCert__source`| Bron voor het ophalen van het uitgaande certificaat van de Event Grid module. De standaard waarde is IoT Edge.

## <a name="webhook-event-handlers"></a>Gebeurtenis-handlers van webhook

Zie [beveiliging en verificatie](security-authentication.md)voor meer informatie over client verificatie in het algemeen. Voor beelden vindt u in [dit artikel](configure-webhook-subscriber-auth.md).

| Naam van eigenschap | Beschrijving |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| Beleid om te bepalen of alleen HTTPS-abonnees worden toegestaan. De standaard waarde is True (alleen HTTPS).
|`outbound__webhook__skipServerCertValidation`| Vlag waarmee wordt bepaald of het abonnee certificaat moet worden gevalideerd. De standaard waarde is True.
|`outbound__webhook__allowUnknownCA`| Beleid om te bepalen of een zelfondertekend certificaat kan worden weer gegeven door een abonnee. De standaard waarde is True. 

## <a name="delivery-and-retry"></a>Leveren en opnieuw proberen

Zie voor meer informatie over deze functie in het algemeen [levering en opnieuw proberen](delivery-retry.md).

| Naam van eigenschap | Beschrijving |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Het maximum aantal pogingen om een gebeurtenis te leveren. De standaardwaarde is 30.
| `broker__defaultEventTimeToLiveInSeconds` | Time-to-Live (TTL) in seconden waarna een gebeurtenis wordt verwijderd als deze niet wordt bezorgd. De standaard waarde is **7200** seconden

## <a name="output-batching"></a>Batchverwerking van uitvoer

Zie [levering en uitvoer batch verwerking](delivery-output-batching.md)voor meer informatie over deze functie in het algemeen.

| Naam van eigenschap | Beschrijving |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | De toegestane maximum waarde voor `ApproxBatchSizeInBytes` de knop. De standaard waarde `1_058_576`is.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | De toegestane maximum waarde voor `MaxEventsPerBatch` de knop. De standaard waarde `50`is.
| `broker__defaultMaxBatchSizeInBytes` | Maximale grootte van leverings aanvraag `MaxEventsPerBatch` wanneer alleen is opgegeven. De standaard waarde `1_058_576`is.
| `broker__defaultMaxEventsPerBatch` | Het maximum aantal gebeurtenissen dat aan een batch moet worden toegevoegd `MaxBatchSizeInBytes` als alleen wordt opgegeven. De standaard waarde `10`is.

## <a name="metrics"></a>Metrische gegevens

Zie [onderwerpen en abonnementen bewaken](monitor-topics-subscriptions.md) voor meer informatie over het gebruik van metrische gegevens met Event Grid op IOT Edge.

| Naam van eigenschap | Beschrijving |
| ---------------- | ------------ |
| `metrics__reporterType` | Type rapporter voor metrische gegevens enpoint. De standaard `none` instelling is en Hiermee worden metrische gegevens uitgeschakeld. Instelling voor `prometheus` het inschakelen van metrische gegevens in de Prometheus Exposition-indeling.