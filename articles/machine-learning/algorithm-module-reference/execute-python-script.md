---
title: 'Python-script uitvoeren: modulenaslag'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de Python Script-module uitvoeren in Azure Machine Learning om Python-code uit te voeren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: 79dc1b188e91028a98f43dc24972228f2d2101be
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684732"
---
# <a name="execute-python-script-module"></a>Python Script-module uitvoeren

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om Python-code uit te voeren. Zie [het volgende artikel](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)voor meer informatie over de architectuur en ontwerpprincipes van Python.

Met Python u taken uitvoeren die momenteel niet worden ondersteund door bestaande modules, zoals:

+ Gegevens visualiseren met behulp van`matplotlib`
+ Python-bibliotheken gebruiken om gegevenssets en -modellen in uw werkruimte op te sommen
+ Gegevens lezen, laden en manipuleren uit bronnen die niet worden ondersteund door de module [Gegevens importeren](./import-data.md)
+ Voer je eigen deep learning-code uit 


Azure Machine Learning maakt gebruik van de Anaconda-distributie van Python, die veel algemene hulpprogramma's voor gegevensverwerking bevat. We zullen de Anaconda-versie automatisch bijwerken. Huidige versie is:
 -  Anaconda 4.5+ distributie voor Python 3.6 

De vooraf geïnstalleerde pakketten zijn:
-    adal==1.2.2
-    applicationinsights==0.11.9
-    attrs==19.3.0
-    azure-common==1.1.25
-    azure-core==1.3.0
-    azure-graphrbac==0,61,1
-    azure-identity==1.3.0
-    azure-mgmt-authorization==0.60.0
-    azure-mgmt-containerregistry==2.8.0
-    azure-mgmt-keyvault==2.2.0
-    azure-mgmt-resource==8.0.1
-    azure-mgmt-storage==8.0.0
-    azure-storage-blob==1.5.0
-    veelvoorkomende azure-storage-common==1.4.2
-    azureml-core==1.1.5.5
-    azureml-dataprep-native==14.1.0
-    azureml-dataprep==1.3.5
-    azureml-defaults==1.1.5.1
-    azureml-designer-classic-modules==0.0.118
-    azureml-designer-core==0.0.31
-    azureml-designer-internal==0.0.18
-    azureml-model-management-sdk==1.0.1b6.post1
-    azureml-pipeline-core==1.1.5
-    azureml-telemetrie==1.1.5.3
-    backports.tempfile==1.0
-    backports.weakref==1.0.post1
-    boto3==1.12.29
-    botocore==1.15.29
-    cachetools==4.0.0
-    certifi==2019.11.28
-    cffi==1.12.3
-    chardet==3.0.4
-    klik==7.1.1
-    cloudpickle==1.3.0
-    configparser==3.7.4
-    contextlib2==0,6.0.post1
-    cryptografie==2.8
-    cycler==0,10.0
-    dille==0,3.1.1
-    distro==1.4.0
-    docker==4.2.0
-    docutils==0.15.2
-    dotnetcore2==2.1.13
-    kolf==1.0.3
-    fusepy==3.0.1
-    gensim==3.8.1
-    google-api-core==1.16.0
-    google-auth==1.12.0
-    google-cloud-core==1.3.0
-    google-cloud-storage==1.26.0
-    google-ressumable-media==0.5.0
-    googleapis-common-protos==1.51.0
-    gunicorn==19.9.0
-    idna==2.9
-    onbalans-leren==0,4.3
-    isodate==0,6.0
-    itsdangerous==1.1.0
-    jeepney==0.4.3
-    jinja2==2.11.1
-    jmespath==0,9,5
-    joblib==0,14.0
-    json-logging-py==0,2
-    jsonpickle==1.3
-    jsonschema==3.0.1
-    kiwisolver==1.1.0
-    liac-arff==2.4.0
-    lightgbm==2.2.3
-    markupsafe==1.1.1
-    matplotlib==3.1.3
-    meer-itertools==6.0.0
-    msal-extensies==0,1,3
-    msal==1.1.0
-    msrest==0.6.11
-    msrestazure==0,6,3
-    ndg-httpsclient==0,5.1
-    nimbusml==1.6.1
-    numpy==1.18.2
-    oauthlib==3.1.0
-    panda's==0.25.3
-    pathspec==0,7.0
-    pip==20.0.2
-    portalocker==1.6.0
-    protobuf==3.11.3
-    pyarrow==0,16,0
-    pyasn1-modules==0,2,8
-    pyasn1==0,4,8
-    pycparser==2.20
-    pycryptodomex==3.7.3
-    pyjwt==1.7.1
-    pyopenssl==19.1.0
-    pyparsing==2.4.6
-    pyrsistent==0,16,0
-    python-dateutil==2.8.1
-    pytz==2019.3
-    requests-oauthlib==1.3.0
-    aanvragen==2.23.0
-    rsa==4.0
-    ruamel.yaml==0.15.89
-    s3transfer==0,3.3
-    scikit-learn==0.22.2
-    scipy==1.4.1
-    secretstorage==3.1.2
-    setuptools==46.1.1.post20200323
-    zes==1.14.0
-    smart-open==1.10.0
-    urllib3==1.25.8
-    websocket-client==0,57.0
-    werkzeug==0,16.1
-    wiel==0.34.2

 Als u andere pakketten wilt installeren die niet in de vooraf geïnstalleerde lijst staan, bijvoorbeeld *scikit-misc,* voegt u de volgende code toe aan uw script: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="upload-files"></a>Bestanden uploaden
