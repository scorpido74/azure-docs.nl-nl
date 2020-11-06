---
title: Azure-app configuratie REST API-consistentie
description: Referentie pagina's voor het garanderen van realtime consistentie met behulp van de Azure-app configuratie REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 4f11e6edcd4bc128f815db7e93b00b72bf990ea8
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424343"
---
# <a name="real-time-consistency"></a>Real-time consistentie

Vanwege de aard van sommige gedistribueerde systemen is consistentie in realtime tussen aanvragen moeilijk om impliciet af te dwingen. Een oplossing is het toestaan van Protocol ondersteuning in de vorm van meerdere **synchronisatie tokens**. Synchronisatie tokens zijn optioneel.

## <a name="initial-request"></a>Eerste aanvraag

Als u real-time consistentie tussen verschillende client instanties en-aanvragen wilt garanderen, gebruikt u optionele `Sync-Token` aanvraag/antwoord headers.

Syntaxis:

```http
Sync-Token: <id>=<value>;sn=<sn>
```

|Parameter|Beschrijving|
|--|--|
| `<id>` | Token-ID (ondoorzichtig) |
| `<value>` | Token waarde (ondoorzichtig). Base64-gecodeerde teken reeks toestaan |
| `<sn>` | Token Sequence Number (versie). Hoger betekent een nieuwere versie van hetzelfde token. Biedt betere gelijktijdigheid en caching van client. De client kan ervoor kiezen om alleen de laatste versie van het token te gebruiken, aangezien de token versies inclusief zijn. Niet vereist voor aanvragen. |

## <a name="response"></a>Antwoord

De service biedt een `Sync-Token` header met elk antwoord.

```http
Sync-Token: jtqGc1I4=MDoyOA==;sn=28
```

## <a name="subsequent-requests"></a>Volgende aanvragen

Elke volgende aanvraag wordt gegarandeerd **real-time** consistente reactie in verhouding tot de gegeven `Sync-Token` .

```http
Sync-Token: <id>=<value>
```

Als de `Sync-Token` header van de aanvraag wordt wegge laten, is het mogelijk dat de service gedurende een korte periode (tot enkele seconden) reageert op gegevens in de cache, voordat deze intern wordt afgehandeld. Dit gedrag kan inconsistente Lees bewerkingen veroorzaken als er direct voor het lezen wijzigingen zijn aangebracht.

## <a name="multiple-sync-tokens"></a>Meerdere synchronisatie-tokens

De server kan reageren met meerdere synchronisatie-tokens voor één aanvraag. Om **realtime** consistentie te blijven voor de volgende aanvraag, moet de client reageren met alle ontvangen synchronisatie-tokens. Per RFC moeten meerdere header-waarden door komma's zijn gescheiden.

```http
Sync-Token: <token1-id>=<value>,<token2-id>=<value>
```
