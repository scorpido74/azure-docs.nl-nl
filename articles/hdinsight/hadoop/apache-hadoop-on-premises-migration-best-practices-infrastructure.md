---
title: 'Infrastructuur: On-premises Apache Hadoop naar Azure HDInsight'
description: Leer aanbevolen procedures voor infrastructuur voor het migreren van on-premises Hadoop-clusters naar Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: d7ee8ae121e3cbb9760a87c95d12109a9b05e0c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74951510"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>On-premises Apache Hadoop-clusters migreren naar Azure HDInsight - aanbevolen procedures voor infrastructuur

In dit artikel worden aanbevelingen gegeven voor het beheer van de infrastructuur van Azure HDInsight-clusters. Het maakt deel uit van een serie die best practices biedt om te helpen bij het migreren van on-premises Apache Hadoop-systemen naar Azure HDInsight.

## <a name="plan-for-hdinsight-cluster-capacity"></a>Capaciteit voor HDInsight-cluster plannen

De belangrijkste keuzes die moeten worden gemaakt voor de capaciteitsplanning van hdInsight-cluster zijn de volgende opties:

**Regio**  
Het Azure-gebied bepaalt waar het cluster fysiek is ingericht. Om de latentie van lezen en schrijven te minimaliseren, moet het cluster zich in hetzelfde gebied als de gegevens bevinden.

**Opslaglocatie en -grootte**  
De standaardopslag moet zich in hetzelfde gebied als het cluster bevinden.Voor een cluster met 48 node wordt aanbevolen om 4 tot 8 opslagaccounts te hebben. Hoewel er mogelijk al voldoende totale opslag is, biedt elk opslagaccount extra netwerkbandbreedte voor de compute-knooppunten. Wanneer er meerdere opslagaccounts zijn, gebruikt u een willekeurige naam voor elk opslagaccount, zonder voorvoegsel. Het doel van willekeurige naamgeving is het verminderen van de kans op opslagknelpunten (beperking) of common-mode fouten in alle accounts. Gebruik voor betere prestaties slechts één container per opslagaccount.

**VM-grootte en -type (ondersteunt nu de G-serie)**  
Elk clustertype heeft een set knooppunttypen en elk knooppunttype heeft specifieke opties voor de grootte en het type VM. De VM-grootte en -type worden bepaald door cpu-verwerkingskracht, RAM-grootte en netwerklatentie. Een gesimuleerde werkbelasting kan worden gebruikt om de optimale VM-grootte en -type voor elke knooppunttypen te bepalen.

**Aantal werknemersknooppunten**  
Het initiële aantal werkknooppunten kan worden bepaald met behulp van de gesimuleerde workloads. Het cluster kan later worden geschaald door meer werknemersknooppunten toe te voegen om te voldoen aan de piekbelastingsvereisten. Het cluster kan later worden teruggeschaald wanneer de extra werkknooppunten niet nodig zijn.

Zie voor meer informatie het artikel [Capaciteitsplanning voor HDInsight-clusters](../hdinsight-capacity-planning.md).

## <a name="use-recommended-virtual-machine-type-for-cluster"></a>Aanbevolen type virtuele machine voor cluster gebruiken

Zie [Standaardknooppuntconfiguratie en virtuele machineformaten voor clusters](../hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) voor aanbevolen virtuele machinetypen voor elk type HDInsight-cluster.

## <a name="check-hadoop-components-availability-in-hdinsight"></a>Controleer de beschikbaarheid van Hadoop-componenten in HDInsight

Elke HDInsight-versie is een clouddistributie van een set Hadoop-ecosysteemcomponenten. Zie [HDInsight Component Versioning](../hdinsight-component-versioning.md) voor meer informatie over alle HDInsight-componenten en hun huidige versies.

U ook Apache Ambari UI of Ambari REST API gebruiken om de Hadoop-componenten en -versies in HDInsight te controleren.

Toepassingen of componenten die beschikbaar waren in on-premises clusters, maar geen deel uitmaken van de HDInsight-clusters, kunnen worden toegevoegd op een randknooppunt of op een VM in hetzelfde VNet als het HDInsight-cluster. Een Hadoop-toepassing van derden die niet beschikbaar is op Azure HDInsight, kan worden geïnstalleerd met de optie 'Toepassingen' in het HDInsight-cluster. Aangepaste Hadoop-toepassingen kunnen worden geïnstalleerd op het HDInsight-cluster met behulp van "scriptacties". In de volgende tabel worden enkele van de algemene toepassingen en hun HDInsight-integratieopties weergegeven:

