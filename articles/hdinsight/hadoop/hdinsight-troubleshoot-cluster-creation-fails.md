---
title: Problemen met het maken van clusters oplossen met Azure HDInsight
description: Meer informatie over het oplossen van problemen met het maken van Apache-clusters voor Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 04/14/2020
ms.openlocfilehash: 14b449590f6ffc5e735faa26baadfcc4e526450c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82996402"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Problemen met het maken van clusters oplossen met Azure HDInsight

De volgende problemen zijn de meest voorkomende oorzaken voor het maken van een cluster:

- Machtigings problemen
- Beperkingen voor het resource beleid
- Firewalls
- Resourcevergrendelingen
- Niet-ondersteunde onderdeel versies
- Naam beperkingen van opslag account
- Service storingen

## <a name="permissions-issues"></a>Problemen met machtigingen

Als u Azure data Lake Storage Gen2 gebruikt en het volgende fout bericht wordt weer gegeven `AmbariClusterCreationFailedErrorCode` : " :::no-loc text="Internal server error occurred while processing the request. Please retry the request or contact support."::: ", opent u de Azure Portal, gaat u naar uw opslag account en klikt u onder Access Control (IAM), zorgt u ervoor dat de rol **BLOB data contributor** van de opslag of de eigenaar van de blob van de **opslag** toegang heeft toegewezen aan de door de **gebruiker toegewezen beheerde identiteit** voor het abonnement. Zie [Machtigingen voor de beheerde identiteit instellen in het Data Lake Storage Gen2-account](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account) voor gedetailleerde instructies.

Als u Azure Data Lake Storage Gen1 gebruikt, raadpleegt u de installatie-en configuratie-instructies [hier](../hdinsight-hadoop-use-data-lake-store.md). Data Lake Storage Gen1 wordt niet ondersteund voor HBase-clusters en wordt niet ondersteund in HDInsight-versie 4,0.

Als Azure Storage wordt gebruikt, moet u ervoor zorgen dat de naam van het opslag account geldig is tijdens het maken van het cluster.

## <a name="resource-policy-restrictions"></a>Beperkingen voor het resource beleid

Met Azure-beleid op basis van een abonnement kan het maken van open bare IP-adressen niet worden geweigerd. Voor het maken van een HDInsight-cluster zijn twee openbare IP-adressen vereist.  

In het algemeen kunnen de volgende beleids regels van invloed zijn op het maken van een cluster:

* Het maken van IP-adressen & load balancers binnen het abonnement wordt verhinderd door beleid.
* Het maken van het opslag account wordt voor komen.
* Het verwijderen van netwerk bronnen (IP-adres/load balancers) wordt voor komen.

## <a name="firewalls"></a>Firewalls

Firewalls in uw virtuele netwerk of opslag account kunnen communicatie met IP-adressen van HDInsight-beheer weigeren.

Verkeer toestaan van de IP-adressen in de onderstaande tabel.

| IP-adres van bron | Doel | Richting |
|---|---|---|
| 168.61.49.99 | *: 443 | Inkomend |
| 23.99.5.239 | *: 443 | Inkomend |
| 168.61.48.131 | *: 443 | Inkomend |
| 138.91.141.162 | *: 443 | Inkomend |

Voeg ook de IP-adressen toe die specifiek zijn voor de regio waarin het cluster is gemaakt. Zie [IP-adressen van HDInsight-beheer](../hdinsight-management-ip-addresses.md) voor een lijst van de adressen voor elke Azure-regio.

Zie [een virtueel netwerk plannen voor Azure HDInsight-verbinding maken met meerdere netwerken](../hdinsight-plan-virtual-network-deployment.md#multinet)als u een snelle route of uw eigen aangepaste DNS-server gebruikt.

## <a name="resources-locks"></a>Resources vergren delen  

Zorg ervoor dat [het virtuele netwerk en de resource groep geen vergren delingen](../../azure-resource-manager/management/lock-resources.md)hebben. Clusters kunnen niet worden gemaakt of verwijderd als de resource groep is vergrendeld. 

## <a name="unsupported-component-versions"></a>Niet-ondersteunde onderdeel versies

Zorg ervoor dat u een [ondersteunde versie van Azure HDInsight](../hdinsight-component-versioning.md) gebruikt en alle [Apache Hadoop onderdelen](../hdinsight-component-versioning.md#apache-components-available-with-different-hdinsight-versions) in uw oplossing.  

## <a name="storage-account-name-restrictions"></a>Naam beperkingen van opslag account

Namen van opslag accounts mogen niet langer zijn dan 24 tekens en mogen geen speciaal teken bevatten. Deze beperkingen zijn ook van toepassing op de standaardnaam voor containers in het opslagaccount.

Andere naamgevings beperkingen zijn ook van toepassing op het maken van een cluster. Zie [beperkingen voor cluster namen](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name)voor meer informatie.

## <a name="service-outages"></a>Service storingen

Controleer de [Azure-status](https://status.azure.com) op mogelijke storingen of Service problemen.

## <a name="next-steps"></a>Volgende stappen

* [Azure HDInsight uitbreiden met behulp van een virtueel Azure-netwerk](../hdinsight-plan-virtual-network-deployment.md).
* [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Azure-opslag gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-blob-storage.md)
* [Clusters in HDInsight instellen met Apache Hadoop, Apache Spark, Apache Kafka en meer](../hdinsight-hadoop-provision-linux-clusters.md)
