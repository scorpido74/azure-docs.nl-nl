---
title: Meerdere eind punten voor een model maken
titleSuffix: ML Studio (classic) - Azure
description: PowerShell gebruiken voor het maken van meerdere Machine Learning-modellen en web-service-eindpunten met dezelfde algoritme, maar met verschillende trainingdatasets.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: 9d94b6627348d08c76fa040255ba0856a2ffa945
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168815"
---
# <a name="use-powershell-to-create-studio-classic-models-and-web-service-endpoints-from-one-experiment"></a>Power shell gebruiken voor het maken van Studio-modellen en web service-eind punten van een experiment

Hier volgt een veelvoorkomend probleem voor machine learning: U wilt Maak veel modellen die dezelfde werkstroom training en gebruikmaken van dezelfde algoritme. Maar u wilt dat ze hebben verschillende trainingdatasets als invoer. Dit artikel laat u zien hoe u dit kunt doen op schaal in Azure Machine Learning Studio (klassiek) door slechts één experiment te gebruiken.

Stel dat u een globale fiets verhuur franchise bedrijf de eigenaar bent. U wilt maken van een regressiemodel om te voorspellen van de verhuur vraag op basis van historische gegevens. Hebt u 1000 verhuur locaties over de hele wereld en u een gegevensset voor elke locatie hebt verzameld. Ze bevatten belangrijke functies zoals datum, tijd, weer en verkeer die specifiek voor elke locatie zijn.

U kunt uw model met één keer een samengevoegde versie van alle gegevenssets op alle locaties kan trainen. Echter elk van uw locaties heeft een unieke omgeving. Zo zou een betere benadering met het trainen van uw regressiemodel afzonderlijk met behulp van de gegevensset voor elke locatie. Op die manier kunnen elke getrainde model kan rekening mee dat de grootte van de andere archieven, volume, Geografie, populatie, verkeer fiets-vriendelijke omgeving en meer.

Dat kan de beste aanpak zijn, maar u wilt geen 1.000-trainings experimenten in Azure Machine Learning Studio (klassiek) maken, met elk een unieke locatie. Naast een enorme taak nog worden, ook lijkt inefficiënt omdat elke experiment allemaal dezelfde onderdelen, met uitzondering van de gegevensset training hebben zou.

Gelukkig kunt u dit doen met behulp van de API voor opnieuw [trainen van Azure machine learning Studio (klassiek)](/azure/machine-learning/studio/retrain-machine-learning-model) en de taak automatiseren met [Azure machine learning Studio (klassiek) Power shell](powershell-module.md).

> [!NOTE]
> Als u uw voorbeeld sneller worden uitgevoerd, verminderen het aantal locaties van 1000 tot en met 10. Maar dezelfde principes en procedures die zijn toegepast op 1000 locaties. Echter, als u wilt trainen van 1000 gegevenssets raadzaam om de volgende PowerShell-scripts parallel uit te voeren. Hoe dat valt buiten het bereik van dit artikel, maar u voorbeelden van PowerShell multithreading kunt vinden op het Internet.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Het trainingsexperiment instellen
Gebruik het voorbeeld [experiment](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) in de [Cortana Intelligence Gallery](https://gallery.azure.ai). Open dit experiment in de werk ruimte [Azure machine learning Studio (klassiek)](https://studio.azureml.net) .

> [!NOTE]
> Als u wilt volgen, samen met het volgende voorbeeld, kunt u een standard-werkruimte in plaats van een gratis werkruimte gebruiken. Maakt u een eindpunt voor elke klant - voor een totaal van 10 eindpunten - en waarvoor een standard-werkruimte is vereist omdat een gratis werkruimte beperkt tot 3-eindpunten is.
> 
> 

Het experiment maakt gebruik van een **import gegevens** module voor het importeren van de trainings gegevensset *customer001. CSV* van een Azure-opslag account. We gaan ervan uit dat u trainings gegevens sets hebt verzameld van alle uithuur locaties van fietsen en ze hebt opgeslagen op dezelfde Blob-opslag locatie met bestands namen, variërend van *rentalloc001. CSV* naar *rentalloc10. CSV*.

![In de lezers module worden gegevens uit een Azure-Blob geïmporteerd](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Houd er rekening mee dat een webservice-module voor het **uitvoeren van webservices** is toegevoegd aan de module **Train model** .
Wanneer dit experiment is geïmplementeerd als een webservice, het eindpunt die zijn gekoppeld aan dat de uitvoer het getrainde model in de indeling van een bestand .ilearner retourneert.

U kunt ook een webservice-para meter instellen waarmee de URL wordt gedefinieerd die wordt gebruikt in de module **import data** . Hiermee kunt u de parameter gebruiken om op te geven van individuele training gegevenssets trainen van het model voor elke locatie.
Er zijn andere manieren waarop u kunt dit hebt gedaan. U kunt een SQL-query met de parameter van een web-service gegevens ophalen uit een SQL Azure-database. U kunt ook een module voor het **invoeren van webservices** gebruiken om in een gegevensset door te geven aan de webservice.

![Een getrainde model module wordt uitgevoerd in een webservice-module voor het uitvoeren van webservices](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Nu gaan we dit trainings experiment uitvoeren met de standaard waarde *rental001. CSV* als trainings gegevensset. Als u de uitvoer van de module **Evaluate** bekijkt (Klik op de uitvoer en selecteert u **visualiseren**), ziet u een goede prestaties van *AUC* = 0,91. Op dit moment u kunt een webservice buiten deze trainingsexperiment implementeren.

## <a name="deploy-the-training-and-scoring-web-services"></a>De training en scoren van webservices implementeren
Als u de training-webservice wilt implementeren, klikt u op de knop **webservice instellen** onder het canvas op het experiment en selecteert u **Web service implementeren**. Deze webservice 'Fiets verhuur Training' aanroepen.

Nu moet u de scoring-webservice implementeren.
Hiertoe klikt u op **webservice instellen** onder het canvas en selecteert u **Predictive web service**. Hiermee maakt u een scoring-experiment.
U moet enkele kleine aanpassingen te kunnen gebruiken als een webservice. De kolom van het label "cnt" verwijderen uit de invoergegevens en de uitvoer naar alleen de exemplaar-id en de bijbehorende voorspelde waarde beperken.

Als u uw werk wilt opslaan, kunt u het [voorspellende experiment](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) openen in de galerie die al is voor bereid.

Als u de webservice wilt implementeren, voert u het voorspellende experiment uit en klikt u op de knop **webservice implementeren** onder het canvas. De naam van de scoring-webservice 'Fiets verhuur score'.

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>10 identieke webservice-eindpunten maken met PowerShell
Deze webservice wordt geleverd met een eindpunt. Maar u niet als geïnteresseerd bent in het standaardeindpunt omdat deze kan niet worden bijgewerkt. Wat u moet doen is het maken van 10 extra eindpunten, één voor elke locatie. U kunt dit doen met PowerShell.

Eerst, instellen van de PowerShell-omgeving:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

Voer de volgende PowerShell-opdracht:

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

U hebt nu 10 eind punten gemaakt en deze bevatten allemaal hetzelfde getrainde model dat is getraind op *customer001. CSV*. U kunt ze weergeven in Azure portal.

![De lijst met getrainde modellen weer geven in de portal](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Bijwerken van de eindpunten voor het gebruik van afzonderlijke trainings-gegevenssets met behulp van PowerShell
De volgende stap is het de eindpunten bijwerken met een unieke getraind op de afzonderlijke gegevens van elke klant-modellen. Maar eerst moet u deze modellen maken op basis van de trainings-webservice voor de **fiets** . We gaan terug naar de web-service voor de **huur training** van de fiets. U moet het eindpunt BES 10 keer met 10 verschillende trainingdatasets aanroepen om te kunnen produceren van 10 verschillende modellen. Gebruik hiervoor de Power shell-cmdlet **InovkeAmlWebServiceBESEndpoint** .

U moet ook referenties voor uw Blob Storage-account opgeven in `$configContent`. Dat wil zeggen, in de velden `AccountName`, `AccountKey`en `RelativeLocation`. De `AccountName` kan een van uw account namen zijn, zoals wordt weer gegeven in het **Azure Portal** (tabblad*opslag* ). Nadat u op een opslag account hebt geklikt, kunt u het `AccountKey` vinden door op de knop **toegangs toetsen beheren** te drukken en de *primaire toegangs sleutel*te kopiëren. Het `RelativeLocation` is het pad dat relatief is ten opzichte van uw opslag locatie waar een nieuw model wordt opgeslagen. Bijvoorbeeld: het pad `hai/retrain/bike_rental/` in het volgende script verwijst naar een container met de naam `hai`en `/retrain/bike_rental/` submappen zijn. Op dit moment kunt u geen submappen maken via de portal-gebruikers interface, maar er zijn [verschillende Azure Storage Explorers](../../storage/common/storage-explorers.md) waarmee u dit kunt doen. Het is raadzaam om een nieuwe container in uw opslag ruimte te maken om de nieuwe getrainde modellen (iLearner-bestanden) als volgt op te slaan: Klik op de knop **toevoegen** onder aan de pagina opslag en geef deze de naam `retrain`. In samen vatting zijn de benodigde wijzigingen in het volgende script van toepassing op `AccountName`, `AccountKey`en `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

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
> De BES-eindpunt is de enige ondersteunde modus voor deze bewerking. RRS kan niet worden gebruikt voor het produceren van getrainde modellen.
> 
> 

Zoals u ziet, in plaats van het maken van 10 verschillende BES taak json-configuratiebestanden, maken u dynamisch het configuratietekenreeks in plaats daarvan. Voer de invoer vervolgens door naar de para meter *jobConfigString* van de cmdlet **InvokeAmlWebServceBESEndpoint** . Er is echt geen nodig om een kopie op schijf bewaren.

Als alles goed gaat, moet u na een tijdje 10. iLearner-bestanden van *model001. iLearner* naar *model010. ILearner*in uw Azure Storage-account zien. Nu bent u klaar om de 10 scores-webservice-eind punten bij te werken met deze modellen met behulp van de Power shell **-cmdlet patch-AmlWebServiceEndpoint** . Vergeet niet opnieuw die u kunt alleen patch uitvoeren voor de niet-standaard-eindpunten die u via een programma eerder hebt gemaakt.

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

Dit moet vrij snel uitvoeren. Wanneer de uitvoering is voltooid, zult u hebben gemaakt 10 voorspellende webservice-eindpunten. Elke bevat een unieke getraind op de specifieke gegevensset naar een locatie verhuur, vanuit een enkele trainingsexperiment model is getraind. Als u dit wilt controleren, kunt u deze eind punten aanroepen met behulp van de **InvokeAmlWebServiceRRSEndpoint** -cmdlet, zodat ze dezelfde invoer gegevens leveren. U kunt verwachten van verschillende voorspellingsresultaten worden weergegeven omdat de modellen worden getraind met verschillende training sets.

## <a name="full-powershell-script"></a>Volledige PowerShell-script
Hier volgt de lijst met de volledige broncode:

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