|**Toepassing**|**Integratie**
|---|---|
|Luchtstroom|IaaS of HDInsight edge node
|Alluxio (Alluxio)|IaaS  
|Arcadia|IaaS 
|Atlas|None (Only HDP)
|Datameer|HDInsight randknooppunt
|Datastax (Cassandra)|IaaS (CosmosDB een alternatief op Azure)
|DataTorrent (DataTorrent)|IaaS 
|Drill|IaaS 
|Ignite|IaaS
|Jethro|IaaS 
|Mapador Mapador|IaaS 
|Mongo|IaaS (CosmosDB een alternatief op Azure)
|NiFi (NiFi)|IaaS 
|Presto|IaaS of HDInsight edge node
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|Sas|IaaS 
|Vertica|IaaS (SQLDW een alternatief op Azure)
|Tableau|IaaS 
|Waterlijn|HDInsight randknooppunt
|Streamsets|HDInsight-rand 
|Palantir|IaaS 
|Sailpoint|Iaas Iaas 

Zie voor meer informatie het artikel [Apache Hadoop componenten beschikbaar met verschillende HDInsight versies](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)

## <a name="customize-hdinsight-clusters-using-script-actions"></a>HDInsight-clusters aanpassen met scriptacties

HDInsight biedt een methode van clusterconfiguratie genaamd een **scriptactie.** Een scriptactie is Bash-script dat wordt uitgevoerd op de knooppunten in een HDInsight-cluster en kan worden gebruikt om extra componenten te installeren en configuratie-instellingen te wijzigen.

Scriptacties moeten worden opgeslagen op een URI die toegankelijk is vanuit het HDInsight-cluster. Ze kunnen worden gebruikt tijdens of na het maken van het cluster en kunnen ook worden beperkt tot alleen worden uitgevoerd op bepaalde knooppunttypen.

Het script kan één keer worden uitgevoerd of uitgevoerd. De doorgaande scripts worden gebruikt om nieuwe werkknooppunten aan te passen die aan het cluster zijn toegevoegd door middel van schaalbewerkingen. Een blijvend script kan ook wijzigingen toepassen op een ander knooppunttype, zoals een hoofdknooppunt, wanneer schaalbewerkingen plaatsvinden.

HDInsight biedt vooraf geschreven scripts om de volgende componenten op HDInsight-clusters te installeren:

- Een Azure Storage-account toevoegen
- Hue installeren
- Presto installeren
- Solr installeren
- Giraph installeren
- Hive-bibliotheken vooraf laden
- Mono installeren of bijwerken

> [!Note]  
> HDInsight biedt geen directe ondersteuning voor aangepaste hadoopcomponenten of componenten die zijn geïnstalleerd met scriptacties.

Scriptacties kunnen ook worden gepubliceerd naar de Azure Marketplace als een HDInsight-toepassing.

Raadpleeg voor meer informatie de volgende artikelen:

