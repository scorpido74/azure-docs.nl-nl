---
title: Een trainingsgegevensset maken voor een aangepast model - Formulierherkenning
titleSuffix: Azure Cognitive Services
description: Meer informatie over hoe u ervoor zorgen dat uw trainingsgegevensset is geoptimaliseerd voor het trainen van een Form Recognizer-model.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 71ad7c5dd3ad74082da552cd3c45142bc0c2d624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75380623"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Een trainingsgegevensset maken voor een aangepast model

Wanneer u het aangepaste model Form Recognizer gebruikt, verstrekt u uw eigen trainingsgegevens, zodat het model kan trainen naar uw branchespecifieke formulieren. U een model trainen met vijf ingevulde formulieren of een leeg formulier (u moet het woord 'leeg' in de bestandsnaam opnemen) plus twee ingevulde formulieren. Zelfs als u voldoende ingevulde formulieren hebt om mee te trainen, kan het toevoegen van een leeg formulier aan uw trainingsgegevensset de nauwkeurigheid van het model verbeteren.

Als u handmatig gelabelde trainingsgegevens wilt gebruiken, moet u beginnen met ten minste vijf vormen van hetzelfde type. U nog steeds niet-gelabelde formulieren en een leeg formulier in dezelfde gegevensset gebruiken.

## <a name="training-data-tips"></a>Tips voor trainingsgegevens

Het is belangrijk om een gegevensset te gebruiken die is geoptimaliseerd voor training. Gebruik de volgende tips om ervoor te zorgen dat u de beste resultaten krijgt van de bewerking [Train Custom Model:](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)

* Gebruik indien mogelijk pdf-documenten op basis van tekst in plaats van op afbeeldingen gebaseerde documenten. Gescande PDF's worden verwerkt als afbeeldingen.
* Gebruik voorbeelden die al hun velden hebben ingevuld voor ingevulde formulieren.
* Gebruik in formulieren met in elk veld verschillende waarden.
* Als uw formulierafbeeldingen van lagere kwaliteit zijn, gebruikt u bijvoorbeeld een grotere gegevensset (10-15 afbeeldingen).
* De totale grootte van de trainingsgegevensset kan oplopen tot 500 pagina's.

## <a name="general-input-requirements"></a>Algemene inputvereisten

Zorg ervoor dat uw trainingsgegevensset ook voldoet aan de invoervereisten voor alle form recognizeer-inhoud. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>Uw trainingsgegevens uploaden

Wanneer u de set formulierdocumenten hebt samengesteld die u voor training gebruikt, moet u deze uploaden naar een Azure blob-opslagcontainer. Als u niet weet hoe u een Azure-opslagaccount met een container maakt, volgt u de [snelstart voor Azure-portal voor Azure-opslag.](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

### <a name="organize-your-data-in-subfolders-optional"></a>Uw gegevens ordenen in submappen (optioneel)

Standaard gebruikt de [API voor aangepast model trainen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) alleen formulierdocumenten die zich aan de basis van uw opslagcontainer bevinden. U echter trainen met gegevens in submappen als u deze opgeeft in de API-aanroep. Normaal gesproken heeft de hoofdtekst van de [aanroep Train Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) het volgende formulier, waar `<SAS URL>` is de URL van de url van de handtekening van de gedeelde toegang van uw container:

```json
{
  "source":"<SAS URL>"
}
```

Als u de volgende inhoud toevoegt aan de aanvraaginstantie, wordt de API getraind met documenten in submappen. Het `"prefix"` veld is optioneel en beperkt de trainingsgegevensset tot bestanden waarvan de paden beginnen met de opgegeven tekenreeks. Dus een `"Test"`waarde van, bijvoorbeeld, zal ertoe leiden dat de API om alleen te kijken naar de bestanden of mappen die beginnen met het woord "Test".

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

Nu u hebt geleerd hoe u een trainingsgegevensset maken, volgt u een snelle start om een aangepast formulierherkenningsmodel te trainen en deze op uw formulieren te gebruiken.

* [Snelstart: een model trainen en formuliergegevens extraheren met behulp van cURL](./quickstarts/curl-train-extract.md)
* [Snelstart: train een model en verpak formuliergegevens met de REST API met Python](./quickstarts/python-train-extract.md)
* [Trainen met labels met behulp van de REST API en Python](./quickstarts/python-labeled-data.md)