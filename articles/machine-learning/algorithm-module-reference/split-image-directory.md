---
title: Map om afbeeldingen te splitsen
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de map voor afbeelding splitsen in Azure Machine Learning Designer (preview) om de installatie kopieën van een map met installatie kopieën te verdelen in twee verschillende sets.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 49c2d2c24cc8acd8468f660189212a8ae71a1d6e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91268924"
---
# <a name="split-image-directory"></a>Map om afbeeldingen te splitsen

In dit onderwerp wordt beschreven hoe u de map voor afbeelding splitsen in Azure Machine Learning Designer kunt gebruiken om de installatie kopieën van een map met installatie kopieën te verdelen in twee verschillende sets.

Deze module is met name handig wanneer u afbeeldings gegevens moet scheiden in trainings-en test sets. 

## <a name="how-to-configure-split-image-directory"></a>Map voor gesplitste afbeeldingen configureren

1. Voeg de module **afbeeldingen splitsen** toe aan de pijp lijn. U kunt deze module vinden onder de categorie Computer Vision/afbeeldings gegevens transformatie.

2. Maak verbinding met de module waarvan de uitvoer een afbeeldings Directory is.

3. Invoer **Fractie van installatie kopieën in de eerste uitvoer** om het percentage gegevens op te geven dat in de linkernavigatiebalk moet worden geplaatst, standaard 0,9.


## <a name="technical-notes"></a>Technische opmerkingen

### <a name="expected-inputs"></a>Verwachte invoer

| Naam                  | Type           | Beschrijving              |
| --------------------- | -------------- | ------------------------ |
| Map met invoer afbeeldingen | ImageDirectory | Te splitsen map voor afbeeldingen |

### <a name="module-parameters"></a>Module parameters

| Naam                                   | Type  | Bereik | Optioneel | Beschrijving                            | Standaard |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| Fractie van afbeeldingen in de eerste uitvoer | Float | 0-1   | Vereist | Fractie van afbeeldingen in de eerste uitvoer | 0,9     |

### <a name="outputs"></a>Uitvoerwaarden

| Naam                    | Type           | Beschrijving                              |
| ----------------------- | -------------- | ---------------------------------------- |
| Uitvoer afbeelding directory1 | ImageDirectory | Afbeelding van de map met geselecteerde installatie kopieën |
| Uitvoer afbeelding directory2 | ImageDirectory | Map met installatie kopieën die alle andere installatie kopieën bevat |

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 

