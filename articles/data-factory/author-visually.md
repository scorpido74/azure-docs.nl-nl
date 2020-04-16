---
title: Visueel ontwerpen
description: Meer informatie over het gebruik van visuele ontwerpen in Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: anandsub
ms.date: 12/19/2019
ms.openlocfilehash: e7de92878dac72470c0b65d1cf18c1a2d526a0bb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418487"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Visueel ontwerpen in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met de gebruikersinterface-ervaring (Azure Data Factory) u resources voor uw gegevensfabriek visueel maken en implementeren zonder dat u code hoeft te schrijven. U activiteiten naar een pijplijncanvas slepen, testuitvoeringen uitvoeren, iteratief debuggen en uw pijplijnuitvoeringen implementeren en controleren.

Momenteel wordt de UX azure datafactory alleen ondersteund in Microsoft Edge en Google Chrome.

## <a name="authoring-canvas"></a>Ontwerpcanvas

Als u het **ontwerpcanvas wilt**openen, klikt u op het potloodpictogram. 

![Ontwerpcanvas](media/author-visually/authoring-canvas.png)

Hier maakt u de pijplijnen, activiteiten, gegevenssets, gekoppelde services, gegevensstromen, triggers en integratie-looptijden die uw fabriek omvatten. Zie [Gegevens kopiëren met de kopieeractiviteit](tutorial-copy-data-portal.md)als u aan de slag wilt gaan met het maken van een pijplijn met het ontwerpcanvas. 

De standaardervaring voor visuele ontwerpbewerking werkt rechtstreeks met de Data Factory-service. Azure Repos Git- of GitHub-integratie wordt ook ondersteund om bronbeheer en samenwerking mogelijk te maken voor het werken aan uw pijplijnen in de gegevensfabriek. Zie [Bronbeheer in Azure Data Factory](source-control.md)voor meer informatie over de verschillen tussen deze ontwerpervaringen.

## <a name="expressions-and-functions"></a>Expressies en functies

Expressies en functies kunnen worden gebruikt in plaats van statische waarden om veel eigenschappen op te geven in Azure Data Factory.

Als u een expressie voor een eigenschapswaarde wilt opgeven, selecteert u **Dynamische inhoud toevoegen** of klikt u op Alt + **P** terwijl u zich op het veld concentreert.

![Dynamische inhoud toevoegen](media/author-visually/dynamic-content-1.png)

Hiermee wordt de **Opbouwfunctie voor gegevensfabrieksexpressies geopend,** waar u expressies bouwen op basis van ondersteunde systeemvariabelen, activiteitsuitvoer, functies en door de gebruiker opgegeven variabelen of parameters. 

![Opbouwfunctie voor expressies](media/author-visually/dynamic-content-2.png)

Zie [Expressies en functies in Azure Data Factory](control-flow-expression-language-functions.md)voor informatie over de expressietaal.

## <a name="provide-feedback"></a>Feedback geven

Selecteer **Feedback** om opmerkingen te maken over functies of om Microsoft op de hoogte te stellen van problemen met het hulpprogramma:

![Feedback](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Volgende stappen

Zie [Pijplijnen programmatisch controleren en beheren](monitor-programmatically.md)voor meer informatie over het bewaken en beheren van pijplijnen.
