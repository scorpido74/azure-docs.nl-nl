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
ms.openlocfilehash: a9bac9a84e1ba034b011691ae82a1cb67eb71af0
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992429"
---
# <a name="event-grid-configuration"></a>Event Grid configuratie

Event Grid biedt veel configuraties die per omgeving kunnen worden gewijzigd. De volgende sectie bevat een verwijzing naar alle beschik bare opties en de standaard waarden.

## <a name="tls-configuration"></a>TLS-configuratie

Zie [beveiliging en verificatie](security-authentication.md)voor meer informatie over client verificatie in het algemeen. Voor beelden van het gebruik vindt u in [dit artikel](configure-api-protocol.md).

| Eigenschaps naam | Beschrijving |
| ---------------- | ------------ |
|`inbound:serverAuth:tlsPolicy`| TLS-beleid van de module Event Grid. De standaard waarde is alleen HTTPS.
|`inbound:serverAuth:serverCert:source`| Bron van server certificaat dat wordt gebruikt door de module Event Grid voor de TLS-configuratie. De standaard waarde is IoT Edge.

## <a name="incoming-client-authentication"></a>Verificatie van binnenkomende clients

Zie [beveiliging en verificatie](security-authentication.md)voor meer informatie over client verificatie in het algemeen. Voor beelden vindt u in [dit artikel](configure-client-auth.md).

| Eigenschaps naam | Beschrijving |
| ---------------- | ------------ |
|`inbound:clientAuth:clientCert:enabled`| Client authenticatie op basis van certificaten in-of uitschakelen. De standaard waarde is True.
|`inbound:clientAuth:clientCert:source`| Bron voor het valideren van client certificaten. De standaard waarde is IoT Edge.
|`inbound:clientAuth:clientCert:allowUnknownCA`| Beleid om een zelfondertekend client certificaat toe te staan. De standaard waarde is True.
|`inbound:clientAuth:sasKeys:enabled`| Client authenticatie op basis van SAS-sleutel in-of uitschakelen. De standaard waarde is uitgeschakeld.
|`inbound:clientAuth:sasKeys:key1`| Een van de waarden voor het valideren van binnenkomende aanvragen.
|`inbound:clientAuth:sasKeys:key2`| Optionele tweede waarde voor het valideren van binnenkomende aanvragen.

## <a name="outgoing-client-authentication"></a>Verificatie van uitgaande clients
Zie [beveiliging en verificatie](security-authentication.md)voor meer informatie over client verificatie in het algemeen. Voor beelden vindt u in [dit artikel](configure-identity-auth.md).

| Eigenschaps naam | Beschrijving |
| ---------------- | ------------ |
|`outbound:clientAuth:clientCert:enabled`| Het koppelen van een identiteits certificaat voor uitgaande aanvragen in-of uitschakelen. De standaard waarde is True.
|`outbound:clientAuth:clientCert:source`| Bron voor het ophalen van het uitgaande certificaat van de Event Grid module. De standaard waarde is IoT Edge.

## <a name="webhook-event-handlers"></a>Gebeurtenis-handlers van webhook

Zie [beveiliging en verificatie](security-authentication.md)voor meer informatie over client verificatie in het algemeen. Voor beelden vindt u in [dit artikel](configure-webhook-subscriber-auth.md).

| Eigenschaps naam | Beschrijving |
| ---------------- | ------------ |
|`outbound:webhook:httpsOnly`| Beleid om te bepalen of alleen HTTPS-abonnees worden toegestaan. De standaard waarde is True (alleen HTTPS).
|`outbound:webhook:skipServerCertValidation`| Vlag waarmee wordt bepaald of het abonnee certificaat moet worden gevalideerd. De standaard waarde is True.
|`outbound:webhook:allowUnknownCA`| Beleid om te bepalen of een zelfondertekend certificaat kan worden weer gegeven door een abonnee. De standaard waarde is True. 

## <a name="delivery-and-retry"></a>Leveren en opnieuw proberen

Zie voor meer informatie over deze functie in het algemeen [levering en opnieuw proberen](delivery-retry.md).

| Eigenschaps naam | Beschrijving |
| ---------------- | ------------ |
| `broker:defaultMaxDeliveryAttempts` | Het maximum aantal pogingen om een gebeurtenis te leveren. De standaard waarde is 30.
| `broker:defaultEventTimeToLiveInSeconds` | Time-to-Live (TTL) in seconden waarna een gebeurtenis wordt verwijderd als deze niet wordt bezorgd. De standaard waarde is **7200** seconden

## <a name="output-batching"></a>Uitvoer batching

Zie [levering en uitvoer batch verwerking](delivery-output-batching.md)voor meer informatie over deze functie in het algemeen.

| Eigenschaps naam | Beschrijving |
| ---------------- | ------------ |
| `api:deliveryPolicyLimits:maxBatchSizeInBytes` | De toegestane maximum waarde voor de `ApproxBatchSizeInBytes` knop. De standaard waarde is `1_058_576`.
| `api:deliveryPolicyLimits:maxEventsPerBatch` | De toegestane maximum waarde voor de `MaxEventsPerBatch` knop. De standaard waarde is `50`.
| `broker:defaultMaxBatchSizeInBytes` | Maximale grootte van de leverings aanvraag wanneer alleen `MaxEventsPerBatch` is opgegeven. De standaard waarde is `1_058_576`.
| `broker:defaultMaxEventsPerBatch` | Het maximum aantal gebeurtenissen dat aan een batch moet worden toegevoegd als alleen `MaxBatchSizeInBytes` is opgegeven. De standaard waarde is `10`.
