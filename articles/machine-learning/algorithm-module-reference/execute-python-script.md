---
title: 'Python-script uitvoeren: Module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over hoe u de script module voor python uitvoeren in Azure Machine Learning service kunt gebruiken om python-code uit te voeren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 4bd3433db92767f2d0d733ab71e4298fc5e618f8
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128812"
---
# <a name="execute-python-script-module"></a>Python-script module uitvoeren

In dit artikel wordt een module van de Visual Interface (preview) voor de Azure Machine Learning-service beschreven.

Gebruik deze module om python-code uit te voeren. Zie [het volgende artikel](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts) voor meer informatie over de architectuur en ontwerp principes van python.

Met python kunt u taken uitvoeren die momenteel niet worden ondersteund door bestaande modules, zoals:

+ Gegevens visualiseren met`matplotlib`
+ Python-bibliotheken gebruiken om gegevens sets en modellen in uw werk ruimte op te sommen
+ Gegevens lezen, laden en bewerken vanuit bronnen die niet worden ondersteund door de module [gegevens importeren](./import-data.md)
+ Voer uw eigen diepe leer code uit 


Azure Machine Learning maakt gebruik van de Anaconda-distributie van python, met veel algemene hulpprogram ma's voor gegevens verwerking. De Anaconda-versie wordt automatisch bijgewerkt. Huidige versie is:
 -  Anaconda 4.5 + Distribution voor python 3,6 

De vooraf geïnstalleerde pakketten zijn:
-  asn1crypto==0.24.0
- attrs==19.1.0
- azure-common==1.1.18
- Azure-Storage-BLOB = = 1.5.0
- Azure-opslag-common = = 1.4.0
- certifi==2019.3.9
- cffi==1.12.2
- chardet==3.0.4
- crypto grafie = = 2.6.1
- distributie = = 1.4.0
- IDNA = = 2.8
- jsonschema==3.0.1
- lightgbm==2.2.3
- more-itertools==6.0.0
- numpy==1.16.2
- pandas==0.24.2
- Pillow==6.0.0
- pip==19.0.3
- pyarrow = = 0.12.1
- pycparser = = 2.19
- pycryptodomex = = 3.7.3
- pyrsistent==0.14.11
- python-dateutil==2.8.0
- pytz==2018.9
- requests==2.21.0
- scikit-learn==0.20.3
- scipy==1.2.1
- installatie-instellingen = = 40.8.0
- zes = = 1.12.0
- torch==1.0.1.post2
- torchvision==0.2.2.post3
- urllib3==1.24.1
- wheel==0.33.1 

 Als u andere pakketten wilt installeren die niet in de vooraf geïnstalleerde lijst voor komt, bijvoorbeeld *scikit-misc*, voegt u de volgende code toe aan het script: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="how-to-use"></a>Gebruiksinstructies

De **script module python uitvoeren** bevat een voor beeld van python-code die u als uitgangs punt kunt gebruiken. Als u de **script** module voor het uitvoeren van python wilt configureren, geeft u een set invoer-en Python-code op die moet worden uitgevoerd in het **python-script** tekstvak.

1. Voeg de **script module python uitvoeren** toe aan uw experiment.

2. Voeg gegevens sets toe aan **Dataset1** uit de interface die u voor invoer wilt gebruiken en maak er verbinding mee. Verwijs naar deze gegevensset in uw python-script als **DataFrame1**.

    Het gebruik van een gegevensset is optioneel, als u gegevens wilt genereren met behulp van python of python-code wilt gebruiken om de gegevens rechtstreeks in de module te importeren.

    Deze module ondersteunt toevoeging van een tweede gegevensset op **Dataset2**. Raadpleeg de tweede gegevensset in uw python-script als DataFrame2.

    Gegevens sets die zijn opgeslagen in Azure Machine Learning worden automatisch geconverteerd naar **Panda** data. frames wanneer deze met deze module worden geladen.

    ![Python-invoer toewijzing uitvoeren](media/module/python-module.png)

4. Als u nieuwe Python-pakketten of-code wilt toevoegen, voegt u het zip-bestand toe dat deze aangepaste resources bevat in de **script bundel**. De invoer voor de **script bundel** moet een zip-bestand zijn dat al is geüpload naar uw werk ruimte. 

    Elk bestand in het geüploade gezipte archief kan worden gebruikt tijdens de uitvoering van het experiment. Als het archief een mapstructuur bevat, blijft de structuur behouden, maar moet u een map met de naam **src** naar het pad laten voorafgaan door.

5. Typ of plak geldige python-script in het tekstvak **python-script** .

    Het **python-script** tekstvak is vooraf ingevuld met enkele instructies in opmerkingen, en voorbeeld code voor gegevens toegang en-uitvoer. **U moet deze code bewerken of vervangen.** Zorg ervoor dat u python-conventies voor inspringing en hoofdletter gebruik volgt.

    + Het script moet een functie bevatten met `azureml_main` de naam van het toegangs punt voor deze module.
    + De functie voor het toegangs punt kan Maxi maal twee invoer argumenten `Param<dataframe1>` bevatten: en`Param<dataframe2>`
    + Gezipte bestanden die zijn verbonden met de derde invoer poort, worden uitgepakt en opgeslagen `.\Script Bundle`in de map,, die ook aan `sys.path`de python worden toegevoegd. 

    Als uw zip-bestand bevat `mymodule.py`, kunt u het ook importeren met. `import mymodule`

    + Er kunnen twee gegevens sets worden geretourneerd naar de interface, die een reeks van het `pandas.DataFrame`type moet zijn. U kunt andere uitvoer in uw Python-code maken en deze rechtstreeks naar Azure Storage schrijven.

6. Voer het experiment uit of selecteer de module en klik op **geselecteerde uitvoeren** om alleen het python-script uit te voeren.

    Alle gegevens en code worden in een virtuele machine geladen en uitgevoerd met behulp van de opgegeven python-omgeving.

## <a name="results"></a>Resultaten

De resultaten van alle berekeningen die door de Inge sloten python-code worden uitgevoerd, moeten als een Panda worden geleverd. Data frame, die automatisch wordt geconverteerd naar de indeling van de Azure Machine Learning gegevensset, zodat u de resultaten met andere modules in het experiment kunt gebruiken.

De module retourneert twee gegevens sets:  
  
+ **Gegevensset 1**die is gedefinieerd door de eerste geretourneerde Panda data frame in Python-script

+ **Resulterende gegevensset 2**, gedefinieerd door de tweede geretourneerde Panda data frame in Python-script


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 