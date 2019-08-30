---
title: Met behulp van de Geo kunstmatige intelligentie Data Science Virtual Machine - Azure | Microsoft Docs
description: Informatie over het gebruik van de Geo AI Data Science Virtual Machine voor het analyseren van gegevens en bouw modellen op basis van georuimtelijke gegevens.
keywords: deep learning, AI, hulpprogramma's voor data science, virtuele machine voor datatechnologie, georuimtelijke analyses
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: vijetaj
ms.openlocfilehash: 22c0d7d604ca41044d2d969d4ddbd2ae1a4d23d5
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70170485"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Met behulp van de Geo kunstmatige intelligentie Data Science Virtual Machine

De Geo AI Data Science VM gebruiken voor het ophalen van gegevens voor analyse, het uitvoeren van data wrangling en bouw modellen voor AI-toepassingen die georuimtelijke gegevens gebruiken. Nadat u uw Geo AI Data Science VM hebt ingericht en u bent aangemeld bij ArcGIS Pro via uw ArcGIS-account, kunt u beginnen met ArcGIS Desktop en ArcGIs online. U kunt ook toegang krijgen tot ArcGIS van python-interfaces en een R-taal brug die vooraf is geconfigureerd op de geo-Data Science VM. Als u uitgebreide AI-toepassingen wilt bouwen, combineert u de geo-Data Science VM met de machine learning-en learning frameworks en andere data Science-software die erop beschikbaar zijn.  


## <a name="configuration-details"></a>Configuratiegegevens

De python-bibliotheek, [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), die wordt gebruikt voor de interface met ArcGIS, wordt geïnstalleerd in de globale hoofd Conda-omgeving van de data Science VM die ```c:\anaconda```is gevonden op.

- Als u python uitvoert vanaf een opdracht prompt, voert u ```activate``` uit om in te scha kelen in de Conda-hoofd-python-omgeving.
- Als u een IDE-of Jupyter-notebook gebruikt, kunt u de omgeving of kernel selecteren om er zeker van te zijn dat u zich in de juiste Conda-omgeving bevindt.

De R-brug naar ArcGIS is geïnstalleerd als R-bibliotheek met de naam [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) in het hoofd Microsoft machine learning server zelfstandige instantie dat ```C:\Program Files\Microsoft\ML Server\R_SERVER```zich bevindt. Visual Studio, RStudio en Jupyter zijn al vooraf geconfigureerd voor het gebruik van deze r-omgeving en hebben toegang tot de ```arcgisbinding``` r-bibliotheek. 


## <a name="geo-ai-data-science-vm-samples"></a>Voorbeelden van de geo AI Data Science VM

Naast de voor beelden op het gebied van machine learning en diep gaande Framework uit het basis Data Science VM, wordt er ook een reeks georuimtelijke steek proeven gegeven als onderdeel van de geo AI-Data Science VM. Deze voor beelden kunnen u helpen bij het starten van de ontwikkeling van AI-toepassingen met behulp van georuimtelijke gegevens en de ArcGIS-software:


1. [Aan de slag met georuimtelijke analyse met python](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Een inleidend voor beeld waarin wordt getoond hoe u met georuimtelijke gegevens kunt werken via de Python-interface naar ArcGIS, wordt gegeven door de [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) -bibliotheek. Ook wordt uitgelegd hoe u traditionele machine learning combineert met georuimtelijke gegevens en vervolgens het resultaat kunt visualiseren op een kaart in ArcGIS.

2. [Aan de slag met een georuimtelijke analyse met R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Een voor beeld van een inleidende informatie over hoe u met georuimtelijke gegevens kunt werken met behulp van de R-interface voor ArcGIS die wordt weer gegeven door de [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) -bibliotheek. 

3. [Classificatie voor land gebruik op pixel niveau](https://github.com/Azure/pixel_level_land_classification): Een zelf studie waarin wordt getoond hoe u een diepe Neural-netwerk model maakt dat een lucht afbeelding als invoer accepteert en een omslag label retourneert. Voor beelden van landings etiketten zijn in een *forest* en *water*. Het model geretourneerd die een label voor elke pixel in de afbeelding. Het model wordt gemaakt met behulp van het micro soft open-source [Cognitive Toolkit (CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) diepe Learning-Framework.


## <a name="next-steps"></a>Volgende stappen

Aanvullende voorbeelden van die gebruikmaken van de Data Science VM zijn hier beschikbaar:

* [Voorbeelden](dsvm-samples-and-walkthroughs.md)

