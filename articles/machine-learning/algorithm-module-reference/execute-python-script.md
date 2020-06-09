---
title: 'Python-script uitvoeren: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de script module python uitvoeren in Azure Machine Learning voor het uitvoeren van python-code.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: tracking-python
author: likebupt
ms.author: keli19
ms.date: 04/27/2020
ms.openlocfilehash: d25a738a76c955ee11f091bb0f8861bd21cc9f1d
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84555861"
---
# <a name="execute-python-script-module"></a>Python-script module uitvoeren

In dit artikel wordt een module in Azure Machine Learning Designer (preview) beschreven.

Gebruik deze module om python-code uit te voeren. Zie [het volgende artikel](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)voor meer informatie over de architectuur en ontwerp principes van python.

Met python kunt u taken uitvoeren die momenteel niet worden ondersteund door bestaande modules, zoals:

+ Gegevens visualiseren met`matplotlib`
+ Python-bibliotheken gebruiken om gegevens sets en modellen in uw werk ruimte op te sommen
+ Gegevens lezen, laden en bewerken vanuit bronnen die niet worden ondersteund door de module [gegevens importeren](./import-data.md)
+ Voer uw eigen diepe leer code uit 


Azure Machine Learning maakt gebruik van de Anaconda-distributie van python, met veel algemene hulpprogram ma's voor gegevens verwerking. De Anaconda-versie wordt automatisch bijgewerkt. Huidige versie is:
 -  Anaconda 4.5 + Distribution voor python 3,6 

