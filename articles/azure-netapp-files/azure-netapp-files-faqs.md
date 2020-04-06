---
title: Veelgestelde vragen over Azure NetApp-bestanden | Microsoft Documenten
description: Beantwoordt veelgestelde vragen over Azure NetApp-bestanden.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: b-juche
ms.openlocfilehash: aebc669a90511e48ddd2a7876553948c04b97710
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667813"
---
# <a name="faqs-about-azure-netapp-files"></a>Veelgestelde vragen over Azure NetApp-bestanden

In dit artikel worden veelgestelde vragen (veelgestelde vragen) over Azure NetApp-bestanden beantwoord. 

## <a name="networking-faqs"></a>Veelgestelde vragen over netwerken

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Gaat het NFS-gegevenspad over het internet?  

Nee. Het NFS-gegevenspad gaat niet over internet. Azure NetApp Files is een Azure native service die wordt geïmplementeerd in het Azure Virtual Network (VNet) waar de service beschikbaar is. Azure NetApp Files maakt gebruik van een gedelegeerd subnet en voorziet een netwerkinterface rechtstreeks op het VNet. 

Zie [Richtlijnen voor de netwerkplanning van Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) voor meer informatie.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Kan ik een VNet verbinden dat ik al heb gemaakt met de Azure NetApp Files-service?

Ja, u VNets die u hebt gemaakt, verbinden met de service. 

Zie [Richtlijnen voor de netwerkplanning van Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) voor meer informatie.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Kan ik een NFS-volume azure NetApp-bestanden monteren met dns-fqdn-naam?

Ja, dat kan, als u de vereiste DNS-vermeldingen maakt. Azure NetApp Files levert het service-IP voor het ingerichte volume. 

> [!NOTE] 
> Azure NetApp Files kunnen indien nodig extra IP's voor de service implementeren.  DNS-vermeldingen moeten mogelijk periodiek worden bijgewerkt.

## <a name="security-faqs"></a>Veelgestelde vragen over beveiliging

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Kan het netwerkverkeer tussen de Azure VM en de opslag worden versleuteld?

Gegevensverkeer (verkeer van de NFSv3-, NFSv4.1- of SMBv3-client naar Azure NetApp Files-volumes) is niet versleuteld. Het verkeer van een Azure VM (met een NFS- of SMB-client) naar Azure NetApp-bestanden is echter net zo veilig als elk ander Azure-VM-naar-VM-verkeer. Dit verkeer is lokaal naar het Azure-datacenternetwerk. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Kan de opslag in rust worden versleuteld?

Alle Azure NetApp-bestanden worden versleuteld met de FIPS 140-2-standaard. Alle sleutels worden beheerd door de Azure NetApp Files-service. 

### <a name="how-are-encryption-keys-managed"></a>Hoe worden encryptiesleutels beheerd? 

Sleutelbeheer voor Azure NetApp-bestanden wordt door de service beheerd. Voor elk volume wordt een unieke XTS-AES-256-gegevensversleutelingssleutel gegenereerd. Een sleutelhiërarchie voor versleuteling wordt gebruikt om alle volumesleutels te versleutelen en te beveiligen. Deze versleutelingssleutels worden nooit weergegeven of gerapporteerd in een onversleutelde indeling. Versleutelingssleutels worden onmiddellijk verwijderd wanneer een volume wordt verwijderd.

Ondersteuning voor door gebruikers beheerde sleutels (Bring Your Own Keys) met Azure Dedicated HSM is op gecontroleerde basis beschikbaar in de regio's US East, US West2 en US South Central.  U toegang **anffeedback@microsoft.com**aanvragen op. Aangezien er capaciteit beschikbaar is, worden aanvragen goedgekeurd.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Kan ik de NFS-exportbeleidsregels configureren om de toegang tot het azure NetApp Files-servicemount-doel te beheren?


Ja, u maximaal vijf regels configureren in één NFS-exportbeleid.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Ondersteunt Azure NetApp Files netwerkbeveiligingsgroepen?

