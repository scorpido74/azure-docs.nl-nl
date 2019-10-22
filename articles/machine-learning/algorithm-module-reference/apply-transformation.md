---
title: 'Trans formatie Toep assen: module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de module trans formatie Toep assen in Azure Machine Learning service om een invoer gegevensset te wijzigen op basis van een eerder berekende trans formatie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: adb2477fe2b7eea0dd0a98c7240843d90411d832
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693879"
---
# <a name="apply-transformation-module"></a>Transformatie module Toep assen

In dit artikel wordt een module van de Visual Interface (preview) voor de Azure Machine Learning-service beschreven.

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

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 