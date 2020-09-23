---
title: Map om afbeeldingen te splitsen
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u de module Score image model in Azure Machine Learning kunt gebruiken om voor spellingen te genereren met behulp van een getraind afbeeldings model.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 63ae7115f905523a3aac131fd7e77b56eb695243
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90890267"
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

| Naam                  | Type           | Description              |
| --------------------- | -------------- | ------------------------ |
| Map met invoer afbeeldingen | ImageDirectory | Te splitsen map voor afbeeldingen |

### <a name="module-parameters"></a>Module parameters

| Naam                                   | Type  | Bereik | Optioneel | Description                            | Standaard |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| Fractie van afbeeldingen in de eerste uitvoer | Float | 0-1   | Vereist | Fractie van afbeeldingen in de eerste uitvoer | 0,9     |

### <a name="outputs"></a>Uitvoerwaarden

| Naam                    | Type           | Description                              |
| ----------------------- | -------------- | ---------------------------------------- |
| Uitvoer afbeelding directory1 | ImageDirectory | Afbeelding van de map met geselecteerde installatie kopieën |
| Uitvoer afbeelding directory2 | ImageDirectory | Map met installatie kopieën die alle andere installatie kopieën bevat |

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 

