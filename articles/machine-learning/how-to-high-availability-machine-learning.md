---
title: Tolerantie verhogen
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u uw Azure Machine Learning gerelateerde resources flexibeler maakt voor storingen met behulp van een configuratie met hoge Beschik baarheid.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 07/16/2020
ms.openlocfilehash: 4f2bf239e1157f5c927c4449857ad5f7793ccb49
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097224"
---
# <a name="increase-the-resiliency-of-azure-machine-learning"></a>De tolerantie van Azure Machine Learning verg Roten

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Meer informatie over het maken van uw Azure Machine Learning gerelateerde resources met behulp van configuraties met een hoge Beschik baarheid. De Azure-Services waarvan Azure Machine Learning afhankelijk is, kunnen worden geconfigureerd voor hoge Beschik baarheid. In dit artikel vindt u informatie over de services die kunnen worden geconfigureerd voor hoge Beschik baarheid en koppelingen naar informatie over het configureren van deze resources.

> [!NOTE]
> Azure Machine Learning zelf biedt geen nood herstel optie.

## <a name="understand-azure-services-for-azure-machine-learning"></a>Meer informatie over Azure-Services voor Azure Machine Learning

Azure Machine Learning is afhankelijk van meerdere Azure-Services en heeft meerdere lagen. Sommige van deze zijn ingericht in uw (klant) abonnement. U bent verantwoordelijk voor de configuratie van hoge Beschik baarheid van deze services. Sommige worden gemaakt in een micro soft-abonnement en worden beheerd door micro soft.

* **Azure machine learning-infra structuur**: door micro soft beheerde omgeving voor Azure machine learning werk ruimte.

* **Gekoppelde resources**: resources die in uw abonnement zijn ingericht tijdens het maken van Azure machine learning werk ruimte. Ze omvatten Azure Storage, Azure Key Vault, Azure Container Registry (ACR) en app Insights. U bent zelf verantwoordelijk voor de instellingen voor hoge Beschik baarheid voor deze resources.
  * De standaard opslag bevat gegevens, zoals model, logboek gegevens van de training en de gegevensset.
  * Key Vault heeft referenties voor Storage, ACR, data stores.
  * ACR heeft docker-installatie kopie voor trainings-en detraining-omgeving.
  * App Insights is voor het bewaken van Azure Machine Learning.

* **Reken resources**: resources die u maakt na de implementatie van de werk ruimte. U kunt bijvoorbeeld een reken instantie of reken cluster maken om een machine learning model te trainen.
  * Reken instantie en reken cluster: micro soft Managed model Development Environment.
  * Andere resources: ze zijn de computer bronnen die kunnen worden bijgevoegd bij Azure Machine Learning zoals Azure Kubernetes service (AKS), Azure Databricks, Azure Container Instances (ACI) en HDInsight. U bent zelf verantwoordelijk voor de instelling maximale Beschik baarheid.

* **Aanvullende gegevens archieven**: Azure machine learning kunt extra gegevens archieven zoals Azure Storage, Azure Data Lake Storage en Azure SQL database koppelen voor trainings gegevens.  Ze bevinden zich in uw abonnement en u bent verantwoordelijk voor de instelling maximale Beschik baarheid.

In de volgende tabel ziet u welke services worden beheerd door micro soft, die door u worden beheerd en die standaard Maxi maal beschikbaar zijn:

| Service | Beheerd door | HA standaard |
| ----- | ----- | ----- |
| **Azure Machine Learning-infra structuur** | Microsoft | |
| **Gekoppelde resources** |
| Azure Storage | U | |
| Azure Key Vault | U | ✓ |
| Azure Container Registry | U | |
| Application Insights | U | N.v.t. |
| **Reken resources** |
| Reken instantie | Microsoft |  |
| Reken cluster | Microsoft |  |
| Andere resources, zoals Azure Kubernetes service, <br>Azure Databricks, Azure-container exemplaar, Azure HDInsight | U |  |
| **Aanvullende gegevens archieven** zoals Azure Storage, Azure SQL database,<br> Azure Database for PostgreSQL, Azure Database for MySQL, <br>Azure Databricks bestands systeem | U | |

