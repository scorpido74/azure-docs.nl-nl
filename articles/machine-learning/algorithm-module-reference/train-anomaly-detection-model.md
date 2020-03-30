---
title: 'Trein anomalie detectiemodel: module referentie'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Treinanomaliedetectiemodel om een getraind anomaliedetectiemodel te maken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 78ed2d85729cce94e8dfa579545f558d2cfe4651
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502969"
---
# <a name="train-anomaly-detection-model"></a>Trein anomalie detectiemodel

In dit artikel wordt beschreven hoe u de module **Treinanomaliedetectiemodel** gebruiken in Azure Machine Learning-ontwerper (preview) om een getraind anomaliedetectiemodel te maken.

De module neemt als invoer een set modelparameters voor anomaliedetectiemodel en een niet-gelabelde gegevensset. Het retourneert een getraind anomaliedetectiemodel, samen met een set labels voor de trainingsgegevens.  

Zie de volgende onderwerpen voor meer informatie over de algoritmen voor anomaliedetectie in de ontwerper: 

+ [PcA-gebaseerde anomaliedetectie](pca-based-anomaly-detection.md)  

## <a name="how-to-configure-train-anomaly-detection-model"></a>Treinanomaliedetectiemodel configureren 

1.  Voeg de module **Train Anomaly Detection Model** toe aan uw pijplijn in de ontwerper. U vindt deze module in de categorie **Anomaliedetectie.**

2. Sluit een van de modules aan die zijn ontworpen voor anomaliedetectie, zoals [PCA-gebaseerde anomaliedetectie](pca-based-anomaly-detection.md)

    Andere soorten modellen worden niet ondersteund; bij het uitvoeren van de pijplijn krijgt u de fout: Alle modellen moeten hetzelfde leerlingtype hebben.  

3.  Configureer de module anomaliedetectie door de labelkolom te kiezen en andere parameters in te stellen die specifiek zijn voor het algoritme.  

4.  Bevestig een trainingsgegevensset aan de rechterinvoer van **Train Anomaly Detection Model.**  

5.  Verzend de pijplijn.  

## <a name="results"></a>Resultaten

Na de training is voltooid:

+ Als u de parameters van het model wilt weergeven, klikt u met de rechtermuisknop op de module en selecteert u **Visualiseren**. 

+ Als u voorspellingen wilt maken, gebruikt u [Scoremodel](score-model.md) met nieuwe invoergegevens.

+ Als u een momentopname van het getrainde model wilt opslaan, selecteert u de module en klikt u op het pictogram **Gegevensset registreren** onder het tabblad **Uitvoer+logboeken** in het rechterdeelvenster.   

 
## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 

Zie [Uitzonderingen en foutcodes voor de ontwerper (voorbeeld)](designer-error-codes.md) voor een lijst met fouten die specifiek zijn voor de ontwerpmodules.
'