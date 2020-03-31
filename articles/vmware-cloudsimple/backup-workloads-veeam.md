---
title: Azure VMware-oplossing door CloudSimple - Back-up van virtuele workloadmachines op Private Cloud met Veeam
description: Beschrijft hoe u een back-up maken van uw virtuele machines die worden uitgevoerd in een CloudSimple Private Cloud in Azure met Veeam B&R 9.5
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3262841efb9109b1de24fe501ea0a7bea0dd612d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025126"
---
# <a name="back-up-workload-vms-on-cloudsimple-private-cloud-using-veeam-br"></a>Back-upworkload VM's op CloudSimple Private Cloud met Veeam B&R

In deze handleiding wordt beschreven hoe u een back-up maken van uw virtuele machines die worden uitgevoerd in een CloudSimple Private Cloud in Azure met Veeam B&R 9.5.

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>Over de Veeam back-up en recovery oplossing

De Veeam-oplossing bevat de volgende componenten.

**Back-upserver**

De back-upserver is een Windows-server (VM) die fungeert als het controlecentrum voor Veeam en deze functies uitvoert: 

* Coördineert back-up-, replicatie-, herstelverificatie- en hersteltaken
* Hiermee bepaalt u taakplanning en toewijzing van resources
* Hiermee u back-upinfrastructuurcomponenten instellen en beheren en globale instellingen voor de back-upinfrastructuur opgeven

**Proxyservers**

Proxyservers worden geïnstalleerd tussen de back-upserver en andere onderdelen van de back-upinfrastructuur. Zij beheren de volgende functies:

* Ophalen van VM-gegevens uit de productieopslag
* Compressie
* Ontdubbeling
* Versleuteling
* Overdracht van gegevens naar de back-upopslagplaats

**Back-upopslagplaats**

De back-uprepository is de opslaglocatie waar Veeam back-upbestanden, VM-kopieën en metagegevens voor gerepliceerde VM's bewaart.  De repository kan een Windows- of Linux-server zijn met lokale schijven (of gemonteerde NFS/SMB) of een hardwareopslagdeduplicatietoestel.

### <a name="veeam-deployment-scenarios"></a>Veeam-implementatiescenario's
U Azure gebruiken om een back-upopslagplaats en een opslagdoel voor back-up en archivering op lange termijn te bieden. Al het back-upnetwerkverkeer tussen VM's in de Private Cloud en de back-uprepository in Azure gaat over een koppeling met hoge bandbreedte met lage latentie. Replicatieverkeer in verschillende regio's reist via het interne Azure-backplane-netwerk, waardoor de bandbreedtekosten voor gebruikers worden verlaagd.

**Basisimplementatie**

Voor omgevingen met minder dan 30 TB om een back-up te maken, raadt CloudSimple de volgende configuratie aan:

* Veeam back-upserver en proxyserver geïnstalleerd op dezelfde VM in de Private Cloud.
* Een op Linux gebaseerde primaire back-uprepository in Azure geconfigureerd als doel voor back-uptaken.
* `azcopy`wordt gebruikt om de gegevens uit de primaire back-upopslagplaats te kopiëren naar een Azure blob-container die naar een andere regio wordt gerepliceerd.

