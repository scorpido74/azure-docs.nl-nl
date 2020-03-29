---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b08516b35a864eae6d15c4c5c928f0550c64c239
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67712449"
---
Fluentd is een open-source dataverzamelaar voor unified logging. De `Fluentd` instellingen beheren de verbinding van de container met een [Fluentd-server.](https://www.fluentd.org) De container bevat een Fluentd logging provider, waarmee uw container logboeken en, optioneel, metrische gegevens naar een Fluentd-server kan schrijven.

In de volgende tabel worden de `Fluentd` configuratie-instellingen beschreven die onder de sectie worden ondersteund.

| Name | Gegevenstype | Beschrijving |
|------|-----------|-------------|
| `Host` | Tekenreeks | De IP-adres- of DNS-hostnaam van de Fluentd-server. |
| `Port` | Geheel getal | De poort van de Fluentd server.<br/> De standaardwaarde is 24224. |
| `HeartbeatMs` | Geheel getal | Het hartslaginterval, in milliseconden. Als er geen gebeurtenisverkeer is verzonden voordat dit interval verloopt, wordt er een heartbeat naar de Fluentd-server verzonden. De standaardwaarde is 60000 milliseconden (1 minuut). |
| `SendBufferSize` | Geheel getal | De netwerkbufferruimte, in bytes, toegewezen voor verzendbewerkingen. De standaardwaarde is 32768 bytes (32 kilobytes). |
| `TlsConnectionEstablishmentTimeoutMs` | Geheel getal | De time-out, in milliseconden, om een SSL / TLS-verbinding met de Fluentd-server tot stand te brengen. De standaardwaarde is 10000 milliseconden (10 seconden).<br/> Als `UseTLS` deze waarde is ingesteld op false, wordt deze waarde genegeerd. |
| `UseTLS` | Booleaans | Geeft aan of de container SSL/TLS moet gebruiken voor communicatie met de Fluentd-server. De standaardwaarde is false. |