- [Installeer Apache Hadoop-toepassingen van derden op HDInsight](../hdinsight-apps-install-applications.md)
- [HDInsight-clusters aanpassen met scriptacties](../hdinsight-hadoop-customize-cluster-linux.md)
- [Een HDInsight-toepassing publiceren in Azure Marketplace](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>HDInsight configs aanpassen met Bootstrap

Wijzigingen in configs in de `core-site.xml`config bestanden zoals , `hive-site.xml` en `oozie-env.xml` kan worden gemaakt met behulp van Bootstrap. Het volgende script is een voorbeeld met de Powershell [AZ module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) cmdlet [New-AzHDInsightClusterConfig:](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster)

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New—AzHDInsightClusterConfig '
    | Set—AzHDInsightDefaultStorage
    —StorageAccountName "$defaultStorageAccountName.blob. core.windows.net" `
    —StorageAccountKey "defaultStorageAccountKey " `
    | Add—AzHDInsightConfigValues `
        —HiveSite $hiveConfigValues

New—AzHDInsightCluster `
    —ResourceGroupName $existingResourceGroupName `
    —Cluster-Name $clusterName `
    —Location $location `
    —ClusterSizeInNodes $clusterSizeInNodes `
    —ClusterType Hadoop `
    —OSType Linux `
    —Version "3.6" `
    —HttpCredential $httpCredential `
    —Config $config
```

Zie voor meer informatie het artikel [HDInsight-clusters aanpassen met Bootstrap](../hdinsight-hadoop-customize-cluster-bootstrap.md).  Zie ook, [HdInsight clusters beheren met behulp van de Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md).

## <a name="access-client-tools-from-hdinsight-hadoop-cluster-edge-nodes"></a>Toegang tot clienthulpprogramma's vanuit HDInsight Hadoop-clusterrandknooppunten

Een leeg randknooppunt is een Virtuele Linux-machine met dezelfde clienttools die zijn geïnstalleerd en geconfigureerd als op de hoofdknooppunten, maar zonder hadoopservices die worden uitgevoerd. Het randknooppunt kan voor de volgende doeleinden worden gebruikt:

- toegang tot het cluster
- clienttoepassingen testen
- hosting clienttoepassingen

Randknooppunten kunnen worden gemaakt en verwijderd via de Azure-portal en kunnen worden gebruikt tijdens of na het maken van het cluster. Nadat het randknooppunt is gemaakt, u verbinding maken met het randknooppunt met SSH en clienthulpprogramma's uitvoeren om toegang te krijgen tot het Hadoop-cluster in HDInsight. Het randknooppunt ssh eindpunt `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22`is .


Zie voor meer informatie het artikel [Lege randknooppunten gebruiken op Apache Hadoop-clusters in HDInsight](../hdinsight-apps-use-edge-node.md).


## <a name="use-scale-up-and-scale-down-feature-of-clusters"></a>Scale-up- en scale-downfunctie van clusters gebruiken

HDInsight biedt elasticiteit door u de mogelijkheid te bieden om het aantal werknemersknooppunten in uw clusters op te schalen en af te schalen. Met deze functie u een cluster na sluitingstijd of in het weekend verkleinen en uitbreiden tijdens piekzakelijke vereisten. Zie voor meer informatie:

* [HdInsight-clusters schalen](../hdinsight-scaling-best-practices.md).
* [Schaalclusters](../hdinsight-administer-use-portal-linux.md#scale-clusters).

## <a name="use-hdinsight-with-azure-virtual-network"></a>HDInsight gebruiken met Azure Virtual Network

Azure Virtual Networks stellen Azure-bronnen, zoals Azure Virtual Machines, in staat om veilig met elkaar, internet en on-premises netwerken te communiceren door netwerkverkeer te filteren en te routeren.

Met Azure Virtual Network met HDInsight worden de volgende scenario's mogelijk gemaakt:

- Rechtstreeks verbinding maken met HDInsight vanuit een on-premises netwerk.
- HDInsight verbinden met gegevensopslag in een Azure Virtual-netwerk.
- Rechtstreeks toegang tot Hadoop-services die niet openbaar beschikbaar zijn via internet. Bijvoorbeeld Kafka API's of de HBase Java API.

HDInsight kan worden toegevoegd aan een nieuw of bestaand Azure Virtual Network. Als HDInsight wordt toegevoegd aan een bestaand virtueel netwerk, moeten de bestaande netwerkbeveiligingsgroepen en door de gebruiker gedefinieerde routes worden bijgewerkt om onbeperkte toegang tot [meerdere IP-adressen](../hdinsight-management-ip-addresses.md) in het Azure-datacenter mogelijk te maken. Zorg er ook voor dat u het verkeer naar de [poorten](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ports)niet blokkeert, die worden gebruikt door HDInsight-services.

> [!Note]  
> HDInsight ondersteunt momenteel geen gedwongen tunneling. Gedwongen tunneling is een subnetinstelling die uitgaand internetverkeer naar een apparaat dwingt voor inspectie en registratie. Verwijder geforceerde tunneling voordat u HDInsight in een subnet installeert of maak een nieuw subnet voor HDInsight. HDInsight ondersteunt ook geen beperking van uitgaande netwerkconnectiviteit.

Raadpleeg voor meer informatie de volgende artikelen:

- [Overzicht van Azure-netwerken](../../virtual-network/virtual-networks-overview.md)
- [Azure HDInsight uitbreiden met een Azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md)

## <a name="securely-connect-to-azure-services-with-azure-virtual-network-service-endpoints"></a>Veilig verbinding maken met Azure-services met Azure Virtual Network-serviceeindpunten

HDInsight ondersteunt [eindpunten voor virtuele netwerkservices](../../virtual-network/virtual-network-service-endpoints-overview.md), waarmee u veilig verbinding maken met Azure Blob Storage, Azure Data Lake Storage Gen2, Cosmos DB en SQL-databases. Door een Service Endpoint in te schakelen voor Azure HDInsight, stroomt het verkeer via een beveiligde route vanuit het Azure-datacenter. Met dit verbeterde beveiligingsniveau op de netwerklaag u big data-opslagaccounts vergrendelen naar hun opgegeven Virtual Networks (VNETs) en hdinsight-clusters nog steeds naadloos gebruiken om die gegevens te openen en te verwerken.

Raadpleeg voor meer informatie de volgende artikelen:

- [Service-eindpunten voor virtueel netwerk](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [HdInsight-beveiliging verbeteren met serviceeindpunten](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>Verbind HDInsight met het on-premises netwerk

HDInsight kan worden verbonden met het on-premises netwerk met behulp van Azure Virtual Networks en een VPN-gateway. De volgende stappen kunnen worden gebruikt om connectiviteit vast te stellen:

- Gebruik HDInsight in een Azure Virtual Network dat verbinding heeft met het on-premises netwerk.
- Dns-naamomzetting configureren tussen het virtuele netwerk en het on-premises netwerk.
- Netwerkbeveiligingsgroepen of door de gebruiker gedefinieerde routes (UDR) configureren om het netwerkverkeer te beheren.

Zie voor meer informatie het artikel [Connect HDInsight op uw on-premises netwerk](../connect-on-premises-network.md)

## <a name="next-steps"></a>Volgende stappen

Lees het volgende artikel in deze serie: [Aanbevolen procedures voor opslag voor on-premises naar Azure HDInsight Hadoop-migratie](apache-hadoop-on-premises-migration-best-practices-storage.md).
