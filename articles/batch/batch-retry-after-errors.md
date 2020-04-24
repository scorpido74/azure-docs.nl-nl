---
title: Opnieuw proberen van taken na een fout
description: Controleer op fouten en probeer het opnieuw.
ms.topic: article
ms.date: 02/15/2020
ms.custom: seodec18
ms.openlocfilehash: 8addc4418f268a2c27b730543bdb309ef45fd5f4
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116533"
---
# <a name="detecting-and-handling-batch-service-errors"></a>Fouten in Batch-service detecteren en afhandelen

Het is belang rijk om te onthouden of er fouten zijn opgetreden bij het werken met een REST-Service-API. Het is niet gebruikelijk dat fouten optreden bij het uitvoeren van batch taken.

## <a name="common-errors"></a>Algemene fouten 

- Netwerk fouten: Dit zijn aanvragen die nooit in batch zijn aangekomen of de batch-respons heeft de client niet op tijd bereikt.
- Interne server fouten: Dit zijn de standaard 5xx-status code HTTP-antwoord.
- Beperking kan fouten veroorzaken, zoals 429 of 503 status code, HTTP-antwoorden met de header opnieuw proberen na.
- 4xx-fouten die dergelijke fouten bevatten, zoals bestaat al en InvalidOperation. Dit betekent dat de resource niet de juiste status heeft voor de status overgang.

Zie [batch-status en fout](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)codes voor meer informatie over de verschillende typen fout codes en specifieke fout codes.

## <a name="when-to-retry"></a>Wanneer opnieuw proberen

De batch-Api's melden u als er een fout is opgetreden. Ze kunnen allemaal opnieuw worden geprobeerd en ze bevatten allemaal een algemene nieuwe handler voor opnieuw proberen. Het is het beste om dit ingebouwde mechanisme te gebruiken.

Na een fout moet u even wachten (enkele seconden tussen nieuwe pogingen) voordat u het opnieuw probeert. Als u te vaak of te snel opnieuw probeert te proberen, wordt de handler voor opnieuw proberen beperkt.

### <a name="for-more-information"></a>Voor meer informatie  

[Batch-api's en hulpprogram ma's](batch-apis-tools.md) koppelingen naar API-referentie gegevens. De .NET API heeft bijvoorbeeld een [RetryPolicyProvider-klasse]( https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet) waarin het vereiste beleid voor opnieuw proberen moet worden opgegeven. 

Lees [batch-status en fout codes](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)voor gedetailleerde informatie over elke API en hun standaard beleid voor opnieuw proberen.
