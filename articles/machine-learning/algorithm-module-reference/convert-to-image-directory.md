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
ms.date: 09/28/2020
ms.openlocfilehash: 9f5f4b2b069ebc65430fba4bc31a9891ed61fedf
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450103"
---
# <a name="convert-to-image-directory"></a>Converteren naar afbeeldingsmap

In dit artikel wordt beschreven hoe u de map Convert to Image Directory kunt gebruiken om de gegevensset van afbeeldingen te converteren naar het gegevens type van de afbeelding. Dit is een gestandaardiseerde gegevens indeling in aan afbeeldingen gerelateerde taken zoals afbeeldings classificatie in Azure Machine Learning Designer.

## <a name="how-to-use-convert-to-image-directory"></a>Converteren naar afbeeldings Directory gebruiken  

1.  Voeg de module **converteren naar afbeeldings mappen** toe aan het canvas. U kunt deze module vinden in de categorie ' Computer Vision/image-gegevens transformatie ' in de module lijst. 

2.  De invoer van **converteren naar een afbeelding** van de Directory-module moet een bestands gegevensset zijn. [Registreer een installatie kopie gegevensset](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) en verbind deze met de invoer poort van de module. Zorg ervoor dat er een afbeelding in de invoer gegevensset staat. De ontwerper ondersteunt momenteel geen gegevensset voor het visualiseren van afbeeldingen.
 
    De volgende indelingen voor gegevensset worden ondersteund:

    - Gecomprimeerd bestand in deze extensies: '. zip ', '. tar ', '. gz ', '. bz2 '.
    - Map met installatie kopieën. **Het is raadzaam om deze map eerst te comprimeren en vervolgens het gecomprimeerde bestand als gegevensset te gebruiken**.

    > [!WARNING]
    > U **kunt** de module **gegevens importeren** niet gebruiken voor het importeren van de gegevensset van een afbeelding, omdat het uitvoer type van de module **import data** de data frame Directory is. deze map bevat alleen een teken reeks voor het bestandspad.
    

    > [!NOTE]
    > - Als u de gegevensset van een afbeelding in Super visie leren gebruiken, moet u het label van de trainings gegevensset opgeven.
    > - Voor een afbeeldings classificatie taak kan label worden gegenereerd als afbeelding ' categorie ' in module-uitvoer als deze afbeeldings gegevensset is ingedeeld in Torchvision ImageFolder-indeling. Anders worden alleen afbeeldingen zonder label opgeslagen. Hieronder volgt een voor beeld van hoe u de afbeeldings-gegevensset kunt ordenen om een label op te halen, de afbeeldings categorie gebruiken als naam voor de submap. 
    > - Het is niet nodig om hetzelfde aantal installatie kopieën in elke categorie map te uploaden.
    > - Afbeeldingen met deze uitbrei dingen (in kleine letters) worden ondersteund:. jpg,. JPEG,. png,. ppm,. bmp,.................... TIFF,. webp. U kunt ook meerdere typen installatie kopieën in één map hebben.    
    > - Raadpleeg Torchvision- [gegevens sets](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) voor meer informatie.
    >
    > ```
    > Your_image_folder_name/Category_1/xxx.png
    > Your_image_folder_name/Category_1/xxy.jpg
    > Your_image_folder_name/Category_1/xxz.jpeg
    >
    > Your_image_folder_name/Category_2/123.png
    > Your_image_folder_name/Category_2/nsdf3.png
    > Your_image_folder_name/Category_2/asd932_.png
    > ```
    > - Als u een image-gegevensset voor scores gebruikt, moet de gegevensset van het invoer bestand van deze module niet-geclassificeerde installatie kopieën bevatten.
    
3.  Verzend de pijp lijn. Deze module kan worden uitgevoerd op een GPU of CPU.

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
