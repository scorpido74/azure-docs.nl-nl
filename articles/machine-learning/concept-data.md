---
title: Gegevens in Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Ontdek hoe Azure Machine Learning veilig verbinding maakt met uw gegevens en die gegevens gebruikt voor machine learning-taken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 03/20/2020
ms.openlocfilehash: 982c9c9eadec4403c8116430e1e25092de99f1d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128496"
---
# <a name="data-access-in-azure-machine-learning"></a>Toegang tot gegevens in Azure Machine Learning

Azure Machine Learning maakt het eenvoudig om verbinding te maken met uw gegevens in de cloud.  Het biedt een abstractielaag over de onderliggende opslagservice, zodat u veilig toegang hebt tot uw gegevens en deze gebruiken zonder dat u code hoeft te schrijven die specifiek is voor uw opslagtype. Azure Machine Learning biedt ook de volgende gegevensmogelijkheden:

*    Versiebeheer en tracking van gegevensafstamming
*    Gegevensetikettering 
*    Bewaking van gegevensafwijking
*    Interoperabiliteit met panda's en Spark DataFrames

## <a name="data-workflow"></a>Gegevenswerkstroom

Wanneer u klaar bent om de gegevens te gebruiken in uw cloudgebaseerde opslagoplossing, raden we u de volgende workflow voor gegevenslevering aan. In deze werkstroom wordt ervan uitgegaan dat u een [Azure-opslagaccount](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) en -gegevens hebt in een cloudgebaseerde opslagservice in Azure. 

