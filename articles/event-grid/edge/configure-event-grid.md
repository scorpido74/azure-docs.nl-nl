---
title: Configuratie - IoT-rand voor Azure-gebeurtenisraster | Microsoft Documenten
description: Configuratie in gebeurtenisraster op IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 841b5092775353bbe3340dbbd55610026f998a15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76846466"
---
# <a name="event-grid-configuration"></a>Gebeurtenisrasterconfiguratie

Event Grid biedt veel configuraties die per omgeving kunnen worden gewijzigd. De volgende sectie is een verwijzing naar alle beschikbare opties en de standaardinstellingen.

## <a name="tls-configuration"></a>TLS-configuratie

Zie [Beveiliging en verificatie](security-authentication.md)voor meer informatie over clientverificatie in het algemeen. Voorbeelden van het gebruik ervan zijn te vinden in [dit artikel.](configure-api-protocol.md)

| Naam van eigenschap | Beschrijving |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| TLS-beleid van de module Gebeurtenisraster. Standaardwaarde is alleen HTTPS.
|`inbound__serverAuth__serverCert__source`| Bron van servercertificaat dat wordt gebruikt door de Gebeurtenisrastermodule voor de TLS-configuratie. Standaardwaarde is IoT Edge.

## <a name="incoming-client-authentication"></a>Binnenkomende clientverificatie

Zie [Beveiliging en verificatie](security-authentication.md)voor meer informatie over clientverificatie in het algemeen. Voorbeelden zijn te vinden in [dit artikel](configure-client-auth.md).

| Naam van eigenschap | Beschrijving |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| Clientverificatie op basis van certificaten in- en uitschakelen. De standaardwaarde is waar.
|`inbound__clientAuth__clientCert__source`| Bron voor het valideren van clientcertificaten. Standaardwaarde is IoT Edge.
|`inbound__clientAuth__clientCert__allowUnknownCA`| Beleid om een zelfondertekende clientcertificaat toe te staan. De standaardwaarde is waar.
|`inbound__clientAuth__sasKeys__enabled`| Om sas-sleutelgebaseerde clientverificatie in te schakelen of uit te schakelen. De standaardwaarde is uitgeschakeld.
|`inbound__clientAuth__sasKeys__key1`| Een van de waarden om binnenkomende aanvragen te valideren.
|`inbound__clientAuth__sasKeys__key2`| Optionele tweede waarde om binnenkomende aanvragen te valideren.

## <a name="outgoing-client-authentication"></a>Uitgaande clientverificatie
Zie [Beveiliging en verificatie](security-authentication.md)voor meer informatie over clientverificatie in het algemeen. Voorbeelden zijn te vinden in [dit artikel](configure-identity-auth.md).

| Naam van eigenschap | Beschrijving |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| Het in- en uitschakelen van het toevoegen van een identiteitscertificaat voor uitgaande aanvragen. De standaardwaarde is waar.
|`outbound__clientAuth__clientCert__source`| Bron voor het ophalen van het uitgaande certificaat van de gebeurtenisrastermodule. Standaardwaarde is IoT Edge.

## <a name="webhook-event-handlers"></a>Webhook-gebeurtenishandlers

Zie [Beveiliging en verificatie](security-authentication.md)voor meer informatie over clientverificatie in het algemeen. Voorbeelden zijn te vinden in [dit artikel](configure-webhook-subscriber-auth.md).

| Naam van eigenschap | Beschrijving |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| Beleid om te bepalen of alleen HTTPS-abonnees zijn toegestaan. Standaardwaarde is waar (alleen HTTPS).
|`outbound__webhook__skipServerCertValidation`| Markeren om te bepalen of het certificaat van de abonnee moet worden gevalideerd. De standaardwaarde is waar.
|`outbound__webhook__allowUnknownCA`| Beleid om te bepalen of een zelfondertekend certificaat door een abonnee kan worden gepresenteerd. De standaardwaarde is waar. 

## <a name="delivery-and-retry"></a>Leveren en opnieuw proberen

Zie [Levering en Opnieuw proberen](delivery-retry.md)voor meer informatie over deze functie in het algemeen.

| Naam van eigenschap | Beschrijving |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Maximaal aantal pogingen om een gebeurtenis te leveren. De standaardwaarde is 30.
| `broker__defaultEventTimeToLiveInSeconds` | Time-to-live (TTL) in seconden waarna een gebeurtenis wordt verwijderd als deze niet wordt geleverd. Standaardwaarde is **7200** seconden

## <a name="output-batching"></a>Batchverwerking van uitvoer

Zie [Batching levering en uitvoer](delivery-output-batching.md)voor meer informatie over deze functie in het algemeen.

| Naam van eigenschap | Beschrijving |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | Maximale waarde toegestaan `ApproxBatchSizeInBytes` voor de knop. Standaardwaarde `1_058_576`is .
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Maximale waarde toegestaan `MaxEventsPerBatch` voor de knop. Standaardwaarde `50`is .
| `broker__defaultMaxBatchSizeInBytes` | Maximale leveringsaanvraaggrootte `MaxEventsPerBatch` wanneer alleen is opgegeven. Standaardwaarde `1_058_576`is .
| `broker__defaultMaxEventsPerBatch` | Maximum aantal gebeurtenissen dat aan een `MaxBatchSizeInBytes` batch moet worden toegevoegd wanneer alleen is opgegeven. Standaardwaarde `10`is .

## <a name="metrics"></a>Metrische gegevens

Zie [Monitoronderwerpen en abonnementen voor](monitor-topics-subscriptions.md) meer informatie over het gebruik van statistieken met Gebeurtenisraster op IoT Edge

| Naam van eigenschap | Beschrijving |
| ---------------- | ------------ |
| `metrics__reporterType` | Het type van de verslaggever voor metrische gegevens enpoint. Standaard `none` is en schakelt metrische gegevens uit. Instellen `prometheus` om metrics in het Prometheus-expositieformaat mogelijk te maken.