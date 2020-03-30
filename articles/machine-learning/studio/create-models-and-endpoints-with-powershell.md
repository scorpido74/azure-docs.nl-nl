---
title: Meerdere eindpunten voor een model maken
titleSuffix: ML Studio (classic) - Azure
description: Gebruik PowerShell om meerdere Machine Learning-modellen en webserviceeindpunten te maken met hetzelfde algoritme, maar verschillende trainingsgegevenssets.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: 4f8bb1f20dcc3a7900e3347616018a6e156962d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218176"
---
# <a name="use-powershell-to-create-studio-classic-models-and-web-service-endpoints-from-one-experiment"></a>PowerShell gebruiken om Studio-(klassieke) modellen en webserviceeindpunten te maken vanuit één experiment

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Hier is een veelvoorkomend probleem met machine learning: u wilt veel modellen maken die dezelfde trainingsworkflow hebben en hetzelfde algoritme gebruiken. Maar u wilt dat ze verschillende trainingsgegevenssets hebben als invoer. In dit artikel ziet u hoe u dit op schaal doen in Azure Machine Learning Studio (klassiek) met slechts één experiment.

Stel dat u eigenaar bent van een wereldwijde franchisebedrijf voor fietsverhuur. U wilt een regressiemodel bouwen om de huurvraag te voorspellen op basis van historische gegevens. Je hebt 1.000 verhuurlocaties over de hele wereld en je hebt een gegevensset verzameld voor elke locatie. Ze bevatten belangrijke functies zoals datum, tijd, weer en verkeer die specifiek zijn voor elke locatie.

U uw model één keer trainen met behulp van een samengevoegde versie van alle gegevenssets op alle locaties. Maar elk van uw locaties heeft een unieke omgeving. Dus een betere aanpak zou zijn om uw regressiemodel afzonderlijk te trainen met behulp van de gegevensset voor elke locatie. Op die manier kan elk getraind model rekening houden met de verschillende winkelgroottes, volume, geografie, bevolking, fietsvriendelijke verkeersomgeving en meer.

Dat is misschien de beste aanpak, maar u wilt geen 1.000 trainingsexperimenten maken in Azure Machine Learning Studio (klassiek) waarbij elk een unieke locatie vertegenwoordigt. Naast het feit dat het een overweldigende taak is, lijkt het ook inefficiënt omdat elk experiment allemaal dezelfde componenten zou hebben, behalve de trainingsgegevensset.

Gelukkig u dit bereiken door de [Azure Machine Learning Studio (klassieke) omscholings-API te](/azure/machine-learning/studio/retrain-machine-learning-model) gebruiken en de taak te automatiseren met Azure Machine Learning Studio [(klassieke) PowerShell.](powershell-module.md)

