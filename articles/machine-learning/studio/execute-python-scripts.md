---
title: Python-scripts uitvoeren
titleSuffix: ML Studio (classic) - Azure
description: Meer informatie over het gebruik van de Python Script-module uitvoeren om Python-code te gebruiken in Machine Learning Studio (klassieke) experimenten en webservices.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: c79f6bd63fa5d8d8c6b22ff271d8ca513a94fd64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218087"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>Python machine learning-scripts uitvoeren in Azure Machine Learning Studio (klassiek)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Python is een waardevol hulpmiddel in de gereedschapskist van veel gegevenswetenschappers. Het wordt gebruikt in elke fase van typische machine learning-workflows, waaronder gegevensverkenning, functieextractie, modeltraining en -validatie en implementatie.

In dit artikel wordt beschreven hoe u de Python Script-module uitvoeren gebruiken om Python-code te gebruiken in uw Azure Machine Learning Studio (klassieke) experimenten en webservices.

## <a name="using-the-execute-python-script-module"></a>De module Python Script uitvoeren gebruiken

De primaire interface naar Python in Studio (klassiek) is via de [Execute Python Script][execute-python-script] module. Het accepteert maximaal drie ingangen en produceert maximaal twee uitgangen, vergelijkbaar met de [Script-module uitvoeren.][execute-r-script] Python-code wordt ingevoerd in het parametervak via `azureml_main`een speciaal genoemde entry-point-functie genaamd .

