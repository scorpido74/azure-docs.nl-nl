---
title: Taken opnieuw proberen na een fout
description: Controleer op fouten en probeer ze opnieuw.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-batch
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/15/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 94ed936e619461a2dbf7ec837c2d80e21c01c88e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919988"
---
# <a name="detecting-and-handling-batch-service-errors"></a>Batchservicefouten detecteren en verwerken

Het is belangrijk om te onthouden om te controleren op fouten bij het werken met een REST service API. Het is niet ongewoon dat er fouten optreden bij het uitvoeren van batchtaken.

## <a name="common-errors"></a>Algemene fouten 

- Netwerkfouten : dit zijn aanvragen die batch nooit hebben bereikt of de batchrespons heeft de client niet op tijd bereikt.
- Interne serverfouten - dit zijn standaard 5xx statuscode HTTP-respons.
- Beperking kan fouten veroorzaken, zoals HTTP-antwoorden met de statuscode 429 of 503 met de koptekst Opnieuw proberen.
- 4xx-fouten met fouten als AlreadyExists en InvalidOperation. Dit betekent dat de resource niet de juiste status heeft voor de statusovergang.

Zie [Batchstatus en foutcodes](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)voor gedetailleerde informatie over de verschillende typen foutcodes en specifieke foutcodes.

## <a name="when-to-retry"></a>Wanneer opnieuw te proberen

De Batch API's zullen u op de hoogte stellen als er een storing optreedt. Ze kunnen allemaal opnieuw worden geprobeerd, en ze bevatten allemaal een wereldwijde retry handler voor dat doel. Het is het beste om dit ingebouwde mechanisme te gebruiken.

Na een storing moet u een beetje wachten (enkele seconden tussen pogingen) voordat u het opnieuw probeert. Als u te vaak of te snel opnieuw probeert, wordt de handler van opnieuw proberen gas geven.

### <a name="for-more-information"></a>Voor meer informatie  

[Batch-API's en -hulpprogramma's](batch-apis-tools.md) koppelingen naar API-referentiegegevens. De .NET API heeft bijvoorbeeld een [klasse RetryPolicyProvider]( https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet) waarin het vereiste beleid voor nieuwe try-try moet worden opgegeven. 

Lees [Batchstatus- en foutcodes](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)voor gedetailleerde informatie over elke API en hun standaardbeleid voor het opnieuw proberen.
