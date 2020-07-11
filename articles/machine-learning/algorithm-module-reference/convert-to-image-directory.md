---
title: Converteren naar afbeeldingsmap
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module converteren naar afbeelding om de gegevensset te converteren naar de indeling van de afbeelding.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: b29b5fa1beb19bc055f94c56b064ae2c0ae175b5
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171139"
---
# <a name="convert-to-image-directory"></a>Converteren naar afbeeldingsmap

In dit artikel wordt beschreven hoe u de map converteren naar afbeelding kunt gebruiken om de gegevensset van de afbeelding te converteren naar het gegevens type van de afbeelding. Dit is een gestandaardiseerde gegevens indeling in aan afbeeldingen gerelateerde taken zoals afbeeldings classificatie in Azure Machine Learning Designer (preview).

## <a name="how-to-use-convert-to-image-directory"></a>Converteren naar afbeeldings Directory gebruiken  

1.  Voeg de module **converteren naar afbeeldings mappen** toe aan uw experiment. U kunt deze module vinden in de categorie ' Computer Vision/image-gegevens transformatie ' in de module lijst. 

2.  [Registreer een installatie kopie gegevensset](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) en verbind deze met de invoer poort van de module. Zorg ervoor dat er een afbeelding in de invoer gegevensset staat. 
    De volgende indelingen voor gegevensset worden ondersteund:

    - Gecomprimeerd bestand in deze extensies: '. zip ', '. tar ', '. gz ', '. bz2 '.
    - Map met installatie kopieën. **Het is raadzaam om deze map eerst te comprimeren en vervolgens het gecomprimeerde bestand als gegevensset te gebruiken**.

    > [!WARNING]
    > U **kunt** de module **gegevens importeren** niet gebruiken voor het importeren van de gegevensset van een afbeelding, omdat het uitvoer type van de module **import data** de data frame Directory is. deze map bevat alleen een teken reeks voor het bestandspad.
    

    > [!NOTE]
    > Als u de gegevensset van de afbeelding in Super visie leren gebruiken, is label vereist.
    > Voor een afbeeldings classificatie taak kan label worden gegenereerd als afbeelding ' categorie ' in module-uitvoer als deze afbeeldings gegevensset is ingedeeld in Torchvision ImageFolder-indeling. Anders worden alleen afbeeldingen zonder label opgeslagen. Hier volgt een voor beeld van hoe u de afbeeldings-gegevensset kunt ordenen om een label op te halen, de afbeeldings categorie gebruiken als naam voor de submap. Raadpleeg Torchvision- [gegevens sets](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) voor meer informatie.
    >
    > ```
    > root/dog/xxx.png
    > root/dog/xxy.png
    > root/dog/xxz.png
    >
    > root/cat/123.png
    > root/cat/nsdf3.png
    > root/cat/asd932_.png
    > ```

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