Het **Python Script uitvoeren** ondersteunt het uploaden van bestanden met Azure Machine Learning Python [SDK.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-)

In het volgende voorbeeld ziet u hoe u een afbeeldingsbestand uploadt in de Module **Python Script uitvoeren:**

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

Nadat de pijplijnrun is voltooid, u een voorbeeld van de afbeelding bekijken in het rechterdeelvenster van de module

> [!div class="mx-imgBorder"]
> ![Geüploade afbeelding](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>Python Script uitvoeren configureren

De **Python Script-module uitvoeren** bevat voorbeeldpython-code die u als uitgangspunt gebruiken. Als u de **Module Python Script uitvoeren** wilt configureren, geeft u een set invoer en Python-code die u wilt uitvoeren in het tekstvak **Python-script.**

1. Voeg de **Python Script-module uitvoeren** toe aan uw pijplijn.

2. Voeg gegevenssets van de ontwerper die u wilt gebruiken voor invoer toe en maak verbinding met **Dataset1.** Verwijs naar deze gegevensset in uw Python-script als **DataFrame1**.

    Het gebruik van een gegevensset is optioneel als u gegevens wilt genereren met Python of python-code wilt gebruiken om de gegevens rechtstreeks in de module te importeren.

    Deze module ondersteunt de toevoeging van een tweede gegevensset op **Dataset2**. Verwijs naar de tweede gegevensset in uw Python-script als DataFrame2.

    Gegevenssets die zijn opgeslagen in Azure Machine Learning worden automatisch geconverteerd naar **panda's** data.frames wanneer ze met deze module worden geladen.

    ![Python-invoerkaart uitvoeren](media/module/python-module.png)

4. Als u nieuwe Python-pakketten of -code wilt opnemen, voegt u het zip-bestand met deze aangepaste bronnen toe op **de Script-bundel.** De invoer naar **scriptbundel** moet een zip-bestand zijn dat naar uw werkruimte is geüpload als gegevensset voor bestandstypen. U de gegevensset **uploaden** in de assetpagina Gegevenssets en u de gegevenssetmodule uit de lijst **Mijn gegevenssets** slepen en neerzetten in de linkermodulestructuur op de pagina met ontwerpen van ontwerpers. 

    Elk bestand in het geüploade archief met rits kan worden gebruikt tijdens de uitvoering van de pijplijn. Als het archief een directorystructuur bevat, blijft de structuur behouden, maar moet u een map met de naam **src** voorbereiden op het pad.

5. Typ of plak in het tekstvak **Python-script** een geldig Python-script.

    > [!NOTE]
    > Wees erg voorzichtig bij het schrijven van uw script en zorg ervoor dat er geen syntaxisfout is, zoals het gebruik van een niet-gedeclareerd object of een niet-geïmporteerde module. Besteed ook extra aandacht aan de vooraf geïnstalleerde modulelijst. Als u modules wilt importeren die niet worden vermeld, installeert u de bijbehorende pakketten in uw script, zoals
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    Het **tekstvak van het Python-script** is vooraf ingevuld met enkele instructies in opmerkingen en voorbeeldcode voor toegang tot gegevens en uitvoer. U moet deze code bewerken of vervangen. Zorg ervoor dat u Python-conventies over inkeping en behuizing volgt.

    + Het script moet een `azureml_main` functie bevatten die is genoemd als ingangspunt voor deze module.
    + De functie item moet twee `Param<dataframe1>` invoerargumenten hebben: en `Param<dataframe2>`zelfs wanneer deze argumenten niet in uw script worden gebruikt.
    + Ziped bestanden aangesloten op de derde ingang poort worden `.\Script Bundle`uitgepakt en opgeslagen in `sys.path`de directory, , die ook wordt toegevoegd aan de Python . 

    Daarom, als uw `mymodule.py`zip-bestand `import mymodule`bevat, importeren met behulp van .

    + Twee gegevenssets kunnen worden geretourneerd naar de ontwerper, `pandas.DataFrame`die een reeks van het type moet zijn. U andere uitvoer in uw Python-code maken en deze rechtstreeks naar Azure-opslag schrijven.

6. Verzend de pijplijn of selecteer de module en klik op **Geselecteerd uitvoeren** om alleen het Python-script uit te voeren.

    Alle gegevens en code worden in een virtuele machine geladen en worden uitgevoerd met de opgegeven Python-omgeving.

## <a name="results"></a>Resultaten

De resultaten van alle berekeningen uitgevoerd door de ingesloten Python-code moeten worden verstrekt als een panda' s. DataFrame, dat automatisch wordt geconverteerd naar de azure machine learning-gegevenssetindeling, zodat u de resultaten gebruiken met andere modules in de pijplijn.

De module retourneert twee gegevenssets:  
  
+ **Resultaten Dataset 1**, gedefinieerd door het eerste geretourneerde panda's dataframe in Python script

+ **Resultaat dataset 2**, gedefinieerd door het tweede geretourneerde panda's dataframe in Python script


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 