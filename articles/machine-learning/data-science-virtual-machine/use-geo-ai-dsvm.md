---
title: De geo AI gebruiken
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over het gebruik van de geo AI-Data Science Virtual Machine om gegevens te analyseren en modellen te bouwen op basis van georuimtelijke gegevens.
keywords: diep leren, AI, data Science-hulpprogram ma's, data technologie virtuele machine, georuimtelijke analyse
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 2d7532c51e2963c0dc9f8d02e7a0e32864e80f92
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "70278420"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>De geo kunst matige intelligentie Data Science Virtual Machine gebruiken

Gebruik de geo AI-Data Science VM om gegevens op te halen voor analyse, gegevens wrangling uit te voeren en modellen te bouwen voor AI-toepassingen die georuimtelijke informatie gebruiken. Nadat u uw Geo AI Data Science VM hebt ingericht en u bent aangemeld bij ArcGIS Pro via uw ArcGIS-account, kunt u beginnen met ArcGIS Desktop en ArcGIs online. U kunt ook toegang krijgen tot ArcGIS van python-interfaces en een R-taal brug die vooraf is geconfigureerd op de geo-Data Science VM. Als u uitgebreide AI-toepassingen wilt bouwen, combineert u de geo-Data Science VM met de machine learning-en learning frameworks en andere data Science-software die erop beschikbaar zijn.  


## <a name="configuration-details"></a>Configuratie Details

De python-bibliotheek, [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), die wordt gebruikt voor de interface met ArcGIS, wordt geïnstalleerd in de globale hoofd Conda-omgeving van de data Science VM die ```c:\anaconda```is gevonden op.

- Als u python uitvoert vanaf een opdracht prompt, voert u ```activate``` uit om in te scha kelen in de Conda-hoofd-python-omgeving.
- Als u een IDE-of Jupyter-notebook gebruikt, kunt u de omgeving of kernel selecteren om er zeker van te zijn dat u zich in de juiste Conda-omgeving bevindt.

De R-brug naar ArcGIS is geïnstalleerd als R-bibliotheek met de naam [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) in het hoofd Microsoft machine learning server zelfstandige instantie dat ```C:\Program Files\Microsoft\ML Server\R_SERVER```zich bevindt. Visual Studio, RStudio en Jupyter zijn al vooraf geconfigureerd voor het gebruik van deze R-omgeving en hebben toegang tot de ```arcgisbinding``` r-bibliotheek. 


## <a name="geo-ai-data-science-vm-samples"></a>Voor beelden van geo AI-Data Science VM

Naast de voor beelden op het gebied van machine learning en diep gaande Framework uit het basis Data Science VM, wordt er ook een reeks georuimtelijke steek proeven gegeven als onderdeel van de geo AI-Data Science VM. Deze voor beelden kunnen u helpen bij het starten van de ontwikkeling van AI-toepassingen met behulp van georuimtelijke gegevens en de ArcGIS-software:


1. [Aan de slag met georuimtelijke analyse met python](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): een inleidend voor beeld waarin wordt getoond hoe u met georuimtelijke gegevens kunt werken via de Python-interface naar ArcGIS, wordt verschaft door de [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) -bibliotheek. Ook wordt uitgelegd hoe u traditionele machine learning combineert met georuimtelijke gegevens en vervolgens het resultaat kunt visualiseren op een kaart in ArcGIS.

2. [Aan de slag met een georuimtelijke analyse met R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): een inleidend voor beeld waarin wordt uitgelegd hoe u met georuimtelijke gegevens kunt werken met behulp van de R-interface voor ArcGIS die wordt verschaft door de [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) -bibliotheek. 

3. [Classificatie op pixel niveau land gebruik](https://github.com/Azure/pixel_level_land_classification): een zelf studie die illustreert hoe u een diepe Neural-netwerk model maakt dat een lucht afbeelding als invoer accepteert en een omslag label retourneert. Voor beelden van landings etiketten zijn in een *forest* en *water*. Het model retourneert een dergelijk label voor elke pixel in de afbeelding. 


## <a name="next-steps"></a>Volgende stappen

Extra voor beelden die gebruikmaken van de Data Science VM zijn hier beschikbaar:

* [Voorbeelden](dsvm-samples-and-walkthroughs.md)