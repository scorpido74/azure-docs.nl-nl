---
title: Opties voor gegevensopname
titleSuffix: Azure Machine Learning
description: Meer informatie over opties voor het innemen van gegevens voor het trainen van uw machine learning-modellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.openlocfilehash: 6b1c671d2079c7d8ab59e9afe981ccef3f58ef27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086889"
---
# <a name="data-ingestion-in-azure-machine-learning"></a>Gegevensopname in Azure Machine Learning

In dit artikel leert u de voor- en nadelen van de volgende opties voor gegevensopname die beschikbaar zijn met Azure Machine Learning. 

1. [Azure Data Factory-pijplijnen](#use-azure-data-factory)
2. [Azure Machine Learning Python SDK](#use-the-python-sdk)

Gegevensopname is het proces waarbij ongestructureerde gegevens uit één of meerdere bronnen worden geëxtraheerd en vervolgens worden voorbereid op het trainen van machine learning-modellen. Het is ook tijdintensief, vooral als het handmatig gebeurt, en als u grote hoeveelheden gegevens uit meerdere bronnen hebt. Het automatiseren van deze inspanning maakt resources vrij en zorgt ervoor dat uw modellen de meest recente en toepasselijke gegevens gebruiken.

Azure Data Factory (ADF) is speciaal gebouwd om gegevens te extraheren, te laden en te transformeren, maar met de Python SDK u een aangepaste codeoplossing ontwikkelen voor basistaken voor het innemen van gegevens. Als geen van beide helemaal is wat u nodig hebt, u ADF en de Python SDK ook samen gebruiken om een algehele workflow voor gegevensopname te maken die aan uw behoeften voldoet. 

## <a name="use-azure-data-factory"></a>Azure Data Factory gebruiken

[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) biedt native ondersteuning voor gegevensbronbewaking en triggers voor gegevensopnamepijplijnen.  

In de volgende tabel worden de voor- en nadelen voor het gebruik van Azure Data Factory voor uw gegevensopnameworkflows samengevat.

|Voordelen|Nadelen
---|---
Speciaal gebouwd om gegevens te extraheren, te laden en te transformeren.|Biedt momenteel een beperkte set azure data factory-pijplijntaken 
Hiermee u gegevensgestuurde workflows maken voor het orkestreren van gegevensverplaatsingen en transformaties op schaal.|Duur te bouwen en te onderhouden. Zie [de prijspagina](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) van Azure Data Factory voor meer informatie.
Geïntegreerd met verschillende Azure-hulpprogramma's zoals [Azure Databricks](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook) en [Azure-functies](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity) | Draait scripts niet native, maar is afhankelijk van afzonderlijke compute voor scriptruns 
Native ondersteunt gegevensbron geactiveerde gegevens opname| 
Gegevensvoorbereiding en modeltrainingsprocessen staan los van elkaar.|
Ingesloten gegevensregelmogelijkheid voor Azure Data Factory-gegevensstromen|
Biedt een low-code [ervaring gebruikersinterface](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) voor niet-scripting benaderingen |

Deze stappen en het volgende diagram illustreren de gegevensopnameworkflow van Azure Data Factory.

1. Haal de gegevens uit de bronnen
1. De gegevens transformeren en opslaan in een uitvoerblobcontainer, die fungeert als gegevensopslag voor Azure Machine Learning
1. Met voorbereide gegevens die zijn opgeslagen, wordt in de Azure Data Factory-pijplijn een computer learning-pijplijn voor training aanroepen die de voorbereide gegevens ontvangt voor modeltraining


    ![ADF-gegevensinname](media/concept-data-ingestion/data-ingest-option-one.svg)
    
Meer informatie over het maken van een pijplijn voor gegevensopname voor Machine Learning met [Azure Data Factory](how-to-data-ingest-adf.md).

## <a name="use-the-python-sdk"></a>De Python SDK gebruiken 

Met de [Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml)u taken voor het innemen van gegevens opnemen in een azure [machine learning-pijplijnstap.](how-to-create-your-first-pipeline.md)

In de volgende tabel worden de voor- en nadelen samengevat voor het gebruik van de SDK en een ML-pijplijnstap voor taken voor gegevensopname.

Voordelen| Nadelen
---|---
Uw eigen Python-scripts configureren | Ondersteunt geen native ondersteuning voor het activeren van gegevensbronwijzigingen. Hiervoor zijn logic-app- of Azure-functie-implementaties vereist
Gegevensvoorbereiding als onderdeel van elke modeltraininguitvoering|Vereist ontwikkelingsvaardigheden om een gegevensopnamescript te maken
Ondersteunt scripts voor gegevensvoorbereiding op verschillende compute-doelen, waaronder [Azure Machine Learning compute](concept-compute-target.md#azure-machine-learning-compute-managed) |Biedt geen gebruikersinterface voor het maken van het innamemechanisme

In het volgende diagram bestaat de Azure Machine Learning-pijplijn uit twee stappen: gegevensopname en modeltraining. De stap voor het innemen van gegevens omvat taken die kunnen worden uitgevoerd met Python-bibliotheken en de Python SDK, zoals het extraheren van gegevens uit lokale/webbronnen en basisgegevenstransformaties, zoals ontbrekende waardetoerekening. De trainingsstap gebruikt vervolgens de voorbereide gegevens als input voor uw trainingsscript om uw machine learning-model te trainen. 

![Azure-pijplijn + opname van SDK-gegevens](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken van een pijplijn voor gegevensopname voor Machine Learning met [Azure Data Factory](how-to-data-ingest-adf.md)
* Meer informatie over het automatiseren en beheren van de ontwikkelingslevenscyclus van uw gegevensopnamepijplijnen met [Azure Pipelines.](how-to-cicd-data-ingestion.md)
