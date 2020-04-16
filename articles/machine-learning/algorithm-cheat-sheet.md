---
title: Machine Learning Algorithm Cheat Sheet - ontwerper
titleSuffix: Azure Machine Learning
description: Met een afdrukbare Machine Learning Algorithm Cheat Sheet u het juiste algoritme kiezen voor uw voorspellende model in Azure Machine Learning designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 03/05/2020
ms.openlocfilehash: accc4766f25f5d58ba2f31c153fe1c5d23413170
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399070"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Opspeelpagina voor machine learning-algoritmes voor Azure Machine Learning-ontwerper

Met de **Azure Machine Learning Algorithm Cheat Sheet** u het juiste algoritme van de ontwerper kiezen voor een voorspellend analysemodel.

Azure Machine Learning heeft een grote bibliotheek met algoritmen uit de ***classificatie,*** ***recommender-systemen,*** ***clustering,*** ***anomaliedetectie,*** ***regressie***en ***tekstanalysefamilies.*** Elk is ontworpen om een ander type machine learning-probleem aan te pakken.

Zie [Algoritmen selecteren voor](how-to-select-algorithms.md) aanvullende richtlijnen

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Download: Machine Learning Algorithm Cheat Sheet

**Download de cheat sheet hier: [Machine Learning Algorithm Cheat Sheet (11x17 in.)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![Opleesblad voor machine learning-algoritmen: meer informatie over het kiezen van een Machine Learning-algoritme.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.svg)

Download en print de Machine Learning Algorithm Cheat Sheet in tabloid grootte om het bij de hand te houden en hulp te krijgen bij het kiezen van een algoritme.

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>Het opspeelblad voor het machine learning-algoritme gebruiken

De suggesties aangeboden in dit algoritme cheat sheet zijn bij benadering vuistregels. Sommige kunnen worden gebogen, en sommige kunnen flagrant worden geschonden. Dit spiekbriefje is bedoeld om een startpunt te suggereren. Wees niet bang om een head-to-head concurrentie tussen verschillende algoritmen op uw gegevens uit te voeren. Er is gewoon geen substituut voor het begrijpen van de principes van elk algoritme en het systeem dat uw gegevens gegenereerd.

Elk machine learning algoritme heeft zijn eigen stijl of inductieve bias. Voor een specifiek probleem kunnen verschillende algoritmen geschikt zijn en kan het ene algoritme beter passen dan andere. Maar het is niet altijd mogelijk om van tevoren te weten wat de beste pasvorm is. In gevallen als deze worden verschillende algoritmen samen in het spiekbriefje weergegeven. Een geschikte strategie zou zijn om een algoritme te proberen, en als de resultaten nog niet bevredigend zijn, probeer de anderen. 

Ga voor meer informatie over de algoritmen in Azure Machine Learning designer naar de [referentie algoritme en module.](algorithm-module-reference/module-reference.md)

## <a name="kinds-of-machine-learning"></a>Soorten machine learning

Er zijn drie hoofdcategorieën van machine learning: *begeleid leren,* *leren zonder toezicht*en *versterkingsleren*.

### <a name="supervised-learning"></a>Onder toezicht leren

Bij begeleid leren wordt elk gegevenspunt gelabeld of gekoppeld aan een categorie of waarde van belang. Een voorbeeld van een categorisch label is het toewijzen van een afbeelding als een 'kat' of een 'hond'. Een voorbeeld van een waardelabel is de verkoopprijs die aan een gebruikte auto is gekoppeld. Het doel van begeleid leren is om veel gelabelde voorbeelden als deze te bestuderen, en vervolgens voorspellingen te kunnen doen over toekomstige gegevenspunten. Bijvoorbeeld het identificeren van nieuwe foto's met het juiste dier of het toewijzen van nauwkeurige verkoopprijzen aan andere gebruikte auto's. Dit is een populaire en nuttige vorm van machine learning.

### <a name="unsupervised-learning"></a>Leren zonder toezicht

Bij onbegeleid leren zijn er geen labels aan gekoppeld. In plaats daarvan is het doel van een onbewaakt leeralgoritme om de gegevens op een bepaalde manier te ordenen of de structuur ervan te beschrijven. Onbegeleid leren groepeert gegevens in clusters, zoals K-means doet, of vindt verschillende manieren om naar complexe gegevens te kijken, zodat deze eenvoudiger lijken.

### <a name="reinforcement-learning"></a>Bekrachtigend leren

Bij reinforcement learning mag het algoritme een actie kiezen als reactie op elk gegevenspunt. Het is een gemeenschappelijke aanpak in robotica, waarbij de set van sensormetingen op een bepaald moment een datapunt is, en het algoritme de volgende actie van de robot moet kiezen. Het is ook een natuurlijke pasvorm voor Internet of Things toepassingen. Het leeralgoritme ontvangt even later ook een beloningssignaal, dat aangeeft hoe goed de beslissing was. Op basis van dit signaal wijzigt het algoritme zijn strategie om de hoogste beloning te bereiken. 

## <a name="next-steps"></a>Volgende stappen

* Zie aanvullende richtlijnen voor [het selecteren van algoritmen](how-to-select-algorithms.md)

* [Meer informatie over studio's in Azure Machine Learning en de Azure-portal](overview-what-is-azure-ml.md).

* [Zelfstudie: Een voorspellingsmodel bouwen in Azure Machine Learning-ontwerper](tutorial-designer-automobile-price-train-score.md).

* [Meer informatie over deep learning versus machine learning](concept-deep-learning-vs-machine-learning.md).
