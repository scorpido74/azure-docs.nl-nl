---
title: DenseNet
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken van een afbeeldings classificatie model met behulp van de densenet-algoritme.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: db0e2f90ee45d4e1c0173cbc037084793a66e149
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84450645"
---
# <a name="densenet"></a>DenseNet

In dit artikel wordt beschreven hoe u de **DenseNet** -module in azure machine learning Designer (preview) gebruikt om een classificatie model voor installatie kopieën te maken met behulp van de DenseNet-algoritme.  

Deze classificatie-algoritme is een geclassificeerde leer methode en vereist een gegevensset met een label. Raadpleeg [converteren naar afbeelding Directory](convert-to-image-directory.md) module voor meer instructies over het ophalen van een gelabelde map met een afbeelding. U kunt het model trainen door het model en de map met de gelabelde afbeelding op te geven als invoer voor het [trainen van Pytorch-model](train-pytorch-model.md). Het getrainde model kan vervolgens worden gebruikt om waarden voor de nieuwe invoer voorbeelden te voors pellen met behulp van [Score afbeeldings model](score-image-model.md).

### <a name="more-about-densenet"></a>Meer informatie over DenseNet

Raadpleeg voor meer informatie de [Convolutional-netwerken met een hoge](https://arxiv.org/abs/1608.06993) benetwerk.

## <a name="how-to-configure-densenet"></a>DenseNet configureren

1.  Voeg de module **DenseNet** toe aan uw pijp lijn in de ontwerp functie.  

2.  Geef bij **model naam**de naam op van een bepaalde densenet-structuur en u kunt kiezen uit de ondersteunde densenet: ' densenet121 ', ' densenet161 ', ' densenet169 ', ' densenet201 '.

3.  Voor vooraf **getrainde**geeft u op of u een voor ImageNet wilt gebruiken. Indien geselecteerd, kunt u model verfijnen op basis van het geselecteerde vooraf getrainde model. Als u dit selectie vakje uitschakelt, kunt u helemaal zelf trainen.

4.  Geef voor **geheugen efficiënt**op of controle punten moeten worden gebruikt. Dit is veel meer geheugen-efficiënt, maar langzamer. Zie https://arxiv.org/pdf/1707.06990.pdf voor meer informatie.

5.  Verbind de uitvoer van de **DenseNet** -module, de trainings-en validatie afbeelding gegevensset-module naar het [Train Pytorch-model](train-pytorch-model.md). 

6. Verzend de pijp lijn.


## <a name="results"></a>Resultaten

Wanneer de uitvoering van de pijp lijn is voltooid, kunt u het model voor scores gebruiken door het model van [Train Pytorch](train-pytorch-model.md) te verbinden met een [Score image model](score-image-model.md)om waarden voor nieuwe invoer voorbeelden te voors pellen.

## <a name="technical-notes"></a>Technische opmerkingen  

###  <a name="module-parameters"></a>Module parameters  

| Name             | Bereik | Type    | Standaard     | Description                              |
| ---------------- | ----- | ------- | ----------- | ---------------------------------------- |
| Modelnaam       | Alle   | Modus    | densenet201 | Naam van een bepaalde densenet-structuur     |
| Voortraind       | Alle   | Boolean-waarde | True        | Of u een vooraf getrainde model wilt gebruiken op ImageNet |
| Geheugen efficiënt | Alle   | Boolean-waarde | False       | Of controle punten moeten worden gebruikt, wat veel geheugen efficiënt maar langzamer is |

###  <a name="output"></a>Uitvoer  

| Naam            | Type                    | Description                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Niet-traind model | UntrainedModelDirectory | Een niet-traind densenet-model dat kan worden verbonden met Train Pytorch model. |

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