Nee, momenteel u netwerkbeveiligingsgroepen niet toepassen op het gedelegeerde subnet van Azure NetApp-bestanden of de netwerkinterfaces die door de service zijn gemaakt.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Kan ik Azure IAM gebruiken met Azure NetApp-bestanden?

Ja, Azure NetApp Files ondersteunt RBAC-functies met Azure IAM.

## <a name="performance-faqs"></a>Prestatieveelgestelde vragen

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Wat moet ik doen om de prestaties van Azure NetApp-bestanden te optimaliseren of af te stemmen?

U de volgende acties uitvoeren volgens de prestatievereisten: 
- Zorg ervoor dat de virtuele machine op de juiste manier is gegroot.
- Versnelde netwerken inschakelen voor de VM.
- Selecteer het gewenste serviceniveau en de gewenste grootte voor de capaciteitsgroep.
- Maak een volume met de gewenste quotumgrootte voor de capaciteit en prestaties.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Hoe converteer ik serviceniveaus op basis van doorvoer van Azure NetApp-bestanden naar IOPS?

U MB/s converteren naar IOPS met de volgende formule:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Hoe wijzig ik het serviceniveau van een volume?

Het wijzigen van het serviceniveau van een volume wordt momenteel niet ondersteund.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Hoe controleer ik de prestaties van Azure NetApp Files?

Azure NetApp Files biedt statistieken over volumeprestaties. U Azure Monitor ook gebruiken voor het bewaken van gebruiksstatistieken voor Azure NetApp-bestanden.  Zie [Statistieken voor Azure NetApp-bestanden](azure-netapp-files-metrics.md) voor de lijst met prestatiestatistieken voor Azure NetApp-bestanden.

## <a name="nfs-faqs"></a>VEELGESTELDE NFS-veelgestelde vragen

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Ik wil dat een volume automatisch wordt gemonteerd wanneer een Azure VM wordt gestart of opnieuw opgestart.  Hoe configureer ik mijn host voor permanente NFS-volumes?

Als u een NFS-volume automatisch wilt monteren bij het `/etc/fstab` starten of opnieuw opstarten van de VM, voegt u een vermelding toe aan het bestand op de host. 

Zie [Een volume voor virtuele Windows- of Linux-machines monteren of demonteren](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) voor meer informatie.  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Waarom wordt in de opdracht DF op nfs-client niet de ingerichte volumegrootte weergegeven?

De volumegrootte die in DF wordt gerapporteerd, is de maximale grootte van het Volume van Azure NetApp-bestanden. De grootte van het volume azure NetApp-bestanden in DF-opdracht is niet een afspiegeling van het quotum of de grootte van het volume.  U de volumegrootte of quota van Azure NetApp Files krijgen via de Azure-portal of de API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Welke NFS-versie ondersteunt Azure NetApp Files?

Azure NetApp Files ondersteunt NFSv3 en NFSv4.1. U [een volume maken](azure-netapp-files-create-volumes.md) met een NFS-versie. 

### <a name="how-do-i-enable-root-squashing"></a>Hoe kan ik wortelpletten inschakelen?

Root squashing wordt momenteel niet ondersteund.

## <a name="smb-faqs"></a>Veelgestelde vragen aan het MKB

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Is een Active Directory-verbinding vereist voor SMB-toegang? 

Ja, u moet een Active Directory-verbinding maken voordat u een SMB-volume implementeert. De opgegeven domeincontrollers moeten toegankelijk zijn via het gedelegeerde subnet van Azure NetApp-bestanden voor een geslaagde verbinding.  Zie [Een SMB-volume maken](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb) voor meer informatie. 

### <a name="how-many-active-directory-connections-are-supported"></a>Hoeveel Active Directory-verbindingen worden ondersteund?

Azure NetApp-bestanden bieden geen ondersteuning voor meerdere AD-verbindingen (Active Directory) in één *regio,* zelfs niet als de AD-verbindingen zich in verschillende NetApp-accounts bevinden. U echter meerdere AD-verbindingen in één *abonnement*hebben, zolang de AD-verbindingen zich in verschillende regio's bevinden. Als u meerdere AD-verbindingen in één regio nodig hebt, u hiervoor afzonderlijke abonnementen gebruiken. 

