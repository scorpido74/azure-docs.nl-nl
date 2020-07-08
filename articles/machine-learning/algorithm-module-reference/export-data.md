---
title: 'Gegevens exporteren: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u de module gegevens exporteren in Azure Machine Learning kunt gebruiken om resultaten, tussenliggende gegevens en werk gegevens van uw pijp lijnen op te slaan in de Cloud opslag buiten Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 807771fd4018c9666f059c965370ebc36d0105df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79456298"
---
# <a name="export-data-module"></a>Gegevens module exporteren

In dit artikel wordt een module in Azure Machine Learning Designer (preview) beschreven.

Gebruik deze module om resultaten, tussenliggende gegevens en werk gegevens van uw pijp lijnen op te slaan in opslag locaties in de Cloud. 

Deze module biedt ondersteuning voor het exporteren van uw gegevens naar de volgende Cloud gegevens Services:

- Azure Blob-container
- Azure-bestandsshare
- Azure Data Lake
- Azure Data Lake Gen2

Voordat u uw gegevens exporteert, moet u eerst een gegevens opslag registreren in uw Azure Machine Learning-werk ruimte. Zie [toegang tot gegevens in azure Storage-services](../how-to-access-data.md)voor meer informatie.

## <a name="how-to-configure-export-data"></a>Export gegevens configureren

1. Voeg de module **gegevens exporteren** toe aan uw pijp lijn in de ontwerp functie. U kunt deze module vinden in de categorie **invoer en uitvoer** .

1. Verbind gegevens met het **exporteren** naar de module die de gegevens bevat die u wilt exporteren.

1. Selecteer **gegevens exporteren** om het deel venster **Eigenschappen** te openen.

1. Selecteer een bestaande gegevens opslag in de vervolg keuzelijst voor **gegevens opslag**. U kunt ook een nieuw gegevens archief maken. Raadpleeg de informatie [in azure Storage-services om toegang te krijgen tot de gegevens](../how-to-access-data.md).

1. Het selectie vakje, **uitvoer opnieuw genereren**, bepaalt of de module moet worden uitgevoerd om de uitvoer tijdens de uitvoering opnieuw te genereren. 

    Deze optie is standaard niet geselecteerd, wat betekent dat als de module met dezelfde para meters eerder is uitgevoerd, de uitvoer van de laatste uitvoering opnieuw wordt gebruikt om de uitvoerings tijd te verminderen. 

    Als deze is geselecteerd, voert het systeem de module opnieuw uit om de uitvoer opnieuw te genereren.

1. Definieer het pad in het gegevens archief waarin de gegevens zich bevinden. Het pad is een relatief pad. De lege paden of URL-paden zijn niet toegestaan.


1. Selecteer bij **bestands indeling**de indeling waarin de gegevens moeten worden opgeslagen.
 
1. Verzend de pijp lijn.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
