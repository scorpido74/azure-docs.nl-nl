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
ms.date: 10/22/2019
ms.openlocfilehash: 11f5bd7f01e142273509ae59ddc19a2557464bde
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152309"
---
# <a name="export-data-module"></a>Gegevens module exporteren

In dit artikel wordt een module in Azure Machine Learning Designer (preview) beschreven.

Gebruik deze module om resultaten, tussenliggende gegevens en werk gegevens van uw pijp lijnen op te slaan in de Cloud opslag buiten Azure Machine Learning. 

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

1. Definieer het pad in het gegevens archief waarnaar de gegevens moeten worden geschreven. 


1. Selecteer bij **bestands indeling**de indeling waarin de gegevens moeten worden opgeslagen.
 
1. Voer de pijplijn uit.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
