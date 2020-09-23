---
title: 'Trans formatie Toep assen: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module trans formatie Toep assen in Azure Machine Learning om een invoer gegevensset te wijzigen op basis van een eerder berekende trans formatie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/05/2020
ms.openlocfilehash: 7573abbbee479bfb0d1710beba3b95d084a5e657
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898881"
---
# <a name="apply-transformation-module"></a>Transformatie module Toep assen

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik deze module om een invoer gegevensset te wijzigen op basis van een eerder berekende trans formatie.

Als u bijvoorbeeld z-scores hebt gebruikt voor het normaliseren van uw trainings gegevens met behulp van de module **normaliseren gegevens** , zou u ook de z-Score waarde willen gebruiken die tijdens de Score fase is berekend voor training. In Azure Machine Learning kunt u de normalisatie methode opslaan als een trans formatie en vervolgens **trans formatie Toep assen** gebruiken om de z-Score toe te passen op de invoer gegevens vóór de score.

## <a name="how-to-save-transformations"></a>Trans formaties opslaan

Met de ontwerp functie kunt u gegevens transformaties opslaan als gegevens **sets** , zodat u ze kunt gebruiken in andere pijp lijnen.

1. Selecteer een module voor gegevens transformatie die met succes is uitgevoerd.

1. Selecteer het tabblad **uitvoer en logboeken** .

1. Zoek de trans formatie-uitvoer en selecteer de **gegevensset registreren** om deze op te slaan als module onder **gegevens sets** in het module palet.

## <a name="how-to-use-apply-transformation"></a>Trans formatie Toep assen gebruiken  
  
1. Voeg de module **trans formatie Toep assen** toe aan uw pijp lijn. U kunt deze module vinden in het gedeelte beoordeling van de **model score &** van het palet van de module. 
  
1. Zoek de opgeslagen trans formatie die u wilt gebruiken onder **gegevens sets** in het module palet.

1. Verbind de uitvoer van de opgeslagen trans formatie met de linker invoer poort van de module **Apply trans formatie** .

    De gegevensset moet exact hetzelfde schema (aantal kolommen, kolom namen, gegevens typen) hebben als de gegevensset waarvoor de trans formatie voor het eerst is ontworpen.  
  
1. Verbind de gegevensset-uitvoer van de gewenste module met de juiste invoer poort van de module **Apply trans formatie** .
  
1. Als u een trans formatie wilt Toep assen op de nieuwe gegevensset, voert u de pijp lijn uit.  

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 