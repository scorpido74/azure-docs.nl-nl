---
title: Batch test TSV-indeling-QnA Maker
titleSuffix: Azure Cognitive Services
description: Meer informatie over de TSV-indeling voor batch tests
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: dccf034b6497651db70b6cc09fff3f1b1d645a1b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507823"
---
# <a name="batch-testing-tsv-format"></a>TSV-indeling voor batch tests

Batch testen is beschikbaar via een [bron code](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) of als een [uitvoerbaar](https://aka.ms/qna_btzip)uitkomend bestand dat kan worden gedownload. De indeling van de opdracht voor het uitvoeren van de batch test is:

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|Verwachte waarde|
|--|--|
|1|naam van een TSV-bestand dat is geformatteerd met [TSV-invoer velden](#tsv-input-fields)|
|2|URI voor het eind punt, met uw-HOST vanaf de pagina publiceren van de QnA Maker Portal.|
|3|EIND punt-sleutel, gevonden op de pagina publiceren van de QnA Maker Portal.|
|4|naam van het TSV-bestand dat door batch test is gemaakt voor resultaten.|

Gebruik de volgende informatie om de TSV-indeling voor batch tests te begrijpen en te implementeren. 

## <a name="tsv-input-fields"></a>TSV-invoer velden

|Velden van het TSV-invoer bestand|Opmerkingen|
|--|--|
|KBID|Uw KB-ID vindt u op de pagina publiceren.|
|Vraag|De vraag die een gebruiker zou invoeren.|
|Tags met metagegevens|Beschrijving|
|Bovenste para meter|Beschrijving| 
|Verwachte antwoord-ID|Beschrijving|

![Invoer indeling voor het TSV-bestand voor batch tests.](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>TSV-uitvoer velden 

|Para meters voor TSV-uitvoer bestand|Opmerkingen|
|--|--|
|KBID|Uw KB-ID vindt u op de pagina publiceren.|
|Vraag|De vraag die u hebt ingevoerd in het invoer bestand.|
|Antwoord|Het beste antwoord van uw Knowledge Base.|
|Antwoord-ID|Antwoord-ID|
|Score|Voorspellings score voor antwoord. |
|Tags met metagegevens|gekoppeld aan het geretourneerde antwoord|
|Verwachte antwoord-ID|optioneel (alleen als de verwachte antwoord-ID is opgegeven)|
|Arrest label|optioneel, waarden kunnen zijn: correct of onjuist (alleen wanneer het verwachte antwoord is opgegeven)|
