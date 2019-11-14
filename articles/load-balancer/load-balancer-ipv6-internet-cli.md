---
title: Een openbare load balancer maken met IPv6 - Azure CLI
titleSuffix: Azure Load Balancer
description: Met dit leer traject kunt u aan de slag met het maken van een open bare load balancer met IPv6 met behulp van Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6-, azure-load balancer, dual-stack, openbaar IP-adres, systeemeigen IPv6-, mobiele, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: f7c0eb9bd258d2efbdb4df78f2cf86861f77a975
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076016"
---
# <a name="create-a-public-load-balancer-with-ipv6-using-azure-cli"></a>Een openbare load balancer maken met IPv6 met Azure CLI

>[! Opmerking: wijziging in Aanbevolen procedures voor IPv6] in dit artikel wordt een inleidende IPv6-functie beschreven waarmee Basic load balancers zowel IPv4-als IPv6-connectiviteit kan bieden.  Uitgebreide IPv6-connectiviteit is nu beschikbaar met [IPv6 voor Azure VNETs](../virtual-network/ipv6-overview.md) , die IPv6-connectiviteit integreert met uw virtuele netwerken en die belang rijke functies bevat, zoals regels voor IPv6-netwerk beveiligings groepen, door IPv6 door de gebruiker gedefinieerde route ring, IPv6-basis en standaard taak verdeling en meer.  IPv6 voor Azure VNETs is de aanbevolen best practice voor IPv6-toepassingen in Azure. 
>Zie [IPv6 voor Azure VNET cli-implementatie](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md)

Azure Load Balancer is een Layer-4 (TCP, UDP) load balancer. Load balancers bieden hoge beschikbaarheid door de distributie van inkomend verkeer over gezonde service-exemplaren in cloudservices of virtuele machines in een load balancer-set. Load balancers kunnen ook deze services op meerdere poorten of meerdere IP-adressen of beide aanwezig.

## <a name="example-deployment-scenario"></a>Voorbeeldscenario voor implementatie

Het volgende diagram illustreert de load balancer-oplossing die wordt geïmplementeerd met behulp van de voorbeeldsjabloon die in dit artikel wordt beschreven.

