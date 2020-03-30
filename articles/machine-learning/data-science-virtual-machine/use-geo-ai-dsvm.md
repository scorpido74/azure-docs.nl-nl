---
title: De Geo AI gebruiken
titleSuffix: Azure Data Science Virtual Machine
description: Leer hoe u de Geo AI Data Science Virtual Machine gebruiken om gegevens te analyseren en modellen te bouwen op basis van georuimtelijke gegevens.
keywords: deep learning, AI, data science tools, data science virtual machine, geospatial analytics
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 2d7532c51e2963c0dc9f8d02e7a0e32864e80f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70278420"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Met behulp van de Geo Artificial Intelligence Data Science Virtual Machine

Gebruik de Geo AI Data Science VM om gegevens op te halen voor analyse, datagetouw te maken en modellen te bouwen voor AI-toepassingen die georuimtelijke informatie gebruiken. Nadat u uw Geo AI Data Science VM hebt ingericht en u hebt aangemeld bij ArcGIS Pro via uw ArcGIS-account, u online communiceren met ArcGIS-desktop en ArcGIs. U hebt ook toegang tot ArcGIS via Python-interfaces en een R-taalbrug die vooraf is geconfigureerd op de Geo-Data Science VM. Om rijke AI-toepassingen te bouwen, combineert u de Geo-Data Science VM met de machine learning- en deep learning-frameworks en andere data science-software die erop beschikbaar zijn.  


## <a name="configuration-details"></a>Configuratiegegevens

De Python-bibliotheek, [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), die wordt gebruikt om te communiceren met ArcGIS, is geïnstalleerd in ```c:\anaconda```de wereldwijde root conda-omgeving van de Data Science VM die wordt gevonden op .

- Als u Python op een opdrachtprompt ```activate``` uitvoert, voert u uit om te activeren in de python-omgeving van de conda-wortel.
- Als u een IDE- of Jupyter-notitieblok gebruikt, u de omgeving of kernel selecteren om te controleren of u zich in de juiste conda-omgeving bevindt.

De R-brug naar ArcGIS is geïnstalleerd als een R-bibliotheek met de naam [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) in het hoofdstouw se id van Microsoft Machine Learning Server dat zich op ```C:\Program Files\Microsoft\ML Server\R_SERVER```. Visual Studio, RStudio en Jupyter zijn al vooraf geconfigureerd om deze ```arcgisbinding``` R-omgeving te gebruiken en hebben toegang tot de R-bibliotheek. 


## <a name="geo-ai-data-science-vm-samples"></a>Geo AI Data Science VM-monsters

Naast de op machine learning en deep learning framework gebaseerde samples van de basis Data Science VM, wordt er ook een set geospatiale samples geleverd als onderdeel van de Geo AI Data Science VM. Deze voorbeelden kunnen u helpen uw ontwikkeling van AI-toepassingen te starten met behulp van geospatiale gegevens en de ArcGIS-software:


1. [Aan de slag met geospatiale analyse met Python](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Een inleidend voorbeeld dat laat zien hoe je met georuimtelijke gegevens werken via de Python-interface naar ArcGIS, wordt geleverd door de [arcpy-bibliotheek.](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) Het laat ook zien hoe traditionele machine learning te combineren met georuimtelijke gegevens en vervolgens visualiseren het resultaat op een kaart in ArcGIS.

2. [Aan de slag met geospatiale analyse met R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Een inleidende steekproef die laat zien hoe je met georuimtelijke gegevens werken door de R-interface te gebruiken voor ArcGIS die wordt geleverd door de [arcgisbinding-bibliotheek.](https://github.com/R-ArcGIS/r-bridge) 

3. [Classificatie landgebruik op pixelniveau](https://github.com/Azure/pixel_level_land_classification): een zelfstudie die illustreert hoe u een diep neuraal netwerkmodel maakt dat een luchtfoto accepteert als invoer en een land-coverlabel retourneert. Voorbeelden van etiketten met landbedekking zijn *bebost* en *water.* Het model retourneert een dergelijk label voor elke pixel in de afbeelding. 


## <a name="next-steps"></a>Volgende stappen

Aanvullende voorbeelden die gebruik maken van de Data Science VM zijn hier beschikbaar:

* [Monsters](dsvm-samples-and-walkthroughs.md)