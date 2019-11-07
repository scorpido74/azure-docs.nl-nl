---
title: Uw experiment uitbreiden met R
titleSuffix: ML Studio (classic) Azure
description: De functionaliteit van Azure Machine Learning Studio (klassiek) uitbreiden via de R-taal met behulp van de script module Execute R.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 1a64303702ffa5ccff9c79e0946d5df45eac9479
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621649"
---
# <a name="azure-machine-learning-studio-classic-extend-your-experiment-with-r"></a>Azure Machine Learning Studio (klassiek): uw experiment uitbreiden met R 
U kunt de functionaliteit van Azure Machine Learning Studio (klassiek) uitbreiden via de R-taal door gebruik te maken van de module [r-script uitvoeren][execute-r-script] .

Deze module accepteert meerdere invoer gegevens sets en levert één gegevensset als uitvoer op. U kunt een R-script in de **r-script** parameter van de module voor het uitvoeren van een [r][execute-r-script] -script typen.

U hebt toegang tot elke invoer poort van de module met behulp van code die er ongeveer als volgt uitziet:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Een lijst met alle momenteel geïnstalleerde pakketten
De lijst met geïnstalleerde pakketten kan worden gewijzigd. Een lijst met geïnstalleerde pakketten vindt u in [R-pakketten die worden ondersteund door Azure machine learning Studio (klassiek)](https://msdn.microsoft.com/library/azure/mt741980.aspx).

U kunt ook de volledige, huidige lijst met geïnstalleerde pakketten ophalen door de volgende code in te voeren in de module voor het [uitvoeren van R-scripts][execute-r-script] :

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Hiermee wordt de lijst met pakketten verzonden naar de uitvoer poort van de module voor het uitvoeren van een [R-script][execute-r-script] .
Als u de pakket lijst wilt weer geven, koppelt u een conversie module, zoals [converteren naar CSV][convert-to-csv] , naar de linkerkant van de [script module Execute R][execute-r-script] , voert u het experiment uit, klikt u op de uitvoer van de module conversie en selecteert u **downloaden**. 

![Download uitvoer van de module Convert to CSV](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](https://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Pakketten importeren
U kunt pakketten importeren die nog niet zijn geïnstalleerd met behulp van de volgende opdrachten in de module voor het uitvoeren van een [R-script][execute-r-script] :

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

waar het `my_favorite_package.zip`-bestand het pakket bevat.




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
