---
title: Een SAP multi-SID-configuratie maken in Azure | Microsoft Documenten
description: Handleiding voor sap NetWeaver-multi-SID-configuratie met hoge beschikbaarheid op virtuele Windows-machines
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d7938f7db22f004a0bf6cdf2e22dc8e103896719
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617398"
---
# <a name="create-an-sap-netweaver-multi-sid-configuration"></a>Een SAP NetWeaver multi-SID-configuratie maken

[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[sap-ha-guide]:sap-high-availability-guide.md 
[sap-ha-guide-figure-6001]:./media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:./media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:./media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:./media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:./media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[sap-ha-guide-9.1.1]:sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 
[sap-ha-guide-8.8]:sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.12.3.3]:sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 
[sap-ha-guide-9]:sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 
[sap-ha-guide-9.1.2]:sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 
[sap-ha-guide-9.1.3]:sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 
[sap-ha-guide-9.1.4]:sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 
[sap-ha-guide-9.4]:sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a 
[sap-ha-guide-9.5]:sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 
[sap-ha-guide-9.6]:sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 
[sap-ha-guide-10]:sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9

In september 2016 heeft Microsoft een functie uitgebracht waarmee u meerdere virtuele IP-adressen beheren met behulp van een interne laadbalans van Azure. Deze functionaliteit bestaat al in de externe load balancer van Azure.

