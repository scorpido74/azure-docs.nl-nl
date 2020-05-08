---
title: Wat zijn open gegevens sets? Gecuratore open bare gegevens sets
titleSuffix: Azure Open Datasets
description: Meer informatie over Azure open gegevens sets, gegevens sets van het open bare domein, zoals weer, telling, feest dagen en locatie naar verrijkende voorspellende oplossingen.
ms.service: open-datasets
ms.topic: overview
author: cjgronlund
ms.author: cgronlun
ms.date: 05/06/2020
ms.openlocfilehash: 24f5a671076a44db382ac37e2f6532e5f0f2a3ae
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930383"
---
# <a name="what-are-azure-open-datasets-and-how-can-you-use-them"></a>Wat zijn Azure open gegevens sets en hoe kunt u deze gebruiken?

[Azure open gegevens sets](https://azure.microsoft.com/services/open-datasets/) zijn alle open bare gegevens sets die u kunt gebruiken om scenario's met specifieke functies toe te voegen aan Machine Learning oplossingen voor nauw keurigere modellen. Open gegevens sets bevinden zich in de Cloud op Microsoft Azure en zijn geïntegreerd in Azure Machine Learning en kunnen direct beschikbaar worden gesteld voor Azure Databricks en Machine Learning Studio (klassiek). U kunt de gegevens sets ook openen via Api's en deze gebruiken in andere producten, zoals Power BI en Azure Data Factory.

Gegevens sets bevatten informatie over openbaar domein voor weer, telling, feest dagen, open bare veiligheid en locatie die u helpen bij het trainen van machine learning modellen en verrijkende voorspellende oplossingen. U kunt ook uw open bare gegevens sets delen in azure open-gegevens sets. 

![Onderdelen van Azure open gegevens sets](./media/overview-what-are-open-datasets/open-datasets-components.png)

## <a name="curated-prepared-datasets"></a>Met curator gekweekte gegevens sets
Met de openstaande open bare gegevens sets in azure kunnen gegevens sets worden geoptimaliseerd voor gebruik in machine learning werk stromen. 

Als u alle beschik bare gegevens sets wilt zien, gaat u naar de [Azure open data sets-catalogus](https://azure.microsoft.com/services/open-datasets/catalog/).

Gegevens wetenschappers best Eden vaak het meren deel van hun tijd over het schoonmaken en voorbereiden van gegevens voor geavanceerde analyses. Open gegevens sets worden gekopieerd naar de Azure-Cloud en worden voor het eerst geverwerkt om tijd te besparen. Met regel matige intervallen worden gegevens uit de bronnen opgehaald, bijvoorbeeld door een FTP-verbinding met het National Oceanic and atmosferisch Administration (NOAA). Vervolgens worden gegevens geparseerd naar een gestructureerde indeling en vervolgens uitgebreid naar wens met functies zoals post code of locatie van het dichtstbijzijnde weer station.

Gegevens sets worden meegehost met Cloud Compute in azure, waardoor u eenvoudiger toegang en manipulaties kunt maken.  

Hieronder vindt u voor beelden van gegevens sets die beschikbaar zijn. 

### <a name="weather-data"></a>Weer gegevens
 
|Gegevensset         | Notebooks     | Beschrijving                                    |
|----------------|---------------|------------------------------------------------|
|[NOAA Integrated Surface data (ISD)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureDatabricks) | Wereld wijd per uur weer gegevens van NOAA met de beste ruimtelijke dekking in Noord-Amerika, Europa, Australië en delen van Azië. Dagelijks bijgewerkt. |
|[NOAA Global-prognose systeem (GFS)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureDatabricks) | 15 dagen van de Amerikaanse weers verwachting van de gegevens van NOAA. Dagelijks bijgewerkt. |

### <a name="calendar-data"></a>Agenda gegevens

|Gegevensset         | Notebooks     | Beschrijving                                    |
|----------------|---------------|------------------------------------------------|
|[Open bare feest dagen](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureDatabricks) | Wereld wijde gegevens over de open bare feestdag, met betrekking tot 41 landen of regio's van 1970 tot 2099. Inclusief land en of de meeste mensen een betaalde time-out hebben. |

## <a name="access-to-datasets"></a>Toegang tot gegevens sets  
Met een Azure-account kunt u toegang krijgen tot open gegevens sets met behulp van code of via de Azure-service-interface. De gegevens worden samen met Azure Cloud Compute-resources gebruikt voor gebruik in uw machine learning-oplossing.  

Open gegevens sets zijn beschikbaar via de Azure Machine Learning-gebruikers interface en-SDK. Gegevens sets openen biedt ook Azure Notebooks en Azure Databricks notitie blokken die u kunt gebruiken om verbinding te maken met Azure Machine Learning en Azure Databricks. Gegevens sets kunnen ook worden geopend via een python-SDK. 

U hebt echter geen Azure-account nodig om toegang te krijgen tot open gegevens sets; u kunt ze openen vanuit elke python-omgeving met of zonder Spark.

## <a name="request-or-contribute-datasets"></a>Gegevens sets voor aanvragen of bijdragen

Als u de gewenste gegevens niet kunt vinden, kunt u een e-mail sturen naar een [gegevensset](mailto:aod@microsoft.com?Subject=Request%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A) of [een gegevensset bijdragen](mailto:aod@microsoft.com?Subject=Contribute%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A). 

## <a name="next-steps"></a>Volgende stappen
* [Voorbeeld notitieblok](samples.md)
* [Zelf studie: regressie modellen met NY-taxi gegevens](/azure/machine-learning/tutorial-auto-train-models?context=azure/open-datasets/context/open-datasets-context)
* [Python-SDK voor open gegevens sets](/python/api/azureml-opendatasets/?view=azure-ml-py)
