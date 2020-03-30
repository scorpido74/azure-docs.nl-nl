---
title: 'Rijen toevoegen: modulenaslag'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Rijen toevoegen in Azure Machine Learning om twee gegevenssets samen te voegen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: cd9b5f8f182c4deab746d2c41e516a6ac23fb7aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477728"
---
# <a name="add-rows-module"></a>Module Rijen toevoegen

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om twee gegevenssets te concaten. In samenvoeging worden de rijen van de tweede gegevensset toegevoegd aan het einde van de eerste gegevensset.  
  
Samenvoeging van rijen is handig in scenario's als deze:  
  
+ U hebt een reeks evaluatiestatistieken gegenereerd en u wilt deze combineren in één tabel voor eenvoudigere rapportage.  
  
+ U hebt met verschillende gegevenssets gewerkt en u wilt de gegevenssets combineren om een definitieve gegevensset te maken.  

## <a name="how-to-use-add-rows"></a>Rijen toevoegen gebruiken  

Om rijen uit twee gegevenssets te laten zien, moeten de rijen precies hetzelfde schema hebben. Dit betekent dat hetzelfde aantal kolommen en hetzelfde type gegevens in de kolommen.

1.  Sleep de module **Rijen toevoegen** in uw pijplijn, u deze vinden onder **Gegevenstransformatie**.

2. Verbind de gegevenssets met de twee invoerpoorten. De gegevensset die u wilt toevoegen, moet worden verbonden met de tweede (juiste) poort. 
  
3.  Verzend de pijplijn. Het aantal rijen in de uitvoergegevensset moet gelijk zijn aan de som van de rijen van beide invoergegevenssets.

    Als u dezelfde gegevensset toevoegt aan beide ingangen van de module **Rijen toevoegen,** wordt de gegevensset gedupliceerd. 

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 