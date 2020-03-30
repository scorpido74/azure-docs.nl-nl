---
title: HBase-clusters maken in een virtueel netwerk - Azure
description: Aan de slag met HBase in Azure HDInsight. Meer informatie over het maken van HDInsight HBase-clusters op Azure Virtual Network.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: e4e15d1c6554fc567f668b2033bff5b5664db918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972801"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Apache HBase-clusters maken op HDInsight in Azure Virtual Network

Meer informatie over het maken van Azure HDInsight Apache HBase-clusters in een [Azure Virtual Network.](https://azure.microsoft.com/services/virtual-network/)

Met virtuele netwerkintegratie kunnen Apache HBase-clusters worden geïmplementeerd in hetzelfde virtuele netwerk als uw toepassingen, zodat toepassingen rechtstreeks met HBase kunnen communiceren. De voordelen zijn:

* Directe connectiviteit van de webapplicatie met de knooppunten van het HBase-cluster, waarmee communicatie via HBase Java remote procedure call (RPC) API's mogelijk is.
* Verbeterde prestaties door uw verkeer niet over meerdere gateways en load-balancers te laten gaan.
* De mogelijkheid om gevoelige informatie op een veiligere manier te verwerken zonder een openbaar eindpunt bloot te leggen.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Apache HBase-cluster maken in virtueel netwerk

In deze sectie maakt u een Apache HBase-cluster op basis van Linux met het afhankelijke Azure Storage-account in een Azure-virtueel netwerk met behulp van een [Azure Resource Manager-sjabloon.](../../azure-resource-manager/templates/deploy-powershell.md) Zie Clusters voor het maken van [HDInsight voor](../hdinsight-hadoop-provision-linux-clusters.md)andere methoden voor het maken van clusters voor het maken van clusters voor het maken van clusters voor het maken van clusters voor het maken van clusters voor het maken van clusters voor het maken van clusters voor het maken van clusters voor het Zie [Apache Hadoop-clusters maken in HDInsight](../hdinsight-hadoop-create-linux-clusters-arm-templates.md) voor meer informatie over het gebruik van een sjabloon voor het maken van Apache Hadoop-clusters in HDInsight met Azure Resource Manager-sjablonen.

> [!NOTE]  
> Sommige eigenschappen zijn hard gecodeerd in de sjabloon. Bijvoorbeeld:
>
> * **Locatie**: Oost-VS 2
> * **Cluster-versie**: 3.6
> * **Aantal clusterwerknemersknooppunt:** 2
> * **Standaardopslagaccount:** een unieke tekenreeks
> * **Virtuele netwerknaam**: CLUSTERNAME-vnet
> * **Virtuele netwerkadresruimte**: 10.0.0.0/16
> * **Subnetnaam**: subnet1
> * **Subnetadresbereik**: 10.0.0.0/24
>
> `CLUSTERNAME`wordt vervangen door de clusternaam die u opgeeft bij het gebruik van de sjabloon.

1. Selecteer de volgende afbeelding om de sjabloon in de Azure-portal te openen. De sjabloon bevindt zich in [Azure quickstart-sjablonen.](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/)

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Selecteer in het dialoogvenster **Aangepaste implementatie** de optie **Sjabloon bewerken**.

1. Op regel 165, `Standard_A3` `Standard_A4_V2`waarde wijzigen in . Selecteer vervolgens **Opslaan**.

1. Vul de resterende sjabloon in met de volgende informatie:

    |Eigenschap |Waarde |
    |---|---|
    |Abonnement|Selecteer een Azure-abonnement dat wordt gebruikt om het HDInsight-cluster, het afhankelijke opslagaccount en het virtuele Azure-netwerk te maken.|
    Resourcegroep|Selecteer **Nieuw maken**en geef een nieuwe naam van de brongroep op.|
    |Locatie|Selecteer een locatie voor de resourcegroep.|
    |Clusternaam|Voer een naam in voor het te maken hadoopcluster.|
    |Gebruikersnaam en wachtwoord voor clusteraanmelding|De standaardgebruikersnaam is **beheerder**. Geef een wachtwoord op.|
    |Ssh gebruikersnaam en wachtwoord|De standaardgebruikersnaam is **sshuser**.  Geef een wachtwoord op.|

    Selecteer **Ik ga akkoord met de voorwaarden en de bovenstaande voorwaarden.**

1. Selecteer **Aankoop**. Het duurt ongeveer 20 minuten om een cluster te maken. Zodra het cluster is gemaakt, u het cluster in de portal selecteren om het te openen.

Nadat u het artikel hebt voltooid, u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt. Zie [Apache Hadoop-clusters beheren in HDInsight](../hdinsight-administer-use-portal-linux.md#delete-clusters)beheren met behulp van de Azure-portal voor de instructies voor het verwijderen van een cluster.

Om met uw nieuwe HBase-cluster te gaan werken, u de procedures gebruiken die worden gevonden in [Aan de slag met Apache HBase met Apache Hadoop in HDInsight.](./apache-hbase-tutorial-get-started-linux.md)

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Verbinding maken met het Apache HBase-cluster met Apache HBase Java RPC API's

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Maak een virtuele infrastructuur als een servicemachine (IaaS) in hetzelfde Azure-virtuele netwerk en hetzelfde subnet. Zie Een virtuele machine maken met [Windows Server](../../virtual-machines/windows/quick-create-portal.md)voor instructies voor het maken van een nieuwe virtuele iaas-machine. Wanneer u de stappen in dit document volgt, moet u de volgende waarden gebruiken voor de netwerkconfiguratie:

* **Virtueel netwerk**: CLUSTERNAME-vnet
* **Subnet**: subnet1

> [!IMPORTANT]  
> Vervang `CLUSTERNAME` de naam die u hebt gebruikt bij het maken van het HDInsight-cluster in eerdere stappen.

Met behulp van deze waarden wordt de virtuele machine in hetzelfde virtuele netwerk en subnet geplaatst als het HDInsight-cluster. Deze configuratie stelt hen in staat om direct met elkaar te communiceren. Er is een manier om een HDInsight-cluster te maken met een leeg randknooppunt. Het randknooppunt kan worden gebruikt om het cluster te beheren.  Zie [Lege randknooppunten gebruiken in HDInsight](../hdinsight-apps-use-edge-node.md)voor meer informatie.

### <a name="obtain-fully-qualified-domain-name"></a>Volledig gekwalificeerde domeinnaam verkrijgen

Wanneer u een Java-toepassing gebruikt om op afstand verbinding te maken met HBase, moet u de volledig gekwalificeerde domeinnaam (FQDN) gebruiken. Om dit te bepalen, moet u het verbindingsspecifieke DNS-achtervoegsel van het HBase-cluster krijgen. Om dat te doen, u een van de volgende methoden gebruiken:

* Gebruik een webbrowser om een [Apache Ambari-gesprek](https://ambari.apache.org/) te voeren:

    Blader naar `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts?minimal_response=true`. Het retourneert een JSON-bestand met de DNS-achtervoegsels.

* Gebruik de Ambari-website:

    1. Blader naar `https://CLUSTERNAME.azurehdinsight.net`.
    2. Selecteer **Hosts** in het bovenste menu.

* Gebruik Curl om REST-gesprekken te voeren:

    ```bash
    curl -u <username>:<password> -k https://CLUSTERNAME.azurehdinsight.net/ambari/api/v1/clusters/CLUSTERNAME.azurehdinsight.net/services/hbase/components/hbrest
    ```

Zoek in de JSON-gegevens (JavaScript Object Notation) de invoer 'host_name'. Het bevat de FQDN voor de knooppunten in het cluster. Bijvoorbeeld:

```
"host_name" : "hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net"
```

Het gedeelte van de domeinnaam dat begint met de clusternaam is het DNS-achtervoegsel. Bijvoorbeeld `hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/hdi-primary-dns-suffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

### <a name="verify-communication-inside-virtual-network"></a>Communicatie binnen virtueel netwerk verifiëren

Als u wilt controleren of de virtuele machine met `ping headnode0.<dns suffix>` het HBase-cluster kan communiceren, gebruikt u de opdracht van de virtuele machine. Bijvoorbeeld `ping hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

Als u deze informatie wilt gebruiken in een Java-toepassing, u de stappen volgen in [Apache Maven gebruiken om Java-toepassingen te bouwen die Apache HBase met HDInsight (Hadoop) gebruiken](./apache-hbase-build-java-maven-linux.md) om een toepassing te maken. Als u wilt dat de toepassing verbinding maakt met een externe HBase-server, wijzigt u het bestand **hbase-site.xml** in dit voorbeeld om de FQDN voor Zookeeper te gebruiken. Bijvoorbeeld:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]  
> Zie [Naamomzetting (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)voor meer informatie over naamomzetting in virtuele Azure-netwerken, inclusief het gebruik van uw eigen DNS-server.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een Apache HBase-cluster maakt. Voor meer informatie zie:

* [Aan de slag met HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Lege randknooppunten gebruiken in HDInsight](../hdinsight-apps-use-edge-node.md)
* [Apache HBase-replicatie configureren in HDInsight](apache-hbase-replication.md)
* [Apache Hadoop clusters maken in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Aan de slag met Apache HBase met Apache Hadoop in HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Overzicht van Virtual Network](../../virtual-network/virtual-networks-overview.md)
