---
title: Beheer van toepassingslevenscyclus
titleSuffix: ML Studio (classic) - Azure
description: Aanbevolen procedures voor application lifecycle management toepassen in Azure Machine Learning Studio (klassiek)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 10/27/2016
ms.openlocfilehash: 3f22ce3b1fb750e33e35d35ee1fe5ad1893abcfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204133"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio-classic"></a>Application Lifecycle Management in Azure Machine Learning Studio (klassiek)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Azure Machine Learning Studio (klassiek) is een hulpmiddel voor het ontwikkelen van machine learning-experimenten die worden geoperationaliseerd in het Azure-cloudplatform. Het is alsof de Visual Studio IDE en schaalbare cloudservice zijn samengevoegd tot één platform. U standaard ALM-procedures (Application Lifecycle Management) opnemen, van het versien van verschillende assets tot geautomatiseerde uitvoering en implementatie, in Azure Machine Learning Studio (klassiek). Dit artikel bespreekt een aantal van de opties en benaderingen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="versioning-experiment"></a>Versie-experiment
Er zijn twee aanbevolen manieren om uw experimenten te versionen. U vertrouwen op de ingebouwde rungeschiedenis of het experiment exporteren in een JSON-indeling om het extern te beheren. Elke aanpak komt met zijn voors en tegens.

### <a name="experiment-snapshots-using-run-history"></a>Momentopnamen experimenteren met Run History
In het uitvoeringsmodel van het Azure Machine Learning Studio (klassiek) leerexperiment wordt een onveranderlijke momentopname van het experiment naar de taakplanner verzonden wanneer u op **Uitvoeren** klikt in de experimenteditor. Als u deze lijst met momentopnamen wilt weergeven, klikt u op **Geschiedenis uitvoeren** op de opdrachtbalk in de weergave van de experimenteditor.

![Knop Geschiedenis uitvoeren](./media/version-control/runhistory.png)

U de momentopname vervolgens openen in de modus Vergrendeld door op de naam van het experiment te klikken op het moment dat het experiment is uitgevoerd en de momentopname is gemaakt. Merk op dat alleen het eerste item in de lijst, dat het huidige experiment vertegenwoordigt, zich in een bewerkbare status bevindt. Merk ook op dat elke momentopname ook in verschillende statusstatussen kan staan, waaronder Voltooid (Gedeeltelijke uitvoering), Mislukt, Mislukt (Gedeeltelijke uitvoering) of Concept.

![Lijst Geschiedenis uitvoeren](./media/version-control/runhistorylist.png)

Nadat het is geopend, u het momentopnameexperiment opslaan als een nieuw experiment en het vervolgens wijzigen. Als de momentopname van uw experiment elementen bevat zoals getrainde modellen, transformaties of gegevenssets met bijgewerkte versies, behoudt de momentopname de verwijzingen naar de oorspronkelijke versie toen de momentopname is gemaakt. Als u de vergrendelde momentopname opslaat als een nieuw experiment, detecteert Azure Machine Learning Studio (klassiek) het bestaan van een nieuwere versie van deze elementen en werkt deze automatisch bij in het nieuwe experiment.

Als u het experiment verwijdert, worden alle momentopnamen van dat experiment verwijderd.

