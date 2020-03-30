---
title: Python
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van Python in Azure Machine Learning designer om gegevens te transformeren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 02/28/2020
ms.openlocfilehash: a2bd9845cd29c7d139e2042f39b4697847639207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455788"
---
# <a name="execute-python-code-in-azure-machine-learning-designer"></a>Python-code uitvoeren in Azure Machine Learning-ontwerper

In dit artikel leert u hoe u de [Module Python Script uitvoeren](algorithm-module-reference/execute-python-script.md) gebruikt om aangepaste logica toe te voegen aan Azure Machine Learning-ontwerper. In de volgende how-to, gebruik je de Panda's bibliotheek om eenvoudige feature engineering te doen.

U de ingebouwde codeeditor gebruiken om snel eenvoudige Python-logica toe te voegen. Als u complexere code wilt toevoegen of extra Python-bibliotheken wilt uploaden, moet u de zip-bestandsmethode gebruiken.

De standaarduitvoeringsomgeving maakt gebruik van de Anacondas-distributie van Python. Zie de [referentiepagina van](algorithm-module-reference/execute-python-script.md) python scriptmodule uitvoeren voor een volledige lijst met vooraf geïnstalleerde pakketten.

![Python-invoerkaart uitvoeren](media/how-to-designer-python/execute-python-map.png)

## <a name="execute-python-written-in-the-designer"></a>Python uitvoeren geschreven in de ontwerper

### <a name="add-the-execute-python-script-module"></a>De module Python Script uitvoeren toevoegen

1. Zoek de **Module Python Script uitvoeren** in het ontwerppalet. Het is te vinden in de sectie **Python Language.**

1. Sleep en zet de module neer op het pijplijncanvas.

### <a name="connect-input-datasets"></a>Invoergegevenssets verbinden

In dit artikel wordt gebruik gemaakt van de voorbeeldgegevensset **Automobile price data (Raw).** 

1. Sleep en zet uw gegevensset neer op het pijplijncanvas.

1. Sluit de uitvoerpoort van de gegevensset aan op de invoerpoort linksboven van de **Python Script-module uitvoeren.** De ontwerper stelt de invoer als parameter bloot aan het itemscript.
    
    De juiste invoerpoort is gereserveerd voor python-bibliotheken met rits.

    ![Gegevenssets verbinden](media/how-to-designer-python/connect-dataset.png)
        

1. Let op welke invoerpoort u gebruikt. De ontwerper wijst de linkerinvoerpoort `dataset1` toe aan de `dataset2`variabele en de middelste invoerpoort aan . 

Invoermodules zijn optioneel omdat u rechtstreeks gegevens genereren of importeren in de **Module Python Script uitvoeren.**

### <a name="write-your-python-code"></a>Schrijf uw Python-code

De ontwerper biedt een eerste toegangspuntscript dat u bewerken en uw eigen Python-code invoeren. 

In dit voorbeeld gebruikt u Panda's om twee kolommen te combineren die in de autogegevensset, **Prijs** en **Pk worden**gevonden, om een nieuwe kolom te creëren, Dollars **per pk**. Deze kolom geeft aan hoeveel je betaalt voor elke pk, die een nuttige functie zou kunnen zijn om te beslissen of een auto is een goede deal voor het geld. 

1. Selecteer de module **Python Script uitvoeren.**

1. Selecteer in het deelvenster rechts van het canvas het tekstvak **Python-script.**

1. Kopieer en plak de volgende code in het tekstvak.

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    Uw pijplijn moet er op de volgende afbeelding uitzien:
    
    ![Python-pijplijn uitvoeren](media/how-to-designer-python/execute-python-pipeline.png)

    Het itemscript moet de `azureml_main`functie bevatten. Er zijn twee functieparameters die worden toegewezen aan de twee invoerpoorten voor de **Python Script-module uitvoeren.**

    De retourwaarde moet een Pandas Dataframe zijn. U maximaal twee gegevensframes retourneren als module-uitvoer.
    
1. Verzend de pijplijn.

Nu heb je een dataset met de nieuwe functie **Dollars / HP**, die nuttig kan zijn bij het trainen van een auto recommender. Dit is een voorbeeld van functieextractie en dimensionaliteitsreductie. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [importeren van uw eigen gegevens](how-to-designer-import-data.md) in Azure Machine Learning designer.