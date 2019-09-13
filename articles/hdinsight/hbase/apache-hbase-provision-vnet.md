---
title: HBase-clusters maken in een Virtual Network-Azure
description: Aan de slag met HBase in azure HDInsight. Meer informatie over het maken van HDInsight HBase-clusters op Azure Virtual Network.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: c8fc7c931f31e1ff58f41faa9a29f7e77e9655fd
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70917319"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Apache HBase-clusters maken op HDInsight in azure Virtual Network
Meer informatie over het maken van Azure HDInsight Apache HBase-clusters in een [Azure-Virtual Network][1].

Met de integratie van virtuele netwerken kunnen Apache HBase-clusters worden geïmplementeerd in hetzelfde virtuele netwerk als uw toepassingen, zodat toepassingen rechtstreeks kunnen communiceren met HBase. De voor delen zijn onder andere:

* Directe connectiviteit van de webtoepassing naar de knoop punten van het HBase-cluster, waarmee communicatie via HBase Java Remote Procedure Call (RPC) Api's mogelijk wordt.
* De prestaties zijn verbeterd doordat het verkeer niet via meerdere gateways en load balancers kan worden uitgevoerd.
* De mogelijkheid om gevoelige informatie op een veiligere manier te verwerken zonder een openbaar eind punt zichtbaar te maken.