De vooraf geïnstalleerde pakketten zijn:
-    adal = = 1.2.2
-    applicationinsights = = 0.11.9
-    attr = = 19.3.0
-    Azure-common = = 1.1.25
-    Azure-Core = = 1.3.0
-    Azure-graphrbac = = 0.61.1
-    Azure-Identity = = 1.3.0
-    Azure-beheer-autorisatie = = 0.60.0
-    Azure-beheer-containerregistry = = 2.8.0
-    Azure-beheer-sleutel kluis = = 2.2.0
-    Azure-beheer-resource = = 8.0.1
-    Azure-beheer-opslag = = 8.0.0
-    Azure-Storage-BLOB = = 1.5.0
-    Azure-opslag-common = = 1.4.2
-    azureml-Core = = 1.1.5.5
-    azureml-dataprep-native = = 14.1.0
-    azureml-dataprep = = 1.3.5
-    azureml-defaults = = 1.1.5.1
-    azureml-Designer-Classic-modules = = 0.0.118
-    azureml-Designer-core = = 0.0.31
-    azureml-Designer-Internal = = 0.0.18
-    azureml-Model-Management-SDK = = 1.0.1 B6. post1
-    azureml-pijp lijn-Core = = 1.1.5
-    azureml-telemetrie = = 1.1.5.3
-    backports. tempfile = = 1.0
-    backports. weakref = = 1.0. post1
-    boto3 = = 1.12.29
-    botocore = = 1.15.29
-    cache-werkset = = 4.0.0
-    Certificeer = = 2019.11.28
-    cffi = = 1.12.3
-    chardet = = 3.0.4
-    Klik = = 7.1.1
-    cloudpickle = = 1.3.0
-    configparser = = 3.7.4
-    contextlib2 = = 0.6.0. post1
-    crypto grafie = = 2.8
-    Cycle = = 0.10.0
-    Dill = = 0.3.1.1
-    distributie = = 1.4.0
-    docker = = 4.2.0
-    docutils = = 0.15.2
-    dotnetcore2 = = 2.1.13
-    kolf = = 1.0.3
-    fusepy = = 3.0.1
-    gensim = = 3.8.1
-    Google-API-Core = = 1.16.0
-    Google-auth = = 1.12.0
-    Google-Cloud-Core = = 1.3.0
-    Google-Cloud-Storage = = 1.26.0
-    Google-hervattingable-media = = 0.5.0
-    googleapis-algemeen-proto = = 1.51.0
-    gunicorn = = 19.9.0
-    IDNA = = 2,9
-    niet in evenwicht: meer informatie = = 0.4.3
-    isodate = = 0.6.0
-    itsdangerous = = 1.1.0
-    jeepney = = 0.4.3
-    jinja2 = = 2.11.1
-    jmespath = = 0.9.5
-    joblib = = 0.14.0
-    JSON-logboek registratie-py = = 0,2
-    jsonpickle = = 1.3
-    jsonschema = = 3.0.1
-    kiwisolver = = 1.1.0
-    Liac-ARFF = = 2.4.0
-    lightgbm = = 2.2.3
-    markupsafe = = 1.1.1
-    matplotlib = = 3.1.3
-    meer-ITER-extra = = 6.0.0
-    msal-Extensions = = 0.1.3
-    msal = = 1.1.0
-    msrest = = 0.6.11
-    msrestazure = = 0.6.3
-    NDG-httpsclient = = 0.5.1
-    nimbusml = = 1.6.1
-    numpy = = 1.18.2
-    oauthlib = = 3.1.0
-    Pandas = = 0.25.3
-    pathspec = = 0.7.0
-    PIP = = 20.0.2
-    portalocker = = 1.6.0
-    protobuf = = 3.11.3
-    pyarrow = = 0.16.0
-    pyasn1-modules = = 0.2.8
-    pyasn1 = = 0.4.8
-    pycparser = = 2.20
-    pycryptodomex = = 3.7.3
-    pyjwt = = 1.7.1
-    pyopenssl = = 19.1.0
-    pyparsing = = 2.4.6
-    pyrsistent = = 0.16.0
-    python-dateutil = = 2.8.1
-    pytz = = 2019.3
-    aanvragen-oauthlib = = 1.3.0
-    aanvragen = = 2.23.0
-    RSA = = 4.0
-    ruamel. yaml = = 0.15.89
-    s3transfer = = 0.3.3
-    scikit-Learn = = 0.22.2
-    scipy = = 1.4.1
-    secretstorage = = 3.1.2
-    installatie-instellingen = = 46.1.1. post20200323
-    zes = = 1.14.0
-    Smart-open = = 1.10.0
-    urllib3 = = 1.25.8
-    WebSocket-client = = 0.57.0
-    werkzeug = = 0.16.1
-    wiel = = 0.34.2

 Als u andere pakketten wilt installeren die niet in de vooraf geïnstalleerde lijst voor komt, bijvoorbeeld *scikit-misc*, voegt u de volgende code toe aan het script: 

 ```python
import os
os.system(f"pip install scikit-misc")
```
> [!NOTE]
> Als uw pijp lijn meerdere python-script modules voor uitvoeren bevat en dezelfde pakketten nodig heeft die niet voor komen in de lijst met vooraf geïnstalleerde, installeert u de pakketten in elke module. 

## <a name="upload-files"></a>Bestanden uploaden
Het **script python uitvoeren** ondersteunt het uploaden van bestanden met behulp van [Azure machine learning python SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-).

In het volgende voor beeld ziet u hoe u een afbeeldings bestand uploadt in de script module voor het **uitvoeren van python** :