> [!NOTE]
> Als u uw monster sneller wilt laten verlopen, vermindert u het aantal locaties van 1.000 naar 10. Maar dezelfde principes en procedures gelden voor 1.000 locaties. Als u echter wilt trainen vanaf 1.000 gegevenssets, wilt u misschien de volgende PowerShell-scripts parallel uitvoeren. Hoe dat te doen is buiten het bereik van dit artikel, maar u voorbeelden van PowerShell multi-threading vinden op het internet.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Het trainingsexperiment instellen
Gebruik het [voorbeeldtrainingsexperiment](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) in de [Cortana Intelligence Gallery](https://gallery.azure.ai). Open dit experiment in uw [Azure Machine Learning Studio (klassieke)](https://studio.azureml.net) werkruimte.

> [!NOTE]
> Als u dit voorbeeld wilt volgen, u een standaardwerkruimte gebruiken in plaats van een gratis werkruimte. U maakt één eindpunt voor elke klant - voor een totaal van 10 eindpunten - en dat vereist een standaardwerkruimte omdat een gratis werkruimte beperkt is tot 3 eindpunten.
> 
> 

Het experiment gebruikt een **module Gegevens importeren** om de trainingsgegevensset *customer001.csv* te importeren vanuit een Azure-opslagaccount. Laten we aannemen dat u trainingsgegevens van alle fietsverhuurlocaties hebt verzameld en deze hebt opgeslagen in dezelfde blob-opslaglocatie met bestandsnamen variërend van *rentalloc001.csv* tot *rentalloc10.csv.*

![Readermodule importeert gegevens uit een Azure-blob](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Houd er rekening mee dat er een **module Webservice uitvoer** is toegevoegd aan de module **Treinmodel.**
Wanneer dit experiment wordt geïmplementeerd als een webservice, retourneert het eindpunt dat aan die uitvoer is gekoppeld, het getrainde model in de indeling van een .ilearner-bestand.

Houd er ook rekening mee dat u een parameter voor de webservice instelt die de URL definieert die de module **Gegevens importeren** gebruikt. Hiermee u de parameter gebruiken om afzonderlijke trainingsgegevenssets op te geven om het model voor elke locatie te trainen.
Er zijn andere manieren waarop je dit had kunnen doen. U een SQL-query met een webserviceparameter gebruiken om gegevens uit een SQL Azure-database te halen. U ook een **webserviceinvoermodule** gebruiken om in een gegevensset door te geven aan de webservice.

![Een getrainde modelmodule wordt uitgevoerd naar een webserviceuitvoermodule](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Laten we dit trainingsexperiment nu uitvoeren met de *standaardwaarderental001.csv* als trainingsgegevensset. Als u de uitvoer van de module **Evalueren** bekijkt (klik op de uitvoer en selecteer **Visualiseer),** u zien dat u een fatsoenlijke prestatie van *AUC* = 0,91 krijgt. Op dit moment bent u klaar om een webservice te implementeren uit dit trainingsexperiment.

## <a name="deploy-the-training-and-scoring-web-services"></a>De webservices voor training en scoren implementeren
Als u de trainingswebservice wilt implementeren, klikt u op de knop **Webservice instellen** onder het experimentcanvas en selecteert u **Webservice implementeren**. Bel deze webservice "Bike Rental Training".

Nu moet u de scoringwebservice implementeren.
Klik hiervoor op **Webservice instellen** onder het canvas en selecteer **Predictive Web Service**. Dit zorgt voor een score-experiment.
U moet een paar kleine aanpassingen maken om het als webservice te laten werken. Verwijder de labelkolom "cnt" uit de invoergegevens en beperk de uitvoer tot alleen de instantie-id en de bijbehorende voorspelde waarde.

Om jezelf dat werk te besparen, u het [voorspellende experiment](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) openen in de galerij die al is voorbereid.

Als u de webservice wilt implementeren, voert u het voorspellende experiment uit en klikt u vervolgens op de knop **Webservice implementeren** onder het canvas. Noem de scorende webservice "Bike Rental Scoring".

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>10 identieke webserviceeindpunten maken met PowerShell
Deze webservice wordt geleverd met een standaardeindpunt. Maar u bent niet zo geïnteresseerd in het standaardeindpunt, omdat het niet kan worden bijgewerkt. Wat u moet doen is 10 extra eindpunten maken, één voor elke locatie. U dit doen met PowerShell.

Eerst stelt u de PowerShell-omgeving in:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

Voer vervolgens de volgende PowerShell-opdracht uit:

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

Nu hebt u 10 eindpunten gemaakt en ze bevatten allemaal hetzelfde getrainde model dat is getraind op *customer001.csv.* U ze bekijken in de Azure-portal.

![Bekijk de lijst met getrainde modellen in de portal](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>De eindpunten bijwerken om afzonderlijke trainingsgegevenssets te gebruiken met PowerShell
De volgende stap is het bijwerken van de eindpunten met modellen die uniek zijn getraind op de individuele gegevens van elke klant. Maar eerst moet je deze modellen te produceren van de **Bike Rental Training** web service. Laten we teruggaan naar de **Bike Rental Training** webservice. U moet het BES-eindpunt 10 keer bellen met 10 verschillende trainingsgegevenssets om 10 verschillende modellen te produceren. Gebruik hiervoor de **InovkeAmlWebServiceBESEndpoint** PowerShell cmdlet.

U moet ook referenties voor uw blob-opslagaccount verstrekken in `$configContent`. Namelijk, op `AccountName`de `AccountKey`velden `RelativeLocation`, , en . Het `AccountName` kan een van uw accountnamen zijn, zoals te zien is in de **Azure-portal** *(tabblad Opslag).* Zodra u op een opslagaccount klikt, kan deze `AccountKey` worden gevonden door onderaan op de knop **Toegangssleutels beheren** en de primaire *toegangssleutel te*kopiëren. Het `RelativeLocation` is het pad ten opzichte van uw opslag waar een nieuw model wordt opgeslagen. Het pad `hai/retrain/bike_rental/` in het volgende script wijst `hai`bijvoorbeeld `/retrain/bike_rental/` naar een container met de naam en zijn submappen. Momenteel u geen submappen maken via de gebruikersinterface van de portal, maar er zijn [verschillende Azure Storage Explorers](../../storage/common/storage-explorers.md) waarmee u dit doen. Het wordt aanbevolen om een nieuwe container in uw opslag te maken om de nieuwe getrainde modellen (.iLearner-bestanden) als `retrain`volgt op te slaan: klik vanaf uw opslagpagina op de knop **Toevoegen** onderaan en geef deze een naam. Samengevat hebben de noodzakelijke wijzigingen in `AccountName`het `AccountKey`volgende `RelativeLocation` script`"retrain/model' + $seq + '.ilearner"`betrekking op , en (: ).

    # Invoke the retraining API 10 times
    # This is the default (and the only) endpoint on the training web service
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

> [!NOTE]
> Het BES-eindpunt is de enige ondersteunde modus voor deze bewerking. RRS kan niet worden gebruikt voor het produceren van getrainde modellen.
> 
> 

Zoals u hierboven zien, maakt u in plaats van het bouwen van 10 verschillende BES-taakconfiguratie-json-bestanden, in plaats daarvan dynamisch de config-tekenreeks. Voer het vervolgens door naar de *parameter jobConfigString* van de **cmdlet InvokeAmlWebServceBESEndpoint.** Er is echt geen noodzaak om een kopie te houden op de schijf.

Als alles goed gaat, ziet u na een tijdje 10 .iLearner-bestanden, van *model001.ilearner* tot *model010.ilearner,* in uw Azure-opslagaccount. Nu bent u klaar om de 10 scorende webserviceeindpunten met deze modellen bij te werken met behulp van de **Patch-AmlWebServiceEndpoint** PowerShell-cmdlet. Vergeet nogmaals dat u alleen de niet-standaard eindpunten patchen die u programmatisch eerder hebt gemaakt.

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '<my_blob_sas_token>'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }

Dit moet vrij snel lopen. Wanneer de uitvoering is voltooid, hebt u 10 voorspellende eindpunten van webservices gemaakt. Elk model bevat een getraind model dat uniek is getraind op de gegevensset die specifiek is voor een huurlocatie, allemaal vanuit één trainingsexperiment. Om dit te verifiëren, u proberen deze eindpunten aan te roepen met de **invokeAmlWebServiceRRSEndpoint-cmdlet,** zodat ze dezelfde invoergegevens krijgen. U mag verschillende voorspellingsresultaten verwachten, omdat de modellen zijn getraind met verschillende trainingssets.

## <a name="full-powershell-script"></a>Full PowerShell-script
Hier is de lijst van de volledige broncode:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and properly set to point to the valid workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

    # Create 10 endpoints on the scoring web service
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpontName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

    # Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '?test'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }
