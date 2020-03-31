---
title: Een webservice opnieuw trainen
titleSuffix: ML Studio (classic) - Azure
description: Meer informatie over het bijwerken van een webservice om een nieuw opgeleid machine learning-model te gebruiken in Azure Machine Learning Studio (klassiek).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/14/2019
ms.openlocfilehash: 218c1c98a2ed775ae86c1657156991879708cc7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217941"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>Een machine learning-model omscholen en implementeren

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Omscholing is een manier om ervoor te zorgen dat machine learning-modellen accuraat blijven en gebaseerd zijn op de meest relevante gegevens die beschikbaar zijn. In dit artikel ziet u hoe u een machine learning-model omtraint en implementeert als een nieuwe webservice in Studio (klassiek). Als u een klassieke webservice wilt omscholen, [bekijkt u dit artikel.](retrain-classic-web-service.md)

In dit artikel wordt ervan uitgegaan dat u al een voorspellende webservice hebt geïmplementeerd. Als u nog geen voorspellende webservice hebt, leest u hier hoe u [een Studio-webservice (klassieke) webservice implementeert.](deploy-a-machine-learning-web-service.md)

U volgt de volgende stappen om een nieuwe webservice voor machine learning opnieuw te trainen en te implementeren:

1. Een **omscholingswebservice implementeren**
1. Train een nieuw model met behulp van uw **omscholingswebservice**
1. Uw bestaande **voorspellende experiment bijwerken** om het nieuwe model te gebruiken

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="deploy-the-retraining-web-service"></a>De omscholingswebservice implementeren

Met een omscholingswebservice u uw model omscholen met een nieuwe set parameters, zoals nieuwe gegevens, en het opslaan voor later. Wanneer u een **Web Service Output** aansluit op een **treinmodel,** wordt in het trainingsexperiment een nieuw model uitgevoerd dat u gebruiken.

Gebruik de volgende stappen om een omscholingswebservice te implementeren:

