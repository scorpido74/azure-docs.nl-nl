---
title: 'Gegevens toewijzen aan cluster: naslaginformatie over modules'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Gegevens toewijzen aan cluster in Azure Machine Learning om clusteringmodel te scoren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 207172f10277589af2b22ae2f41b07234a0925b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477711"
---
# <a name="module-assign-data-to-clusters"></a>Module: Gegevens toewijzen aan clusters

In dit artikel wordt beschreven hoe u de module *Gegevens toewijzen aan clusters* gebruiken in Azure Machine Learning-ontwerper (voorbeeld). De module genereert voorspellingen via een clusteringmodel dat is getraind met het *K-means-clustering-algoritme.*

De module Gegevens toewijzen aan clusters retourneert een gegevensset met de waarschijnlijke toewijzingen voor elk nieuw gegevenspunt. 

## <a name="how-to-use-assign-data-to-clusters"></a>Gegevens toewijzen aan clusters gebruiken
  
1. Zoek in Azure Machine Learning-ontwerper een eerder getraind clustermodel. U een clustermodel maken en trainen met behulp van een van de volgende methoden:  
  
    - Configureer het clusteralgoritme voor K-means-clustering met behulp van de [clustermodule K-Means clustering](k-means-clustering.md) en train het model met behulp van een gegevensset en de module Treinclusteringmodel (dit artikel).  
  
    - U ook een bestaand getraind clustermodel toevoegen vanuit de groep **Opgeslagen modellen** in uw werkruimte.

2. Koppel het getrainde model aan de linkerinvoerpoort van **Gegevens toewijzen aan clusters**.  

3. Voeg een nieuwe gegevensset toe als invoer. 

   In deze gegevensset zijn labels optioneel. Over het algemeen is clustering een leermethode zonder toezicht. Van u wordt niet verwacht dat u de categorieën van tevoren kent. De invoerkolommen moeten echter dezelfde zijn als de kolommen die zijn gebruikt bij het trainen van het clustermodel of als er een fout optreedt.

    > [!TIP]
    > Als u het aantal kolommen wilt verminderen dat naar de ontwerper wordt geschreven uit de clustervoorspellingen, gebruikt u [Kolommen selecteren in de gegevensset](select-columns-in-dataset.md)en selecteert u een subset van de kolommen. 
    
4. Laat het **selectievakje Controleren op toevoegen of uitvinken voor resultaat alleen** ingeschakeld als u wilt dat de resultaten de volledige invoergegevensset bevatten, inclusief een kolom met de resultaten (clustertoewijzingen).
  
    Als u dit selectievakje uitschakelt, worden alleen de resultaten geretourneerd. Deze optie kan handig zijn wanneer u voorspellingen maakt als onderdeel van een webservice.
  
5.  Verzend de pijplijn.  
  
### <a name="results"></a>Resultaten

+  Als u de waarden in de gegevensset wilt weergeven, klikt u met de rechtermuisknop op de module en selecteert u **Vervolgens Visualiseren**. Of Selecteer de module en ga naar het tabblad **Uitvoer** in het rechterdeelvenster en klik op het histogrampictogram in de **poortuitvoer** om het resultaat te visualiseren.

