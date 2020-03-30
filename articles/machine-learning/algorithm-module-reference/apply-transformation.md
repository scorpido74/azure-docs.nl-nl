---
title: 'Transformatie toepassen: naslaginformatie over modules'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Transformatie toepassen in Azure Machine Learning om een invoergegevensset te wijzigen op basis van een eerder berekende transformatie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/05/2020
ms.openlocfilehash: ccf9d0c3eef50c7dfd838f1929e52506e8984879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78395258"
---
# <a name="apply-transformation-module"></a>Transformatiemodule toepassen

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om een invoergegevensset te wijzigen op basis van een eerder berekende transformatie.

Als u bijvoorbeeld z-scores gebruikt om uw trainingsgegevens te normaliseren met behulp van de module **Gegevens normaliseren,** wilt u ook de z-scorewaarde gebruiken die tijdens de scorefase is berekend voor training. In Azure Machine Learning u de normalisatiemethode opslaan als een transformatie en vervolgens **Transformatie toepassen** gebruiken om de z-score toe te passen op de invoergegevens voordat u scoort.

## <a name="how-to-save-transformations"></a>Transformaties opslaan

Met de ontwerper u gegevenstransformaties opslaan als **gegevenssets,** zodat u ze in andere pijplijnen gebruiken.

1. Selecteer een module voor gegevenstransformatie die is uitgevoerd.

1. Selecteer het tabblad **Uitvoer + logboeken.**

1. Selecteer het **pictogram Opslaan** om de **resultaattransformatie**op te slaan .

## <a name="how-to-use-apply-transformation"></a>Transformatie toepassen  
  
1. Voeg de module **Transformatie toepassen** toe aan uw pijplijn. U vindt deze module in de sectie **Modelscoring & Evaluatie** van het modulepalet. 
  
1. Zoek de opgeslagen transformatie die u wilt gebruiken onder **Gegevenssets** > **Mijn gegevenssets** in het modulepalet.

1. Verbind de uitvoer van de opgeslagen transformatie met de linkerinvoerpoort van de module **Transformatie toepassen.**

    De gegevensset moet exact hetzelfde schema hebben (aantal kolommen, kolomnamen, gegevenstypen) als de gegevensset waarvoor de transformatie voor het eerst is ontworpen.  
  
1. Verbind de gegevenssetuitvoer van de gewenste module met de juiste invoerpoort van de module **Transformatie toepassen.**
  
1. Voer de pijplijn uit om een transformatie toe te passen op de nieuwe gegevensset.  

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 