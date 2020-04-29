---
title: 'Model voor afwijkings detectie trainen: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module voor het detecteren van detectie van Train-modellen voor het maken van een getraind anomalie detectie model.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 78ed2d85729cce94e8dfa579545f558d2cfe4651
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502969"
---
# <a name="train-anomaly-detection-model"></a>Anomaliedetectiemodel trainen

In dit artikel wordt beschreven hoe u met behulp van de module voor het detecteren van een **Train-detectie model** in azure machine learning Designer (preview) een getraind anomalie detectie model maakt.

De module gebruikt als invoer een set model parameters voor het model voor anomalie detectie en een niet-gelabelde gegevensset. Er wordt een getraind anomalie detectie model geretourneerd, samen met een set labels voor de trainings gegevens.  

Zie de volgende onderwerpen voor meer informatie over de algoritmen voor anomalie detectie die zijn opgenomen in de Designer: 

+ [Anomaliedetectie op basis van PCA](pca-based-anomaly-detection.md)  

## <a name="how-to-configure-train-anomaly-detection-model"></a>Het detectie model voor het afwijkings proces van Train configureren 

1.  Voeg de module **afwijkingen detectie model** toe aan uw pijp lijn in de ontwerp functie. U kunt deze module vinden in de categorie **anomalie detectie** .

2. Verbinding maken met een van de modules die zijn ontworpen voor anomalie detectie, zoals [op PCA gebaseerde anomalie detectie](pca-based-anomaly-detection.md)

    Andere typen modellen worden niet ondersteund. bij het uitvoeren van de pijp lijn wordt de volgende fout weer geven: alle modellen moeten hetzelfde type informatieer hebben.  

3.  Configureer de module anomalie detectie door de kolom label te kiezen en andere para meters in te stellen die specifiek zijn voor het algoritme.  

4.  Een trainings gegevensset koppelen aan de rechter invoer van het **model voor detectie van afwijkingen van Train**.  

5.  Verzend de pijp lijn.  

## <a name="results"></a>Resultaten

Nadat de training is voltooid:

+ Als u de para meters van het model wilt weer geven, klikt u met de rechter muisknop op de module en selecteert u **visualiseren**. 

+ Als u voor spellingen wilt maken, gebruikt u het [score model](score-model.md) met nieuwe invoer gegevens.

+ Als u een moment opname van het getrainde model wilt opslaan, selecteert u de module en klikt u op het pictogram **gegevensset registreren** onder uitvoer op het tabblad **Logboeken** in het rechterdeel venster.   

 
## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 

Zie [uitzonde ringen en fout codes voor de ontwerp functie (preview)](designer-error-codes.md) voor een lijst met fouten die specifiek zijn voor de ontwerp modules.
'