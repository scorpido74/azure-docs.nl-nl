---
title: 'Trans formatie Toep assen: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module trans formatie Toep assen in Azure Machine Learning om een invoer gegevensset te wijzigen op basis van een eerder berekende trans formatie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 19385870d184654d902cd40b45d4be3646c87b46
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493900"
---
# <a name="apply-transformation-module"></a>Transformatie module Toep assen

In dit artikel wordt een module in Azure Machine Learning Designer (preview) beschreven.

Gebruik deze module om een invoer gegevensset te wijzigen op basis van een eerder berekende trans formatie.  
  
Als u bijvoorbeeld z-scores hebt gebruikt voor het normaliseren van uw trainings gegevens met behulp van de module **normaliseren gegevens** , zou u ook de z-Score waarde willen gebruiken die tijdens de Score fase is berekend voor training. In Azure Machine Learning kunt u de normalisatie methode opslaan als een trans formatie en vervolgens **trans formatie Toep assen** gebruiken om de z-Score toe te passen op de invoer gegevens vóór de score.
  
Azure Machine Learning biedt ondersteuning voor het maken en Toep assen van verschillende soorten aangepaste trans formaties. Stel dat u trans formaties wilt opslaan en opnieuw wilt gebruiken voor:  
  
- Ontbrekende waarden verwijderen of vervangen, met behulp van **schone ontbrekende gegevens**
- Gegevens normaliseren met behulp van **Normal data**
  

## <a name="how-to-use-apply-transformation"></a>Trans formatie Toep assen gebruiken  
  
1. Voeg de module **trans formatie Toep assen** toe aan uw pijp lijn. U kunt deze module vinden onder **machine learning**in de categorie **Score** . 
  
2. Zoek een bestaande trans formatie die moet worden gebruikt als invoer.  Eerder opgeslagen trans formaties vindt u in de groep **trans formatie** in het navigatie deel venster aan de linkerkant.  
  
   
  
3. Verbind de gegevensset die u wilt transformeren. De gegevensset moet exact hetzelfde schema (aantal kolommen, kolom namen, gegevens typen) hebben als de gegevensset waarvoor de trans formatie voor het eerst is ontworpen.  
  
4. Er hoeven geen andere para meters te worden ingesteld, omdat alle aanpassingen worden uitgevoerd bij het definiëren van de trans formatie.  
  
5. Als u een trans formatie wilt Toep assen op de nieuwe gegevensset, voert u de pijp lijn uit.  

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 