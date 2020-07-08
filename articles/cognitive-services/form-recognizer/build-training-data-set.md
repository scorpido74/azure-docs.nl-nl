---
title: Een trainings gegevensverzameling maken voor een aangepaste herkenner van het model formulier
titleSuffix: Azure Cognitive Services
description: Meer informatie over hoe u ervoor kunt zorgen dat uw trainings gegevensverzameling is geoptimaliseerd voor het trainen van een model voor formulier herkenning.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: c6780d952b9ce6ea58fc6c8a2509a4526add7149
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86028257"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Een trainings gegevensverzameling voor een aangepast model bouwen

Wanneer u het aangepaste model voor formulier herkenning gebruikt, geeft u uw eigen trainings gegevens op zodat het model kan worden opgeleid voor uw branchespecifieke formulieren. 

Als u training hebt zonder hand matige labels, kunt u vijf ingevulde formulieren gebruiken of een leeg formulier (u moet het woord ' empty ' in de bestands naam) plus twee ingevulde formulieren toevoegen. Zelfs als u voldoende ingevulde formulieren hebt, kunt u een leeg formulier toevoegen aan uw trainings gegevensset om de nauw keurigheid van het model te verbeteren.

Als u hand matig gelabelde trainings gegevens wilt gebruiken, moet u beginnen met ten minste vijf ingevulde formulieren van hetzelfde type. Naast de vereiste gegevensset kunt u nog steeds niet-gelabelde formulieren en een leeg formulier gebruiken.

## <a name="training-data-tips"></a>Tips voor trainings gegevens

Het is belang rijk dat u een gegevens verzameling gebruikt die is geoptimaliseerd voor training. Gebruik de volgende tips om ervoor te zorgen dat u de beste resultaten krijgt van de bewerking [aangepast model van Train](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) :

* Gebruik zo mogelijk PDF-documenten op basis van tekst in plaats van op afbeeldingen gebaseerde documenten. Gescande Pdf's worden behandeld als installatie kopieën.
* Voor ingevulde formulieren gebruikt u voor beelden waarvoor alle velden zijn ingevuld.
* Gebruik in formulieren met in elk veld verschillende waarden.
* Als uw formulier afbeeldingen een lagere kwaliteit hebben, gebruikt u een grotere gegevensset (bijvoorbeeld 10-15 installatie kopieën).
* De totale grootte van de set met trainings gegevens kan Maxi maal 500 pagina's zijn.

## <a name="general-input-requirements"></a>Algemene invoer vereisten

Zorg ervoor dat uw trainings gegevensverzameling ook voldoet aan de invoer vereisten voor alle inhoud van de formulier herkenning. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>Uw trainings gegevens uploaden

Wanneer u de set formulier documenten die u voor training gebruikt, hebt samengesteld, moet u deze uploaden naar een Azure Blob Storage-container. Als u niet weet hoe u een Azure-opslag account maakt met een container, volgt u de [Azure Storage Snelstartgids voor Azure Portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

Als u hand matig gelabelde gegevens wilt gebruiken, moet u ook de *.labels.js* uploaden en *.ocr.jsop* bestanden die overeenkomen met uw trainings documenten. U kunt het voor [beeld-label programma](./quickstarts/label-tool.md) (of uw eigen gebruikers interface) gebruiken om deze bestanden te genereren.

### <a name="organize-your-data-in-subfolders-optional"></a>Organiseer uw gegevens in submappen (optioneel)

Standaard worden alleen formulier documenten gebruikt die zich [in de hoofdmap](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) van uw opslag container bevinden. U kunt echter met gegevens in submappen trainen als u deze in de API-aanroep opgeeft. Normaal gesp roken heeft de hoofd tekst van de [trein aangepaste model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) aanroep de volgende indeling, waarbij `<SAS URL>` de URL van de gedeelde Access-hand tekening van de container is:

```json
{
  "source":"<SAS URL>"
}
```

Als u de volgende inhoud aan de aanvraag tekst toevoegt, wordt de API getraind met documenten die zich in submappen bevinden. Het `"prefix"` veld is optioneel en beperkt de set trainings gegevens tot bestanden waarvan het pad begint met de opgegeven teken reeks. Een waarde van bijvoorbeeld `"Test"` resulteert in de API alleen om te kijken naar de bestanden of mappen die beginnen met het woord ' test '.

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een set trainings gegevens bouwt, volgt u een Snelstartgids om een aangepast model voor formulier herkenning te trainen en te beginnen met het gebruik ervan in uw formulieren.

* [Een model trainen en formulier gegevens extra heren met behulp van krul](./quickstarts/curl-train-extract.md)
* [Een model trainen en formulier gegevens extra heren met behulp van de REST API en python](./quickstarts/python-train-extract.md)
* [Trainen met labels met behulp van het voor beeld-label programma](./quickstarts/label-tool.md)
* [Trainen met labels met behulp van de REST API en Python](./quickstarts/python-labeled-data.md)
