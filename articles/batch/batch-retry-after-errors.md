---
title: Opnieuw proberen van taken na een fout
description: Controleer op fouten en probeer het opnieuw.
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
ms.openlocfilehash: eda567fda13d6caca679d0ce4947e042eca9530d
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77652007"
---
# <a name="detecting-and-handling-batch-service-errors"></a>Fouten in de batch-service detecteren en verwerken

Het is belang rijk om te onthouden of er fouten zijn opgetreden bij het werken met een REST-Service-API. Het is niet gebruikelijk dat fouten optreden bij het uitvoeren van batch taken.

## <a name="common-errors"></a>Algemene fouten 

- Netwerk fouten: Dit zijn aanvragen die nooit in batch zijn aangekomen of de batch-respons heeft de client niet op tijd bereikt.
- Interne server fouten: Dit zijn de standaard 5xx-status code HTTP-antwoord.
- Beperking kan fouten veroorzaken, zoals 429 of 503 status code, HTTP-antwoorden met de header opnieuw proberen na.
- 4xx-fouten die dergelijke fouten bevatten, zoals bestaat al en InvalidOperation. Dit betekent dat de resource niet de juiste status heeft voor de status overgang.

## <a name="when-to-retry"></a>Wanneer opnieuw proberen

De batch-Api's melden u als er een fout is opgetreden. Ze kunnen allemaal opnieuw worden geprobeerd en ze bevatten allemaal een algemene nieuwe handler voor opnieuw proberen. Het is het beste om dit ingebouwde mechanisme te gebruiken.

Na een fout moet u even wachten (enkele seconden tussen nieuwe pogingen) voordat u het opnieuw probeert. Als u te vaak of te snel opnieuw probeert te proberen, wordt de handler voor opnieuw proberen beperkt.


Lees [batch-status en fout codes](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)voor gedetailleerde informatie over elke API en hun standaard beleid voor opnieuw proberen.