Gebruik de informatie in de rest van dit document voor meer informatie over de acties die nodig zijn om elk van deze services Maxi maal beschikbaar te maken.

## <a name="associated-resources"></a>Gekoppelde resources

> [!IMPORTANT]
> Azure Machine Learning biedt geen ondersteuning voor de standaard failover van het opslag account met behulp van geo-redundante opslag (GRS) of geografisch redundante opslag met lees toegang (RA-GRS) of geo-redundante opslag met lees toegang (RA-GZRS).

Zorg ervoor dat de instelling hoge Beschik baarheid van elke resource met onderstaande informatie wordt gecontroleerd.

* **Azure Storage**: voor het configureren van de instelling maximale Beschik baarheid raadpleegt u [Azure Storage redundantie](https://docs.microsoft.com/azure/storage/common/storage-redundancy).
* **Azure Key Vault**: het biedt standaard service voor hoge Beschik baarheid en u hoeft niet in te grijpen.  Zie [Azure Key Vault Beschik baarheid en redundantie](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance).
* **Azure container Registry**: Kies Premium SKU voor geo-replicatie. Zie [geo-replicatie in azure container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
* **Application Insights**: de instelling biedt geen hoge Beschik baarheid. U kunt de Bewaar periode voor gegevens en Details in het [verzamelen, bewaren en opslaan van gegevens aanpassen in Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#how-long-is-the-data-kept).

## <a name="compute-resources"></a>Reken resources

Zorg ervoor dat de instelling hoge Beschik baarheid van elke resource met onderstaande documentatie wordt gecontroleerd.

* **Azure Kubernetes service**: Zie [Aanbevolen procedures voor bedrijfs continuïteit en herstel na nood gevallen in azure KUBERNETES service (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) en [Maak een Azure KUBERNETES service-cluster (AKS) dat gebruikmaakt van beschikbaarheids zones](https://docs.microsoft.com/azure/aks/availability-zones). Als het AKS-cluster is gemaakt door Azure Machine Learning (met behulp van Studio, SDK of ML CLI), wordt de hoge Beschik baarheid van meerdere regio's niet ondersteund.
* **Azure Databricks**: Zie [regionale nood herstel voor Azure Databricks clusters](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery).
* **Azure container instance**: ACI Orchestrator is verantwoordelijk voor failover. Zie [Azure container instances en container Orchestrator](https://docs.microsoft.com/azure/container-instances/container-instances-orchestrator-relationship).
* **Azure hdinsight**: Zie [Services voor hoge Beschik baarheid die worden ondersteund door Azure hdinsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-components).

## <a name="additional-data-stores"></a>Aanvullende gegevens archieven

Zorg ervoor dat de instelling hoge Beschik baarheid van elke resource met onderstaande documentatie wordt gecontroleerd.

* **Azure Blob-container/Azure file share/Azure data Lake Gen2**: zelfde als standaard opslag.
* **Azure data Lake gen1**: Zie[richt lijnen voor herstel na nood gevallen voor gegevens in azure data Lake Storage gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-disaster-recovery-guidance).
* **Azure SQL database**: Zie [hoge Beschik baarheid en Azure SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability).
* **Azure database for PostgreSQL**: Bekijk [concepten met hoge Beschik baarheid in azure database for PostgreSQL-één server](https://docs.microsoft.com/azure/postgresql/concepts-high-availability).
* **Azure database for MySQL**: Zie [inzicht in bedrijfs continuïteit in azure database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-business-continuity).
* **Databricks-bestands systeem**: Zie [regionale nood herstel voor Azure Databricks clusters](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery).

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Als u uw eigen sleutel (door de klant beheerde sleutel) voor de implementatie van Azure Machine Learning-werk ruimte opgeeft, wordt Cosmos DB ook in uw abonnement ingericht. In dat geval bent u verantwoordelijk voor de hoge Beschik baarheid. Bekijk [hoge Beschik baarheid met Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability)

## <a name="next-steps"></a>Volgende stappen

Gebruik een [Azure Resource Manager sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced)om Azure machine learning met gekoppelde resources te implementeren met de instellingen voor hoge Beschik baarheid.