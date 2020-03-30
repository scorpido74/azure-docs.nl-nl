---
title: Een public load balancer maken met IPv6 - Azure CLI
titleSuffix: Azure Load Balancer
description: Ga met dit leerpad aan de slag met het maken van een public load balancer met IPv6 met Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, azure load balancer, dual stack, public ip, native ipv6, mobile, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: bff6a7ca6eb1a6859ec25d488f564c66946a780b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045404"
---
# <a name="create-a-public-load-balancer-with-ipv6-using-azure-cli"></a>Een public load balancer maken met IPv6 met Azure CLI

>[!NOTE] 
>In dit artikel wordt een inleidende IPv6-functie beschreven waarmee Basic Load Balancers zowel IPv4- als IPv6-connectiviteit kunnen bieden. Uitgebreide IPv6-connectiviteit is nu beschikbaar met [IPv6 voor Azure VNETs](../virtual-network/ipv6-overview.md) die IPv6-connectiviteit integreert met uw virtuele netwerken en belangrijke functies bevat, zoals IPv6 Network Security Group-regels, IPv6-gebruikersgedefinieerde routering, IPv6 Basic en Standard load balancing, en meer.  IPv6 voor Azure VNETs is de aanbevolen standaard voor IPv6-toepassingen in Azure. Zie [IPv6 voor Azure VNET Powershell-implementatie](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) 

Azure Load Balancer is een Layer-4 (TCP, UDP) load balancer. Load balancers bieden een hoge beschikbaarheid door binnenkomend verkeer te verdelen over gezonde service-exemplaren in cloudservices of virtuele machines in een load balancer-set. Load balancers kunnen deze services ook op meerdere poorten of meerdere IP-adressen of beide presenteren.

## <a name="example-deployment-scenario"></a>Voorbeeldimplementatiescenario

In het volgende diagram wordt de oplossing voor het balanceren van de last en de oplossing geïllustreerd die wordt geïmplementeerd met behulp van de voorbeeldsjabloon die in dit artikel wordt beschreven.

