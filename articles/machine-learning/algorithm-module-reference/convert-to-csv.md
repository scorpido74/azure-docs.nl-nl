---
title: 'Converteren naar CSV: module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de module Convert to CSV in Azure Machine Learning service om een gegevensset te converteren naar een CSV-indeling die kan worden gedownload, geëxporteerd of worden gedeeld met R-of python-script modules.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 37c1653b31049dc0142cf8c0c6f6d405338b1d1f
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693200"
---
# <a name="convert-to-csv-module"></a>Converteren naar CSV-module

In dit artikel wordt een module van de Visual Interface (preview) voor de Azure Machine Learning-service beschreven.

Gebruik deze module om een gegevensset te converteren naar een CSV-indeling die kan worden gedownload, geëxporteerd of worden gedeeld met R-of python-script modules.

### <a name="more-about-the-csv-format"></a>Meer informatie over de CSV-indeling 

De CSV-indeling, die staat voor ' door komma's gescheiden waarden ', is een bestands indeling die wordt gebruikt door veel externe machine learning-hulpprogram ma's. CSV is een gemeen schappelijke Interchange-indeling bij het werken met open-source talen, zoals R of python.

Zelfs als u het meren deel van uw werk in Azure Machine Learning hebt, zijn er situaties waarin het handig kan zijn om uw gegevensset om te zetten in CSV om te gebruiken in externe hulpprogram ma's. Bijvoorbeeld:

+ Down load het CSV-bestand om het te openen met Excel of importeer het in een relationele data base.  
+ Sla het CSV-bestand op in de Cloud opslag en maak er verbinding mee vanuit Power BI om visualisaties te maken.  
+ Gebruik de CSV-indeling om gegevens voor te bereiden voor gebruik in R en python. Klik met de rechter muisknop op de uitvoer van de module om de code te genereren die nodig is voor toegang tot de gegevens rechtstreeks vanuit Python of een Jupyter-notebook. 

Wanneer u een gegevensset converteert naar CSV, wordt het bestand opgeslagen in uw Azure ML-werk ruimte. U kunt een Azure-opslag hulpprogramma gebruiken om het bestand rechtstreeks te openen en te gebruiken, of u kunt met de rechter muisknop op de module-uitvoer klikken en het CSV-bestand downloaden naar uw computer of gebruiken in R-of python-code.  

## <a name="how-to-configure-convert-to-csv"></a>Conversie naar CSV configureren

1.  Voeg de module [Convert to CSV](./convert-to-csv.md) toe aan de pijp lijn. Deze module vindt u in de groep **gegevens indeling conversies** in de-interface. 

2. Verbind het met een wille keurige module die een gegevensset uitvoert.   
  
3.  Voer de pijplijn uit.

### <a name="results"></a>Resultaten
  

Dubbel klik op de uitvoer van [converteren naar CSV](./convert-to-csv.md)en selecteer een van deze opties.  

 + **Resultaat gegevensset-> downloaden**: Hiermee opent u direct een kopie van de gegevens in CSV-indeling die u kunt opslaan in een lokale map. Als u geen map opgeeft, wordt een standaard bestands naam toegepast en wordt het CSV-bestand opgeslagen in de lokale bibliotheek met **down loads** .


 + **Resultaat gegevensset-> opslaan als gegevensset**: slaat het CSV-bestand weer op in de Azure ml-werk ruimte als afzonderlijke gegevensset.

 + **Gegevens toegangs code genereren**: Azure ml genereert twee sets code voor toegang tot de gegevens, hetzij met behulp van python, hetzij met behulp van R. Kopieer het code fragment in uw toepassing om toegang te krijgen tot de gegevens. (*Gegevens toegangs code genereren wordt binnenkort beschikbaar.* )

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 