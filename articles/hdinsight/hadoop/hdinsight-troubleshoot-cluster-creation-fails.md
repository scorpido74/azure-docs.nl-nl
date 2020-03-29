---
title: Fouten bij het maken van groepen oplossen met Azure HDInsight
description: Meer informatie over het oplossen van problemen met het maken van Apache-clustercreatie voor Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/26/2019
ms.openlocfilehash: 1e13c7ef8eae81ef2a12a8761b0596f6329f94dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937912"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Fouten bij het maken van groepen oplossen met Azure HDInsight

De volgende problemen zijn de meest voorkomende oorzaken voor fouten bij het maken van clusteren:

- Machtigingsproblemen
- Beperkingen voor resourcebeleid
- Firewalls
- Resourcevergrendelingen
- Niet-ondersteunde componentversies
- Beperkingen voor naam van het opslagaccount
- Serviceonderbrekingen

## <a name="permissions-issues"></a>Problemen met machtigingen

Als u Azure Data Lake Storage Gen2 ```AmbariClusterCreationFailedErrorCode```gebruikt ```Internal server error occurred while processing the request. Please retry the request or contact support.```en de fout ontvangt , opent u de Azure-portal, gaat u naar uw opslagaccount en onder Toegangsbeheer (IAM), controleert u of de **opslagblobgegevensbijdrager** of de rol **Opslagblob-gegevenseigenaar** toegang heeft gekregen tot de **door de gebruiker toegewezen beheerde identiteit** voor het abonnement. Zie [Machtigingen voor de beheerde identiteit instellen in het Data Lake Storage Gen2-account](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account) voor gedetailleerde instructies.

Als u Azure Data Lake Storage Gen1 gebruikt, raadpleegt u [hier](../hdinsight-hadoop-use-data-lake-store.md)installatie- en configuratie-instructies. Data Lake Storage Gen1 wordt niet ondersteund voor HBase-clusters en wordt niet ondersteund in HDInsight-versie 4.0.

Als u Azure Storage gebruikt, moet u ervoor zorgen dat de naam van het opslagaccount geldig is tijdens het maken van het cluster.

## <a name="resource-policy-restrictions"></a>Beperkingen voor resourcebeleid

Azure-beleid op basis van abonnementen kan het maken van openbare IP-adressen weigeren. Voor het maken van een HDInsight-cluster zijn twee openbare IP-adressen vereist.  

In het algemeen kan het volgende beleid van invloed zijn op het maken van clusteren:

* Beleid dat het maken van IP-adres & Load balancers binnen het abonnement verhindert.
* Beleid dat het aanmaken van een opslagaccount verhindert.
* Beleid dat verwijdering van netwerkbronnen voorkomt (IP-adres /Load Balancers).

## <a name="firewalls"></a>Firewalls

Firewalls op uw virtuele netwerk- of opslagaccount kunnen de communicatie met HDInsight-beheer-IP-adressen weigeren.

Verkeer toestaan vanaf de IP-adressen in de onderstaande tabel.

| IP-adres van bron | Doel | Richting |
|---|---|---|
| 168.61.49.99 | *:443 | Inkomend |
| 23.99.5.239 | *:443 | Inkomend |
| 168.61.48.131 | *:443 | Inkomend |
| 138.91.141.162 | *:443 | Inkomend |

Voeg ook de IP-adressen toe die specifiek zijn voor het gebied waar het cluster wordt gemaakt. Zie [IP-adressen van HDInsight-beheer](../hdinsight-management-ip-addresses.md) voor een lijst met de adressen voor elke Azure-regio.

Als u een expresroute of uw eigen aangepaste DNS-server gebruikt, [raadpleegt u Een virtueel netwerk plannen voor Azure HDInsight - meerdere netwerken verbinden](../hdinsight-plan-virtual-network-deployment.md#multinet).

## <a name="resources-locks"></a>Resources-vergrendelingen  

Zorg ervoor dat er geen vergrendelingen zijn [op uw virtuele netwerk- en resourcegroep.](../../azure-resource-manager/management/lock-resources.md) Clusters kunnen niet worden gemaakt of verwijderd als de brongroep is vergrendeld. 

## <a name="unsupported-component-versions"></a>Niet-ondersteunde componentversies

Zorg ervoor dat u een [ondersteunde versie van Azure HDInsight](../hdinsight-component-versioning.md) en eventuele Apache [Hadoop-componenten](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions) in uw oplossing gebruikt.  

## <a name="storage-account-name-restrictions"></a>Beperkingen voor naam van het opslagaccount

De namen van opslagaccounten mogen niet meer dan 24 tekens bevatten en mogen geen speciaal teken bevatten. Deze beperkingen zijn ook van toepassing op de standaardnaam voor containers in het opslagaccount.

Andere naamgevingsbeperkingen zijn ook van toepassing op het maken van clusteren. Zie [Clusternaambeperkingen](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name)voor meer informatie.

## <a name="service-outages"></a>Serviceonderbrekingen

Controleer [de Azure-status](https://status.azure.com) op mogelijke uitval of serviceproblemen.

## <a name="next-steps"></a>Volgende stappen

* [Azure HDInsight uitbreiden met een Azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md)
* [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Azure-opslag gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-blob-storage.md)
* [Clusters in HDInsight instellen met Apache Hadoop, Apache Spark, Apache Kafka en meer](../hdinsight-hadoop-provision-linux-clusters.md)