![Load balancer-scenario](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

In dit scenario maakt u de volgende Azure-resources:

* Twee virtuele machines (VM's)
* Een virtuele netwerkinterface voor elke virtuele machine met zowel IPv4 als IPv6-adressen die zijn toegewezen
* Een openbare load balancer met een IPv4- en een openbaar IPv6-adres
* Een beschikbaarheidsset met de twee virtuele machines
* Twee laden regels voor taakverdeling voor de openbare VIP's toewijzen aan de persoonlijke eindpunten

## <a name="deploy-the-solution-by-using-azure-cli"></a>De oplossing implementeren met behulp van Azure CLI

De volgende stappen laten zien hoe u een openbare load balancer maakt met behulp van Azure CLI. Met behulp van CLI, maakt en configureert u elk object afzonderlijk, en plaats u ze samen voor het maken van een resource.

Voor het implementeren van een load balancer maken en configureren van de volgende objecten:

* **Front-end-IP-configuratie**: bevat openbare IP-adressen voor inkomend netwerkverkeer.
* **Back-end-adresgroep**: bevat netwerkinterfaces (NIC's) voor de virtuele machines netwerkverkeer kunnen ontvangen van de load balancer.
* **Regels voor taakverdeling**: bevat regels die een openbare poort op de load balancer aan een poort in de back-end-adresgroep toewijst.
* **Inkomende NAT-regels**: bevat regels network address translation (NAT) die een openbare poort op de load balancer aan een poort voor een specifieke virtuele machine in de back-end-adresgroep toewijst.
* **Tests**: bevat statustests die worden gebruikt voor het controleren van de beschikbaarheid van VM-exemplaren in de back-end-adresgroep.

## <a name="set-up-azure-cli"></a>Azure CLI instellen

In dit voorbeeld kunt u de Azure CLI-hulpprogramma's uitvoeren in een PowerShell-opdrachtvenster. Ter verbetering van leesbaarheid en opnieuw gebruiken, moet u van PowerShell-scripts mogelijkheden, niet de Azure PowerShell-cmdlets gebruiken.

1. [Installeren en configureren van de Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) door de stappen in het gekoppelde artikel en meld u aan bij uw Azure-account te volgen.

2. Instellen van de PowerShell-variabelen voor gebruik met de Azure CLI-opdrachten:

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

4. In dit virtuele netwerk, twee subnetten te maken:

    ```azurecli
    $subnet1 = az network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = az network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Openbare IP-adressen voor de front-end-pool maken

1. De PowerShell-variabelen instellen:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Maak een openbaar IP-adres voor de front-end-IP-adresgroep:

    ```azurecli
    $publicipV4 = az network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --version IPv4 --allocation-method Dynamic --dns-name $dnsLabel
    $publicipV6 = az network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --version IPv6 --allocation-method Dynamic --dns-name $dnsLabel
    ```

    > [!IMPORTANT]
    > De load balancer gebruikt het domeinlabel van het openbare IP-adres als de volledig gekwalificeerde domeinnaam (FQDN). Dit een wijziging ten opzichte van klassieke implementatie, die gebruikmaakt van de cloudservice een naam als de FQDN-naam van load balancer.
    >
    > In dit voorbeeld wordt de FQDN-naam is *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Front-end en back-end-adresgroepen maken

In deze sectie maakt maken u de volgende IP-adresgroepen:
* De front-end-IP-adresgroep die het binnenkomende netwerkverkeer op de load balancer ontvangt.
* De back-end IP-pool waar de front-endpool het netwerkverkeer met load balancing verzendt.

1. De PowerShell-variabelen instellen:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Maak een front-end IP-adresgroep, en koppel deze aan het openbare IP-adres dat u hebt gemaakt in de vorige stap en de load balancer.

    ```azurecli
    $frontendV4 = az network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-address $publicIpv4Name --lb-name $lbName
    $frontendV6 = az network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-address $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>De test- en NAT-regels maken en load balancer-regels

In dit voorbeeld worden de volgende items gemaakt:

* Een test-regel om te controleren op connectiviteit met TCP-poort 80.
* Een NAT-regel voor de omzetting van alle verkeer dat binnenkomt op poort 3389 voor poort 3389 voor RDP.\*
* Een NAT-regel om alle verkeer dat binnenkomt op poort 3391 naar poort 3389 voor remote desktop protocol (RDP).\*
* Een load balancer-regel om al het binnenkomende verkeer op poort 80 naar poort 80 op de adressen in de back-end-pool in balans.

\* NAT-regels zijn gekoppeld aan een specifiek exemplaar van de virtuele machines achter de load balancer. Het netwerkverkeer dat op poort 3389 binnenkomt wordt verzonden naar het specifieke virtuele machine en de poort die is gekoppeld aan de NAT-regel. U moet een protocol (UDP of TCP) voor een NAT-regel opgeven. U kunt beide protocollen toewijzen aan dezelfde poort.

1. De PowerShell-variabelen instellen:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Maak de test.

    Het volgende voorbeeld wordt een TCP-test waarmee wordt gecontroleerd voor verbinding met de back-end-TCP-poort 80 elke 15 seconden. Na twee opeenvolgende fouten, worden de back-end-resource niet beschikbaar is gemarkeerd.

    ```azurecli
    $probeV4V6 = az network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --threshold 2 --lb-name $lbName
    ```

3. Maak inkomende NAT-regels die RDP-verbindingen met de back-end-resources toestaan:

    ```azurecli
    $inboundNatRuleRdp1 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Maak de load balancer-regels die verkeer verzenden naar verschillende back-end-poorten, afhankelijk van de front-end die de aanvraag ontvangen.

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

NIC's maken en deze koppelen aan NAT-regels, load balancer-regels en tests.

1. De PowerShell-variabelen instellen:

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

2. Maak een NIC voor elke back-end en een IPv6-configuratie toevoegen:

    ```azurecli
    $nic1 = az network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet1Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule1V4Id
    $nic1IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = az network nic create --name $nic2Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet2Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule2V4Id
    $nic2IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>De back-end-VM-resources maken en elke NIC koppelen

Voor het maken van virtuele machines, moet u een storage-account hebben. Voor de taakverdeling moeten de virtuele machines worden leden van een beschikbaarheidsset. Zie voor meer informatie over het maken van virtuele machines [een Azure-VM maken met behulp van PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. De PowerShell-variabelen instellen:

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
    > Dit voorbeeld wordt de gebruikersnaam en het wachtwoord voor de virtuele machines in niet-versleutelde tekst. Passende maatregelen treffen wanneer u deze referenties in niet-versleutelde tekst. Zie voor een veiligere methode voor het verwerken van de referenties in PowerShell, de [ `Get-Credential` ](https://technet.microsoft.com/library/hh849815.aspx) cmdlet.

2. De beschikbaarheidsset maken:

    ```azurecli
    $availabilitySet = az vm availability-set create --name $availabilitySetName --resource-group $rgName --location $location
    ```

3. De virtuele machines met de gekoppelde NIC's maken:

    ```azurecli
    az vm create --resource-group $rgname --name $vm1Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic1Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 

    az vm create --resource-group $rgname --name $vm2Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic2Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 
    ```