### <a name="exportimport-experiment-in-json-format"></a>Experiment exporteren/importeren in JSON-indeling
De momentopnamen voor rungeschiedenis behouden een onveranderlijke versie van het experiment in Azure Machine Learning Studio (klassiek) elke keer dat het wordt verzonden om uit te voeren. U ook een lokale kopie van het experiment opslaan en inchecken in uw favoriete broncontrolesysteem, zoals Team Foundation Server, en later een experiment opnieuw maken vanuit dat lokale bestand. U de [Azure Machine Learning PowerShell-opdrachten](https://aka.ms/amlps) [*Export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph) en [*Import-AmlExperimentGraph gebruiken*](https://github.com/hning86/azuremlps#import-amlexperimentgraph) om dat te bereiken.

Het JSON-bestand is een tekstuele weergave van de experimentgrafiek, die een verwijzing naar elementen in de werkruimte kan bevatten, zoals een gegevensset of een getraind model. Het bevat geen geserialiseerde versie van het item. Als u het JSON-document opnieuw probeert te importeren in de werkruimte, moeten de naar de verwijzing genoemde elementen al bestaan met dezelfde asset-id's waarnaar in het experiment wordt verwezen. Anders hebt u geen toegang tot het geïmporteerde experiment.

## <a name="versioning-trained-model"></a>Versiemodel
Een getraind model in Azure Machine Learning Studio (klassiek) wordt geserialiseerd naar een indeling die bekend staat als een iLearner-bestand (`.iLearner`) en wordt opgeslagen in het Azure Blob-opslagaccount dat is gekoppeld aan de werkruimte. Een manier om een kopie van het iLearner-bestand te krijgen, is via de omscholings-API. [In dit artikel](/azure/machine-learning/studio/retrain-machine-learning-model) wordt uitgelegd hoe de API omscholen werkt. De stappen op hoog niveau:

1. Stel je trainingsexperiment in.
2. Voeg een webserviceuitvoerpoort toe aan de module Treinmodel of de module die het getrainde model produceert, zoals Tune Model Hyperparameter of Create R-model.
3. Voer uw trainingsexperiment uit en implementeer het vervolgens als een webservice voor modeltraining.
4. Bel het BES-eindpunt van de webservice voor training en geef de gewenste iLearner-bestandsnaam en blob-opslagaccountlocatie op waar deze wordt opgeslagen.
5. Oogst het geproduceerde iLearner-bestand nadat de BES-oproep is voltooid.

Een andere manier om het iLearner-bestand op te halen is via de PowerShell-commandlet [*Download-AmlExperimentNodeOutput.*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput) Dit is misschien makkelijker als u gewoon een kopie van het iLearner-bestand wilt krijgen zonder dat u het model programmatisch hoeft om te scholen.

Nadat u het iLearner-bestand met het getrainde model hebt, u vervolgens uw eigen versiestrategie gebruiken. De strategie kan zo eenvoudig zijn als het toepassen van een pre/postfix als een naamgevingsconventie en het verlaten van het iLearner-bestand in Blob-opslag, of het kopiëren/importeren in uw versiebeheersysteem.

Het opgeslagen iLearner-bestand kan vervolgens worden gebruikt om te scoren via geïmplementeerde webservices.

## <a name="versioning-web-service"></a>Webservice voor versiebeheer
U twee typen webservices implementeren vanuit een experiment met Azure Machine Learning Studio (klassiek). De klassieke webservice is nauw gekoppeld aan het experiment en de werkruimte. De nieuwe webservice maakt gebruik van het Azure Resource Manager-framework en is niet langer gekoppeld aan het oorspronkelijke experiment of de werkruimte.

### <a name="classic-web-service"></a>Klassieke webservice
Als u een klassieke webservice wilt gebruiken, u profiteren van de webservice-eindpuntconstructie. Hier is een typische stroom:

1. Vanuit uw voorspellende experiment implementeert u een nieuwe klassieke webservice, die een standaardeindpunt bevat.
2. U maakt een nieuw eindpunt met de naam EP2, waarmee de huidige versie van het experiment/getrainde model wordt blootgesteld.
3. Je gaat terug en werkt je voorspellende experiment en getraind model bij.
4. U implementeert het voorspellende experiment opnieuw, waardoor het standaardeindpunt wordt bijgewerkt. Maar dit zal ep2 niet veranderen.
5. U maakt een extra eindpunt met de naam EP3, waarmee de nieuwe versie van het experiment en het getrainde model worden blootgesteld.
6. Ga terug naar stap 3 indien nodig.

Na verloop van tijd u veel eindpunten hebben gemaakt in dezelfde webservice. Elk eindpunt vertegenwoordigt een point-in-time kopie van het experiment met de point-in-time versie van het getrainde model. U vervolgens externe logica gebruiken om te bepalen welk eindpunt u wilt aanroepen, wat in feite betekent dat u een versie van het getrainde model voor de scoringsrun moet selecteren.

U ook veel identieke eindpunten voor webservice maken en vervolgens verschillende versies van het iLearner-bestand patchen naar het eindpunt om een vergelijkbaar effect te bereiken. [In dit artikel](create-models-and-endpoints-with-powershell.md) wordt nader uitgelegd hoe u dat bereiken.

### <a name="new-web-service"></a>Nieuwe webservice
Als u een nieuwe webservice op basis van Azure Resource Manager maakt, is de geconstrueerd voor eindpunten niet meer beschikbaar. In plaats daarvan u WSD-bestanden (Webservice definition) genereren, in JSON-indeling, vanuit uw voorspellende experiment met behulp van de [opdrachtuitvoer van Export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) PowerShell of met behulp van de [*Opdrachtuitvoer-AzMlWebservice*](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) PowerShell exporteren vanuit een geïmplementeerde op Resource Manager gebaseerde webservice.

Nadat u het geëxporteerde WSD-bestand en versiebeheer hebt, u de WSD ook implementeren als een nieuwe webservice in een ander webserviceplan in een andere Azure-regio. Zorg ervoor dat u de juiste configuratie van het opslagaccount en de nieuwe webservice-abonnement ID. Als u in verschillende iLearner-bestanden wilt patchen, u het WSD-bestand wijzigen en de locatieverwijzing van het getrainde model bijwerken en implementeren als een nieuwe webservice.

## <a name="automate-experiment-execution-and-deployment"></a>Implementatie en implementatie van experimenten automatiseren
Een belangrijk aspect van ALM is om het uitvoerings- en implementatieproces van de toepassing te kunnen automatiseren. In Azure Machine Learning Studio (klassiek) u dit bereiken met behulp van de [PowerShell-module](https://aka.ms/amlps). Hier volgt een voorbeeld van end-to-end stappen die relevant zijn voor een standaard ALM geautomatiseerd uitvoerings-/implementatieproces met behulp van de [Azure Machine Learning Studio (klassieke) PowerShell-module.](https://aka.ms/amlps) Elke stap is gekoppeld aan een of meer PowerShell-opdrachten die u gebruiken om die stap te volbrengen.

1. [Een gegevensset uploaden](https://github.com/hning86/azuremlps#upload-amldataset).
2. Kopieer een trainingsexperiment naar de werkruimte vanuit een [werkruimte](https://github.com/hning86/azuremlps#copy-amlexperiment) of vanuit [Galerie](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery)of [importeer](https://github.com/hning86/azuremlps#import-amlexperimentgraph) een [geëxporteerd](https://github.com/hning86/azuremlps#export-amlexperimentgraph) experiment vanaf de lokale schijf.
3. [Werk de gegevensset](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) in het trainingsexperiment bij.
4. [Voer het trainingsexperiment uit.](https://github.com/hning86/azuremlps#start-amlexperiment)
5. [Promoot het getrainde model.](https://github.com/hning86/azuremlps#promote-amltrainedmodel)
6. [Kopieer een voorspellend experiment](https://github.com/hning86/azuremlps#copy-amlexperiment) naar de werkruimte.
7. [Werk het getrainde model](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) bij in het voorspellende experiment.
8. [Voer het voorspellende experiment uit.](https://github.com/hning86/azuremlps#start-amlexperiment)
9. [Implementeer een webservice](https://github.com/hning86/azuremlps#new-amlwebservice) vanuit het voorspellende experiment.
10. Test de webservice [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) of [BES-eindpunt.](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint)

## <a name="next-steps"></a>Volgende stappen
* Download de [Azure Machine Learning Studio (klassieke) PowerShell-module](https://aka.ms/amlps) en begin met het automatiseren van uw ALM-taken.
* Leer hoe u [een groot aantal ML-modellen maakt en beheert met slechts één experiment](create-models-and-endpoints-with-powershell.md) via PowerShell en de API omscholen.
* Meer informatie over [het implementeren van Azure Machine Learning-webservices](deploy-a-machine-learning-web-service.md).