1. Sluit een **Web Service Input-module** aan op uw gegevensinvoer. Doorgaans wilt u ervoor zorgen dat uw invoergegevens op dezelfde manier worden verwerkt als uw oorspronkelijke trainingsgegevens.
1. Sluit een **webserviceuitvoermodule** aan op de uitvoer van uw **treinmodel.**
1. Als u een module **Model evalueren** hebt, u een **webserviceuitvoermodule** aansluiten om de evaluatieresultaten uit te voeren
1. Voer je experiment uit.

    Na het uitvoeren van het experiment moet de resulterende werkstroom vergelijkbaar zijn met de volgende afbeelding:

    ![Resulterende werkstroom](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    U implementeert het trainingsexperiment nu als een omscholingswebservice die een getraind model en modelevaluatieresultaten uitvoert.

1. Klik onder aan het experimentcanvas op **Webservice instellen**
1. Selecteer **Webservice implementeren [Nieuw]**. De Azure Machine Learning Web Services-portal wordt geopend op de pagina **Webservice implementeren.**
1. Typ een naam voor uw webservice en kies een betalingsplan.
1. Selecteer **Implementeren**.

## <a name="retrain-the-model"></a>Het model opnieuw trainen

In dit voorbeeld gebruiken we C# om de omscholingstoepassing te maken. U python- of R-voorbeeldcode ook gebruiken om deze taak uit te voeren.

Gebruik de volgende stappen om de omscholings-API's aan te roepen:

1. Een C#-consoletoepassing maken in Visual Studio: **Nieuwe** > **Project** > **Visual C#** > **Windows Classic Desktop** > **Console App (.NET Framework).**
1. Meld u aan bij de Machine Learning Web Services-portal.
1. Klik op de webservice waarmee u werkt.
1. Klik **op Consumeren**.
1. Klik onder aan de pagina **Consumeren** in de sectie **Voorbeeldcode** op **Batch**.
1. Kopieer de voorbeeldc#-code voor batchuitvoering en plak deze in het Program.cs-bestand. Zorg ervoor dat de naamruimte intact blijft.

Voeg het NuGet-pakket Microsoft.AspNet.WebApi.Client toe, zoals aangegeven in de opmerkingen. Als u de verwijzing naar Microsoft.WindowsAzure.Storage.dll wilt toevoegen, moet u mogelijk de [clientbibliotheek voor Azure Storage-services](https://www.nuget.org/packages/WindowsAzure.Storage)installeren.

In de volgende schermafbeelding wordt de pagina **Verbruik** weergegeven in de Azure Machine Learning Web Services-portal.

![Pagina Consumeren](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>De apikey-verklaring bijwerken

Zoek de **apikey-verklaring:**

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Zoek in de sectie **Basisverbruiksgegevens** van de pagina **Consumeren** de primaire sleutel en kopieer deze naar de **apikey-verklaring.**

### <a name="update-the-azure-storage-information"></a>De Azure-opslaggegevens bijwerken

De BES-voorbeeldcode uploadt een bestand van een lokaal station (bijvoorbeeld "C:\temp\CensusInput.csv") naar Azure Storage, verwerkt het bestand en schrijft de resultaten terug naar Azure Storage.

1. Aanmelden bij Azure Portal
1. Klik in de linkernavigatiekolom op **Meer services,** zoek naar **opslagaccounts**en selecteer deze.
1. Selecteer er een in de lijst met opslagaccounts om het omscholingsmodel op te slaan.
1. Klik in de linkernavigatiekolom op **Toegangstoetsen**.
1. Kopieer en sla de **primaire toegangssleutel op.**
1. Klik in de linkernavigatiekolom op **Blobs**.
1. Selecteer een bestaande container of maak een nieuwe container en sla de naam op.

Zoek de aangiften *StorageAccountName,* *StorageAccountKey*en *StorageContainerName* en werk de waarden bij die u van de portal hebt opgeslagen.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

U moet er ook voor zorgen dat het invoerbestand beschikbaar is op de locatie die u in de code opgeeft.

### <a name="specify-the-output-location"></a>De uitvoerlocatie opgeven

Wanneer u de uitvoerlocatie in de payload aanvragen opgeeft, moet de extensie `ilearner`van het bestand dat is opgegeven in *Relatievelocatie* worden opgegeven als .

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Hier is een voorbeeld van omscholing output:

![Omscholingsuitvoer](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>De omscholingsresultaten evalueren

Wanneer u de toepassing uitvoert, bevat de uitvoer het token URL en gedeelde toegangshandtekeningen die nodig zijn om toegang te krijgen tot de evaluatieresultaten.

U de prestatieresultaten van het omgeschoold model bekijken door de *BaseLocatie*, *RelativeLocatie*en *SasBlobToken* te combineren in de uitvoerresultaten voor *uitvoer2* en de volledige URL in de adresbalk van de browser te plakken.

Bekijk de resultaten om te bepalen of het nieuw getrainde model beter presteert dan het bestaande model.

Sla de *BaseLocation*, *RelativeLocation*en *SasBlobToken* op in de uitvoerresultaten.

## <a name="update-the-predictive-experiment"></a>Het voorspellende experiment bijwerken

### <a name="sign-in-to-azure-resource-manager"></a>Aanmelden bij Azure Resource Manager

Meld u eerst aan bij uw Azure-account vanuit de PowerShell-omgeving met de cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

### <a name="get-the-web-service-definition-object"></a>Het object WebService Definition zoeken

Download vervolgens het object WebService Definitie door de [cmdlet Get-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservice) aan te roepen.

    $wsd = Get-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Als u de naam van de resourcegroep van een bestaande webservice wilt bepalen, voert u de cmdlet Get-AzMlWebService uit zonder parameters om de webservices in uw abonnement weer te geven. Zoek de webservice en bekijk vervolgens de webservice-id. De naam van de resourcegroep is het vierde element in de id, net na het element *resourceGroups.* In het volgende voorbeeld is de naam van de brongroep Default-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

U zich ook aanmelden bij de Azure Machine Learning Web Services-portal om de naam van de brongroep van een bestaande webservice te bepalen. Selecteer de webservice. De naam van de brongroep is het vijfde element van de URL van de webservice, net na het element *resourceGroups.* In het volgende voorbeeld is de naam van de brongroep Default-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237

### <a name="export-the-web-service-definition-object-as-json"></a>Het object Webservicedefinitie exporteren als JSON

Als u de definitie van het getrainde model wilt wijzigen om het nieuw getrainde model te gebruiken, moet u eerst de cmdlet [Export-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) gebruiken om het te exporteren naar een JSON-formaatbestand.

    Export-AzMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

### <a name="update-the-reference-to-the-ilearner-blob"></a>De verwijzing naar de ilearner blob bijwerken

Zoek in de assets het [getrainde model], werk de *uriwaarde* in het *knooppunt locationInfo* bij met de URI van de ilearner blob. De URI wordt gegenereerd door de *BaseLocation* en de *RelativeLocation te* combineren vanuit de uitvoer van de BES omscholingsoproep.

     "asset3": {
        "name": "Retrain Sample [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

### <a name="import-the-json-into-a-web-service-definition-object"></a>De JSON importeren in een object WebService Definitie

Gebruik de cmdlet [Import-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/import-azmlwebservice) om het gewijzigde JSON-bestand om te zetten in een webservicedefinitieobject dat u gebruiken om het predicatieve experiment bij te werken.

    $wsd = Import-AzMlWebService -InputFile "C:\temp\mlservice_export.json"

### <a name="update-the-web-service"></a>De webservice bijwerken

Tot slot u de cmdlet [Update-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/update-azmlwebservice) gebruiken om het voorspellende experiment bij te werken.

    Update-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het beheren van webservices of het bijhouden van meerdere experimenten:

* [Ontdek de Web Services-portal](manage-new-webservice.md)
* [Experimentherhalingen beheren](manage-experiment-iterations.md)
