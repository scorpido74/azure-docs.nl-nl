---
title: Verbinding maken met Kafka via virtuele netwerken - Azure HDInsight
description: Meer informatie over hoe u rechtstreeks verbinding maken met Kafka op HDInsight via een Azure Virtual Network. Leer hoe u verbinding maken met Kafka via ontwikkelingsclients via een VPN-gateway of van clients in uw on-premises netwerk met behulp van een VPN-gateway-apparaat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/04/2020
ms.openlocfilehash: 36ff0d5f1fc96b2013555d37a869ebf629a22be7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272121"
---
# <a name="connect-to-apache-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Verbinding maken met Apache Kafka in HDInsight via een virtueel Azure-netwerk

Meer informatie over hoe u rechtstreeks verbinding maken met Apache Kafka op HDInsight via een Azure Virtual Network. Dit document bevat informatie over het maken van verbinding met Kafka met behulp van de volgende configuraties:

* Uit bronnen in een on-premises netwerk. Deze verbinding wordt tot stand gebracht door gebruik te maken van een VPN-apparaat (software of hardware) op uw lokale netwerk.
* Vanuit een ontwikkelomgeving met behulp van een VPN-softwareclient.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="architecture-and-planning"></a>Architectuur en planning

HDInsight staat geen directe verbinding met Kafka via het openbare internet toe. In plaats daarvan moeten klanten van Kafka (producenten en consumenten) een van de volgende verbindingsmethoden gebruiken:

* Voer de client uit in hetzelfde virtuele netwerk als Kafka op HDInsight. Deze configuratie wordt gebruikt in [het document Start with Apache Kafka op HDInsight.](apache-kafka-get-started.md) De client draait rechtstreeks op de HDInsight-clusterknooppunten of op een andere virtuele machine in hetzelfde netwerk.