![Python Script-module uitvoeren](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Voorbeeld python-code in parametervak module](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Invoerparameters

Ingangen naar de Python-module worden weergegeven als Pandas DataFrames. De `azureml_main` functie accepteert maximaal twee optionele Pandas DataFrames als parameters.

De toewijzing tussen invoerpoorten en functieparameters is positioneel:

- De eerste verbonden invoerpoort wordt toegewezen aan de eerste parameter van de functie.
- De tweede invoer (indien verbonden) wordt toegewezen aan de tweede parameter van de functie.
- De derde invoer wordt gebruikt om [extra Python-modules](#import-modules)te importeren.

Meer gedetailleerde semantiek van hoe de invoerpoorten `azureml_main` worden toegewezen aan parameters van de functie worden hieronder weergegeven.

![Tabel met invoerpoortconfiguraties en resulterende Python-handtekening](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Rendementswaarden voor uitvoer

De `azureml_main` functie moet een enkele Panda's DataFrame retourneren die is verpakt in een [Python-reeks,](https://docs.python.org/2/c-api/sequence.html) zoals een tuple-, lijst- of NumPy-array. Het eerste element van deze reeks wordt teruggezet naar de eerste uitvoerpoort van de module. De tweede uitvoerpoort van de module wordt gebruikt voor [visualisaties](#visualizations) en vereist geen retourwaarde. Deze regeling wordt hieronder weergegeven.

![Invoerpoorten toewijzen aan parameters en retourwaarde naar uitvoerpoort](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Vertaling van invoer- en uitvoergegevenstypen

Studio datasets zijn niet hetzelfde als Panda DataFrames. Als gevolg hiervan worden invoergegevenssets in Studio (klassiek) geconverteerd naar Pandas DataFrame en worden uitvoerdataframes teruggezet naar Studio (klassieke) datasets. Tijdens dit conversieproces worden ook de volgende vertalingen uitgevoerd:

 **Python-gegevenstype** | **Studio vertaalprocedure** |
| --- | --- |
| Tekenreeksen en numerieke gegevens| Vertaald zoals het is |
| Panda's 'NA' | Vertaald als 'Ontbrekende waarde' |
| Indexvectoren | Niet ondersteund* |
| Niet-tekenreekskolomnamen | Oproep `str` aan kolomnamen |
| Kolomnamen dupliceren | Voeg numeriek achtervoegsel toe: (1), (2), (3), enzovoort.

**Alle invoergegevensframes in de Functie Python hebben altijd een 64-bits numerieke index van 0 tot het aantal rijen min 1*

## <a name="importing-existing-python-script-modules"></a><a id="import-modules"></a>Bestaande Python-scriptmodules importeren

De backend die wordt gebruikt om Python uit te voeren is gebaseerd op [Anaconda](https://www.anaconda.com/distribution/), een veelgebruikte wetenschappelijke Python-distributie. Het wordt geleverd met bijna 200 van de meest voorkomende Python-pakketten die worden gebruikt in datacentrische workloads. Studio (klassiek) ondersteunt momenteel geen gebruik van package management systemen zoals Pip of Conda voor het installeren en beheren van externe bibliotheken.  Als u de noodzaak vindt om extra bibliotheken op te nemen, gebruikt u het volgende scenario als leidraad.

Een veelgebruikte use-case is om bestaande Python-scripts op te nemen in Studio (klassieke) experimenten. De [Module Python Script uitvoeren][execute-python-script] accepteert een zip-bestand met Python-modules bij de derde invoerpoort. Het bestand wordt uitgepakt door het uitvoeringskader tijdens runtime en de inhoud wordt toegevoegd aan het bibliotheekpad van de Python-tolk. De `azureml_main` entry point-functie kan deze modules vervolgens rechtstreeks importeren. 

Als voorbeeld, overweeg het bestand Hello.py met een eenvoudige "Hallo, Wereld" functie.

![Door de gebruiker gedefinieerde functie in Hello.py bestand](./media/execute-python-scripts/figure4.png)

Vervolgens maken we een bestand Hello.zip dat Hello.py bevat:

![Zip-bestand met door de gebruiker gedefinieerde Python-code](./media/execute-python-scripts/figure5.png)

Upload het zip-bestand als gegevensset naar Studio (klassiek). Maak en voer vervolgens een experiment uit dat de Python-code in het Hello.zip-bestand gebruikt door deze te koppelen aan de derde invoerpoort van de **Module Python Script uitvoeren,** zoals in de volgende afbeelding wordt weergegeven.

![Voorbeeldexperiment met Hello.zip als invoer voor een Python Script-module uitvoeren](./media/execute-python-scripts/figure6a.png)

![Door de gebruiker gedefinieerde Python-code geüpload als zip-bestand](./media/execute-python-scripts/figure6b.png)

De module-uitvoer geeft aan dat het zip-bestand is onverpakt en dat de functie `print_hello` is uitgevoerd.

![Moduleuitvoer met door de gebruiker gedefinieerde functie](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Toegang tot Azure Storage Blobs

U hebt met de volgende stappen toegang tot gegevens die zijn opgeslagen in een Azure Blob Storage-account:

1. Download het [Azure Blob Storage-pakket voor Python](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) lokaal.
1. Upload het zip-bestand naar uw Studio (klassieke) werkruimte als een gegevensset.
1. Uw BlobService-object maken met`protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. Veilige overdracht uitschakelen **die vereist is** op het tabblad **Opslagconfiguratie**

![Secure-overdracht uitschakelen die vereist is in de Azure-portal](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Python-scripts operationaliseren

Alle [Python Script-modules uitvoeren][execute-python-script] die in een scoreexperiment worden gebruikt, worden aangeroepen wanneer ze worden gepubliceerd als een webservice. In de onderstaande afbeelding wordt bijvoorbeeld een scoreexperiment weergegeven dat de code bevat om één Python-expressie te evalueren.

![Studiowerkruimte voor een webservice](./media/execute-python-scripts/figure3a.png)

![Python Panda's expressie](./media/execute-python-scripts/python-script-with-python-pandas.png)

Een webservice die met dit experiment is gemaakt, neemt de volgende acties op zich:

1. Een Python-expressie als invoer (als tekenreeks)
1. De Python-expressie naar de Python-tolk verzenden
1. Retourneert een tabel met zowel de expressie als het geëvalueerde resultaat.

## <a name="working-with-visualizations"></a><a id="visualizations"></a>Werken met visualisaties

Plots die zijn gemaakt met MatplotLib kunnen worden geretourneerd door het [Python Script uitvoeren.][execute-python-script] Plots worden echter niet automatisch doorgestuurd naar afbeeldingen zoals ze zijn bij het gebruik van R. Dus de gebruiker moet expliciet opslaan van percelen om PNG-bestanden.

Als u afbeeldingen van MatplotLib wilt genereren, moet u de volgende stappen ondernemen:

1. Schakel de backend over naar "AGG" van de standaard Qt-gebaseerde renderer.
1. Een nieuw figuurobject maken.
1. Haal de as en het genereren van alle percelen in.
1. Sla de figuur op in een PNG-bestand.

Dit proces wordt geïllustreerd in de volgende afbeeldingen die een scatter plot matrix maken met behulp van de scatter_matrix functie in Panda's.

![Code om MatplotLib-figuren op te slaan in afbeeldingen](./media/execute-python-scripts/figure-v1-8.png)

![Klik op visualiseren op een Python Script-module uitvoeren om de cijfers weer te geven](./media/execute-python-scripts/figure-v2-9a.png)

![Plots visualiseren voor een voorbeeldexperiment met Python-code](./media/execute-python-scripts/figure-v2-9b.png)

Het is mogelijk om meerdere cijfers terug te sturen door ze op te slaan in verschillende afbeeldingen. Studio (klassieke) runtime pikt alle beelden op en concatenates ze voor visualisatie.

## <a name="advanced-examples"></a>Geavanceerde voorbeelden

De Anaconda-omgeving die in Studio (klassiek) is geïnstalleerd, bevat veelvoorkomende pakketten zoals NumPy, SciPy en Scikits-Learn. Deze pakketten kunnen effectief worden gebruikt voor gegevensverwerking in een machine learning-pijplijn.

Het volgende experiment en script illustreren bijvoorbeeld het gebruik van ensembleleerlingen in Scikits-Learn om functiebelangscores voor een gegevensset te berekenen. De scores kunnen worden gebruikt om onder toezicht functieselectie uit te voeren voordat ze in een ander model worden ingevoerd.

Hier is de Python-functie die wordt gebruikt om de belangscores te berekenen en de functies te ordenen op basis van de scores:

![Functie om functies te rangschikken op scores](./media/execute-python-scripts/figure8.png)

Het volgende experiment berekent en retourneert vervolgens de belangscores van functies in de gegevensset "Pima Indian Diabetes" in Azure Machine Learning Studio (klassiek):

![Experimenteer om functies te rangschikken in de Pima Indian Diabetes-gegevensset met Behulp van Python](./media/execute-python-scripts/figure9a.png)

![Visualisatie van de uitvoer van de Python Script-module uitvoeren](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Beperkingen

De [Module Python Script uitvoeren][execute-python-script] heeft momenteel de volgende beperkingen:

### <a name="sandboxed-execution"></a>Sandbox-uitvoering

De Python-runtime is momenteel sandboxed en biedt geen toegang tot het netwerk of het lokale bestandssysteem op een permanente manier. Alle bestanden die lokaal zijn opgeslagen, worden geïsoleerd en verwijderd zodra de module is voltooid. De Python-code heeft geen toegang tot de meeste mappen op de machine waarop deze wordt uitgevoerd, met uitzondering de huidige map en de submappen.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>Gebrek aan geavanceerde ondersteuning voor ontwikkeling en debuggen

De Python-module ondersteunt momenteel geen IDE-functies zoals intellisense en foutopsporing. Als de module bij runtime uitvalt, is ook de volledige Python-stacktracering beschikbaar. Maar het moet worden bekeken in de output log voor de module. We raden u momenteel aan Python-scripts te ontwikkelen en te debuggen in een omgeving zoals IPython en de code vervolgens in de module te importeren.

### <a name="single-data-frame-output"></a>Uitvoer van één gegevensframe

Het invoerpunt Python is slechts toegestaan om één gegevensframe als uitvoer terug te sturen. Het is momenteel niet mogelijk om willekeurige Python-objecten zoals getrainde modellen direct terug te sturen naar de Studio (klassieke) runtime. Net als [Execute R Script][execute-r-script], die dezelfde beperking heeft, is het mogelijk in veel gevallen om objecten pickle in een byte array en vervolgens terug te keren dat binnenkant van een dataframe.

### <a name="inability-to-customize-python-installation"></a>Onvermogen om Python-installatie aan te passen

Momenteel is de enige manier om aangepaste Python-modules toe te voegen via het zip-bestandsmechanisme dat eerder is beschreven. Hoewel dit haalbaar is voor kleine modules, is het omslachtig voor grote modules (vooral modules met native DLL's) of een groot aantal modules.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg het [Python Developer Center](https://azure.microsoft.com/develop/python/) voor meer informatie.

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script
