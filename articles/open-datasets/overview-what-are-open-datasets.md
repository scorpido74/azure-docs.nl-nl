---
title: Wat zijn open datasets? Samengestelde openbare gegevenssets
titleSuffix: Azure Open Datasets
description: Meer informatie over Azure Open Datasets, samengestelde gegevenssets uit het publieke domein zoals weer, volkstelling, feestdagen en locatie om voorspellende oplossingen te verrijken.
ms.service: open-datasets
ms.topic: overview
author: cjgronlund
ms.author: cgronlun
ms.date: 11/04/2019
ms.openlocfilehash: fd5697f9c325dc4ad866c333ce1b20e008ebfa24
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73606162"
---
# <a name="what-are-azure-open-datasets-and-how-can-you-use-them"></a>Wat zijn Azure Open Datasets en hoe u ze gebruiken?

[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) zijn samengestelde openbare gegevenssets die u gebruiken om scenariospecifieke functies toe te voegen aan machine learning-oplossingen voor nauwkeurigere modellen. Open datasets bevinden zich in de cloud op Microsoft Azure en zijn geïntegreerd in Azure Machine Learning en direct beschikbaar voor Azure Databricks en Machine Learning Studio (klassiek). U de gegevenssets ook openen via API's en deze gebruiken in andere producten, zoals Power BI en Azure Data Factory.

Gegevenssets bevatten gegevens uit het publieke domein voor weer, telling, feestdagen, openbare veiligheid en locatie waarmee u machine learning-modellen trainen en voorspellende oplossingen verrijken. U uw openbare gegevenssets ook delen op Azure Open Datasets. 

![Azure Open Datasets-onderdelen](./media/overview-what-are-open-datasets/open-datasets-components.png)

## <a name="curated-prepared-datasets"></a>Samengestelde, voorbereide datasets
Samengestelde openbare gegevenssets in Azure Open Datasets zijn geoptimaliseerd voor verbruik in machine learning-workflows. 

Gegevenswetenschappers besteden vaak het grootste deel van hun tijd aan het schoonmaken en voorbereiden van gegevens op geavanceerde analyses. Open gegevenssets worden gekopieerd naar de Azure-cloud en vooraf verwerkt om u tijd te besparen. Op regelmatige tijdstippen worden gegevens uit de bronnen gehaald, bijvoorbeeld door een FTP-verbinding met de National Oceanic and Atmospheric Administration (NOAA). Vervolgens worden gegevens geparseerd in een gestructureerde indeling en vervolgens naar gelang van het geval verrijkt met functies zoals postcode of locatie van het dichtstbijzijnde weerstation.

Gegevenssets worden samen gehost met cloud compute in Azure, waardoor toegang en manipulatie eenvoudiger wordt.  

Hieronder volgen voorbeelden van beschikbare datasets. 

### <a name="weather-data"></a>Weersgegevens
 
|Gegevensset         | Notebooks     | Beschrijving                                    |
|----------------|---------------|------------------------------------------------|
|[NOAA Integrated Surface Data (ISD)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureDatabricks) | Wereldwijde weersgegevens per uur van NOAA met de beste ruimtelijke dekking in Noord-Amerika, Europa, Australië en delen van Azië. Dagelijks bijgewerkt. |
|[NOAA Global Forecast System (GFS)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureDatabricks) | 15-daagse Amerikaanse weersvoorspellingsgegevens per uur van NOAA. Dagelijks bijgewerkt. |

### <a name="calendar-data"></a>Agendagegevens

|Gegevensset         | Notebooks     | Beschrijving                                    |
|----------------|---------------|------------------------------------------------|
|[Feestdagen](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureDatabricks) | Wereldwijde vakantiegegevens, die betrekking hebben op 41 landen of regio's van 1970 tot 2099. Inclusief land en of de meeste mensen hebben betaald vrije tijd. |

## <a name="access-to-datasets"></a>Toegang tot gegevenssets  
Met een Azure-account hebt u toegang tot geopende gegevenssets met behulp van code of via de Azure-serviceinterface. De gegevens worden gekoppeld aan Azure cloud compute resources voor gebruik in uw machine learning-oplossing.  

Open gegevenssets zijn beschikbaar via de Azure Machine Learning UI en SDK. Open Datasets biedt ook Azure-notitieblokken en Azure Databricks-notitieblokken die u gebruiken om gegevens te verbinden met Azure Machine Learning en Azure Databricks. Datasets zijn ook toegankelijk via een Python SDK. 

U hebt echter geen Azure-account nodig om toegang te krijgen tot Open Datasets. u ze openen vanuit elke Python-omgeving met of zonder Spark.

## <a name="request-or-contribute-datasets"></a>Gegevenssets aanvragen of bijdragen

Als u de gewenste gegevens niet vinden, u ons een e-mail sturen om [een gegevensset](mailto:aod@microsoft.com?Subject=Request%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A) aan te vragen of [een gegevensset bij te dragen.](mailto:aod@microsoft.com?Subject=Contribute%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A) 

## <a name="next-steps"></a>Volgende stappen
* [Voorbeeldnotitieblok](samples.md)
* [Zelfstudie: Regressiemodellering met NY-taxigegevens](/azure/machine-learning/service/tutorial-auto-train-models?context=azure/open-datasets/context/open-datasets-context)
* [Python SDK voor Open Datasets](/python/api/azureml-opendatasets/?view=azure-ml-py)
