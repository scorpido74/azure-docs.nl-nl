---
title: 'Gegevens exporteren: moduleverwijzing'
titleSuffix: Azure Machine Learning
description: Lees hoe u de module Gegevens exporteren in Azure Machine Learning gebruikt om resultaten, tussengegevens en werkgegevens uit uw pijplijnen op te slaan in cloudopslagbestemmingen buiten Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 807771fd4018c9666f059c965370ebc36d0105df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456298"
---
# <a name="export-data-module"></a>Module Gegevens exporteren

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om resultaten, tussenliggende gegevens en werkgegevens van uw pijplijnen op te slaan in cloudopslagbestemmingen. 

Deze module ondersteunt het exporteren van uw gegevens naar de volgende cloudgegevensservices:

- Azure Blob-container
- Azure-bestandsshare
- Azure Data Lake
- Azure Data Lake Gen2

Voordat u uw gegevens exporteert, moet u eerst een gegevensarchief registreren in uw Azure Machine Learning-werkruimte. Zie [Access-gegevens in Azure-opslagservices](../how-to-access-data.md)voor meer informatie.

## <a name="how-to-configure-export-data"></a>Exportgegevens configureren

1. Voeg de module **Gegevens exporteren** toe aan uw pijplijn in de ontwerper. U deze module vinden in de categorie **Invoer en uitvoer.**

1. Koppel **Exportgegevens** aan de module die de gegevens bevat die u wilt exporteren.

1. Selecteer **Gegevens exporteren** om het deelvenster Eigenschappen **te** openen.

1. Selecteer voor **Gegevensarchief**een bestaand gegevensarchief in de vervolgkeuzelijst. U ook een nieuwe datastore maken. Controleer hoe u [access-gegevens in Azure-opslagservices bezoekt.](../how-to-access-data.md)

1. Het selectievakje **Regenerate output**bepaalt of de module moet worden uitgevoerd om de uitvoer bij het uitvoeren van de looptijd te regenereren. 

    Het is standaard niet geselecteerd, wat betekent dat als de module eerder met dezelfde parameters is uitgevoerd, het systeem de uitvoer van de laatste uitvoering opnieuw gebruikt om de runtime te verkorten. 

    Als deze optie is geselecteerd, voert het systeem de module opnieuw uit om de uitvoer te regenereren.

1. Definieer het pad in het gegevensarchief waar de gegevens zich bevinden. Het pad is een relatief pad. De lege paden of een URL-paden zijn niet toegestaan.


1. Selecteer **bij Bestandsindeling**de indeling waarin gegevens moeten worden opgeslagen.
 
1. Verzend de pijplijn.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 
