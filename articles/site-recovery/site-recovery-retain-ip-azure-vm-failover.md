---
title: IP-adressen behouden na azure vm-failover met Azure Site Recovery
description: Beschrijft hoe u IP-adressen behouden wanneer u niet meer dan Azure VM's voor noodherstel naar een secundaire regio met Azure Site Recovery
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 650fb7f0877a98ef53ed3868550f9c084ecb5885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257561"
---
# <a name="retain-ip-addresses-during-failover"></a>IP-adressen behouden tijdens failover

[Azure Site Recovery](site-recovery-overview.md) maakt disaster recovery voor Azure VM's mogelijk door VM's te repliceren naar een andere Azure-regio, niet als er een storing optreedt en niet terug te keren naar het primaire gebied wanneer alles weer normaal is.

Tijdens failover wilt u misschien de IP-adressering in het doelgebied identiek houden aan het brongebied:

- Wanneer u noodherstel voor Azure VM's inschakelt, maakt Site Recovery standaard doelbronnen op basis van bronbroninstellingen. Voor Azure VM's die zijn geconfigureerd met statische IP-adressen, probeert Site Recovery hetzelfde IP-adres in te richten voor de doel-VM, als deze niet in gebruik is. Voor een volledige uitleg over hoe Site Recovery omgaat met aanpakken, [bekijk dit artikel](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).
- Voor eenvoudige toepassingen is de standaardconfiguratie voldoende. Voor complexere apps moet u mogelijk extra resources inrichten om ervoor te zorgen dat de connectiviteit werkt zoals verwacht na een failover.


In dit artikel vindt u enkele voorbeelden voor het behouden van IP-adressen in complexere voorbeeldscenario's. De voorbeelden zijn:

- Failover voor een bedrijf met alle resources die in Azure worden uitgevoerd
- Failover voor een bedrijf met een hybride implementatie en resources die zowel on-premises als in Azure worden uitgevoerd

## <a name="resources-in-azure-full-failover"></a>Resources in Azure: volledige failover

Bedrijf A heeft al zijn apps uitgevoerd in Azure.

### <a name="before-failover"></a>Vóór failover

Hier is de architectuur voor failover.

- Bedrijf A heeft identieke netwerken en subnetten in bron- en doelgebieden voor Azure.Company A has identical networks and subnets in source and target Azure regions.
- Om de hersteltijddoelstelling (RTO) te verminderen, gebruikt het bedrijf replicaknooppunten voor SQL Server Always On, domeincontrollers, enz. Deze replicaknooppunten bevinden zich in een andere VNet in het doelgebied, zodat ze VPN-site-to-site-connectiviteit tussen de bron- en doelregio's kunnen tot stand kunnen brengen. Dit is niet mogelijk als dezelfde IP-adresruimte wordt gebruikt in de bron en het doel.  
- Voordat failover, de netwerkarchitectuur is als volgt:
    - Primaire regio is Azure Oost-Azië
        - Oost-Azië heeft een VNet (**Bron VNet**) met adresruimte 10.1.0.0/16.
        - Oost-Azië heeft workloads verdeeld over drie subnetten in de VNet:
            - **Subnet 1**: 10.1.1.0/24
            - **Subnet 2**: 10.1.2.0/24
            - **Subnet 3**: 10.1.3.0/24
    - Secundaire (doel)regio is Azure Zuidoost-Azië
        - Zuidoost-Azië heeft een herstel VNet (**Recovery VNet**) identiek aan **Bron VNet**.
        - Zuidoost-Azië heeft een extra VNet **(Azure VNet)** met adresruimte 10.2.0.0/16.
        - **Azure VNet** bevat een subnet (**Subnet 4**) met adresruimte 10.2.4.0/24.
        - Replicaknooppunten voor SQL Server Always On, domeincontroller etc. bevinden zich in **Subnet 4.**
    - **Bron VNet** en **Azure VNet** zijn verbonden met een VPN-site-to-site-verbinding.
    - **Recovery VNet** is niet verbonden met een ander virtueel netwerk.
    - **Bedrijf A** wijst doel-IP-adressen toe/verifieert voor gerepliceerde items. Het doel-IP is hetzelfde als bron-IP voor elke VM.