Als u een SAP-implementatie hebt, u een interne load balancer gebruiken om een Windows-clusterconfiguratie voor SAP ASCS/SCS te maken, zoals is gedocumenteerd in de [handleiding voor SAP NetWeaver met hoge beschikbaarheid op Windows VM's.][sap-ha-guide]

In dit artikel wordt gekeken naar het verplaatsen van één ASCS/SCS-installatie naar een SAP multi-SID-configuratie door extra geclusterde SAP ASCS/SCS-clusterexemplaren te installeren in een bestaand WSFC-cluster (Windows Server Failover Clustering). Wanneer dit proces is voltooid, hebt u een SAP multi-SID-cluster geconfigureerd.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten
U hebt al een WSFC-cluster geconfigureerd dat wordt gebruikt voor één SAP ASCS/SCS-exemplaar, zoals besproken in de [handleiding voor SAP NetWeaver met hoge beschikbaarheid op Windows VM's][sap-ha-guide] en zoals in dit diagram wordt weergegeven.

![SAP ASCS/SCS-exemplaar met hoge beschikbaarheid][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>Doelarchitectuur

Het doel is om meerdere SAP ABAP ASCS of SAP Java SCS geclusterde exemplaren te installeren in hetzelfde WSFC-cluster, zoals hier wordt geïllustreerd:

![Meerdere GECLUSTERde SAP ASCS/SCS-exemplaren in Azure][sap-ha-guide-figure-6002]

> [!NOTE]
>Er is een limiet aan het aantal particuliere front-end IP's voor elke interne laadbakvan Azure.
>
>Het maximum aantal SAP ASCS/SCS-exemplaren in één WSFC-cluster is gelijk aan het maximum aantal particuliere front-end IP's voor elke interne laadbalans van Azure.
>

Zie 'Private front-end IP per load balancer' in [Netwerklimieten: Azure Resource Manager voor][networking-limits-azure-resource-manager]meer informatie over limieten voor load-balancer.

Het complete landschap met twee sap-systemen met hoge beschikbaarheid zou er als volgt uitzien:

![SAP high-availability multi-SID setup met twee SAP-systeem SID's][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> De instelling moet aan de volgende voorwaarden voldoen:
> - De SAP ASCS/SCS-exemplaren moeten hetzelfde WSFC-cluster delen.
> - Elke DBMS SID moet zijn eigen dedicated WSFC-cluster hebben.
> - SAP-toepassingsservers die deel uitmaken van één SAP-systeem SID moeten hun eigen dedicated VM's hebben.


## <a name="prepare-the-infrastructure"></a>De infrastructuur voorbereiden
Om uw infrastructuur voor te bereiden, u een extra SAP ASCS/SCS-exemplaar installeren met de volgende parameters:

| Parameternaam | Waarde |
| --- | --- |
| SAP ASCS/SCS SID |pr1-lb-ascs |
| SAP DBMS interne load balancer | PR5 (PR5) |
| SAP virtuele hostnaam | pr5-sap-cl |
| SAP ASCS/SCS virtual host IP-adres (extra AZURE load balancer IP-adres) | 10.0.0.50 |
| SAP ASCS/SCS-instantienummer | 50 |
| ILB-sondepoort voor extra SAP ASCS/SCS-exemplaar | 62350 |

> [!NOTE]
> Voor SAP ASCS/SCS-clusterexemplaren vereist elk IP-adres een unieke sondepoort. Als bijvoorbeeld één IP-adres op een interne laadbalans van Azure sondepoort 62300 gebruikt, kan geen enkel ander IP-adres op die load balancer sondepoort 62300 gebruiken.
>
>Omdat sondepoort 62300 al gereserveerd is, gebruiken we sondepoort 62350.

U extra SAP ASCS/SCS-exemplaren installeren in het bestaande WSFC-cluster met twee knooppunten:

| Rol virtuele machine | Naam virtuele machinehost | Statisch IP-adres |
| --- | --- | --- |
| 1e clusterknooppunt voor ASCS/SCS-exemplaar |pr1-ascs-0 |10.0.0.10 |
| 2e clusterknooppunt voor ASCS/SCS-exemplaar |pr1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Een virtuele hostnaam maken voor het geclusterde SAP ASCS/SCS-exemplaar op de DNS-server

U een DNS-vermelding maken voor de virtuele hostnaam van het ASCS/SCS-exemplaar met behulp van de volgende parameters:

| Nieuwe SAP ASCS/SCS virtuele hostnaam | Gekoppeld IP-adres |
| --- | --- |
|pr5-sap-cl |10.0.0.50 |

De nieuwe hostnaam en het IP-adres worden weergegeven in DNS-beheer, zoals in de volgende schermafbeelding wordt weergegeven:

![DNS Manager-lijst met de gedefinieerde DNS-vermelding voor de nieuwe virtuele naam SAP ASCS/SCS-cluster en TCP/IP-adres][sap-ha-guide-figure-6004]

De procedure voor het maken van een DNS-vermelding wordt ook in detail beschreven in de [hoofdhandleiding voor SAP NetWeaver met hoge beschikbaarheid op Windows VM's.][sap-ha-guide-9.1.1]

> [!NOTE]
> Het nieuwe IP-adres dat u aan de virtuele hostnaam van het extra ASCS/SCS-exemplaar toewijst, moet hetzelfde zijn als het nieuwe IP-adres dat u hebt toegewezen aan de SAP Azure-loadbalancer.
>
>In ons scenario is het IP-adres 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>Een IP-adres toevoegen aan een bestaande interne laadbalans van Azure met PowerShell

Als u meer dan één SAP ASCS/SCS-instantie in hetzelfde WSFC-cluster wilt maken, gebruikt u PowerShell om een IP-adres toe te voegen aan een bestaande interne laadbalans van Azure. Elk IP-adres vereist zijn eigen load-balancing regels, probe port, front-end IP pool en back-end pool.

In het volgende script wordt een nieuw IP-adres toegevoegd aan een bestaande load balancer. Werk de PowerShell-variabelen voor uw omgeving bij. Het script maakt alle benodigde load-balancing regels voor alle SAP ASCS/SCS-poorten.

```powershell

# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure VNet and subnet
$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzLoadBalancer

# Get new updated configuration
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
# Get new updated LP FrontendIP COnfig
$FEConfig = Get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add new back-end configuration into existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzLoadBalancer

# Get new updated config
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to backend pool
$BEPool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzNetworkInterface -NetworkInterface $NIC
        #start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzLoadBalancer

Write-Host "Successfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
Nadat het script is uitgevoerd, worden de resultaten weergegeven in de Azure-portal, zoals weergegeven in de volgende schermafbeelding:

![Nieuwe front-end IP-pool in de Azure-portal][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Schijven toevoegen aan clustermachines en de sios-clustershareschijf configureren

U moet een nieuwe clustershareschijf toevoegen voor elk extra SAP ASCS/SCS-exemplaar. Voor Windows Server 2012 R2 is de WSFC-schijf voor clustershare die momenteel in gebruik is, de SIOS DataKeeper-softwareoplossing.

Ga als volgt te werk:
1. Voeg een extra schijf of schijven van dezelfde grootte (die u moet strepen) toe aan elk van de clusterknooppunten en maak ze op.
2. Opslagreplicatie configureren met SIOS DataKeeper.

Bij deze procedure wordt ervan uitgegaan dat u SIOS DataKeeper al hebt geïnstalleerd op de WSFC-clustermachines. Als u deze hebt geïnstalleerd, moet u nu de replicatie tussen de machines configureren. Het proces wordt in detail beschreven in de [hoofdhandleiding voor SAP NetWeaver met hoge beschikbaarheid op Windows VM's.][sap-ha-guide-8.12.3.3]  

![DataKeeper synchrone spiegeling voor de nieuwe SAP ASCS/SCS-sharedisk][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>VM's implementeren voor SAP-toepassingsservers en DBMS-cluster

Ga als volgt te werk om de infrastructuurvoorbereiding voor het tweede SAP-systeem te voltooien:

1. Implementeer speciale VM's voor SAP-toepassingsservers en plaats ze in hun eigen specifieke beschikbaarheidsgroep.
2. Implementeer speciale VM's voor DBMS-cluster en plaats ze in hun eigen speciale beschikbaarheidsgroep.


## <a name="install-the-second-sap-sid2-netweaver-system"></a>Het tweede SAP SID2 NetWeaver-systeem installeren

Het volledige proces van het installeren van een tweede SAP SID2-systeem wordt beschreven in de [hoofdhandleiding voor SAP NetWeaver met hoge beschikbaarheid op Windows VM's.][sap-ha-guide-9]

De procedure op hoog niveau is als volgt:

1. [Installeer het eerste clusterknooppunt van SAP][sap-ha-guide-9.1.2].  
 In deze stap installeert u SAP met een ASCS/SCS-exemplaar met hoge beschikbaarheid op het **bestaande WSFC-clusterknooppunt 1**.

2. [Wijzig het SAP-profiel van de instantie ASCS/SCS][sap-ha-guide-9.1.3].

3. [Een sondepoort configureren][sap-ha-guide-9.1.4].  
 In deze stap configureert u een SAP-clusterbron SAP-SID2-IP-sondepoort met PowerShell. Voer deze configuratie uit op een van de SAP ASCS/SCS-clusterknooppunten.

4. [De database-instantie installeren] [sap-ha-guide-9.2].  
 In deze stap installeert u DBMS op een speciaal WSFC-cluster.

5. [Het tweede clusterknooppunt installeren] [sap-ha-guide-9.3].  
 In deze stap installeert u SAP met een ASCS/SCS-exemplaar met hoge beschikbaarheid op het bestaande WSFC-clusterknooppunt 2.

6. Open Windows Firewall-poorten voor het SAP ASCS/SCS-exemplaar en ProbePort.  
 Op beide clusterknooppunten die worden gebruikt voor SAP ASCS/SCS-exemplaren, opent u alle Windows Firewall-poorten die worden gebruikt door SAP ASCS/SCS. Deze poorten worden vermeld in de [handleiding voor SAP NetWeaver met hoge beschikbaarheid op Windows VM's.][sap-ha-guide-8.8]  
 Open ook de Azure internal load balancer probe-poort, die 62350 is in ons scenario.

7. [Wijzig het starttype van de SAP ERS Windows-serviceinstantie][sap-ha-guide-9.4].

8. [Installeer de SAP primaire toepassingsserver][sap-ha-guide-9.5] op de nieuwe speciale VM.

9. [Installeer de SAP extra applicatieserver][sap-ha-guide-9.6] op de nieuwe speciale VM.

10. [Test de failover- en SIOS-replicatie van DE SAP ASCS/SCS-instantie][sap-ha-guide-10].

## <a name="next-steps"></a>Volgende stappen

- [Netwerklimieten: Azure Resource Manager][networking-limits-azure-resource-manager]
- [Meerdere VIP's voor Azure Load Balancer][load-balancer-multivip-overview]
- [Handleiding voor SAP NetWeaver met hoge beschikbaarheid op Windows VM's][sap-ha-guide]
