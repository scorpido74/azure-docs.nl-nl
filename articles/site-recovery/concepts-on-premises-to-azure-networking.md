---
title: Verbinding maken met on-premises failover van Azure VM's met Azure Site Recovery
description: Beschrijft hoe u verbinding maakt met Azure VM's na failover van on-premises naar Azure met Azure Site Recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: f222cdd315b79503b1bdea032f495c71df4682b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281988"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>Verbinding maken met Azure VM's na failover van on-premises 


In dit artikel wordt beschreven hoe u connectiviteit instellen, zodat u na een failoververbinding met Azure VM's verbinding maken met Azure VM's.

Wanneer u noodherstel van on-premises virtuele machines (VM's) en fysieke servers instelt op [Azure,](site-recovery-overview.md) wordt gestart met het repliceren van machines naar Azure. Wanneer er vervolgens storingen optreden, u vanaf uw on-premises site niet naar Azure gaan. Bij een failerstoring maakt Site Recovery Azure VM's met behulp van gerepliceerde on-premises gegevens. Als onderdeel van de planning voor noodherstel moet u uitzoeken hoe u verbinding maken met apps die na een failover op deze Azure VM's worden uitgevoerd.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Bereid on-premises machines voor failover.
> * Azure VM's voorbereiden na een failover. 
> * Bewaar IP-adressen op Azure VM's na failover.
> * Nieuwe IP-adressen toewijzen aan Azure VM's na failover.

## <a name="prepare-on-premises-machines"></a>On-premises machines voorbereiden

Bereid uw on-premises machines voor voordat u failover wilt.

### <a name="prepare-windows-machines"></a>Windows-machines voorbereiden

Ga als volgt te werk op on-premises Windows-machines:

1. Windows-instellingen configureren. Deze omvatten het verwijderen van statische permanente routes of WinHTTP proxy, en het instellen van de schijf SAN beleid om **OnlineAll**. [Volg](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure) deze instructies.

2. Zorg ervoor dat [deze services](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) worden uitgevoerd.

3. Extern bureaublad (RDP) inschakelen om externe verbindingen met de on-premises machine toe te staan. [Meer informatie over het](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) inschakelen van RDP met PowerShell.

4. Als u na een failover toegang wilt krijgen tot een Azure-vm via internet, u in Windows Firewall op de on-premises machine TCP en UDP toestaan in het openbare profiel en RDP instellen als toegestane app voor alle profielen.

5. Als u na een failover toegang wilt krijgen tot een Azure VM via een site-to-site VPN, u in Windows Firewall op de on-premises machine RDP toestaan voor de domein- en privéprofielen. [Meer informatie over](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) het toestaan van RDP-verkeer.
6. Zorg ervoor dat er geen Windows-updates in behandeling zijn op de on-premises VM wanneer u een failover activeert. Als dat het geval is, kunnen updates na een failover op de Azure VM worden geïnstalleerd en u zich pas aanmelden bij de VM als de updates zijn voltooid.

### <a name="prepare-linux-machines"></a>Linux-machines voorbereiden

Ga als volgt te werk op on-premises Linux-machines:

1. Controleer of de Secure Shell-service is ingesteld om automatisch te starten bij het opstarten van het systeem.
2. Controleer of SSH-verbindingen zijn toegestaan door de firewallregels.


## <a name="configure-azure-vms-after-failover"></a>Azure VM's configureren na failover

Ga na failover als volgt te werk op de Azure VM's die zijn gemaakt.

1. Als u via internet verbinding wilt maken met de virtuele machine, wijst u een openbaar IP-adres toe aan de virtuele machine. U niet hetzelfde openbare IP-adres gebruiken voor de Azure VM die u voor uw on-premises machine hebt gebruikt. [Meer informatie](../virtual-network/virtual-network-public-ip-address.md)
2. Controleer of de REGELS van de netwerkbeveiligingsgroep (NSG) op de VM binnenkomende verbindingen met de RDP- of SSH-poort toestaan.
3. Controleer [Diagnostische gegevens opstarten](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) om de vm weer te geven.


> [!NOTE]
> De Azure Bastion-service biedt privé-RDP- en SSH-toegang tot Azure VM's. [Meer informatie](../bastion/bastion-overview.md) over deze service.

## <a name="set-a-public-ip-address"></a>Een openbaar IP-adres instellen

Als alternatief voor het handmatig toewijzen van een openbaar IP-adres aan een Azure-vm, u het adres tijdens failover toewijzen met behulp van een script of Azure-automatiseringsrunbook in een [herstelplan](site-recovery-create-recovery-plans.md)voor siteherstel of u routering op DNS-niveau instellen met Azure Traffic Manager. [Meer informatie](concepts-public-ip-address-with-site-recovery.md) over het instellen van een openbaar adres.


## <a name="assign-an-internal-address"></a>Een intern adres toewijzen

Als u het interne IP-adres van een Azure VM na failering wilt instellen, hebt u een aantal opties:

- **Behoudt hetzelfde IP-adres:** u hetzelfde IP-adres op de Azure-vm gebruiken als het adres dat is toegewezen aan de on-premises machine.
- **Gebruik een ander IP-adres:** U een ander IP-adres gebruiken voor de Azure VM.


## <a name="retain-ip-addresses"></a>IP-adressen behouden

Met Site recovery u dezelfde IP-adressen behouden wanneer u niet overgaat naar Azure. Het behouden van hetzelfde IP-adres voorkomt potentiële netwerkproblemen na failover, maar brengt wel enige complexiteit met zich mee.

- Als de doel-Azure VM hetzelfde IP-adres/subnet gebruikt als uw on-premises site, u geen verbinding tussen deze apparaten maken via een vpn-verbinding van site naar site of ExpressRoute, vanwege de overlap tussen het adres. Subnetten moeten uniek zijn.
- U hebt een verbinding nodig van on-premises naar Azure na failover, zodat apps beschikbaar zijn op Azure VM's. Azure ondersteunt geen uitgerekte VLAN's, dus als u IP-adressen wilt behouden, moet u de IP-ruimte naar Azure brengen door niet over het gehele subnet te falen, naast de on-premises machine.
- Subnet failover zorgt ervoor dat een specifiek subnet niet tegelijkertijd on-premises en in Azure beschikbaar is.

Voor het behouden van IP-adressen zijn de volgende stappen vereist:

- Stel in de Compute &-netwerkeigenschappen van het gerepliceerde item netwerk- en IP-adressering in voor de doel-Azure VM om de on-premises instelling te weerspiegelen.
- Subnetten moeten worden beheerd als onderdeel van het noodherstelproces. U hebt een Azure VNet nodig dat overeenkomt met het on-premises netwerk en na failovernetwerkroutes moeten worden gewijzigd om aan te geven dat het subnet is verplaatst naar Azure en nieuwe IP-adreslocaties.  

### <a name="failover-example"></a>Voorbeeld van failover

Laten we eens naar een voorbeeld kijken.

- Het fictieve bedrijf Woodgrove Bank host hun zakelijke apps on-premises Ze hosten hun mobiele apps in Azure.
- Ze maken verbinding van on-premises met Azure via site-to-site VPN. 
- Woodgrove gebruikt Site recovery om on-premises machines te repliceren naar Azure.
- Hun on-premises apps gebruiken hard-coded IP-adressen, dus ze willen dezelfde IP-adressen in Azure behouden.
- On-premises draaien de machines waarop de apps worden uitgevoerd in drie subnetten:
    - 192.168.1.0/24.
    - 192.168.2.0/24
    - 192.168.3.0/24
- Hun apps die in Azure worden uitgevoerd, bevinden zich in het Azure VNet **Azure Network** in twee subnetten:
- 172.16.1.0/24
- 172.16.2.0/24.

Om de adressen te behouden, is dit wat ze doen.

1. Wanneer ze replicatie inschakelen, geven ze op dat machines moeten worden gerepliceerd naar het **Azure-netwerk.**
2. Ze maken **herstelnetwerk** in Azure. Deze VNet weerspiegelt het subnet 192.168.1.0/24 in hun on-premises netwerk.
3. Woodgrove zet een [VNet-to-VNet-verbinding](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) op tussen de twee netwerken. 

    > [!NOTE]
    > Afhankelijk van de toepassingsvereisten kan een VNet-naar-VNet-verbinding worden ingesteld voordat de failover is mislukt, omdat een handmatige stap/gescripte stap/Azure-automatiseringsrunbook in een [herstelplan voor](site-recovery-create-recovery-plans.md)siteherstel of nadat failover is voltooid.

4. Voordat failover, op de machine eigenschappen in Site Recovery, stellen ze het doel IP-adres op het adres van de on-premises machine, zoals beschreven in de volgende procedure.
5. Na failover worden de Azure VM's gemaakt met hetzelfde IP-adres. Woodgrove maakt verbinding van **Azure Network** naar **Recovery Network** VNet met VNet-peering (met transitconnectiviteit ingeschakeld).
6. On-premises moet Woodgrove netwerkwijzigingen aanbrengen, inclusief het wijzigen van routes om aan te geven dat 192.168.1.0/24 is verhuisd naar Azure.  

**Infrastructuur vóór failover**

![Vóór subnetfailover](./media/site-recovery-network-design/network-design7.png)


**Infrastructuur na failover**

![Na subnetfailover](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>Doelnetwerkinstellingen instellen

Geef vóór failover de netwerkinstellingen en het IP-adres voor de doel-Azure-vm op.

1.  Selecteer in de kluis Recovery Services -> **gerepliceerde items**de on-premises machine.
2. Klik op de pagina **Gegevenskracht en netwerk** voor de machine op **Bewerken**om netwerk- en adapterinstellingen voor de doel-Azure-vm te configureren.
3. Selecteer **in netwerkeigenschappen**het doelnetwerk waarin de Azure VM zich bevindt wanneer deze is gemaakt na een failover.
4. Configureer **in netwerkinterfaces**de netwerkadapters in het doelnetwerk. Siteherstel toont standaard alle gedetecteerde NIC's op de on-premises machine.
    - In **doelnetwerkinterfacetype** u elke NIC instellen als **Primair,** **Secundair**of **Niet maken** als u die specifieke NIC niet nodig hebt in het doelnetwerk. Eén netwerkadapter moet worden ingesteld als primair voor failover. Houd er rekening mee dat het wijzigen van het doelnetwerk van invloed is op alle NIC's voor de Azure VM.
    - Klik op de NIC-naam om het subnet op te geven waarin de Azure VM wordt geïmplementeerd.
    - Overschrijven **Dynamic** met het privé-IP-adres dat u wilt toewijzen aan de doelgroep van Azure VM. Als een IP-adres niet is opgegeven, wijst Site recovery het volgende beschikbare IP-adres in het subnet toe aan de NIC bij failover.
    - [Meer informatie](site-recovery-manage-network-interfaces-on-premises-to-azure.md) over het beheren van NIC's voor on-premises failover naar Azure.


## <a name="get-new-ip-addresses"></a>Nieuwe IP-adressen oppakken

In dit scenario krijgt de Azure VM een nieuw IP-adres na een failover. Een DNS-update om records bij te werken voor mislukte gegevens over machines om naar het IP-adres van de Azure VM te wijzen.



## <a name="next-steps"></a>Volgende stappen
[Meer informatie over](site-recovery-active-directory.md) het repliceren van on-premises Active Directory en DNS naar Azure.


