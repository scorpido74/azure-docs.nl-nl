---
title: Batch test TSV formaat - QnA Maker
titleSuffix: Azure Cognitive Services
description: Inzicht in het TSV-formaat voor batchtesten
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: dccf034b6497651db70b6cc09fff3f1b1d645a1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73507823"
---
# <a name="batch-testing-tsv-format"></a>TSV-indeling voor batchtesten

Batchtesten zijn beschikbaar via [de broncode](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) of als [downloadbare uitvoerbare rits.](https://aka.ms/qna_btzip) De indeling van de opdracht voor het uitvoeren van de batchtest is:

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|Verwachte waarde|
|--|--|
|1|naam van tsv-bestand dat is opgemaakt met [TSV-invoervelden](#tsv-input-fields)|
|2|URI voor eindpunt, met YOUR-HOST vanaf de publicatiepagina van de QnA Maker-portal.|
|3|ENDPOINT-KEY, gevonden op de publicatiepagina van de QnA Maker-portal.|
|4|naam van tsv-bestand gemaakt door batchtest voor resultaten.|

Gebruik de volgende informatie om de TSV-indeling te begrijpen en te implementeren voor batchtests. 

## <a name="tsv-input-fields"></a>TSV-invoervelden

|TSV-invoerbestandsvelden|Opmerkingen|
|--|--|
|KBID|Uw KB-id op de pagina Publiceren.|
|Vraag|De vraag die een gebruiker zou invoeren.|
|Tags met metagegevens|optioneel|
|Bovenste parameter|optioneel| 
|Verwachte antwoord-ID|optioneel|

![Invoerindeling voor TSV-bestand voor batchtests.](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>TSV-uitvoervelden 

|Bestandsparameters voor TSV-uitvoer|Opmerkingen|
|--|--|
|KBID|Uw KB-id op de pagina Publiceren.|
|Vraag|De vraag die is ingevoerd in het invoerbestand.|
|Antwoord|Top antwoord van uw kennisbank.|
|Antwoord-id|Antwoord-id|
|Score|Voorspelling score voor antwoord. |
|Tags met metagegevens|gekoppeld aan geretourneerd antwoord|
|Verwachte antwoord-ID|optioneel (alleen wanneer verwachte antwoord-ID wordt gegeven)|
|Oordeellabel|optioneel, kunnen de waarden zijn: correct of onjuist (alleen wanneer het verwachte antwoord wordt gegeven)|
