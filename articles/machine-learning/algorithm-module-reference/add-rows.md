---
title: 'Rijen toevoegen: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module rijen toevoegen in Azure Machine Learning om twee gegevens sets samen te voegen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: a16dc0bfc3fae8852c46435f6407c56149e1b14e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493945"
---
# <a name="add-rows-module"></a>Module rijen toevoegen

In dit artikel wordt een module in Azure Machine Learning Designer (preview) beschreven.

Gebruik deze module om twee gegevens sets samen te voegen. In samen voeging worden de rijen van de tweede gegevensset toegevoegd aan het einde van de eerste gegevensset.  
  
Het samen voegen van rijen is handig in scenario's zoals:  
  
+ U hebt een reeks evaluatie statistieken gegenereerd en wilt u deze combi neren in één tabel om de rapportage te vergemakkelijken.  
  
+ U hebt met verschillende gegevens sets gewerkt en u wilt de gegevens sets combi neren om een definitieve gegevensset te maken.  

## <a name="how-to-use-add-rows"></a>Rijen toevoegen gebruiken  

Als u rijen wilt samen voegen vanuit twee gegevens sets, moeten de rijen precies hetzelfde schema hebben. Dit betekent dat hetzelfde aantal kolommen en hetzelfde type gegevens in de kolommen.

1.  Sleep de module **rijen toevoegen** naar uw pijp lijn, u kunt deze vinden onder **gegevens transformatie**in de categorie **bewerken** .

2. Verbind de gegevens sets met de twee invoer poorten. De gegevensset die u wilt toevoegen, moet zijn verbonden met de tweede poort (rechts). 
  
3.  Voer de pijplijn uit. Het aantal rijen in de uitvoer gegevensset moet gelijk zijn aan de som van de rijen van beide invoer gegevens sets.

    Als u dezelfde gegevensset toevoegt aan beide invoer van de module **rijen toevoegen** , wordt de gegevensset gedupliceerd. 

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 