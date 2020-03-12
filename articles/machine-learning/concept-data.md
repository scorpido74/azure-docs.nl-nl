---
title: Gegevens in Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe Azure Machine Learning interactie met uw gegevens en hoe deze worden gebruikt in uw machine learning experimenten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 12/09/2019
ms.openlocfilehash: a2af1e87ce7b17183ae09fb02b2652a04f585e84
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129556"
---
# <a name="data-access-in-azure-machine-learning"></a>Toegang tot gegevens in Azure Machine Learning

In dit artikel vindt u informatie over de oplossingen voor gegevens beheer en integratie van Azure Machine Learning voor uw machine learning-taken. In dit artikel wordt ervan uitgegaan dat u al een [Azure-opslag account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) en [Azure Storage-service](https://docs.microsoft.com/azure/storage/common/storage-introduction)hebt gemaakt.

Wanneer u klaar bent voor het gebruik van de gegevens in uw opslag, raden we u aan

1. Maak een Azure Machine Learning gegevens opslag.
2. Maak een Azure Machine Learning-gegevensset vanuit het gegevens archief. 
3. Gebruik deze gegevensset in uw machine learning experiment door ofwel 
    1. Deze koppelen aan het reken doel van het experiment voor model training

        **OF** 

    1. Gebruik het rechtstreeks in Azure Machine Learning oplossingen zoals geautomatiseerde machine learning (Automated ML) experimenten, machine learning pijp lijnen en de [Azure machine learning Designer](concept-designer.md).
4. Maak gegevensset-monitors voor uw model-uitvoer gegevensset om te detecteren voor gegevens drift. 
5. Als er gegevens drift wordt gedetecteerd, werkt u uw invoer gegevensset bij en traint u uw model dienovereenkomstig opnieuw.

Het volgende diagram biedt een visuele demonstratie van deze aanbevolen werk stroom voor gegevens toegang.

![Gegevens-concept diagram](./media/concept-data/data-concept-diagram.svg)

## <a name="access-data-in-storage"></a>Toegang tot gegevens in de opslag

Om toegang te krijgen tot uw gegevens in uw opslag account, biedt Azure Machine Learning gegevens opslag en gegevens sets. Data stores antwoord op de vraag: hoe kan ik veilig verbinding maken met mijn gegevens in mijn Azure Storage? Data stores bieden een laag van abstractie voor uw opslag service. Deze hulp middelen zijn in beveiliging en toegankelijker voor uw opslag, omdat de verbindings gegevens in de gegevens opslag worden bewaard en niet in scripts worden weer gegeven. 

Gegevens sets beantwoorden de vraag: Hoe krijg ik specifieke gegevens bestanden in mijn gegevens opslag? Gegevens sets verwijzen naar de specifieke bestanden in uw onderliggende opslag die u wilt gebruiken voor uw machine learning-experiment. Data stores en gegevens sets bieden samen een veilige, schaal bare werk stroom voor het leveren van gegevens voor uw machine learning taken.

### <a name="datastores"></a>Gegevens opslag

Een Azure Machine Learning Data Store is een opslag abstractie voor uw Azure Storage-services. [Registreer en maak een gegevens opslag](how-to-access-data.md) om eenvoudig verbinding te maken met uw Azure Storage-account en om toegang te krijgen tot de gegevens in uw onderliggende Azure Storage-services.

Ondersteunde Azure Storage-services die kunnen worden geregistreerd als data stores:
+ Azure Blob-container
+ Azure-bestandsshare
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database for PostgreSQL
+ Databricks-bestandssysteem
+ Azure Database for MySQL

### <a name="datasets"></a>Gegevenssets

[Maak een Azure machine learning-gegevensset](how-to-create-register-datasets.md) om te communiceren met gegevens in uw data stores en Verdeel uw gegevens in een verbruikt object voor machine learning taken. Registreer de gegevensset voor uw werk ruimte om deze te delen en opnieuw te gebruiken in verschillende experimenten zonder complexiteit van gegevens opname.

Gegevens sets kunnen worden gemaakt op basis van lokale bestanden, open bare url's, [Azure open gegevens sets](#open)of specifieke bestanden in uw gegevens opslag. Als u een gegevensset wilt maken op basis van een in geheugen-Panda data frame, schrijft u de gegevens naar een lokaal bestand, zoals een CSV, en maakt u uw gegevensset vanuit dat bestand. Gegevens sets worden niet gekopieerd naar de gegevens in uw opslag service, maar zijn verwijzingen die verwijzen naar de data in uw Storage services, zodat er geen extra opslag kosten in rekening worden gebracht. 

In het volgende diagram ziet u dat als u geen Azure Storage-service hebt, u rechtstreeks een gegevensset kunt maken vanuit lokale bestanden, open bare url's of een open gegevensset van Azure. Als u dit doet, wordt uw gegevensset verbonden met de standaard gegevens opslag die automatisch is gemaakt met de [Azure machine learning-werk ruimte](concept-workspace.md)van uw experiment.

![Gegevens-concept diagram](./media/concept-data/dataset-workflow.svg)

Aanvullende gegevens sets kunnen worden gevonden in de volgende documentatie:

+ [Versie en track](how-to-version-track-datasets.md) gegevensset afkomst.
+ [Bewaak uw gegevensset](how-to-monitor-datasets.md) om te helpen bij het detecteren van gegevens drift.
+  Raadpleeg de volgende documentatie voor meer informatie over de twee typen gegevens sets:
    + [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) vertegenwoordigt gegevens in tabel vorm door het bestand of de lijst met bestanden te parseren. Hiermee kunt u de gegevens in een Panda-of Spark-data frame realiseren voor verdere manipulatie en opschoning. Voor een volledige lijst met bestanden waaruit u TabularDatasets kunt maken, raadpleegt u de [klasse TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

    + [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) verwijst naar één of meer bestanden in uw gegevens opslag of open bare url's. Met deze methode kunt u bestanden downloaden of koppelen van uw keuze aan uw reken doel als een FileDataset-object.

## <a name="work-with-your-data"></a>Werk met uw gegevens

Met gegevens sets kunt u een aantal machine learning taken uitvoeren via een naadloze integratie met Azure Machine Learning-functies. 

+ [Train machine learning-modellen](how-to-train-with-datasets.md).
+ Gegevens sets gebruiken in 
     + [automatische ML experimenten](how-to-use-automated-ml-for-ml-models.md)
     + de [ontwerper](tutorial-designer-automobile-price-train-score.md#import-data) 
+ Toegang tot gegevens sets voor het afgeven van een batch-deinterferentie in [machine learning pijp lijnen](how-to-create-your-first-pipeline.md).
+ Een project voor het [labelen van gegevens](#label)maken.
+ Stel een monitor voor gegevensset in voor de detectie van [gegevens drift](#drift) .

<a name="open"></a>

## <a name="azure-open-datasets"></a>Azure Open-gegevenssets

[Azure open gegevens sets](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets) zijn alle open bare gegevens sets die u kunt gebruiken om scenario's met specifieke functies toe te voegen aan Machine Learning oplossingen voor nauw keurigere modellen. Open gegevens sets bevinden zich in de Cloud op Microsoft Azure en zijn geïntegreerd in Azure Machine Learning. U kunt de gegevens sets ook openen via Api's en deze gebruiken in andere producten, zoals Power BI en Azure Data Factory.

Azure open data sets bevatten gegevens van een openbaar domein voor weer, telling, feest dagen, open bare veiligheid en locatie waarmee u machine learning modellen en verrijkende voorspellende oplossingen kunt trainen. U kunt ook uw open bare gegevens sets delen in azure open-gegevens sets.

<a name="label"></a>

## <a name="data-labeling"></a>Gegevens labelen

Het labelen van grote hoeveel heden gegevens is vaak een hoofd in machine learning projecten. Voor degenen met een computer vision-onderdeel, zoals een afbeeldings classificatie of object detectie, zijn in het algemeen duizenden afbeeldingen en de bijbehorende labels vereist.

Azure Machine Learning biedt u een centrale locatie voor het maken, beheren en bewaken van label projecten. Het labelen van projecten helpt bij het coördineren van de gegevens, labels en team leden, zodat u de taken voor het label efficiënter kunt beheren. Momenteel ondersteunde taken zijn afbeeldings classificatie, ofwel meerdere labels, meerdere klassen en object-id's die gebruikmaken van begrensde vakken.

+ Maak een [gegevens label project](how-to-create-labeling-projects.md)en uitvoer een gegevensset voor gebruik in machine learning experimenten.

<a name="drift"></a>

## <a name="data-drift"></a>Gegevens drift

In de context van machine learning is gegevens drift de wijziging in model invoer gegevens die leidt tot het model leren van prestaties. Het is een van de belangrijkste redenen om de nauw keurigheid van het model te verslechteren, waardoor gegevens drift helpt bij het detecteren van prestatie problemen van modellen.
Zie het artikel [een gegevensset maken](how-to-monitor-datasets.md) voor meer informatie over het detecteren en waarschuwen van gegevens drift op nieuwe gegevens in een gegevensset.

## <a name="next-steps"></a>Volgende stappen 

+ Een gegevensset maken in Azure Machine Learning Studio of met de python-SDK, [gebruikt u deze stappen.](how-to-create-register-datasets.md)
+ Probeer gegevensset-trainings voorbeelden uit met onze [voorbeeld notitieblokken](https://aka.ms/dataset-tutorial).
+ Zie de [zelf studie over data](https://aka.ms/datadrift-notebook)-drift voor voor beelden van gegevens opslag.