```Python

# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to
import pandas as pd

# The entry point function must have two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"
    plt.savefig(img_file)

    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    run.upload_file(f"graphics/{img_file}", img_file)

    # Return value must be of a sequence of pandas.DataFrame
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

Nadat de pijplijn uitvoering is voltooid, kunt u een voor beeld van de afbeelding in het rechterdeel venster van de module bekijken

> [!div class="mx-imgBorder"]
> ![Geüpload-afbeelding](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>Het configureren van een script voor het uitvoeren van python

De **script module python uitvoeren** bevat een voor beeld van python-code die u als uitgangs punt kunt gebruiken. Als u de **script** module voor het uitvoeren van python wilt configureren, geeft u een set invoer-en Python-code op die moet worden uitgevoerd in het **python-script** tekstvak.

1. Voeg de **script module python uitvoeren** toe aan de pijp lijn.

2. Voeg gegevens sets toe aan **Dataset1** uit de ontwerp functie die u voor invoer wilt gebruiken en maak er verbinding mee. Verwijs naar deze gegevensset in uw python-script als **DataFrame1**.

    Het gebruik van een gegevensset is optioneel, als u gegevens wilt genereren met behulp van python of python-code wilt gebruiken om de gegevens rechtstreeks in de module te importeren.

    Deze module ondersteunt toevoeging van een tweede gegevensset op **Dataset2**. Raadpleeg de tweede gegevensset in uw python-script als DataFrame2.

    Gegevens sets die zijn opgeslagen in Azure Machine Learning worden automatisch geconverteerd naar **Panda** data. frames wanneer deze met deze module worden geladen.

    ![Python-invoer toewijzing uitvoeren](media/module/python-module.png)

4. Als u nieuwe Python-pakketten of-code wilt toevoegen, voegt u het zip-bestand toe dat deze aangepaste resources bevat in de **script bundel**. De invoer voor de **script bundel** moet een zip-bestand zijn dat is geüpload naar uw werk ruimte als een dataset van het bestands type. U kunt de gegevensset uploaden op de Asset-pagina **gegevens sets** en u kunt de module gegevensset slepen en neerzetten vanuit de lijst **mijn gegevens sets** in de structuur van de module voor ontwerpen in Designer. 

    Elk bestand in het geüploade gezipte archief kan worden gebruikt tijdens de uitvoering van de pijp lijn. Als het archief een mapstructuur bevat, blijft de structuur behouden, maar moet u een map met de naam **src** naar het pad laten voorafgaan door.

5. Typ of plak geldige python-script in het tekstvak **python-script** .

    > [!NOTE]
    > Wees zeer voorzichtig bij het schrijven van uw script en zorg ervoor dat er geen syntaxis fout is, zoals het gebruik van niet-gedeclareerde objecten of niet-geïmporteerde modules. U kunt ook extra aandacht best Eden aan de lijst met vooraf geïnstalleerde modules. Als u modules wilt importeren die niet worden weer gegeven, installeert u de bijbehorende pakketten in uw script, zoals
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    Het **python-script** tekstvak is vooraf ingevuld met enkele instructies in opmerkingen, en voorbeeld code voor gegevens toegang en-uitvoer. U moet deze code bewerken of vervangen. Zorg ervoor dat u python-conventies voor inspringing en hoofdletter gebruik volgt.

    + Het script moet een functie bevatten met `azureml_main` de naam van het toegangs punt voor deze module.
    + De functie voor het toegangs punt moet twee invoer argumenten hebben: `Param<dataframe1>` en `Param<dataframe2>` , zelfs wanneer deze argumenten niet worden gebruikt in uw script.
    + Gezipte bestanden die zijn verbonden met de derde invoer poort, worden uitgepakt en opgeslagen in de map, `.\Script Bundle` , die ook aan de python worden toegevoegd `sys.path` . 

    Als uw zip-bestand bevat, kunt u `mymodule.py` het ook importeren met `import mymodule` .

    + Er kunnen twee gegevens sets worden geretourneerd naar de ontwerper. dit moet een reeks van het type zijn `pandas.DataFrame` . U kunt andere uitvoer in uw Python-code maken en deze rechtstreeks naar Azure Storage schrijven.

6. Verzend de pijp lijn of selecteer de module en klik op **geselecteerde uitvoeren** om alleen het python-script uit te voeren.

    Alle gegevens en code worden in een virtuele machine geladen en uitgevoerd met behulp van de opgegeven python-omgeving.

## <a name="results"></a>Resultaten

De resultaten van alle berekeningen die door de Inge sloten python-code worden uitgevoerd, moeten als een Panda worden geleverd. Data frame, die automatisch wordt geconverteerd naar de indeling van de Azure Machine Learning gegevensset, zodat u de resultaten kunt gebruiken met andere modules in de pijp lijn.

De module retourneert twee gegevens sets:  
  
+ **Gegevensset 1**die is gedefinieerd door de eerste geretourneerde Panda data frame in Python-script

+ **Resulterende gegevensset 2**, gedefinieerd door de tweede geretourneerde Panda data frame in Python-script


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