![Load balancer-scenario](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

In dit scenario maakt u de volgende Azure-bronnen:

* Twee virtuele machines (VM's)
* A virtual network interface for each VM with both IPv4 and IPv6 addresses assigned
* Een public load balancer met een IPv4 en een IPv6 openbaar IP-adres
* Een beschikbaarheidsset met de twee VM's
* Twee regels voor het balanceren van de lasten om de openbare VIP's in kaart te brengen naar de privéeindpunten

## <a name="deploy-the-solution-by-using-azure-cli"></a>De oplossing implementeren met Azure CLI

In de volgende stappen wordt uitgelegd hoe u een public load balancer maakt met Azure CLI. Met CLI maakt en configureert u elk object afzonderlijk en stelt u ze samen om een resource te maken.

Als u een load balancer wilt implementeren, maakt en configureert u de volgende objecten:

* **Front-end IP-configuratie**: bevat openbare IP-adressen voor binnenkomend netwerkverkeer.
* **Back-end adresgroep**: Bevat netwerkinterfaces (NIC's) voor de virtuele machines om netwerkverkeer van de load balancer te ontvangen.
* **Regels voor taakverdeling**: bevat regels die een openbare poort op de load balancer toewijzen aan een poort in de groep back-endadres.
* **Inkomende NAT-regels**: Bevat NAT-regels (Network Address Translation) die een openbare poort op de load balancer toewijzen aan een poort voor een specifieke virtuele machine in de groep back-endadres.
* **Probes:** Bevat statussondes die worden gebruikt om de beschikbaarheid van virtuele machine-exemplaren in de groep back-endadressen te controleren.

## <a name="set-up-azure-cli"></a>Azure CLI instellen

In dit voorbeeld voert u de Azure CLI-hulpprogramma's uit in een PowerShell-opdrachtvenster. Om de leesbaarheid en hergebruik te verbeteren, gebruikt u de scriptmogelijkheden van PowerShell, niet de Azure PowerShell-cmdlets.

1. [Installeer en configureer de Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) door de stappen in het gekoppelde artikel te volgen en u aan te melden bij uw Azure-account.

2. PowerShell-variabelen instellen voor gebruik met de Azure CLI-opdrachten:

    ```powershell
    $subscriptionid = "########-####-####-####-############"  # enter subscription id
    $location = "southcentralus"
    $rgName = "pscontosorg1southctrlus09152016"
    $vnetName = "contosoIPv4Vnet"
    $vnetPrefix = "10.0.0.0/16"
    $subnet1Name = "clicontosoIPv4Subnet1"
    $subnet1Prefix = "10.0.0.0/24"
    $subnet2Name = "clicontosoIPv4Subnet2"
    $subnet2Prefix = "10.0.1.0/24"
    $dnsLabel = "contoso09152016"
    $lbName = "myIPv4IPv6Lb"
    ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Een resourcegroep, een load balancer, een virtueel netwerk en subnetten maken

1. Een resourcegroep maken:

    ```azurecli
    az group create --name $rgName --location $location
    ```

2. Maak een load balancer:

    ```azurecli
    $lb = az network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Een virtueel netwerk maken:

    ```azurecli
    $vnet = az network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. Maak in dit virtuele netwerk twee subnetten:

    ```azurecli
    $subnet1 = az network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = az network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Openbare IP-adressen maken voor de front-end pool

1. Stel de PowerShell-variabelen in:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Maak een openbaar IP-adres voor de front-end IP-groep:

    ```azurecli
    $publicipV4 = az network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --version IPv4 --allocation-method Dynamic --dns-name $dnsLabel
    $publicipV6 = az network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --version IPv6 --allocation-method Dynamic --dns-name $dnsLabel
    ```

    > [!IMPORTANT]
    > De load balancer gebruikt het domeinlabel van het openbare IP als volledig gekwalificeerde domeinnaam (FQDN). Dit is een wijziging ten opzichte van de klassieke implementatie, die de naam van de cloudservice gebruikt als de load balancer FQDN.
    >
    > In dit voorbeeld wordt de FQDN *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Front-end- en back-endpools maken

In deze sectie maakt u de volgende IP-groepen:
* De front-end IP-pool die het binnenkomende netwerkverkeer op de load balancer ontvangt.
* De back-end IP-pool waar de front-end pool het load-balanced netwerkverkeer verzendt.

1. Stel de PowerShell-variabelen in:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Maak een front-end IP-groep en koppel deze aan het openbare IP-adres dat u in de vorige stap hebt gemaakt en de load balancer.

    ```azurecli
    $frontendV4 = az network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-address $publicIpv4Name --lb-name $lbName
    $frontendV6 = az network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-address $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>De sonde, NAT-regels en regels voor de balans balans maken

In dit voorbeeld worden de volgende items gemaakt:

* Een sonderegel om te controleren op connectiviteit met TCP-poort 80.
* Een NAT-regel om al het binnenkomende verkeer op poort 3389 te vertalen naar poort 3389 voor RDP.\*
* Een NAT-regel om al het binnenkomende verkeer op poort 3391 te vertalen naar poort 3389 voor extern bureaublad-protocol (RDP).\*
* Een regel voor load balancer om al het binnenkomende verkeer op poort 80 naar poort 80 in evenwicht te brengen op de adressen in de back-endpool.

\*NAT-regels zijn gekoppeld aan een specifieke instantie voor virtuele machines achter de load balancer. Het netwerkverkeer dat aankomt op poort 3389 wordt verzonden naar de specifieke virtuele machine en poort die is gekoppeld aan de NAT-regel. U moet een protocol (UDP of TCP) voor een NAT-regel opgeven. U beide protocollen niet aan dezelfde poort toewijzen.

1. Stel de PowerShell-variabelen in:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Maak de sonde.

    In het volgende voorbeeld wordt elke 15 seconden een TCP-sonde uitgevoerd die controleert op connectiviteit met de back-end TCP-poort 80. Na twee opeenvolgende fouten markeert de back-endbron als niet beschikbaar.

    ```azurecli
    $probeV4V6 = az network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --threshold 2 --lb-name $lbName
    ```

3. Maak binnenkomende NAT-regels waarmee RDP-verbindingen met de back-endresources kunnen worden gebruikt:

    ```azurecli
    $inboundNatRuleRdp1 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Maak regels voor load balancer die verkeer naar verschillende back-endpoorten verzenden, afhankelijk van de front-end die de aanvraag heeft ontvangen.

    ```azurecli
    $lbruleIPv4 = az network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = az network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Controleer uw instellingen:

    ```azurecli
    az network lb show --resource-group $rgName --name $lbName
    ```

    Verwachte uitvoer:

        info:    Executing command network lb show
        info:    Looking up the load balancer "myIPv4IPv6Lb"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
        data:    Name                            : myIPv4IPv6Lb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : southcentralus
        data:    Provisioning state              : Succeeded
        data:
        data:    Frontend IP configurations:
        data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
        data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
        data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
        data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
        data:
        data:    Probes:
        data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
        data:    -------------------  ------------------  --------  ----  ----  --------  -----
        data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
        data:
        data:    Backend Address Pools:
        data:    Name             Provisioning state
        data:    ---------------  ------------------
        data:    BackendPoolIPv4  Succeeded
        data:    BackendPoolIPv6  Succeeded
        data:
        data:    Load Balancing Rules:
        data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
        data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
        data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
        data:
        data:    Inbound NAT Rules:
        data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
        data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
        data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
        info:    network lb show

## <a name="create-nics"></a>NIC's maken

Nics maken en deze koppelen aan NAT-regels, regels voor load balancer en sondes.

1. Stel de PowerShell-variabelen in:

    ```powershell
    $nic1Name = "myIPv4IPv6Nic1"
    $nic2Name = "myIPv4IPv6Nic2"
    $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
    $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
    $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
    $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
    $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
    $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"
    ```

2. Maak een NIC voor elke back-end en voeg een IPv6-configuratie toe:

    ```azurecli
    $nic1 = az network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet1Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule1V4Id
    $nic1IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = az network nic create --name $nic2Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet2Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule2V4Id
    $nic2IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Maak de back-end VM-resources en voeg elke NIC toe

Als u VM's wilt maken, moet u een opslagaccount hebben. Voor load balancing moeten de VM's lid zijn van een beschikbaarheidsset. Zie [Een Azure VM maken met PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)voor meer informatie over het maken van VM's.

1. Stel de PowerShell-variabelen in:

    ```powershell
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $imageurn = "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > In dit voorbeeld wordt de gebruikersnaam en het wachtwoord voor de VM's in cleartext gebruikt. Let goed op wanneer u deze referenties in cleartext gebruikt. Zie de [`Get-Credential`](https://technet.microsoft.com/library/hh849815.aspx) cmdlet voor een veiligere methode voor het hanteren van referenties in PowerShell.

2. Maak de beschikbaarheidsset:

    ```azurecli
    $availabilitySet = az vm availability-set create --name $availabilitySetName --resource-group $rgName --location $location
    ```

3. Maak de virtuele machines met de bijbehorende NIC's:

    ```azurecli
    az vm create --resource-group $rgname --name $vm1Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic1Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 

    az vm create --resource-group $rgname --name $vm2Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic2Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 
    ```