* Verbind een privénetwerk, zoals uw on-premises netwerk, met het virtuele netwerk. Met deze configuratie kunnen clients in uw on-premises netwerk rechtstreeks met Kafka werken. Voer de volgende taken uit om deze configuratie in te schakelen:

  1. Maak een virtueel netwerk.
  2. Maak een VPN-gateway die gebruikmaakt van een site-to-site-configuratie. De configuratie die in dit document wordt gebruikt, maakt verbinding met een VPN-gatewayapparaat in uw on-premises netwerk.
  3. Maak een DNS-server in het virtuele netwerk.
  4. Doorsturen configureren tussen de DNS-server in elk netwerk.
  5. Maak een Kafka op HDInsight-cluster in het virtuele netwerk.

     Zie verbinding maken met Apache Kafka voor meer informatie [vanuit een on-premises netwerksectie.](#on-premises)

* Verbind individuele machines met het virtuele netwerk via een VPN-gateway en VPN-client. Voer de volgende taken uit om deze configuratie in te schakelen:

  1. Maak een virtueel netwerk.
  2. Maak een VPN-gateway die een point-to-site-configuratie gebruikt. Deze configuratie kan worden gebruikt met zowel Windows- als MacOS-clients.
  3. Maak een Kafka op HDInsight-cluster in het virtuele netwerk.
  4. Configureer Kafka voor IP-advertenties. Met deze configuratie kan de client verbinding maken met IP-adressen van broker in plaats van domeinnamen.
  5. Download en gebruik de VPN-client op het ontwikkelsysteem.

     Zie voor meer informatie de [sectie Verbinding met Apache Kafka met een VPN-clientsectie.](#vpnclient)

     > [!WARNING]  
     > Deze configuratie wordt alleen aanbevolen voor ontwikkelingsdoeleinden vanwege de volgende beperkingen:
     >
     > * Elke client moet verbinding maken via een VPN-softwareclient.
     > * De VPN-client geeft geen aanvragen voor naamomzetting door aan het virtuele netwerk, dus u moet IP-adressering gebruiken om met Kafka te communiceren. IP-communicatie vereist extra configuratie op het Kafka-cluster.

Zie [Een virtueel netwerk plannen voor Azure HDInsight-clusters voor](../hdinsight-plan-virtual-network-deployment.md)meer informatie over het gebruik van HDInsight in een virtueel netwerk.

## <a name="connect-to-apache-kafka-from-an-on-premises-network"></a><a id="on-premises"></a>Verbinding maken met Apache Kafka vanuit een on-premises netwerk

Als u een Kafka-cluster wilt maken dat communiceert met uw on-premises netwerk, voert u de stappen in het [Connect HDInsight naar uw on-premises netwerkdocument.](./../connect-on-premises-network.md)

> [!IMPORTANT]  
> Selecteer bij het maken van het HDInsight-cluster het clustertype __Kafka.__

Met deze stappen wordt de volgende configuratie gemaakt:

* Azure Virtual Network
* Site-to-site VPN-gateway
* Azure Storage-account (gebruikt door HDInsight)
* Kafka op HDInsight

Als u wilt controleren of een Kafka-client vanuit on-premises verbinding kan maken met het cluster, gebruikt u de stappen in de sectie [Voorbeeld: Python-client.](#python-client)

## <a name="connect-to-apache-kafka-with-a-vpn-client"></a><a id="vpnclient"></a>Verbinding maken met Apache Kafka met een VPN-client

Gebruik de stappen in deze sectie om de volgende configuratie te maken:

* Azure Virtual Network
* Point-to-site VPN-gateway
* Azure-opslagaccount (gebruikt door HDInsight)
* Kafka op HDInsight

1. Volg de stappen in het document [Werken met zelfondertekende certificaten voor point-to-site verbindingen.](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md) Met dit document worden de certificaten gemaakt die nodig zijn voor de gateway.

2. Open een PowerShell-prompt en gebruik de volgende code om u aan te melden bij uw Azure-abonnement:

    ```powershell
    Connect-AzAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzSubscription -SubscriptionName "name of your subscription"
    ```

3. Gebruik de volgende code om variabelen te maken die configuratie-informatie bevatten:

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.6"
    $hdiType = "Kafka"
    ```

4. Gebruik de volgende code om de Azure-brongroep en het virtuele netwerk te maken:

    ```powershell
    # Create the resource group that contains everything
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]  
    > Het kan enkele minuten duren voordat dit proces is voltooid.

5. Gebruik de volgende code om het Azure Storage-account en de blobcontainer te maken:

    ```powershell
    # Create the storage account
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -SkuName Standard_GRS `
        -Location $location `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly 1

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. Gebruik de volgende code om het HDInsight-cluster te maken:

    ```powershell
    # Create the HDInsight cluster
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -DisksPerWorkerNode 2 `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

   > [!WARNING]  
   > Dit proces duurt ongeveer 15 minuten.

### <a name="configure-kafka-for-ip-advertising"></a>Kafka configureren voor IP-reclame

Apache Zookeeper geeft standaard de domeinnaam van de Kafka-makelaars terug aan klanten. Deze configuratie werkt niet met de VPN-softwareclient, omdat deze geen naamomzetting kan gebruiken voor entiteiten in het virtuele netwerk. Gebruik voor deze configuratie de volgende stappen om Kafka te configureren om IP-adressen te adverteren in plaats van domeinnamen:

1. Ga in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net`. Vervang `CLUSTERNAME` de naam van het cluster Kafka op HDInsight.

    Gebruik de https-gebruikersnaam en -wachtwoord voor het cluster wanneer u daarom wordt gevraagd. De Ambari Web UI voor het cluster wordt weergegeven.

2. Als u informatie over Kafka wilt weergeven, selecteert u __Kafka__ in de lijst aan de linkerkant.

    ![Servicelijst met Kafka gemarkeerd](./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Als u de Kafka-configuratie wilt bekijken, selecteert u __Configs__ in het bovenste midden.

    ![Configuratie apache Ambari-services](./media/apache-kafka-connect-vpn-gateway/select-kafka-config1.png)

4. Als u de __kafka-env-configuratie__ wilt vinden, voert u `kafka-env` het veld __Filter__ rechtsboven in.

    ![Kafka-configuratie, voor kafka-env](./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Als u Kafka wilt configureren om IP-adressen te adverteren, voegt u de volgende tekst toe aan de onderkant van het veld __kafka-env-sjabloon:__

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Als u de interface wilt configureren `listeners` waarop Kafka luistert, voert u het veld __Filter__ rechtsboven in.

7. Als u Kafka wilt configureren om te luisteren __listeners__ op `PLAINTEXT://0.0.0.0:9092`alle netwerkinterfaces, wijzigt u de waarde in het veld listeners in .

8. Als u de configuratiewijzigingen wilt opslaan, gebruikt u de knop __Opslaan.__ Voer een sms-bericht in waarin de wijzigingen worden beschreven. Selecteer __OK__ zodra de wijzigingen zijn opgeslagen.

    ![Apache Ambari opslaan configuratie](./media/apache-kafka-connect-vpn-gateway/save-configuration-button.png)

9. Als u fouten wilt voorkomen bij het opnieuw starten van Kafka, gebruikt u de knop __Serviceacties__ en selecteert u __Onderhoudsmodus inschakelen__. Selecteer OK om deze bewerking te voltooien.

    ![Serviceacties, waarbij het onderhoud is gemarkeerd](./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Als u Kafka opnieuw wilt starten, gebruikt u de knop __Opnieuw starten__ en selecteert u Alle getroffen __opnieuw starten__. Bevestig het opnieuw opstarten en gebruik de knop __OK__ nadat de bewerking is voltooid.

    ![Knop Opnieuw starten met alle getroffen gemarkeerde gemarkeerde gemarkeerde opnieuw starten](./media/apache-kafka-connect-vpn-gateway/restart-required-button.png)

11. Als u de onderhoudsmodus wilt uitschakelen, gebruikt u de knop __Serviceacties__ en selecteert u __Onderhoudsmodus uitschakelen__. Selecteer **OK** om deze bewerking te voltooien.

### <a name="connect-to-the-vpn-gateway"></a>Verbinding maken met de VPN-gateway

Als u verbinding wilt maken met de VPN-gateway, gebruikt u de sectie __Verbinding maken met Azure__ van het [verbindingsdocument Point-to-Site configureren.](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect)

## <a name="example-python-client"></a><a id="python-client"></a>Voorbeeld: Python-client

Als u de connectiviteit met Kafka wilt valideren, gebruikt u de volgende stappen om een Python-producent en consument te maken en uit te voeren:

1. Gebruik een van de volgende methoden om de volledig gekwalificeerde domeinnaam (FQDN) en IP-adressen van de knooppunten in het Kafka-cluster op te halen:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    In dit script `$resourceGroupName` wordt ervan uitgegaan dat dit de naam is van de Azure-brongroep die het virtuele netwerk bevat.

    Sla de geretourneerde informatie op voor gebruik in de volgende stappen.

2. Gebruik het volgende om de [kafka-python-client](https://kafka-python.readthedocs.io/) te installeren:

    ```bash
    pip install kafka-python
    ```

3. Als u gegevens naar Kafka wilt verzenden, gebruikt u de volgende Python-code:

   ```python
   from kafka import KafkaProducer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # NOTE: you don't need the full list of worker nodes, just one or two.
   producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
   for _ in range(50):
      producer.send('testtopic', b'test message')
   ```

    Vervang `'kafka_broker'` de vermeldingen door de adressen die in deze sectie zijn geretourneerd uit stap 1:

   * Als u een __VPN-client voor software__gebruikt, vervangt u de `kafka_broker` vermeldingen door het IP-adres van uw werknemersknooppunten.

   * Als u naamomzetting hebt ingeschakeld via een `kafka_broker` aangepaste __DNS-server,__ vervangt u de vermeldingen door de FQDN van de werknemersknooppunten.

     > [!NOTE]
     > Deze code stuurt `test message` de `testtopic`tekenreeks naar het onderwerp . De standaardconfiguratie van Kafka op HDInsight is om het onderwerp te maken als het niet bestaat.

4. Als u de berichten uit Kafka wilt ophalen, gebruikt u de volgende Python-code:

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Again, you only need one or two, not the full list.
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    Vervang `'kafka_broker'` de vermeldingen door de adressen die in deze sectie zijn geretourneerd uit stap 1:

    * Als u een __VPN-client voor software__gebruikt, vervangt u de `kafka_broker` vermeldingen door het IP-adres van uw werknemersknooppunten.

    * Als u naamomzetting hebt ingeschakeld via een `kafka_broker` aangepaste __DNS-server,__ vervangt u de vermeldingen door de FQDN van de werknemersknooppunten.

## <a name="next-steps"></a>Volgende stappen

Zie Het document [Plan een virtueel netwerkimplementatie voor Azure HDInsight-clusters voor](../hdinsight-plan-virtual-network-deployment.md) meer informatie over het gebruik van HDInsight met een virtueel netwerk.

Zie de volgende documenten voor meer informatie over het maken van een Azure Virtual Network met Point-to-Site VPN-gateway:

* [Een Point-to-Site-verbinding configureren met de Azure-portal](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Een Point-to-Site-verbinding configureren met Azure PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Zie de volgende documenten voor meer informatie over het werken met Apache Kafka op HDInsight:

* [Aan de slag met Apache Kafka op HDInsight](apache-kafka-get-started.md)
* [Gebruik mirroring met Apache Kafka op HDInsight](apache-kafka-mirroring.md)