Een AD-verbinding is geconfigureerd per NetApp-account; de AD-verbinding is alleen zichtbaar via het NetApp-account waarin het is gemaakt.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Ondersteunt Azure NetApp Files Azure Active Directory? 

Zowel [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) als Active Directory Domain Services [(AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) worden ondersteund. U bestaande Active Directory-domeincontrollers gebruiken met Azure NetApp-bestanden. Domeincontrollers kunnen zich in Azure bevinden als virtuele machines of on-premises via ExpressRoute of S2S VPN. Azure NetApp Files biedt op dit moment geen ondersteuning voor AD join voor [Azure Active Directory.](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/)

Als u Azure NetApp-bestanden gebruikt met Azure Active Directory `OU=AADDC Computers` Domain Services, wordt het pad van de organisatie-eenheid geopend wanneer u Active Directory configureert voor uw NetApp-account.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Welke versies van Windows Server Active Directory worden ondersteund?

Azure NetApp Files ondersteunt Windows Server 2008r2SP1-2019-versies van Active Directory Domain Services.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Waarom wordt de beschikbare ruimte op mijn MKB-client niet weergegeven in de ingerichte grootte?

De volumegrootte die door de SMB-client wordt gerapporteerd, is de maximale grootte van het Volume van Azure NetApp-bestanden. De grootte van het Azure NetApp-bestandenvolume zoals weergegeven op de SMB-client, weerspiegelt niet het quotum of de grootte van het volume. U de volumegrootte of quota van Azure NetApp Files krijgen via de Azure-portal of de API.

<!--
### Does Azure NetApp Files support Kerberos encryption?

Yes, by default, Azure NetApp Files supports both AES-128 and AES-256 encryption for traffic between the service and the targeted Active Directory domain controllers. See [Create an SMB volume for Azure NetApp Files](azure-netapp-files-create-volumes-smb.md) for requirements. 
-->

<!--
### Does Azure NetApp Files support LDAP signing? 

Yes, Azure NetApp Files supports LDAP signing by default. This functionality enables secure LDAP lookups between the Azure NetApp Files service and the user-specified [Active Directory Domain Services domain controllers](https://docs.microsoft.com/windows/win32/ad/active-directory-domain-services). For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).
--> 

## <a name="capacity-management-faqs"></a>Gestelde vragen over capaciteitsbeheer

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Hoe controleer ik het gebruik voor capaciteitsgroep en volume van Azure NetApp-bestanden? 

Azure NetApp Files biedt statistieken over capaciteitspool en volumegebruik. U Azure Monitor ook gebruiken om het gebruik van Azure NetApp-bestanden te controleren. Zie [Statistieken voor Azure NetApp-bestanden](azure-netapp-files-metrics.md) voor meer informatie. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Kan ik Azure NetApp-bestanden beheren via Azure Storage Explorer?

Nee. Azure NetApp-bestanden worden niet ondersteund door Azure Storage Explorer.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Hoe bepaal ik of een map de limietgrootte nadert?

U `stat` de opdracht van een client gebruiken om te zien of een map de maximale groottelimiet (320 MB) nadert.

Voor een map van 320 MB is het aantal blokken 655360, waarbij elke blokgrootte 512 bytes bedraagt.  (Dat wil zeggen, 320x1024x1024/512.)  

Voorbeelden:

    [makam@cycrh6rtp07 ~]$ stat bin
    File: 'bin'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

    [makam@cycrh6rtp07 ~]$ stat tmp
    File: 'tmp'
    Size: 12288           Blocks: 24         IO Block: 65536  directory
 
    [makam@cycrh6rtp07 ~]$ stat tmp1
    File: 'tmp1'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

## <a name="data-migration-and-protection-faqs"></a>Veelgestelde vragen over gegevensmigratie en bescherming

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Hoe migreer ik gegevens naar Azure NetApp-bestanden?
Azure NetApp Files biedt NFS- en SMB-volumes.  U elk op bestanden gebaseerd kopieergereedschap gebruiken om gegevens naar de service te migreren. 

NetApp biedt een SaaS-gebaseerde oplossing, [NetApp Cloud Sync.](https://cloud.netapp.com/cloud-sync-service)  Met de oplossing u NFS- of SMB-gegevens repliceren naar NFS-export of SMB-export of SMB-shares van Azure NetApp Files. 

U ook een breed scala aan gratis tools gebruiken om gegevens te kopiëren. Voor NFS u hulpprogramma's voor workloads zoals [rsync](https://rsync.samba.org/examples.html) gebruiken om brongegevens te kopiëren en te synchroniseren naar een Azure NetApp-bestandenvolume. Voor SMB u [workloads robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) op dezelfde manier gebruiken.  Deze hulpprogramma's kunnen ook bestands- of mapmachtigingen repliceren. 

De vereisten voor gegevensmigratie van on-premises naar Azure NetApp-bestanden zijn als volgt: 

- Controleer of Azure NetApp-bestanden beschikbaar zijn in het doelazure-gebied.
- Netwerkconnectiviteit tussen de bron en het doeladres van Azure NetApp Files valideren. Gegevensoverdracht tussen on-premises en de Azure NetApp Files-service wordt ondersteund via ExpressRoute.
- Maak het doelvolume Azure NetApp-bestanden.
- Breng de brongegevens over naar het doelvolume met behulp van het hulpprogramma voor bestandskopie van de voorkeur.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Hoe maak ik een kopie van een Azure NetApp Files-volume in een ander Azure-gebied?
    
Azure NetApp Files biedt NFS- en SMB-volumes.  Elk hulpmiddel voor bestandskopieën kan worden gebruikt om gegevens tussen Azure-regio's te repliceren. 

NetApp biedt een SaaS gebaseerde oplossing, [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  Met de oplossing u NFS- of SMB-gegevens repliceren naar NFS-export of SMB-export of SMB-shares van Azure NetApp Files. 

U ook een breed scala aan gratis tools gebruiken om gegevens te kopiëren. Voor NFS u hulpprogramma's voor workloads zoals [rsync](https://rsync.samba.org/examples.html) gebruiken om brongegevens te kopiëren en te synchroniseren naar een Azure NetApp-bestandenvolume. Voor SMB u [workloads robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) op dezelfde manier gebruiken.  Deze hulpprogramma's kunnen ook bestands- of mapmachtigingen repliceren. 

De vereisten voor het repliceren van een Azure NetApp Files-volume naar een andere Azure-regio zijn als volgt: 
- Controleer of Azure NetApp-bestanden beschikbaar zijn in het doelazure-gebied.
- Netwerkconnectiviteit tussen VNets in elke regio valideren. Momenteel wordt globale peering tussen VNets niet ondersteund.  U verbinding maken tussen VNets door te koppelen met een ExpressRoute-circuit of via een S2S VPN-verbinding. 
- Maak het doelvolume Azure NetApp-bestanden.
- Breng de brongegevens over naar het doelvolume met behulp van het hulpprogramma voor bestandskopie van de voorkeur.

### <a name="is-migration-with-azure-data-box-supported"></a>Wordt migratie met Azure Data Box ondersteund?

Nee. Azure Data Box biedt momenteel geen ondersteuning voor Azure NetApp Files. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Wordt migratie met Azure Import/Export-service ondersteund?

Nee. Azure Import/Export-service biedt momenteel geen ondersteuning voor Azure NetApp-bestanden.

## <a name="next-steps"></a>Volgende stappen  

- [Veelgestelde vragen van Microsoft Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Veelgestelde vragen over het Virtuele Netwerk van Microsoft Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Een ondersteuningsaanvraag maken voor Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)
- [Veelgestelde vragen over de prestaties van het MKB voor Azure NetApp-bestanden](azure-netapp-files-smb-performance.md)
