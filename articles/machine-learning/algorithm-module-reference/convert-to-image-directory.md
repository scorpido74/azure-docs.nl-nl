---
title: Converteren naar afbeelding map
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module converteren naar afbeelding om de gegevensset te converteren naar de indeling van de afbeelding.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: dc40e0a644f692b397b1f2107b27b1d940d2b284
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450631"
---
# <a name="convert-to-image-directory"></a>Converteren naar afbeelding map

In dit artikel wordt beschreven hoe u de map converteren naar afbeelding kunt gebruiken om de gegevensset van de afbeelding te converteren naar het gegevens type van de afbeelding. Dit is een gestandaardiseerde gegevens indeling in aan afbeeldingen gerelateerde taken zoals afbeeldings classificatie in Azure Machine Learning Designer (preview).

## <a name="how-to-use-convert-to-image-directory"></a>Converteren naar afbeeldings Directory gebruiken  

1.  Voeg de module **converteren naar afbeeldings mappen** toe aan uw experiment. U kunt deze module vinden in de categorie ' Computer Vision/image-gegevens transformatie ' in de module lijst. 

2.  Een gegevensset voor een installatie kopie verbinden als invoer. Zorg ervoor dat er een afbeelding in de invoer gegevensset staat.
    De volgende indelingen voor gegevensset worden ondersteund:

    - Gecomprimeerd bestand in deze extensies: '. zip ', '. tar ', '. gz ', '. bz2 '.
    - Map met één gecomprimeerd bestand met een geldige extensie. 
    - Map met installatie kopieën.

    > [!NOTE]
    > De afbeeldings categorie kan worden vastgelegd in module-uitvoer als deze afbeeldings gegevensset is ingedeeld in Torchvision ImageFolder-indeling, raadpleegt u [Torchvision-gegevens sets](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) voor meer informatie. Anders worden alleen afbeeldingen opgeslagen.

3.  Verzend de pijp lijn.

## <a name="results"></a>Resultaten

De uitvoer van de modus **converteren naar afbeeldings mappen** is in de indeling afbeeldings mappen en kan worden verbonden met andere installatie kopieën die betrekking hebben op de indeling van de invoer poort, ook wel afbeelding map.

## <a name="technical-notes"></a>Technische opmerkingen 

###  <a name="expected-inputs"></a>Verwachte invoer  

| Naam          | Type                  | Beschrijving   |
| ------------- | --------------------- | ------------- |
| Invoer gegevensset | AnyDirectory, ZipFile | Invoer gegevensset |

###  <a name="output"></a>Uitvoer  

| Naam                   | Type           | Beschrijving            |
| ---------------------- | -------------- | ---------------------- |
| Uitvoer afbeelding map | ImageDirectory | Uitvoer afbeelding map |

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