### <a name="prerequisites"></a>Vereisten
Voordat u met dit artikel begint, moet u beschikken over de volgende items:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Een werkstation met Azure PowerShell**. Zie [Azure PowerShell installeren en gebruiken](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Een Apache HBase-cluster maken in een virtueel netwerk
In deze sectie maakt u een op Linux gebaseerd Apache HBase-cluster met het afhankelijke Azure Storage account in een virtueel Azure-netwerk met behulp van een [Azure Resource Manager sjabloon](../../azure-resource-manager/resource-group-template-deploy.md). Zie [HDInsight-clusters maken](../hdinsight-hadoop-provision-linux-clusters.md)voor andere methoden voor het maken van een cluster en de uitleg van de instellingen. Zie [Apache Hadoop clusters in Hdinsight maken met behulp van Azure Resource Manager-sjablonen](../hdinsight-hadoop-create-linux-clusters-arm-templates.md) voor meer informatie over het gebruik van een sjabloon voor het maken van Apache Hadoop clusters in hdinsight.

> [!NOTE]  
> Sommige eigenschappen worden vastgelegd in de sjabloon. Bijvoorbeeld:
>
> * **Locatie**: US - oost 2
> * **Cluster versie**: 3.6
> * **Aantal cluster worker-knoop punten**: 2
> * **Standaard opslag account**: een unieke teken reeks
> * **Naam van virtueel netwerk**: &lt;Cluster naam >-vnet
> * **Adres ruimte van virtueel netwerk**: 10.0.0.0/16
> * **Subnetnaam**: subnet1
> * **Subnetadresbereik**: 10.0.0.0/24
>
> &lt;De cluster naam > wordt vervangen door de naam van het cluster dat u opgeeft wanneer u de sjabloon gebruikt.


1. Klik op de volgende afbeelding om de sjabloon in Azure Portal te openen. De sjabloon bevindt zich in [Azure Quick](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/)start-sjablonen.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure"></a>
2. Voer op de Blade **aangepaste implementatie** de volgende eigenschappen in:

   * **Abonnement**: Selecteer een Azure-abonnement dat is gebruikt voor het maken van het HDInsight-cluster, het afhankelijke opslag account en het virtuele Azure-netwerk.
   * **Resourcegroep**: Selecteer **nieuwe maken**en geef een nieuwe naam op voor de resource groep.
   * **Locatie**: Selecteer een locatie voor de resourcegroep.
   * **Clustername**: Voer een naam in voor het Hadoop-cluster dat moet worden gemaakt.
   * **Aanmeldings naam en wacht woord**voor het cluster: De standaardaanmeldingsnaam is **admin**.
   * **SSH-gebruikers naam en-wacht woord**: De standaardgebruikersnaam is **sshuser**.  U kunt de naam wijzigen.
   * **Ik ga akkoord met de voor waarden en bepalingen die hierboven worden vermeld**: Uitgeschakeld
3. Klik op **Kopen**. Het duurt ongeveer 20 minuten om een cluster te maken. Zodra het cluster is gemaakt, kunt u klikken op de Blade cluster in de portal om dit te openen.

Nadat u het artikel hebt voltooid, wilt u het cluster misschien verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt. Zie [Apache Hadoop clusters in HDInsight beheren met behulp van de Azure Portal](../hdinsight-administer-use-portal-linux.md#delete-clusters)voor instructies over het verwijderen van een cluster.

Als u wilt werken met uw nieuwe HBase-cluster, kunt u de procedures in aan de [slag met Apache HBase gebruiken met Apache Hadoop in HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Verbinding maken met het Apache HBase-cluster met behulp van Apache HBase Java RPC-Api's
1. Maak een virtuele IaaS-machine (Infrastructure as a Service) in hetzelfde virtuele Azure-netwerk en hetzelfde subnet. Zie [een virtuele machine met Windows Server maken](../../virtual-machines/windows/quick-create-portal.md)voor instructies over het maken van een nieuwe virtuele IaaS-machine. Wanneer u de stappen in dit document volgt, moet u de volgende waarden voor de netwerk configuratie gebruiken:

   * **Virtueel netwerk**: &lt;Cluster naam >-vnet
   * **Subnet**: subnet1

   > [!IMPORTANT]  
   > Vervang &lt;de cluster naam > door de naam die u hebt gebruikt bij het maken van het HDInsight-cluster in de vorige stappen.

   Als u deze waarden gebruikt, wordt de virtuele machine in hetzelfde virtuele netwerk en subnet geplaatst als het HDInsight-cluster. Met deze configuratie kunnen ze direct met elkaar communiceren. Er is een manier om een HDInsight-cluster te maken met een leeg Edge-knoop punt. Het Edge-knoop punt kan worden gebruikt om het cluster te beheren.  Zie [lege Edge-knoop punten in HDInsight gebruiken](../hdinsight-apps-use-edge-node.md)voor meer informatie.

2. Wanneer u een Java-toepassing gebruikt om extern verbinding te maken met HBase, moet u de Fully Qualified Domain Name (FQDN) gebruiken. Om dit te bepalen, moet u het verbindingsspecifiek DNS-achtervoegsel van het HBase-cluster ophalen. Hiervoor kunt u een van de volgende methoden gebruiken:

   * Een webbrowser gebruiken om een [Apache Ambari](https://ambari.apache.org/) -aanroep te maken:

     Blader naar https://&lt;clustername >. azurehdinsight. net/API/v1/clusters/&lt;clustername >/hosts? minimal_response = True. Hiermee wordt een JSON-bestand met de DNS-achtervoegsels.
   * De Ambari-website gebruiken:

     1. Blader naar https://&lt;clustername >. azurehdinsight. net.
     2. Klik op **hosts** in het bovenste menu.
   * Krul gebruiken om REST-aanroepen te maken:

     ```bash
        curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest
     ```

     Zoek in de JavaScript Object Notation (JSON)-gegevens die worden geretourneerd, de vermelding "host_name". De FQDN-naam voor de knoop punten in het cluster bevat. Bijvoorbeeld:

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     Het deel van de domein naam die begint met de cluster naam is het DNS-achtervoegsel. Bijvoorbeeld mycluster.b1.cloudapp.net.
   * Azure PowerShell gebruiken

     Gebruik het volgende Azure PowerShell script om de functie **Get-ClusterDetail** te registreren, die kan worden gebruikt om het DNS-achtervoegsel te retour neren:

     ```powershell
        function Get-ClusterDetail(
            [String]
            [Parameter( Position=0, Mandatory=$true )]
            $ClusterDnsName,
            [String]
            [Parameter( Position=1, Mandatory=$true )]
            $Username,
            [String]
            [Parameter( Position=2, Mandatory=$true )]
            $Password,
            [String]
            [Parameter( Position=3, Mandatory=$true )]
            $PropertyName
            )
        {
        <#
            .SYNOPSIS
            Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
            .Description
            This command shows the following 4 properties of an HDInsight cluster:
            1. ZookeeperQuorum (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.quorum".
            2. ZookeeperClientPort (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.property.clientPort".
            3. HBaseRestServers (supports only HBase type cluster)
                Shows a list of host FQDNs that run the HBase REST server.
            4. FQDNSuffix (supports all cluster types)
                Shows the FQDN suffix of hosts in the cluster.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
            This command shows the value of HBase property "hbase.zookeeper.quorum".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
            This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
            This command shows a list of host FQDNs that run the HBase REST server.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
            This command shows the FQDN suffix of hosts in the cluster.
        #>

            $DnsSuffix = ".azurehdinsight.net"

            $ClusterFQDN = $ClusterDnsName + $DnsSuffix
            $webclient = new-object System.Net.WebClient
            $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

            if($PropertyName -eq "ZookeeperQuorum")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
            }
            if($PropertyName -eq "ZookeeperClientPort")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
            }
            if($PropertyName -eq "HBaseRestServers")
            {
                $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                $Response1 = $webclient.DownloadString($Url1)
                $JsonObject1 = $Response1 | ConvertFrom-Json
                $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                $Response2 = $webclient.DownloadString($Url2)
                $JsonObject2 = $Response2 | ConvertFrom-Json
                foreach ($host_component in $JsonObject2.host_components)
                {
                    $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                    Write-host $ConnectionString
                }
            }
            if($PropertyName -eq "FQDNSuffix")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                $pos = $FQDN.IndexOf(".")
                $Suffix = $FQDN.Substring($pos + 1)
                Write-host $Suffix
            }
        }
     ```

     Nadat u het Azure PowerShell script hebt uitgevoerd, gebruikt u de volgende opdracht om het DNS-achtervoegsel te retour neren met behulp van de **Get-ClusterDetail-** functie. Geef de naam van uw HDInsight HBase-cluster, de beheerders naam en het beheerders wachtwoord op wanneer u deze opdracht gebruikt.

     ```powershell
        Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>
     ```

     Met deze opdracht wordt het DNS-achtervoegsel geretourneerd. Bijvoorbeeld **yourclustername.B4.internal.cloudapp.net**.


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

Als u wilt controleren of de virtuele machine kan communiceren met het HBase-cluster, `ping headnode0.<dns suffix>` gebruikt u de opdracht van de virtuele machine. Bijvoorbeeld: ping headnode0.mycluster.b1.cloudapp.net.

Als u deze informatie wilt gebruiken in een Java-toepassing, kunt u de stappen in [Apache Maven gebruiken om Java-toepassingen te bouwen die Apache HBase gebruiken met HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) om een toepassing te maken. Als u wilt dat de toepassing verbinding maakt met een externe HBase-server, wijzigt u in dit voor beeld het bestand **hbase-site. XML** om de FQDN voor Zookeeper te gebruiken. Bijvoorbeeld:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]  
> Zie [name resolution (DNS) (Engelstalig)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)voor meer informatie over naam omzetting in azure Virtual Networks, inclusief het gebruik van uw eigen DNS-server.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u een Apache HBase-cluster maakt. Voor meer informatie zie:

* [Aan de slag met HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Lege Edge-knoop punten in HDInsight gebruiken](../hdinsight-apps-use-edge-node.md)
* [Apache HBase-replicatie in HDInsight configureren](apache-hbase-replication.md)
* [Apache Hadoop clusters maken in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Aan de slag met Apache HBase met Apache Hadoop in HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Overzicht van Virtual Network](../../virtual-network/virtual-networks-overview.md)

[1]: https://azure.microsoft.com/services/virtual-network/
[2]: https://technet.microsoft.com/library/ee176961.aspx
[3]: https://technet.microsoft.com/library/hh847889.aspx

