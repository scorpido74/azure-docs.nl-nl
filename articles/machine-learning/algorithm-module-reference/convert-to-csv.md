---
title: 'Converteren naar CSV: modulenaslag'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Converteren naar CSV in Azure Machine Learning om een gegevensset om te zetten in een CSV-indeling die kan worden gedownload, geëxporteerd of gedeeld met R- of Python-scriptmodules.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: fc2a043e8f1565cf5fe45ba0b072ad015076635e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477677"
---
# <a name="convert-to-csv-module"></a>Converteren naar CSV-module

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om een gegevensset om te zetten in een CSV-indeling die kan worden gedownload, geëxporteerd of gedeeld met R- of Python-scriptmodules.

### <a name="more-about-the-csv-format"></a>Meer informatie over de CSV-indeling 

De CSV-indeling, die staat voor "door komma's gescheiden waarden", is een bestandsindeling die door veel externe machine learning-tools wordt gebruikt. CSV is een veelgebruikte uitwisselingsindeling bij het werken met open-sourcetalen zoals R of Python.

Zelfs als u het grootste deel van uw werk in Azure Machine Learning doet, zijn er momenten waarop u het handig vindt om uw gegevensset om te zetten naar CSV om te gebruiken in externe hulpprogramma's. Bijvoorbeeld:

+ Download het CSV-bestand om het te openen met Excel of importeer het in een relationele database.  
+ Sla het CSV-bestand op in cloudopslag en maak er verbinding mee vanuit Power BI om visualisaties te maken.  
+ Gebruik de CSV-indeling om gegevens voor te bereiden op gebruik in R en Python. 

Wanneer u een gegevensset converteert naar CSV, wordt de csv opgeslagen in uw Azure ML-werkruimte. U een Azure-opslaghulpprogramma gebruiken om het bestand rechtstreeks te openen en te gebruiken. U ook toegang krijgen tot de CSV in de ontwerper door de module **Converteren naar CSV te** selecteren en vervolgens het histogrampictogram onder het tabblad **Uitvoer** in het rechterdeelvenster te selecteren om de uitvoer weer te geven. U de CSV downloaden van de map Resultaten naar een lokale map.  

## <a name="how-to-configure-convert-to-csv"></a>Converteren naar CSV configureren


1.  Voeg de module Converteren naar CSV toe aan de pijplijn. U vindt deze module in de groep **Gegevenstransformatie** in de ontwerper. 

2. Sluit deze aan op elke module die een gegevensset uitvoert.   
  
3.  Verzend de pijplijn.

### <a name="results"></a>Resultaten
  

Selecteer het tabblad **Uitvoer** in het rechterdeelvenster van **Converteren naar CSV**en selecteer op een van deze pictogrammen onder de **poortuitvoer**.  

+ **Gegevensset Registreren**: Selecteer het pictogram en sla het CSV-bestand terug naar de Azure ML-werkruimte als afzonderlijke gegevensset. U vindt de gegevensset als module in de modulestructuur onder de sectie **Mijn gegevenssets.**

 + **Weergaveuitvoer:** Selecteer het oogpictogram en volg de instructie om door de **map Results_dataset** te bladeren en download het bestand data.csv.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 