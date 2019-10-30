---
title: Overwegingen voor Azure Files-netwerken | Microsoft Docs
description: Een overzicht van de netwerk opties voor Azure Files.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 596479652478bffb6d18a90fc53d5972b3839408
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73141792"
---
# <a name="azure-files-networking-considerations"></a>Azure Files-netwerk overwegingen 
U kunt op twee manieren verbinding maken met een Azure-bestands share:

- Rechtstreeks toegang tot de share via de SMB-of FileREST-protocollen. Dit toegangs patroon wordt voornamelijk gebruikt om zoveel mogelijk lokale servers te elimineren.
- Het maken van een cache van de Azure-bestands share op een on-premises server met Azure File Sync, en toegang tot de gegevens van de bestands share vanaf de on-premises server met het Protocol van Choice (SMB, NFS, FTPS, enzovoort) voor uw gebruiks voorbeeld. Dit toegangs patroon is handig omdat het het beste is voor zowel on-premises als voor de Cloud geschaalde en serverloze koppel bare Services, zoals Azure Backup.

In dit artikel wordt uitgelegd hoe u netwerken kunt configureren voor wanneer uw use-case aanroepen voor het rechtstreeks openen van de Azure-bestands share in plaats van Azure File Sync te gebruiken. Zie [Azure file sync proxy-en Firewall instellingen configureren](storage-sync-files-firewall-and-proxy.md)voor meer informatie over netwerk overwegingen voor een Azure file sync-implementatie.

## <a name="storage-account-settings"></a>Instellingen voor het opslag account
Een opslag account is een beheer constructie die een gedeelde opslag groep vertegenwoordigt, waarbij u meerdere bestands shares en andere opslag resources, zoals BLOB-containers of wacht rijen, kunt implementeren. In azure Storage-accounts worden twee basis sets instellingen beschikbaar voor het beveiligen van het netwerk: versleutelen van door Voer en firewalls en virtuele netwerken (VNets).