![Resources in Azure voordat volledige failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Na failover

Als er een regionale bronstoring optreedt, kan bedrijf A al zijn resources naar de doelregio mislukken.

- Met doel-IP-adressen die al voor de failover zijn geïnstalleerd, kan Bedrijf A failover orkestreren en automatisch verbindingen tot stand brengen na failover tussen **Recovery VNet** en **Azure VNet**. Dit wordt geïllustreerd in het volgende diagram..
- Afhankelijk van de app-vereisten kunnen verbindingen tussen de twee VNets (**Recovery VNet** en **Azure VNet**) in het doelgebied worden gemaakt voor, tijdens (als tussenstap) of na de failover.
  - Het bedrijf kan [herstelplannen](site-recovery-create-recovery-plans.md) gebruiken om aan te geven wanneer verbindingen worden gemaakt.
  - Ze kunnen verbinding maken tussen de VNets via VNet peering of site-to-site VPN.
      - Bij VNET-peering wordt geen VPN-gateway gebruikt en er gelden diverse beperkingen voor.
      - VNet peering [prijzen](https://azure.microsoft.com/pricing/details/virtual-network) worden anders berekend dan VNet-to-VNet VPN Gateway [prijzen](https://azure.microsoft.com/pricing/details/vpn-gateway). Voor failovers adviseren we over het algemeen om dezelfde connectiviteitsmethode te gebruiken als bronnetwerken, inclusief het verbindingstype, om onvoorspelbare netwerkincidenten te minimaliseren.

    ![Resources in Azure volledige failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Resources in Azure: geïsoleerde failover van apps

Mogelijk moet u op app-niveau mislukken. Bijvoorbeeld om te falen boven een specifieke app of app-laag in een speciaal subnet.

- In dit scenario, hoewel u IP-adressering behouden, is het over het algemeen niet aan te raden omdat het de kans op inconsistenties in de connectiviteit vergroot. U verliest ook subnetconnectiviteit met andere subnetten binnen hetzelfde Azure VNet.
- Een betere manier om subnet-level app failover te doen is het gebruik van verschillende doel IP-adressen voor failover (als je connectiviteit met andere subnetten op bron VNet), of om elke app te isoleren in zijn eigen dedicated VNet in de bronregio. Met deze laatste benadering u de verbinding tussen netwerken in het brongebied tot stand brengen en hetzelfde gedrag emuleren wanneer u niet naar het doelgebied gaat.  

In dit voorbeeld plaatst Bedrijf A apps in het brongebied in speciale VNets en wordt er verbinding tussen deze VNets tot gevolg. Met dit ontwerp kunnen ze geïsoleerde app-failover uitvoeren en de bronprivé-IP-adressen in het doelnetwerk behouden.

### <a name="before-failover"></a>Vóór failover

Voordat failover, de architectuur is als volgt:

- Vm's voor toepassingen worden gehost in de primaire Azure East Asia-regio:
    - **App1** Vm's bevinden zich in VNet **Bron 1**: 10.1.0.0/16.
    - **App2** Vm's bevinden zich in VNet **Source VNet 2**: 10.2.0.0/16.
    - **Bron VNet 1** heeft twee subnetten.
    - **Bron VNet 2** heeft twee subnetten.
- Secundaire (doel)regio is Azure Zuidoost-Azië - Zuidoost-Azië heeft een herstel VNets (**Herstel VNet 1** en **Herstel VNet 2**) die identiek zijn aan Bron **VNet 1** en Bron **VNet 2**.
        - **Herstel VNet 1** en **Recovery VNet 2** hebben elk twee subnetten die overeenkomen met de subnetten in Bron **VNet 1** en Bron **VNet 2** - Zuidoost-Azië heeft een extra VNet **(Azure VNet)** met adresruimte 10.3.0.0/16.
        - **Azure VNet** bevat een subnet (**Subnet 4**) met adresruimte 10.3.4.0/24.
        - Replicaknooppunten voor SQL Server Always On, domeincontroller etc. bevinden zich in **Subnet 4.**
- Er zijn een aantal site-to-site VPN-verbindingen: 
    - **Bron VNet 1** en **Azure VNet**
    - **Bron VNet 2** en **Azure VNet**
    - **Bron VNet 1** en **Bron VNet 2** zijn verbonden met VPN site-to-site
- **Herstel VNet 1** en **Recovery VNet 2** zijn niet verbonden met andere VNets.
- **Bedrijf A** configureert VPN-gateways op **Recovery VNet 1** en Recovery **VNet 2**om RTO te verminderen.  
- **Recovery VNet1** en **Recovery VNet2** zijn niet verbonden met een ander virtueel netwerk.
- Om de hersteltijddoelstelling (RTO) te verminderen, zijn VPN-gateways geconfigureerd op **Recovery VNet1** en **Recovery VNet2** voordat ze mislukken.

    ![Resources in Azure voordat de failover van de app mislukt](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>Na failover

In het geval van een storing of probleem dat van invloed is op één app (in **Bron VNet 2 in ons voorbeeld), kan bedrijf A de betreffende app als volgt herstellen:


- Vpn-verbindingen loskoppelen tussen **Bron VNet1** en **Bron VNet2**en tussen **Bron VNet2** en **Azure VNet** .
- Vpn-verbindingen tot stand brengen tussen **Bron VNet1** en **Recovery VNet2**en tussen **Recovery VNet2** en **Azure VNet**.
- Fail over VM's in **Bron VNet2** naar **Recovery VNet2**.

![Failover resources in Azure-apps](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- Dit voorbeeld kan worden uitgebreid met meer toepassingen en netwerkverbindingen. De aanbeveling is om een vergelijkbaar verbindingsmodel te volgen, voor zover mogelijk, wanneer het niet over van bron naar doel.
- VPN-gateways gebruiken openbare IP-adressen en gateway-hop om verbindingen tot stand te brengen. Als u geen openbare IP-adressen wilt gebruiken of als u extra hop wilt vermijden, u [Azure VNet-peering](../virtual-network/virtual-network-peering-overview.md) gebruiken om virtuele netwerken te peeren in [ondersteunde Azure-regio's.](../virtual-network/virtual-network-manage-peering.md#cross-region)

## <a name="hybrid-resources-full-failover"></a>Hybride bronnen: volledige failover

In dit scenario voert **bedrijf B** een hybride bedrijf uit, waarbij een deel van de toepassingsinfrastructuur op Azure wordt uitgevoerd en de rest on-premises wordt uitgevoerd. 

### <a name="before-failover"></a>Vóór failover

Zo ziet de netwerkarchitectuur eruit voor failover.

- Vm's voor toepassingen worden gehost in Azure Oost-Azië.
- Oost-Azië heeft een VNet (**Bron VNet**) met adresruimte 10.1.0.0/16.
  - Oost-Azië heeft workloads verdeeld over drie subnetten in **Bron VNet:**
    - **Subnet 1**: 10.1.1.0/24
    - **Subnet 2**: 10.1.2.0/24
    - **Subnet 3**: 10.1.3.0/24, gebruikmakend van een Virtueel Azure-netwerk met adresruimte 10.1.0.0/16. Dit virtuele netwerk heet **Source VNet**
      - De secundaire (doel)regio is Azure Zuidoost-Azië:
  - Zuidoost-Azië heeft een herstel VNet (**Recovery VNet**) identiek aan **Bron VNet**.
- VM's in Oost-Azië zijn verbonden met een on-premises datacenter met Azure ExpressRoute of site-to-site VPN.
- Om RTO te verminderen, voorziet Bedrijf B gateways op Recovery VNet in Azure Zuidoost-Azië voordat deze mislukt.
- Bedrijf B wijst of verifieert doel-IP-adressen voor gerepliceerde VM's. Het doel-IP-adres is hetzelfde als het IP-adres van de bron voor elke virtuele machine.


![On-premises-naar-Azure-connectiviteit voor failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Na failover


Als er een regionale bronstoring optreedt, kan bedrijf B al zijn resources naar de doelregio beheren.

- Met doel-IP-adressen die al voor de failover zijn geïnstalleerd, kan Bedrijf B failover orkestreren en automatisch verbindingen tot stand brengen na failover tussen **Recovery VNet** en **Azure VNet**.
- Afhankelijk van de app-vereisten kunnen verbindingen tussen de twee VNets (**Recovery VNet** en **Azure VNet**) in het doelgebied worden gemaakt voor, tijdens (als tussenstap) of na de failover. Het bedrijf kan [herstelplannen](site-recovery-create-recovery-plans.md) gebruiken om aan te geven wanneer verbindingen worden gemaakt.
- De oorspronkelijke verbinding tussen Azure East Asia en het on-premises datacenter moet worden verbroken voordat de verbinding tot stand wordt gebracht tussen Azure Zuidoost-Azië en on-premises datacenter.
- De on-premises routering is opnieuw geconfigureerd om te wijzen op het doelgebied en gateways post failover.

![On-premises-naar-Azure-connectiviteit na failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>Hybride bronnen: geïsoleerde failover van apps

Bedrijf B kan niet falen ten opzichte van geïsoleerde apps op subnetniveau. Dit komt omdat de adresruimte op bron- en herstelvnets hetzelfde is en de oorspronkelijke bron voor on-premises verbinding actief is.

 - Voor app-tolerantie moet bedrijf B elke app in zijn eigen speciale Azure VNet plaatsen.
 - Met elke app in een afzonderlijke VNet kan bedrijf B mislukken via geïsoleerde apps en routebronverbindingen naar het doelgebied.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [herstelplannen](site-recovery-create-recovery-plans.md).
