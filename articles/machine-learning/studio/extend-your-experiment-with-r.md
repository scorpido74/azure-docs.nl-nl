---
title: Uw experiment uitbreiden met R
titleSuffix: ML Studio (classic) - Azure
description: De functionaliteit van Azure Machine Learning Studio (klassiek) uitbreiden via de R-taal met behulp van de Script-module Uitvoeren.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7b4b869695eb2073121a889cd81d99c4fc06d4b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218040"
---
# <a name="azure-machine-learning-studio-classic-extend-your-experiment-with-r"></a>Azure Machine Learning Studio (klassiek): breid uw experiment uit met R 

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
U de functionaliteit van Azure Machine Learning Studio (klassiek) uitbreiden via de R-taal met behulp van de [Script-module Uitvoeren.][execute-r-script]

Deze module accepteert meerdere invoergegevenssets en levert één gegevensset op als uitvoer. U een R-script typen in de parameter **R Script** van de scriptmodule [uitvoeren.][execute-r-script]

U krijgt toegang tot elke invoerpoort van de module met behulp van code die vergelijkbaar is met de volgende:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Alle momenteel geïnstalleerde pakketten aanbieden
De lijst met geïnstalleerde pakketten kan worden gewijzigd. Een lijst met momenteel geïnstalleerde pakketten is te vinden in [R-pakketten die worden ondersteund door Azure Machine Learning Studio (klassiek).](https://msdn.microsoft.com/library/azure/mt741980.aspx)

U ook de volledige, huidige lijst met geïnstalleerde pakketten krijgen door de volgende code in te voeren in de [Script-module uitvoeren:][execute-r-script]

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Hiermee wordt de lijst met pakketten naar de uitvoerpoort van de [Script-module Uitvoeren][execute-r-script] verzendt.
Als u de pakketlijst wilt weergeven, verbindt u een conversiemodule zoals [Converteren naar CSV][convert-to-csv] naar de linkeruitvoer van de scriptmodule [uitvoeren,][execute-r-script] voert u het experiment uit, klikt u vervolgens op de uitvoer van de conversiemodule en selecteert u **Downloaden**. 

![Downloaduitvoer van module 'Converteren naar CSV'](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](https://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Pakketten importeren
U pakketten importeren die nog niet zijn geïnstalleerd met behulp van de volgende opdrachten in de module [R-script uitvoeren:][execute-r-script]

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

waar `my_favorite_package.zip` het bestand uw pakket bevat.




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