![Basisimplementatiescenario's](media/veeam-basicdeployment.png)

**Geavanceerde implementatie**

Voor omgevingen met meer dan 30 TB om een back-up te maken, raadt CloudSimple de volgende configuratie aan:

* Eén proxyserver per knooppunt in het vSAN-cluster, zoals aanbevolen door Veeam.
* Windows-gebaseerde primaire back-uprepository in de Private Cloud om vijf dagen aan gegevens in de cache te plaatsen voor snelle herstelt.
* Linux back-up repository in Azure als een doel voor back-up kopie taken voor langere duur retentie. Deze repository moet worden geconfigureerd als een scale-out back-up repository.
* `azcopy`wordt gebruikt om de gegevens uit de primaire back-upopslagplaats te kopiëren naar een Azure blob-container die naar een andere regio wordt gerepliceerd.

![Basisimplementatiescenario's](media/veeam-advanceddeployment.png)

In het vorige cijfer wordt opgemerkt dat de back-upproxy een VM is met Hot Add-toegang tot vm-werkbelastingschijven in het vSAN-gegevensarchief. Veeam maakt gebruik van Virtual Appliance back-up proxy transport mode voor vSAN.

## <a name="requirements-for-veeam-solution-on-cloudsimple"></a>Vereisten voor Veeam-oplossing op CloudSimple

De Veeam-oplossing vereist dat u het volgende doet:

* Geef uw eigen Veeam-licenties op.
* Implementeer en beheer Veeam om een back-up te maken van de workloads die worden uitgevoerd in de CloudSimple Private Cloud.

Deze oplossing biedt u volledige controle over de Veeam back-up tool en biedt de keuze om de native Veeam-interface of de Veeam vCenter-plug-in te gebruiken om VM-back-uptaken te beheren.

Als u een bestaande Veeam-gebruiker bent, u de sectie op Veeam Solution Components overslaan en rechtstreeks naar [Veeam Deployment Scenarios](#veeam-deployment-scenarios)gaan.

## <a name="install-and-configure-veeam-backups-in-your-cloudsimple-private-cloud"></a>Veeam-back-ups installeren en configureren in uw CloudSimple Private Cloud

In de volgende secties wordt beschreven hoe u een Veeam-back-upoplossing voor uw CloudSimple Private Cloud installeert en configureert.

Het implementatieproces bestaat uit de volgende stappen:

1. [vCenter-gebruikersinterface: infrastructuurservices instellen in uw private cloud](#vcenter-ui-set-up-infrastructure-services-in-your-private-cloud)
2. [CloudSimple-portal: Private Cloud-netwerken instellen voor Veeam](#cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam)
3. [CloudSimple-portal: Bevoegdheden escaleren](#cloudsimple-private-cloud-escalate-privileges-for-cloudowner)
4. [Azure-portal: verbind uw virtuele netwerk met de Private Cloud](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
5. [Azure-portal: een back-upopslagplaats maken in Azure](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
6. [Azure-portal: Azure blob-opslag configureren voor het bewaren van gegevens op lange termijn](#configure-azure-blob-storage-for-long-term-data-retention)
7. [vCenter-gebruikersinterface van Private Cloud: Veeam B&R installeren](#vcenter-console-of-private-cloud-install-veeam-br)
8. [Veeam Console: Veeam Backup & Recovery software configureren](#veeam-console-install-veeam-backup-and-recovery-software)
9. [CloudSimple-portal: Veeam-toegangs- en de-escalerende bevoegdheden instellen](#cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>Voordat u begint

U bent verplicht voordat u begint met de implementatie van Veeam:

* Een Azure-abonnement dat eigendom is van u
* Een vooraf gemaakte Azure-brongroep
* Een virtueel Azure-netwerk in uw abonnement
* Een Azure Storage-account
* Een [Private Cloud](create-private-cloud.md) die is gemaakt met behulp van de CloudSimple-portal.  

Tijdens de implementatiefase zijn de volgende punten nodig:

* VMware-sjablonen voor Windows om Veeam te installeren (zoals Windows Server 2012 R2 - 64-bits afbeelding)
* Eén beschikbare VLAN die is geïdentificeerd voor het back-upnetwerk
* CIDR van het subnet dat moet worden toegewezen aan het back-upnetwerk
* Veeam 9.5 u3 installable media (ISO) geüpload naar de vSAN datastore van de Private Cloud

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-private-cloud"></a>vCenter-gebruikersinterface: infrastructuurservices instellen in uw private cloud

Configureer infrastructuurservices in de Private Cloud om het eenvoudig te maken om uw workloads en hulpprogramma's te beheren.

* U een externe identiteitsprovider toevoegen zoals beschreven in [VCenter-identiteitsbronnen instellen om Active Directory te gebruiken](set-vcenter-identity.md) als een van de volgende wijzigingen is toegepast:

  * U wilt gebruikers identificeren vanuit uw on-premises Active Directory (AD) in uw private cloud.
  * U wilt een AD instellen in uw private cloud voor alle gebruikers.
  * U wilt Azure AD gebruiken.
* Als u IP-adreszoeking, IP-adresbeheer en naamomzettingsservices wilt bieden voor uw workloads in de Private Cloud, stelt u een DHCP- en DNS-server in zoals beschreven in [DNS- en DHCP-toepassingen en -workloads instellen in uw CloudSimple Private Cloud.](dns-dhcp-setup.md)

### <a name="cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam"></a>CloudSimple Private Cloud: Private Cloud-netwerken instellen voor Veeam

Krijg toegang tot de CloudSimple-portal om Private Cloud-netwerken in te stellen voor de Veeam-oplossing.

Maak een VLAN voor het back-upnetwerk en wijs deze een subnet CIDR toe. Zie [VLAN's/subnetten maken en beheren voor](create-vlan-subnet.md)instructies.

Maak firewallregels tussen het beheersubnet en het back-upnetwerk om netwerkverkeer toe te staan op poorten die door Veeam worden gebruikt. Zie het Veeam-onderwerp [Gebruikte poorten](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95). Zie [Firewalltabellen en -regels instellen](firewall.md)voor instructies voor het maken van firewallregels.

In de volgende tabel vindt u een poortlijst.

| Pictogram | Beschrijving | Pictogram | Beschrijving |
| ------------ | ------------- | ------------ | ------------- |
| Back-upserver  | vCenter  | HTTPS / TCP  | 443 |
| Back-upserver <br> *Vereist voor het implementeren van Veeam Backup & Replicatiecomponenten* | Back-upproxy  | TCP/UDP  | 135, 137 tot 139 en 445 |
    | Back-upserver   | DNS  | UDP  | 53  | 
    | Back-upserver   | Veeam Update-meldingsserver  | TCP  | 80  | 
    | Back-upserver   | Veeam-licentieupdateserver  | TCP  | 443  | 
    | Back-upproxy   | vCenter |   |   | 
    | Back-upproxy  | Linux Backup Repository   | TCP  | 22  | 
    | Back-upproxy  | Windows Backup Repository  | TCP  | 49152 - 65535   | 
    | Back-uprepository  | Back-upproxy  | TCP  | 2500 -5000  | 
    | Bronback-uprepository<br> *Wordt gebruikt voor back-upkopietaken*  | Target Backup Repository  | TCP  | 2500 - 5000  | 

Maak firewallregels tussen het subnet van de werkbelasting en het back-upnetwerk zoals beschreven in [Firewalltabellen en -regels instellen.](firewall.md)  Voor toepassingsbewuste back-up en herstel moeten [extra poorten](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95) worden geopend op de workload VM's die specifieke toepassingen hosten.

Standaard biedt CloudSimple een 1Gbps ExpressRoute-koppeling. Voor grotere omgevingsformaten kan een koppeling met een hogere bandbreedte nodig zijn. Neem contact op met Azure-ondersteuning voor meer informatie over koppelingen met een hogere bandbreedte.

Als u de installatie wilt voortzetten, hebt u de autorisatiesleutel en peer circuit URI en toegang tot uw Azure-abonnement nodig.  Deze informatie is beschikbaar op de pagina Virtual Network Connection in de CloudSimple-portal. Zie [Peering-informatie voor Azure virtual network voor CloudSimple-verbinding verkrijgen voor](virtual-network-connection.md)instructies. Als u problemen hebt met het verkrijgen van de informatie, neem [dan contact op met de ondersteuning.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

### <a name="cloudsimple-private-cloud-escalate-privileges-for-cloudowner"></a>CloudSimple Private Cloud: Bevoegdheden voor cloudeigenaar escaleren

De standaardgebruiker 'cloudowner' heeft niet voldoende bevoegdheden in het VCenter voor Private Cloud om VEEAM te installeren, dus de vCenter-bevoegdheden van de gebruiker moeten worden geëscaleerd. Zie [Bevoegdheden escaleren voor](escalate-private-cloud-privileges.md)meer informatie .

### <a name="azure-portal-connect-your-virtual-network-to-the-private-cloud"></a>Azure-portal: verbind uw virtuele netwerk met de Private Cloud

Verbind uw virtuele netwerk met de Private Cloud door de instructies in Azure Virtual Network Connection te volgen [met ExpressRoute.](azure-expressroute-connection.md)

### <a name="azure-portal-create-a-backup-repository-vm"></a>Azure-portal: een VM voor back-upopslagplaatsen maken

1. Maak een standaard D2 v3 VM met (2 vCPU's en 8 GB geheugen).
2. Selecteer de afbeelding op centos 7.4.
3. Configureer een netwerkbeveiligingsgroep (NSG) voor de VM. Controleer of de VM geen openbaar IP-adres heeft en niet bereikbaar is via het openbare internet.
4. Maak een gebruikersnaam en wachtwoord gebaseerd gebruikersaccount voor de nieuwe VM. Zie Een [virtuele Linux-machine maken in de Azure-portal](../virtual-machines/linux/quick-create-portal.md)voor instructies.
5. Maak 1x512 GiB standaard HDD en bevestig deze aan de repository VM.  Zie Een [beheerde gegevensschijf koppelen aan een Windows-vm in de Azure-portal](../virtual-machines/windows/attach-managed-disk-portal.md)voor instructies.
6. [Maak een XFS-volume op de beheerde schijf](https://www.digitalocean.com/docs/volumes/how-to/). Meld u aan bij de VM met behulp van de eerder genoemde referenties. Voer het volgende script uit om een logisch volume te maken, [partition](https://www.digitalocean.com/docs/volumes/how-to/partition/) voeg de schijf eraan toe, maak een XFS-bestandssysteempartitie en [monteer](https://www.digitalocean.com/docs/volumes/how-to/mount/) de partitie onder het /backup1-pad.

    Voorbeeldscript:

    ```
    sudo pvcreate /dev/sdc
    sudo vgcreate backup1 /dev/sdc
    sudo lvcreate -n backup1 -l 100%FREE backup1
    sudo mkdir -p /backup1
    sudo chown veeamadmin /backup1
    sudo chmod 775 /backup1
    sudo mkfs.xfs -d su=64k -d sw=1 -f /dev/mapper/backup1-backup1
    sudo mount -t xfs /dev/mapper/backup1-backup1 /backup1
    ```

7. Stel /backup1 bloot als een NFS-mountpunt naar de Veeam-back-upserver die in de Private Cloud wordt uitgevoerd. Zie voor instructies het artikel Hoe [stel je een NFS Mount in op CentOS 6](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6). Gebruik deze nfs-sharenaam wanneer u de back-uprepository configureert in de Veeam-back-upserver.

8. Filterregels configureren in de NSG voor de VM voor back-uprepository om al het netwerkverkeer van en naar de VM expliciet toe te staan.

> [!NOTE]
> Veeam Backup & Replication gebruikt het SSH-protocol om te communiceren met Linux back-uprepositories en vereist het SCP-hulpprogramma voor Linux-repositories. Controleer of de SSH-daemon correct is geconfigureerd en of SCP beschikbaar is op de Linux-host.

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>Azure blob-opslag configureren voor het bewaren van gegevens op lange termijn

1. Maak een GPv2 (General Purpose Storage-account) van standaardtype en een blobcontainer zoals beschreven in de Microsoft-video [Aan de slag met Azure Storage.](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
2. Maak een Azure-opslagcontainer, zoals beschreven in de referentie [Container maken.](https://docs.microsoft.com/rest/api/storageservices/create-container)
2. Download `azcopy` het hulpprogramma voor de opdrachtregel voor Linux van Microsoft. U de volgende opdrachten in de bashshell in CentOS 7.5 gebruiken.

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. Gebruik `azcopy` de opdracht om back-upbestanden van en naar de blobcontainer te kopiëren.  Zie [Gegevens overbrengen met AzCopy op Linux](../storage/common/storage-use-azcopy-linux.md) voor gedetailleerde opdrachten.

### <a name="vcenter-console-of-private-cloud-install-veeam-br"></a>vCenter console van Private Cloud: Installeer Veeam B&R

Toegang tot vCenter vanuit uw Private Cloud om een Veeam-serviceaccount aan te maken, Veeam B&R 9.5 te installeren en Veeam te configureren met behulp van het serviceaccount.

1. Maak een nieuwe rol met de naam 'Veeam Backup Role' en wijs deze de benodigde machtigingen toe, zoals aanbevolen door Veeam. Zie voor meer informatie het Veeam-onderwerp [Vereiste machtigingen](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95).
2. Maak een nieuwe 'Veeam User Group'-groep in vCenter en wijs deze toe aan de 'Veeam Backup Role'.
3. Maak een nieuwe 'Veeam Service Account' gebruiker en voeg deze toe aan de 'Veeam User Group'.

    ![Een Veeam-serviceaccount maken](media/veeam-vcenter01.png)

4. Maak een gedistribueerde poortgroep in vCenter met behulp van het back-upnetwerk VLAN. Bekijk de VMware-video [Een gedistribueerde poortgroep maken in de vSphere-webclient](https://www.youtube.com/watch?v=wpCd5ZbPOpA)voor meer informatie.
5. Maak de VM's voor de Veeam-back-up- en proxyservers in vCenter volgens de [Veeam-systeemvereisten.](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95) U Windows 2012 R2 of Linux gebruiken. Zie Vereisten [voor het gebruik van Linux back-uprepositories voor](https://www.veeam.com/kb2216)meer informatie.
6. Monteer de installeerbare Veeam ISO als cdrom-apparaat in de Veeam-back-upserver VM.
7. Installeer [Veeam B&R 9.5u3](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95) in een Windows 2012 R2 VM met behulp van een RDP-sessie op de Windows 2012 R2-machine (het doel voor de Veeam-installatie).
8. Zoek het interne IP-adres van de Veeam-back-upserver VM en configureer het IP-adres als statisch in de DHCP-server. De exacte stappen die hiervoor nodig zijn, zijn afhankelijk van de DHCP-server. In het Netgate-artikel worden bijvoorbeeld <a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">statische DHCP-toewijzingen</a> uitgelegd hoe u een DHCP-server configureert met een pfSense-router.

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Veeam console: Installeer Veeam back-up en recovery software

Configureer veeam back-up- en herstelsoftware via de Veeam-console. Zie [Veeam Backup & Replicatie v9 - Installatie en implementatie](https://www.youtube.com/watch?v=b4BqC_WXARk)voor meer informatie.

1. VMware vSphere toevoegen als beheerde serveromgeving. Geef dereferenties op van het Veeam-serviceaccount dat u aan het begin van vCenter Console of Private Cloud hebt [gemaakt: Installeer Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br).

    * Gebruik standaardinstellingen voor belastingscontrole en standaard geavanceerde instellingen.
    * Stel de locatie van de bevestigingsserver in als de back-upserver.
    * Wijzig de locatie van de configuratieback-up voor de Veeam-server in de externe opslagplaats.

2. Voeg de Linux-server in Azure toe als back-upopslagplaats.

    * Gebruik standaardinstellingen voor belastingscontrole en voor de geavanceerde instellingen. 
    * Stel de locatie van de bevestigingsserver in als de back-upserver.
    * Wijzig de locatie van de configuratieback-up voor de Veeam-server in de externe opslagplaats.

3. Versleuteling van configuratieback-up inschakelen met **home> configuration backup Settings**.

4. Voeg een Windows-server-VM toe als proxyserver voor de VMware-omgeving. Met behulp van 'Verkeersregels' voor een proxy, versleutelen back-upgegevens via de draad.

5. Back-uptaken configureren.
    * Als u back-uptaken wilt configureren, volgt u de instructies voor [Het maken van een back-uptaak.](https://www.youtube.com/watch?v=YHxcUFEss4M)
    * Versleuteling van back-upbestanden inschakelen onder **Geavanceerde instellingen > Opslag**.

6. Back-upkopietaken configureren.

    * Als u back-upkopietaken wilt configureren, volgt u de instructies in de video [Een taak voor back-upkopiemaken](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s).
    * Versleuteling van back-upbestanden inschakelen onder **Geavanceerde instellingen > Opslag**.

### <a name="cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges"></a>CloudSimple-portal: Veeam-toegangs- en de-escalerende bevoegdheden instellen
Maak een openbaar IP-adres voor de Veeam-back-up- en herstelserver. Zie Openbare [IP-adressen toewijzen](public-ips.md)voor instructies .

Maak een firewallregel waarmee de Veeam-back-upserver een uitgaande verbinding met de Veeam-website kan maken voor het downloaden van updates/patches op TCP-poort 80. Zie [Firewalltabellen en -regels instellen](firewall.md)voor instructies.

Zie [Bevoegdheden de-escaleren](escalate-private-cloud-privileges.md#de-escalate-privileges)als u bevoegdheden wilt de-escaleren.

## <a name="references"></a>Verwijzingen

### <a name="cloudsimple-references"></a>CloudSimple-verwijzingen

* [Een privécloud maken](create-private-cloud.md)
* [VLAn's/Subnetten maken en beheren](create-vlan-subnet.md)
* [vCenter-identiteitsbronnen](set-vcenter-identity.md)
* [Workload DNS en DHCP Setup](dns-dhcp-setup.md)
* [Bevoegdheden escaleren](escalate-privileges.md)
* [Firewalltabellen en -regels instellen](firewall.md)
* [Machtigingen voor private cloud](learn-private-cloud-permissions.md)
* [Openbare IP-adressen toewijzen](public-ips.md)

### <a name="veeam-references"></a>Veeam Referenties

* [Gebruikte poorten](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [Vereiste machtigingen](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [Systeemvereisten](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [Veeam-back-up & replicatie installeren](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [Vereiste modules en machtigingen voor Multi-OS FLR- en Repository-ondersteuning voor Linux](https://www.veeam.com/kb2216)
* [Veeam Backup & Replicatie v9 - Installatie en implementatie - Video](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [Veeam v9 Een back-uptaak maken - Video](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam v9 Een back-upkopietaak maken - Video](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Azure-verwijzingen

* [Een virtuele netwerkgateway voor ExpressRoute configureren met behulp van de Azure-portal](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [Een VNet aansluiten op een circuit - ander abonnement](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [Een Virtuele Linux-machine maken in de Azure-portal](../virtual-machines/linux/quick-create-portal.md)
* [Een beheerde gegevensschijf koppelen aan een Windows-vm in de Azure-portal](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Aan de slag met Azure Storage - Video](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [Container maken](https://docs.microsoft.com/rest/api/storageservices/create-container)
* [Gegevens overdragen met AzCopy voor Linux](../storage/common/storage-use-azcopy-linux.md)

### <a name="vmware-references"></a>VMware-verwijzingen

* [Een gedistribueerde poortgroep maken in de vSphere-webclient - Video](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>Andere verwijzingen

* [Een XFS-volume maken op de beheerde schijf - RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [Hoe stel je een NFS-mount in op CentOS 7 - HowToForge](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [De DHCP-server configureren - Netgate](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