1. Maak een [Azure Machine Learning-gegevensarchief](#datastores) om verbindingsgegevens op te slaan in uw Azure-opslag.

2. Maak vanuit dat gegevensarchief een [Azure Machine Learning-gegevensset](#datasets) om een specifiek bestand(en) in uw onderliggende opslag aan te wijzen. 

3. Als u die gegevensset wilt gebruiken in uw machine learning-experiment, u
    1. Monteer het aan het rekendoel van uw experiment voor modeltraining.

        **Of** 

    1. Gebruik het rechtstreeks in Azure Machine Learning-oplossingen zoals geautomatiseerde ML-experimentuitvoeringen (automated machine learning), machine learning-pijplijnen of de [Azure Machine Learning-ontwerper.](concept-designer.md)

4. Maak [gegevenssetmonitors](#data-drift) voor de gegevensset van uw modeluitvoer om te detecteren op gegevensdrift. 

5. Als gegevensdrift wordt gedetecteerd, werkt u de invoergegevensset bij en stelt u uw model dienovereenkomstig opnieuw in.

Het volgende diagram geeft een visuele weergave van deze aanbevolen werkstroom.

![Data-concept-diagram](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>Gegevenswinkels

Azure Machine Learning-gegevenswinkels bewaren de verbindingsgegevens veilig met uw Azure-opslag, zodat u deze niet hoeft te coderen in uw scripts. [Registreer en maak een datastore](how-to-access-data.md) om eenvoudig verbinding te maken met uw opslagaccount en toegang te krijgen tot de gegevens in uw onderliggende Azure-opslagservice. 

Ondersteunde cloudgebaseerde opslagservices in Azure die kunnen worden geregistreerd als datastores:

+ Azure Blob-container
+ Azure-bestandsshare
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database for PostgreSQL
+ Databricks-bestandssysteem
+ Azure Database for MySQL

## <a name="datasets"></a>Gegevenssets

Azure Machine Learning-gegevenssets zijn verwijzingen die verwijzen naar de gegevens in uw opslagservice. Het zijn geen kopieën van uw gegevens, dus er worden geen extra opslagkosten gemaakt. Als u wilt communiceren met uw gegevens in de opslag, [maakt u een gegevensset](how-to-create-register-datasets.md) om uw gegevens te verpakken in een verbruiksobject voor machine learning-taken. Registreer de gegevensset in uw werkruimte om deze te delen en te hergebruiken in verschillende experimenten zonder complexiteit van gegevensopname.

Gegevenssets kunnen worden gemaakt op lokale bestanden, openbare url's, [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/)of Azure-opslagservices via datastores. Als u een gegevensset wilt maken uit een in-memory pandas-gegevensframe, schrijft u de gegevens naar een lokaal bestand, zoals een parket, en maakt u uw gegevensset uit dat bestand.  

We ondersteunen 2 soorten gegevenssets: 
+ Een [Tabeltabeldataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) vertegenwoordigt gegevens in een tabelindeling door het opgegeven bestand of de lijst met bestanden te ontleden. U een TabularDataset laden in een Panda's of Spark DataFrame voor verdere manipulatie en reiniging. Zie de [klasse TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference)voor een volledige lijst met gegevensindelingen waaruit u tabelsetssets maken.

+ Een [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) verwijst naar enkele of meerdere bestanden in uw gegevensopslag of openbare URL's. U bestanden waarop FileDatasets [verwijst, downloaden of monteren](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) naar uw compute-doel.

Aanvullende gegevenssets mogelijkheden zijn te vinden in de volgende documentatie:

+ Regellijn van de versie en de gegevensset [bijhouden.](how-to-version-track-datasets.md)
+ [Controleer uw gegevensset](how-to-monitor-datasets.md) om te helpen bij het detecteren van gegevensdrift.    

## <a name="work-with-your-data"></a>Werken met uw gegevens

Met gegevenssets u een aantal machine learning-taken uitvoeren door naadloze integratie met Azure Machine Learning-functies. 

+ Een [project voor het labelen van gegevens maken](#label).
+ Train machine learning-modellen:
     + [geautomatiseerde ML-experimenten](how-to-use-automated-ml-for-ml-models.md)
     + de [ontwerper](tutorial-designer-automobile-price-train-score.md#import-data)
     + [Notebooks](how-to-train-with-datasets.md)
     + [Azure Machine Learning-pijplijnen](how-to-create-your-first-pipeline.md)
+ Toegang tot gegevenssets voor het scoren met [batch-gevolgtrekking](how-to-use-parallel-run-step.md) in [machine learning-pijplijnen](how-to-create-your-first-pipeline.md).
+ Stel een gegevenssetmonitor in voor [detectie van gegevensdrift.](#drift)

<a name="label"></a>

## <a name="data-labeling"></a>Gegevensetikettering

Het labelen van grote hoeveelheden gegevens is vaak een hoofdpijn in machine learning projecten. Personen met een computervisiecomponent, zoals beeldclassificatie of objectdetectie, vereisen over het algemeen duizenden afbeeldingen en bijbehorende labels.

Azure Machine Learning biedt u een centrale locatie voor het maken, beheren en bewaken van etiketteringsprojecten. Labelingprojecten helpen bij het coördineren van de gegevens, labels en teamleden, zodat u de labeltaken efficiënter beheren. Momenteel ondersteunde taken zijn beeldclassificatie, meerdere of meerdere klassen, en objectidentificatie met behulp van begrensde vakken.

Maak een [project voor het labelen van gegevens](how-to-create-labeling-projects.md)en uitvoer een gegevensset uit voor gebruik in machine learning-experimenten.

<a name="drift"></a>

## <a name="data-drift"></a>Gegevensdrift

In de context van machine learning is datadrift de verandering in modelinvoergegevens die leidt tot prestatiedegradatie van het model. Het is een van de belangrijkste redenen model nauwkeurigheid degradeert na verloop van tijd, dus monitoring van gegevens drift helpt detecteren model prestaties problemen.

Zie het artikel [Een gegevenssetmonitor maken](how-to-monitor-datasets.md) voor meer informatie over het detecteren en waarschuwen voor gegevensdrift op nieuwe gegevens in een gegevensset.

## <a name="next-steps"></a>Volgende stappen 

+ Maak met deze stappen een gegevensset in Azure Machine Learning-studio of met de Python [SDK.](how-to-create-register-datasets.md)
+ Probeer voorbeelden van gegevenssetstraining uit met onze [voorbeeldnotitieblokken.](https://aka.ms/dataset-tutorial)
+ Zie deze [zelfstudie voor gegevensdrift](https://aka.ms/datadrift-notebook)voor voorbeelden van gegevensdrift.