### <a name="encryption-in-transit"></a>Versleuteling in transit
Standaard hebben alle Azure Storage-accounts versleuteling in transit ingeschakeld. Dit betekent dat wanneer u een bestands share koppelt via SMB of toegang krijgt via het FileREST-Protocol (zoals via de Azure Portal, Power shell/CLI of Azure Sdk's), Azure Files alleen de verbinding toestaat als deze wordt gemaakt met SMB 3.0 + met versleuteling of HTTPS. Clients die geen ondersteuning bieden voor SMB 3,0 of clients die SMB 3,0 ondersteunen maar geen SMB-versleuteling, kunnen de Azure-bestands share niet koppelen als versleuteling in transit is ingeschakeld. Zie de gedetailleerde documentatie voor [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)en [Linux](storage-how-to-use-files-linux.md)voor meer informatie over welke besturings systemen SMB 3,0 met versleuteling ondersteunen. Alle huidige versies van de Power shell, CLI en Sdk's ondersteunen HTTPS.  

U kunt versleuteling voor een Azure Storage-account uitschakelen in door voer. Wanneer versleuteling is uitgeschakeld, wordt door Azure Files ook SMB 2,1, SMB 3,0 zonder versleuteling en niet-versleutelde FileREST-API-aanroepen via HTTP toegestaan. De belangrijkste reden om versleuteling in transit uit te scha kelen, is het ondersteunen van een verouderde toepassing die moet worden uitgevoerd op een ouder besturings systeem, zoals Windows Server 2008 R2 of een oudere Linux-distributie. Met Azure Files worden SMB 2,1-verbindingen alleen toegestaan binnen dezelfde Azure-regio als de Azure-bestands share. een SMB 2,1-client buiten de Azure-regio van de Azure-bestands share, zoals on-premises of in een andere Azure-regio, heeft geen toegang tot de bestands share.

Zie [veilige overdracht vereisen in azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)voor meer informatie over het door sturen van versleuteling.

### <a name="firewalls-and-virtual-networks"></a>Firewalls en virtuele netwerken 
Een firewall is een netwerk beleid waarmee aanvragen toegang hebben tot de Azure-bestands shares en andere opslag resources in uw opslag account. Wanneer een opslag account met de standaard netwerk instellingen wordt gemaakt, is het niet beperkt tot een specifiek netwerk en is het daarom toegankelijk via internet. Dit betekent niet dat iedereen op Internet toegang heeft tot de gegevens die zijn opgeslagen op de Azure-bestands shares die worden gehost in uw opslag account, maar dat het opslag account gemachtigde aanvragen van elk netwerk accepteert. Aanvragen kunnen worden geautoriseerd met een sleutel voor een opslag account, een SAS-token (Shared Access Signature) (alleen FileREST) of met een Active Directory gebruikers-principal. 

Het firewall beleid voor een opslag account kan worden gebruikt om de toegang tot bepaalde IP-adressen of bereiken of een virtueel netwerk te beperken. Over het algemeen beperkt de meeste firewall-beleids regels voor een opslag account netwerk toegang tot een virtueel netwerk. 

Een [virtueel netwerk](../../virtual-network/virtual-networks-overview.md), of VNet, is vergelijkbaar met een traditioneel netwerk dat u in uw eigen Data Center zou kunnen uitvoeren. U kunt hiermee een beveiligd communicatie kanaal maken voor uw Azure-resources, zoals Azure-bestands shares, Vm's, SQL-data bases, enzovoort. Net als een Azure-opslag account of een Azure-VM is een VNet een Azure-resource die in een resource groep is geïmplementeerd. Met extra netwerk configuratie kan Azure VNets ook worden verbonden met uw on-premises netwerken.

Wanneer resources, zoals een Azure-VM, worden toegevoegd aan een virtueel netwerk, is een virtuele netwerk interface (NIC) die is gekoppeld aan de virtuele machine beperkt tot specifiek het VNet. Dit is mogelijk omdat Azure-Vm's gevirtualiseerde computers zijn die natuurlijk Nic's hebben. Virtuele machines worden aangeboden als onderdeel van de infra structuur van Azure Infrastructure-as-a-service, of IaaS. Omdat Azure-bestands shares serverloze bestands shares zijn, hebben ze geen NIC om aan een VNet toe te voegen. Als onderdeel van de platform-as-a-service of PaaS van Azure wordt een andere manier Azure Files aangeboden. Azure biedt ondersteuning voor een concept voor PaaS Services, de zogeheten service-eind punten, waarmee een opslag account kan deel uitmaken van een VNet. Een service-eind punt maakt het mogelijk dat PaaS Services deel uitmaken van een virtueel netwerk. Zie [service-eind punten voor virtuele netwerken](../../virtual-network/virtual-network-service-endpoints-overview.md)voor meer informatie over service-eind punten.

Een opslag account kan worden toegevoegd aan een of meer virtuele netwerken. Zie [firewalls en virtuele netwerken voor Azure Storage configureren](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)voor meer informatie over het toevoegen van uw opslag account aan een virtueel netwerk of het configureren van andere firewall instellingen.

## <a name="azure-networking"></a>Azure-netwerken
Azure-Services met inbegrip van Azure Files kunnen standaard worden benaderd via internet. Omdat standaard verkeer naar uw opslag account wordt versleuteld (en SMB 2,1-koppelingen zijn nooit toegestaan buiten een Azure-regio), is er niets wat u kunt doen om toegang te krijgen tot uw Azure-bestands shares via internet. Op basis van het beleid of de unieke regelgeving van uw organisatie is het mogelijk dat u meer beperkend communicatie nodig hebt met Azure. Daarom biedt Azure verschillende manieren om te beperken hoe verkeer van buiten Azure wordt Azure Files. U kunt uw netwerk verder beveiligen bij het openen van uw Azure-bestands share met behulp van de volgende service aanbiedingen:

- [Azure VPN gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): een VPN-gateway is een specifiek type virtuele netwerk gateway dat wordt gebruikt voor het verzenden van versleuteld verkeer tussen een virtueel Azure-netwerk en een alternatieve locatie (zoals on-premises) via internet. Een Azure-VPN Gateway is een Azure-resource die kan worden geïmplementeerd in een resource groep naast de kant van een opslag account of andere Azure-resources. VPN-gateways bieden twee verschillende soorten verbindingen:
    - [Punt-naar-site-VPN-gateway verbindingen (P2S)](../../vpn-gateway/point-to-site-about.md) , die VPN-verbindingen tussen Azure en een afzonderlijke client zijn. Deze oplossing is vooral nuttig voor apparaten die geen deel uitmaken van het on-premises netwerk van uw organisatie, zoals telewerkers die hun Azure-bestands share willen kunnen koppelen aan thuis, in een café of in een hotel, terwijl u onderweg bent. Als u een P2S VPN-verbinding met Azure Files wilt gebruiken, moet er een P2S VPN-verbinding worden geconfigureerd voor elke client die verbinding wil maken. 
    - [Site-naar-site-VPN (S2S)](../../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti): VPN-verbindingen tussen Azure en het netwerk van uw organisatie. Met een S2S-VPN-verbinding kunt u een VPN-verbinding eenmaal configureren, voor een VPN-server of apparaat dat wordt gehost op het netwerk van uw organisatie, in plaats van elk client apparaat dat toegang heeft tot uw Azure-bestands share.
- [ExpressRoute](../../expressroute/expressroute-introduction.md), waarmee u een gedefinieerde route kunt maken tussen Azure en uw on-premises netwerk dat geen verbinding met internet heeft. Omdat ExpressRoute een toegewezen pad heeft tussen uw on-premises Data Center en Azure, is ExpressRoute mogelijk handig wanneer de netwerk prestaties van belang zijn. ExpressRoute is ook een goede optie wanneer het beleid of de wettelijke vereisten van uw organisatie een deterministisch pad naar uw resources in de Cloud vereisen.

## <a name="securing-access-from-on-premises"></a>Toegang vanaf on-premises beveiligen 
Wanneer u bestands shares voor algemene doel einden (voor zaken als Office-documenten, Pdf's, CAD-documenten enzovoort) migreert naar Azure Files, blijven uw gebruikers doorgaans toegang tot hun bestanden vanaf on-premises apparaten, zoals hun werk stations, laptops en tablets. De belangrijkste overweging voor een bestands share voor algemeen gebruik is de manier waarop on-premises gebruikers veilig toegang kunnen krijgen tot hun bestands shares via internet of WAN.

De eenvoudigste manier om toegang te krijgen tot uw Azure-bestands share vanuit on-premises is het openen van uw on-premises netwerk naar poort 445, de poort die door SMB wordt gebruikt en de koppeling van het UNC-pad dat wordt verschaft door de Azure Portal. Hiervoor is geen speciaal netwerk vereist. Veel klanten zijn niet langer poort 445 open vanwege verouderde beveiligings richtlijnen rond SMB 1,0, wat micro soft niet overweegt om een Internet-Safe protocol te zijn. Azure Files implementeert geen SMB 1,0. 

SMB 3,0 is ontworpen met de expliciete vereiste van Internet Safe File share-protocollen. Daarom wordt bij het gebruik van SMB 3.0 + vanuit het perspectief van computer netwerken het openen van poort 445 niet anders dan het openen van poort 443, de poort die wordt gebruikt voor HTTPS-verbindingen. In plaats van poort 445 te blok keren om onveilig SMB 1,0-verkeer te voor komen, raadt micro soft de volgende stappen aan:

> [!Important]  
> Zelfs als u besluit poort 445 gesloten te laten worden door uitgaand verkeer, raadt micro soft u aan de volgende stappen uit te voeren om SMB 1,0 te verwijderen uit uw-omgeving.

1. Controleer of SMB 1,0 is verwijderd of is uitgeschakeld op de apparaten van uw organisatie. Alle momenteel ondersteunde versies van Windows en Windows Server ondersteunen het verwijderen of uitschakelen van SMB 1,0 en beginnen met Windows 10 versie 1709, SMB 1,0 wordt standaard niet op de Windows geïnstalleerd. Ga voor meer informatie over het uitschakelen van SMB 1,0 naar onze specifieke pagina's voor het besturings systeem:
    - [Windows/Windows Server beveiligen](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Linux beveiligen](storage-how-to-use-files-linux.md#securing-linux)
1. Zorg ervoor dat er in uw organisatie geen SMB 1,0 is vereist en verwijder de producten die dat doen. We onderhouden een [SMB1-product Clearinghouse](https://aka.ms/stillneedssmb1), dat alle producten van de eerste en derde partij bevat die bekend zijn bij micro soft om SMB 1,0 te vereisen. 
1. Beschrijving Gebruik een firewall van derden met het on-premises netwerk van uw organisatie om het SMB 1,0-verkeer te voor komen.

Als uw organisatie vereist dat poort 445 wordt geblokkeerd per beleid of voor schrift, kunt u Azure VPN Gateway of ExpressRoute gebruiken om verkeer via poort 443 te tunnelen. Zie voor meer informatie over de specifieke stappen voor het implementeren van deze procedures onze specifieke instructies op pagina's:
- [Een site-naar-site-VPN (S2S) configureren voor gebruik met Azure Files](storage-files-configure-s2s-vpn.md)
- [Een punt-naar-site-VPN (P2S) in Windows configureren voor gebruik met Azure Files](storage-files-configure-p2s-vpn-windows.md)
- [Een punt-naar-site-VPN (P2S) op Linux configureren voor gebruik met Azure Files](storage-files-configure-p2s-vpn-linux.md)

Uw organisatie heeft mogelijk de extra vereiste dat verkeer uitgaande van uw on-premises site een deterministisch pad naar uw resources in de Cloud moet volgen. Als dit het geval is, kan ExpressRoute voldoen aan deze vereiste.

## <a name="securing-access-from-cloud-resources"></a>Toegang beveiligen tegen cloud resources
Wanneer een on-premises toepassing wordt opgetrokken en naar de Cloud wordt geschoven, worden de gegevens van de toepassing en de toepassing op hetzelfde moment verplaatst. Dit betekent dat de primaire overweging voor een lift-en Shift-migratie de toegang tot de Azure-bestands share vergrendelt voor de specifieke virtuele machines of Azure-Services die toegang moeten hebben tot de bestands share om te kunnen werken. 

U kunt VNets gebruiken om te beperken welke Vm's of andere Azure-bronnen mogen worden gebruikt om netwerk verbindingen (SMB-koppels of REST API aanroepen naar uw Azure-bestands share) te maken. We raden u aan om uw Azure-bestands share in een VNet te plaatsen als u niet-versleuteld verkeer naar uw opslag account toestaat. Of u nu VNets gebruikt, is een beslissing dat moet worden aangestuurd door uw bedrijfs vereisten en het beleid van de organisatie.

De belangrijkste reden voor het toestaan van niet-versleuteld verkeer naar uw Azure-bestands share is het ondersteunen van Windows Server 2008 R2, Windows 7 of een ander ouder besturings systeem dat toegang heeft tot uw Azure-bestands share met SMB 2,1 (of SMB 3,0 zonder versleuteling voor een aantal Linux-distributies). Het gebruik van SMB 2,1 of SMB 3,0 wordt niet aanbevolen zonder versleuteling van besturings systemen die ondersteuning bieden voor SMB 3.0 + met versleuteling.

## <a name="see-also"></a>Zie ook
- [Overzicht van Azure Files](storage-files-introduction.md)
- [Implementatie van Azure Files plannen](storage-files-planning.md)