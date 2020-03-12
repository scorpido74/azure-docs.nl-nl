---
title: Opties voor gegevens opname
titleSuffix: Azure Machine Learning
description: Meer informatie over opties voor gegevens opname voor het trainen van uw machine learning-modellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.openlocfilehash: 6b1c671d2079c7d8ab59e9afe981ccef3f58ef27
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086889"
---
# <a name="data-ingestion-in-azure-machine-learning"></a>Gegevens opname in Azure Machine Learning

In dit artikel vindt u informatie over de voor-en nadelen van de volgende opties voor gegevens opname die beschikbaar zijn met Azure Machine Learning. 

1. [Azure Data Factory](#use-azure-data-factory) pijp lijnen
2. [Python SDK voor Azure Machine Learning](#use-the-python-sdk)

Gegevens opname is het proces waarin ongestructureerde gegevens worden geëxtraheerd uit een of meer bronnen en vervolgens worden voor bereid voor de trainings machine learning modellen. Het is ook tijdrovend, vooral als hand matig wordt gedaan, en als u grote hoeveel heden gegevens uit meerdere bronnen hebt. Als u deze inspanning automatiseert, worden bronnen vrijgemaakt en kunnen uw modellen de meest recente en toepasselijke gegevens gebruiken.

Azure Data Factory (ADF) is speciaal ontworpen voor het uitpakken, laden en transformeren van gegevens, maar met de python-SDK kunt u een aangepaste code oplossing ontwikkelen voor basis taken voor gegevens opname. Als dat niet het geval is, kunt u ook ADF en de python-SDK gebruiken om een algemene werk stroom voor gegevens opname te maken die aan uw behoeften voldoet. 

## <a name="use-azure-data-factory"></a>Azure Data Factory gebruiken

[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) biedt systeem eigen ondersteuning voor de bewaking van gegevens bronnen en triggers voor gegevens opname pijplijnen.  

De volgende tabel bevat een overzicht van de voor-en nadelen voor het gebruik van Azure Data Factory voor uw werk stromen voor gegevens opname.

|Professionals|Nadelen
---|---
Speciaal gebouwd om gegevens te extra heren, te laden en te transformeren.|Biedt momenteel een beperkte set Azure Data Factory pijplijn taken 
Hiermee kunt u gegevensgestuurde werk stromen maken voor het organiseren van gegevens verplaatsing en-trans formaties op schaal.|Duur om te bouwen en te onderhouden. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) van Azure Data Factory voor meer informatie.
Geïntegreerd met verschillende Azure-hulpprogram ma's zoals [Azure Databricks](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook) en [Azure functions](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity) | Voert geen systeem eigen scripts uit, in plaats daarvan afhankelijk van afzonderlijke Compute voor script uitvoeringen 
Systeem eigen ondersteuning voor door gegevens bron geactiveerde gegevens opname| 
Gegevens voorbereiding en model trainings processen zijn gescheiden.|
Afkomst mogelijkheid voor Inge sloten gegevens voor Azure Data Factory gegevens stromen|
Biedt een [gebruikers interface](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) met lage code ervaring voor niet-script benaderingen |

Deze stappen en het volgende diagram illustreren de werk stroom voor gegevens opname van Azure Data Factory.

1. Gegevens uit de bronnen ophalen
1. Transformeer de gegevens en sla deze op in een uitvoer BLOB-container, die fungeert als gegevens opslag voor Azure Machine Learning
1. Met de voor bereide gegevens die worden opgeslagen, roept de Azure Data Factory-pijp lijn een training Machine Learning pijp lijn aan die de voor bereide gegevens voor model training ontvangt


    ![Opname van ADF-gegevens](media/concept-data-ingestion/data-ingest-option-one.svg)
    
Meer informatie over het bouwen van een pijp lijn voor gegevens opname voor Machine Learning met [Azure Data Factory](how-to-data-ingest-adf.md).

## <a name="use-the-python-sdk"></a>De python-SDK gebruiken 

Met de [python-SDK](https://docs.microsoft.com/python/api/overview/azure/ml)kunt u gegevens opname taken opnemen in een [Azure machine learning pijplijn](how-to-create-your-first-pipeline.md) stap.

In de volgende tabel vindt u een overzicht van de voor-en hand leidingen voor het gebruik van de SDK en een stap van ML-pijp lijnen voor gegevens opname taken.

Professionals| Nadelen
---|---
Uw eigen python-scripts configureren | Ondersteunt geen systeem eigen ondersteuning voor het activeren van gegevens bronnen. Vereist logische app-of Azure-functie-implementaties
Gegevens voorbereiding als onderdeel van elke model training-uitvoering|Ontwikkel vaardigheden vereist voor het maken van een script voor gegevens opname
Ondersteunt gegevens voorbereidings scripts op verschillende reken doelen, waaronder [Azure machine learning Compute](concept-compute-target.md#azure-machine-learning-compute-managed) |Biedt geen gebruikers interface voor het maken van het opname mechanisme

In het volgende diagram bestaat de Azure Machine Learning-pijp lijn uit twee stappen: gegevens opname en model training. De stap gegevens opname omvat taken die kunnen worden uitgevoerd met behulp van python-bibliotheken en de python-SDK, zoals het extra heren van gegevens uit lokale/webbronnen en basis gegevens transformaties, zoals ontbrekende waarde-overlopende waarden. De trainings stap gebruikt vervolgens de voor bereide gegevens als invoer voor uw trainings script om uw machine learning model te trainen. 

![Azure-pijp lijn en SDK-gegevens opname](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het bouwen van een pijp lijn voor gegevens opname voor Machine Learning met [Azure Data Factory](how-to-data-ingest-adf.md)
* Leer hoe u de ontwikkelings levenscyclus van uw gegevens opname pijplijnen kunt automatiseren en beheren met [Azure-pijp lijnen](how-to-cicd-data-ingestion.md).
