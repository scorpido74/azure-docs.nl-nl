---
title: Virtuele netwerken maken voor Azure HDInsight-clusters
description: Meer informatie over het maken van een Azure Virtual Network om HDInsight te verbinden met andere cloudbronnen of resources in uw datacenter.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/16/2020
ms.openlocfilehash: 0c7791d43ffbbc13ab151362c5c3026ebbdb0d34
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531013"
---
# <a name="create-virtual-networks-for-azure-hdinsight-clusters"></a>Virtuele netwerken maken voor Azure HDInsight-clusters

In dit artikel vindt u voorbeelden en codevoorbeelden voor het maken en configureren van [Azure Virtual Networks.](../virtual-network/virtual-networks-overview.md) Te gebruiken met Azure HDInsight-clusters. Gedetailleerde voorbeelden van het maken van netwerkbeveiligingsgroepen (NSG's) en het configureren van DNS worden gepresenteerd.

Zie Een virtueel netwerk voor Azure HDInsight plannen voor achtergrondinformatie over het gebruik van virtuele netwerken met Azure [HDInsight.](hdinsight-plan-virtual-network-deployment.md)

## <a name="prerequisites-for-code-samples-and-examples"></a>Voorwaarden voor codevoorbeelden en voorbeelden

Voordat u een van de codevoorbeelden in dit artikel uitvoert, hebt u inzicht in TCP/IP-netwerken. Als u niet bekend bent met TCP/IP-netwerken, raadpleegt u iemand voordat u wijzigingen aanbrengt in productienetwerken.

Andere voorwaarden voor de monsters in dit artikel zijn de volgende punten:

* Als u PowerShell gebruikt, moet u de [AZ-module](https://docs.microsoft.com/powershell/azure/overview)installeren.
* Zie [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)installeren als u Azure CLI wilt gebruiken en deze nog niet hebt geïnstalleerd.

> [!IMPORTANT]  
> Als u stap voor stap advies zoekt over het aansluiten van HDInsight op uw on-premises netwerk met behulp van een Azure Virtual Network, raadpleegt u het [Connect HDInsight met uw on-premises netwerkdocument.](connect-on-premises-network.md)

## <a name="example-network-security-groups-with-hdinsight"></a><a id="hdinsight-nsg"></a>Voorbeeld: netwerkbeveiligingsgroepen met HDInsight

De voorbeelden in deze sectie laten zien hoe u regels voor netwerkbeveiligingkunt maken. Met de regels kan HDInsight communiceren met de Azure-beheerservices. Voordat u de voorbeelden gebruikt, past u de IP-adressen aan die overeenkomen met de adressen voor de Azure-regio die u gebruikt. U deze informatie vinden in [HDInsight management IP-adressen.](hdinsight-management-ip-addresses.md)

### <a name="azure-resource-management-template"></a>Azure Resource Management-sjabloon

Met de volgende sjabloon Resourcebeheer wordt een virtueel netwerk gemaakt dat binnenkomend verkeer beperkt, maar verkeer toestaat vanaf de IP-adressen die door HDInsight worden vereist. Deze sjabloon maakt ook een HDInsight-cluster in het virtuele netwerk.

* [Een beveiligd Azure Virtual Network en een HDInsight Hadoop-cluster implementeren](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

### <a name="azure-powershell"></a>Azure PowerShell

Gebruik het volgende PowerShell-script om een virtueel netwerk te maken dat binnenkomend verkeer beperkt en verkeer toestaat vanaf de IP-adressen voor de regio Noord-Europa.

> [!IMPORTANT]  
> Wijzig de IP-adressen voor `hdirule1` en `hdirule2` in dit voorbeeld naar de Azure-regio die u gebruikt. U deze informatie [HDInsight management IP-adressen](hdinsight-management-ip-addresses.md)vinden.

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"

# Get the Virtual Network object
$vnet = Get-AzVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName

# Get the region the Virtual network is in.
$location = $vnet.Location

# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName

# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `

# Set the changes to the security group
Set-AzNetworkSecurityGroup -NetworkSecurityGroup $nsg

# Apply the NSG to the subnet
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

In dit voorbeeld wordt uitgelegd hoe u regels toevoegen om binnenkomend verkeer op de vereiste IP-adressen toe te staan. Het bevat geen regel om binnenkomende toegang van andere bronnen te beperken. De volgende code laat zien hoe u SSH-toegang vanaf het internet inschakelt:

```powershell
Get-AzNetworkSecurityGroup -Name hdisecure -ResourceGroupName RESOURCEGROUP |
Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
```

### <a name="azure-cli"></a>Azure CLI

Gebruik de volgende stappen om een virtueel netwerk te maken dat binnenkomend verkeer beperkt, maar verkeer toestaat vanaf de IP-adressen die door HDInsight worden vereist.

1. Gebruik de volgende opdracht om een `hdisecure`nieuwe netwerkbeveiligingsgroep met de naam te maken. Vervang `RESOURCEGROUP` de brongroep die het Azure Virtual Network bevat. Vervang `LOCATION` de locatie (regio) waarin de groep is gemaakt.

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    Zodra de groep is gemaakt, ontvangt u informatie over de nieuwe groep.

2. Gebruik het volgende om regels toe te voegen aan de nieuwe netwerkbeveiligingsgroep die binnenkomende communicatie op poort 443 van de azure HDInsight-status- en beheerservice toestaat. Vervang `RESOURCEGROUP` de naam van de brongroep die het Azure Virtual Network bevat.

    > [!IMPORTANT]  
    > Wijzig de IP-adressen voor `hdirule1` en `hdirule2` in dit voorbeeld naar de Azure-regio die u gebruikt. U deze informatie vinden in [HDInsight management IP-adressen.](hdinsight-management-ip-addresses.md)

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. Als u de unieke id voor deze netwerkbeveiligingsgroep wilt ophalen, gebruikt u de volgende opdracht:

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query "id"
    ```

    Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is met de volgende tekst:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

4. Gebruik de volgende opdracht om de netwerkbeveiligingsgroep toe te passen op een subnet. Vervang `GUID` de `RESOURCEGROUP` waarden en waarden door de waarden die zijn geretourneerd uit de vorige stap. Vervang `VNETNAME` `SUBNETNAME` en door de virtuele netwerknaam en subnetnaam die u wilt maken.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Zodra deze opdracht is voltooid, u HDInsight installeren in het virtuele netwerk.

Deze stappen openen alleen toegang tot de HDInsight-status- en beheerservice in de Azure-cloud. Elke andere toegang tot het HDInsight-cluster van buiten het virtuele netwerk wordt geblokkeerd. Als u toegang van buiten het virtuele netwerk wilt inschakelen, moet u aanvullende regels voor netwerkbeveiligingsgroepen toevoegen.

De volgende code laat zien hoe u SSH-toegang vanaf het internet inschakelt:

```azurecli
az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n ssh --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
```

## <a name="example-dns-configuration"></a><a id="example-dns"></a>Voorbeeld: DNS-configuratie

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Naamomzetting tussen een virtueel netwerk en een verbonden on-premises netwerk

In dit voorbeeld worden de volgende aannames gegeven:

* U hebt een Azure Virtual Network dat is verbonden met een on-premises netwerk met behulp van een VPN-gateway.

* De aangepaste DNS-server in het virtuele netwerk draait Linux of Unix als besturingssysteem.

* [Bind](https://www.isc.org/downloads/bind/) is geïnstalleerd op de aangepaste DNS-server.

Op de aangepaste DNS-server in het virtuele netwerk:

1. Gebruik Azure PowerShell of Azure CLI om het DNS-achtervoegsel van het virtuele netwerk te vinden:

    Vervang `RESOURCEGROUP` de naam van de resourcegroep die het virtuele netwerk bevat en voer de opdracht in:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

1. Gebruik op de aangepaste DNS-server voor het virtuele netwerk `/etc/bind/named.conf.local` de volgende tekst als inhoud van het bestand:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Vervang `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` de waarde door het DNS-achtervoegsel van uw virtuele netwerk.

    Met deze configuratie worden alle DNS-aanvragen voor het DNS-achtervoegsel van het virtuele netwerk doorgestoofd naar de recursieve resolver van Azure.

1. Gebruik op de aangepaste DNS-server voor het virtuele netwerk `/etc/bind/named.conf.options` de volgende tekst als inhoud van het bestand:

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Vervang `10.0.0.0/16` de waarde door het IP-adresbereik van uw virtuele netwerk. Met deze vermelding kunnen adressen voor naamomzettingbinnen dit bereik worden aangevraagd.

    * Voeg het IP-adresbereik van het `acl goodclients { ... }` on-premises netwerk toe aan de sectie.  met vermelding kunnen naamoplossingsaanvragen van resources in het on-premises netwerk worden ingeschakeld.
    
    * Vervang de `192.168.0.1` waarde door het IP-adres van uw on-premises DNS-server. Met deze vermelding worden alle andere DNS-aanvragen doorgestoofd naar de on-premises DNS-server.

1. Als u de configuratie wilt gebruiken, start u Binding opnieuw. Bijvoorbeeld `sudo service bind9 restart`.

1. Voeg een voorwaardelijke expediteer toe aan de on-premises DNS-server. Configureer de voorwaardelijke expediteer om aanvragen voor het DNS-achtervoegsel van stap 1 naar de aangepaste DNS-server te verzenden.

    > [!NOTE]  
    > Raadpleeg de documentatie voor uw DNS-software voor details over het toevoegen van een voorwaardelijke expediteer.

Nadat u deze stappen hebt voltooid, u verbinding maken met bronnen in beide netwerken met behulp van volledig gekwalificeerde domeinnamen (FQDN). U HDInsight nu installeren in het virtuele netwerk.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Naamresolutie tussen twee verbonden virtuele netwerken

In dit voorbeeld worden de volgende aannames gegeven:

* U hebt twee Azure Virtual Networks die zijn verbonden met behulp van een VPN-gateway of peering.

* De aangepaste DNS-server in beide netwerken draait Linux of Unix als besturingssysteem.

* [Bind](https://www.isc.org/downloads/bind/) is geïnstalleerd op de aangepaste DNS-servers.

1. Gebruik Azure PowerShell of Azure CLI om het DNS-achtervoegsel van beide virtuele netwerken te vinden:

    Vervang `RESOURCEGROUP` de naam van de resourcegroep die het virtuele netwerk bevat en voer de opdracht in:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Gebruik de volgende tekst als `/etc/bind/named.config.local` de inhoud van het bestand op de aangepaste DNS-server. Breng deze wijziging aan op de aangepaste DNS-server in beide virtuele netwerken.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Vervang `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` de waarde door het DNS-achtervoegsel van het __andere__ virtuele netwerk. Met deze vermelding worden aanvragen voor het DNS-achtervoegsel van het externe netwerk doorgeslopen naar de aangepaste DNS in dat netwerk.

3. Gebruik op de aangepaste DNS-servers in beide virtuele netwerken `/etc/bind/named.conf.options` de volgende tekst als de inhoud van het bestand:

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```

   Vervang `10.0.0.0/16` de `10.1.0.0/16` waarden en waarden door de IP-adresbereiken van uw virtuele netwerken. Met deze vermelding kunnen resources in elk netwerk verzoeken van de DNS-servers indienen.

    Aanvragen die niet voor de DNS-achtervoegsels van de virtuele netwerken (bijvoorbeeld microsoft.com) zijn, worden verwerkt door de recursieve resolver van Azure.

4. Als u de configuratie wilt gebruiken, start u Binding opnieuw. Bijvoorbeeld `sudo service bind9 restart` op beide DNS-servers.

Nadat u deze stappen hebt voltooid, u verbinding maken met bronnen in het virtuele netwerk met behulp van volledig gekwalificeerde domeinnamen (FQDN). U HDInsight nu installeren in het virtuele netwerk.

## <a name="next-steps"></a>Volgende stappen

* Zie HDInsight aansluiten op [een on-premises netwerk](./connect-on-premises-network.md)voor een volledig voorbeeld van het configureren van HDInsight om verbinding te maken met een on-premises netwerk.
* Zie [Apache HBase-clusters maken op HDInsight in Azure Virtual Network voor](hbase/apache-hbase-provision-vnet.md)het configureren van Apache HBase-clusters in virtuele Azure-netwerken.
* Zie [Apache HBase-clusterreplicatie instellen in azure virtuele netwerken](hbase/apache-hbase-replication.md)voor het configureren van geo-replicatie van Apache HBase.
* Zie het overzicht van het [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)voor meer informatie over virtuele Azure-netwerken.

* Zie [Netwerkbeveiligingsgroepen](../virtual-network/security-overview.md)voor meer informatie over netwerkbeveiligingsgroepen.

* Zie [Door de gebruiker gedefinieerde routes en IP-forwarding](../virtual-network/virtual-networks-udr-overview.md)voor meer informatie over door de gebruiker gedefinieerde routes.
