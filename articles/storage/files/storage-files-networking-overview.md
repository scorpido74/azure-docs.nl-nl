---
title: Overwegingen voor Azure Files-netwerken | Microsoft Docs
description: Een overzicht van de netwerk opties voor Azure Files.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 02/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 383ad5e5063a0a207320a517c34f3b41cc57804a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80067155"
---
# <a name="azure-files-networking-considerations"></a>Azure Files-netwerk overwegingen 
U kunt op twee manieren verbinding maken met een Azure-bestands share:

- Rechtstreeks toegang tot de share via de SMB-of FileREST-protocollen. Dit toegangs patroon wordt voornamelijk gebruikt om zoveel mogelijk lokale servers te elimineren.
- Het maken van een cache van de Azure-bestands share op een on-premises server met Azure File Sync, en toegang tot de gegevens van de bestands share vanaf de on-premises server met het Protocol van Choice (SMB, NFS, FTPS, enzovoort) voor uw gebruiks voorbeeld. Dit toegangs patroon is handig omdat het het beste is voor zowel on-premises als voor de Cloud geschaalde en serverloze koppel bare Services, zoals Azure Backup.

In dit artikel wordt uitgelegd hoe u netwerken kunt configureren voor wanneer uw use-case aanroepen voor het rechtstreeks openen van de Azure-bestands share in plaats van Azure File Sync te gebruiken. Zie [Azure file sync proxy-en Firewall instellingen configureren](storage-sync-files-firewall-and-proxy.md)voor meer informatie over netwerk overwegingen voor een Azure file sync-implementatie.

Netwerk configuratie voor Azure-bestands shares wordt uitgevoerd op het Azure-opslag account. Een opslag account is een beheer constructie die een gedeelde opslag groep vertegenwoordigt, waarbij u meerdere bestands shares en andere opslag resources, zoals BLOB-containers of wacht rijen, kunt implementeren. Opslag accounts stellen meerdere instellingen beschikbaar waarmee u netwerk toegang tot uw bestands shares kunt beveiligen: netwerk eindpunten, Firewall-instellingen voor opslag accounts en versleuteling in transit. 

Het is raadzaam om de [planning voor een Azure files-implementatie](storage-files-planning.md) te lezen voordat u deze conceptuele hand leiding leest.

## <a name="accessing-your-azure-file-shares"></a>Toegang tot uw Azure-bestands shares
Wanneer u een Azure-bestands share in een opslag account implementeert, is uw bestands share direct toegankelijk via het open bare eind punt van het opslag account. Dit betekent dat geverifieerde aanvragen, zoals aanvragen die zijn geautoriseerd door de aanmeldings identiteit van een gebruiker, veilig kunnen zijn van binnen of buiten Azure. 

In veel klanten omgevingen kan een initiële koppeling van de Azure-bestands share op uw on-premises werk station mislukken, zelfs als de koppeling van Azure-Vm's slaagt. De reden hiervoor is dat veel organisaties en Internet serviceproviders (Isp's) de poort blok keren die door SMB wordt gebruikt om te communiceren, poort 445. Deze praktijk is afkomstig uit beveiligings richtlijnen over verouderde en afgeschafte versies van het SMB-protocol. Hoewel SMB 3,0 een Internet-Safe protocol is, zijn oudere versies van SMB, met name SMB 1,0, niet. Azure-bestands shares zijn mogelijk alleen extern toegankelijk via SMB 3,0 en het FileREST-Protocol (dit is ook een Internet Safe Protocol) via het open bare eind punt.

Omdat de eenvoudigste manier om toegang te krijgen tot uw Azure-bestands share vanaf on-premises, is het openen van uw on-premises netwerk naar poort 445, micro soft raadt u aan de volgende stappen uit te voeren om SMB 1,0 te verwijderen uit uw omgeving:

1. Controleer of SMB 1,0 is verwijderd of is uitgeschakeld op de apparaten van uw organisatie. Alle momenteel ondersteunde versies van Windows en Windows Server ondersteunen het verwijderen of uitschakelen van SMB 1,0 en beginnen met Windows 10 versie 1709, SMB 1,0 wordt standaard niet op de Windows geïnstalleerd. Ga voor meer informatie over het uitschakelen van SMB 1,0 naar onze specifieke pagina's voor het besturings systeem:
    - [Windows/Windows Server beveiligen](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Linux beveiligen](storage-how-to-use-files-linux.md#securing-linux)
2. Zorg ervoor dat er in uw organisatie geen SMB 1,0 is vereist en verwijder de producten die dat doen. We onderhouden een [SMB1-product Clearinghouse](https://aka.ms/stillneedssmb1), dat alle producten van de eerste en derde partij bevat die bekend zijn bij micro soft om SMB 1,0 te vereisen. 
3. Beschrijving Gebruik een firewall van derden met het on-premises netwerk van uw organisatie om te voor komen dat SMB 1,0-verkeer uw organisatie grens verlaat.

Als uw organisatie vereist dat poort 445 wordt geblokkeerd per beleid of voor schrift, of als uw organisatie verkeer naar Azure vereist om een deterministisch pad te volgen, kunt u Azure VPN Gateway of ExpressRoute gebruiken om verkeer naar uw Azure-bestands shares te tunnelen.

> [!Important]  
> Zelfs als u besluit een alternatieve methode te gebruiken om toegang te krijgen tot uw Azure-bestands shares, raadt micro soft aan om SMB 1,0 nog steeds te verwijderen uit uw omgeving.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Verkeer tunnelen via een virtueel particulier netwerk of een ExpressRoute
Wanneer u een netwerk tunnel tussen uw on-premises netwerk en Azure tot stand brengt, wordt uw on-premises netwerk gekoppeld aan een of meer virtuele netwerken in Azure. Een [virtueel netwerk](../../virtual-network/virtual-networks-overview.md), of VNet, is vergelijkbaar met een traditioneel netwerk dat u on-premises zou uitvoeren. Net als een Azure-opslag account of een Azure-VM is een VNet een Azure-resource die in een resource groep is geïmplementeerd. 

Azure Files ondersteunt de volgende mechanismen om verkeer tussen uw on-premises werk stations en servers en Azure te tunnelen:

- [Azure VPN gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): een VPN-gateway is een specifiek type virtuele netwerk gateway dat wordt gebruikt voor het verzenden van versleuteld verkeer tussen een virtueel Azure-netwerk en een alternatieve locatie (zoals on-premises) via internet. Een Azure-VPN Gateway is een Azure-resource die kan worden geïmplementeerd in een resource groep naast de kant van een opslag account of andere Azure-resources. VPN-gateways bieden twee verschillende soorten verbindingen:
    - [Punt-naar-site-VPN-gateway verbindingen (P2S)](../../vpn-gateway/point-to-site-about.md) , die VPN-verbindingen tussen Azure en een afzonderlijke client zijn. Deze oplossing is vooral nuttig voor apparaten die geen deel uitmaken van het on-premises netwerk van uw organisatie, zoals telewerkers die hun Azure-bestands share willen kunnen koppelen aan thuis, in een café of in een hotel, terwijl u onderweg bent. Als u een P2S VPN-verbinding met Azure Files wilt gebruiken, moet er een P2S VPN-verbinding worden geconfigureerd voor elke client die verbinding wil maken. Zie [Configure a point-to-site (P2S) VPN in Windows voor gebruik met Azure files](storage-files-configure-p2s-vpn-windows.md) en [Configureer een punt-naar-site (P2S) VPN op Linux voor gebruik met Azure files](storage-files-configure-p2s-vpn-linux.md)voor het vereenvoudigen van de implementatie van een P2S-VPN-verbinding.
    - [Site-naar-site-VPN (S2S)](../../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti): VPN-verbindingen tussen Azure en het netwerk van uw organisatie. Met een S2S-VPN-verbinding kunt u een VPN-verbinding eenmaal configureren, voor een VPN-server of apparaat dat wordt gehost op het netwerk van uw organisatie, in plaats van elk client apparaat dat toegang heeft tot uw Azure-bestands share. Zie [Configure a site-to-site (S2S) VPN voor gebruik met Azure files](storage-files-configure-s2s-vpn.md)voor het vereenvoudigen van de implementatie van een S2S-VPN-verbinding.
- [ExpressRoute](../../expressroute/expressroute-introduction.md), waarmee u een gedefinieerde route kunt maken tussen Azure en uw on-premises netwerk dat geen verbinding met internet heeft. Omdat ExpressRoute een toegewezen pad heeft tussen uw on-premises Data Center en Azure, is ExpressRoute mogelijk handig wanneer de netwerk prestaties van belang zijn. ExpressRoute is ook een goede optie wanneer het beleid of de wettelijke vereisten van uw organisatie een deterministisch pad naar uw resources in de Cloud vereisen.

Ongeacht welke Tunnel methode u gebruikt om toegang te krijgen tot uw Azure-bestands shares, hebt u een mechanisme nodig om ervoor te zorgen dat het verkeer naar uw opslag account via de tunnel overgaat in plaats van uw normale Internet verbinding. Het technisch mogelijk is om te leiden naar het open bare eind punt van het opslag account, maar hiervoor moeten alle IP-adressen voor de Azure Storage-clusters in een regio worden gedecodeerd, omdat opslag accounts kunnen worden verplaatst tussen opslag clusters op elk gewenst moment. Dit vereist ook voortdurend het bijwerken van de IP-adres toewijzingen, omdat nieuwe clusters de enige tijd worden toegevoegd.

In plaats van het IP-adres van uw opslag accounts in uw VPN-routerings regels op te lossen, raden we u aan om privé-eind punten te gebruiken, waarmee uw opslag account een IP-adres van de adres ruimte van een virtueel Azure-netwerk krijgt. Omdat het maken van een tunnel naar Azure is ingesteld op peering tussen uw on-premises netwerk en een of meer virtuele netwerken, kan de juiste route ring op een duurzame manier worden ingeschakeld.

### <a name="private-endpoints"></a>Privé-eind punten
Naast het standaard open bare eind punt voor een opslag account, biedt Azure Files de mogelijkheid om een of meer persoonlijke eind punten te hebben. Een persoonlijk eind punt is een eind punt dat alleen toegankelijk is binnen een virtueel Azure-netwerk. Wanneer u een persoonlijk eind punt voor uw opslag account maakt, krijgt uw opslag account een persoonlijk IP-adres in de adres ruimte van uw virtuele netwerk, zoals hoe een on-premises Bestands server of een NAS-apparaat een IP-adres ontvangt binnen de toegewezen adres ruimte van uw on-premises netwerk. 

Een individueel persoonlijk eind punt is gekoppeld aan een specifiek subnet van het virtuele netwerk van Azure. Een opslag account kan een persoonlijk eind punt hebben in meer dan één virtueel netwerk.

Door gebruik te maken van persoonlijke eind punten met Azure Files kunt u het volgende doen:
- U kunt veilig verbinding maken met uw Azure-bestands shares van on-premises netwerken met behulp van een VPN-of ExpressRoute-verbinding met privé-peering.
- Beveilig uw Azure-bestands shares door de firewall van het opslag account zodanig te configureren dat alle verbindingen op het open bare eind punt worden geblokkeerd. Als u een persoonlijk eind punt maakt, worden de verbindingen met het open bare eind punt standaard niet geblokkeerd.
- Verg root de beveiliging van het virtuele netwerk door u in staat te stellen exfiltration van gegevens van het virtuele netwerk (en de grenzen van peering) te blok keren.

Zie voor het maken van een persoonlijk eind punt [persoonlijke eind punten configureren voor Azure files](storage-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Persoonlijke eind punten en DNS
Wanneer u een persoonlijk eind punt maakt, maakt u standaard ook een privé-DNS-zone (of een bestaande) die overeenkomt met het `privatelink` subdomein. Het is niet vereist om een privé-DNS-zone te maken voor het gebruik van een persoonlijk eind punt voor uw opslag account, maar dit wordt sterk aanbevolen in het algemeen en expliciet vereist bij het koppelen van uw Azure-bestands share met een Active Directory gebruikers-principal of het openen van de FileREST-API.

> [!Note]  
> In dit artikel wordt het DNS-achtervoegsel van het opslag account gebruikt voor `core.windows.net`de open bare Azure-regio's. Dit commentaar is ook van toepassing op Azure soevereine Clouds, zoals de Azure-Cloud voor de Amerikaanse overheid en de cloud van Azure China. Vervang gewoon de juiste achtervoegsels voor uw omgeving. 

In uw privé-DNS-zone maken we een A- `storageaccount.privatelink.file.core.windows.net` record voor en een CNAME-record voor de reguliere naam van het opslag account, die `storageaccount.file.core.windows.net`het patroon volgt. Omdat uw privé-DNS-zone van Azure is verbonden met het virtuele netwerk met het persoonlijke eind punt, kunt u de DNS-configuratie `Resolve-DnsName` observeren wanneer u de cmdlet aanroept vanuit Power `nslookup` shell in een Azure-VM (in Windows en Linux):

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

In dit voor beeld wordt het opslag `storageaccount.file.core.windows.net` account omgezet naar het privé-IP-adres van het privé-eind punt, `192.168.0.4`dat wil zeggen.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4


Name                   : privatelink.file.core.windows.net
QueryType              : SOA
TTL                    : 269
Section                : Authority
NameAdministrator      : azureprivatedns-host.microsoft.com
SerialNumber           : 1
TimeToZoneRefresh      : 3600
TimeToZoneFailureRetry : 300
TimeToExpiration       : 2419200
DefaultTTL             : 300
```

Als u dezelfde opdracht vanuit on-premises uitvoert, ziet u dat dezelfde naam voor het opslag account wordt omgezet in het open bare IP-adres van het opslag account. `storageaccount.file.core.windows.net` is een CNAME-record `storageaccount.privatelink.file.core.windows.net`voor, die op zijn beurt een CNAME-record is voor het Azure Storage-cluster dat als host fungeert voor het opslag account:

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
net
storageaccount.privatelink.file.c CNAME  60    Answer     file.par20prdstr01a.store.core.windows.net
ore.windows.net

Name       : file.par20prdstr01a.store.core.windows.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 52.239.194.40
```

Dit weerspiegelt het feit dat het opslag account zowel het open bare eind punt als een of meer persoonlijke eind punten kan bloot stellen. Om ervoor te zorgen dat de naam van het opslag account wordt omgezet naar het privé-IP-adres van het privé-eind punt, moet u de configuratie op uw lokale DNS-servers wijzigen. Dit kan op verschillende manieren worden uitgevoerd:

- Het hosts-bestand op uw clients wijzigen zodat `storageaccount.file.core.windows.net` het wordt omgezet naar het privé-IP-adres van het gewenste privé-eind punt. Dit wordt sterk afgeraden voor productie omgevingen, omdat u deze wijzigingen moet aanbrengen in elke client die uw Azure-bestands shares wil koppelen en wijzigingen aan het opslag account of het persoonlijke eind punt worden niet automatisch verwerkt.
- Het maken van een A `storageaccount.file.core.windows.net` -record voor in uw on-premises DNS-servers. Dit heeft als voor deel dat clients in uw on-premises omgeving automatisch het opslag account kunnen omzetten zonder dat elke client hoeft te worden geconfigureerd. deze oplossing is echter ook zwakke om het hosts-bestand te wijzigen, omdat de wijzigingen niet worden doorgevoerd. Hoewel deze oplossing zwakke is, is het mogelijk de beste keuze voor sommige omgevingen.
- Stuur de `core.windows.net` zone van uw on-PREMISES DNS-servers naar uw persoonlijke Azure-DNS-zone. De privé-DNS-host van Azure kan worden bereikt via een speciaal`168.63.129.16`IP-adres () dat alleen toegankelijk is in virtuele netwerken die zijn gekoppeld aan de privé-DNS-zone van Azure. Als u deze beperking wilt omzeilen, kunt u extra DNS-servers in uw virtuele netwerk `core.windows.net` uitvoeren die worden doorgestuurd naar de privé-DNS-zone van Azure. Om deze instelling te vereenvoudigen, hebben we Power shell-cmdlets opgegeven waarmee DNS-servers automatisch worden geïmplementeerd in uw virtuele Azure-netwerk en deze naar wens configureren. Zie [DNS configureren met Azure files](storage-files-networking-dns.md)voor meer informatie over het instellen van DNS-door sturen.

## <a name="storage-account-firewall-settings"></a>Firewall instellingen van opslag account
Een firewall is een netwerk beleid dat bepaalt welke aanvragen toegang hebben tot het open bare eind punt voor een opslag account. Met de firewall van het opslag account kunt u de toegang tot het open bare eind punt van het opslag account beperken tot bepaalde IP-adressen of bereiken of een virtueel netwerk. Over het algemeen beperkt de meeste firewall-beleids regels voor een opslag account netwerk toegang tot een of meer virtuele netwerken. 

Er zijn twee manieren om de toegang tot een opslag account te beperken tot een virtueel netwerk:
- Maak een of meer persoonlijke eind punten voor het opslag account en beperk de toegang tot het open bare eind punt. Dit zorgt ervoor dat alleen verkeer dat afkomstig is van binnen de gewenste virtuele netwerken toegang heeft tot de Azure-bestands shares in het opslag account.
- Beperk het open bare eind punt tot een of meer virtuele netwerken. Dit werkt met behulp van een mogelijkheid van het virtuele netwerk met de naam *service-eind punten*. Wanneer u het verkeer beperkt tot een opslag account via een service-eind punt, hebt u nog steeds toegang tot het opslag account via het open bare IP-adres.

Zie [firewalls en virtuele netwerken voor Azure Storage configureren](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)voor meer informatie over het configureren van de firewall voor het opslag account.

## <a name="encryption-in-transit"></a>Versleuteling 'in transit'
Standaard hebben alle Azure Storage-accounts versleuteling in transit ingeschakeld. Dit betekent dat wanneer u een bestands share koppelt via SMB of toegang krijgt via het FileREST-Protocol (zoals via de Azure Portal, Power shell/CLI of Azure Sdk's), Azure Files alleen de verbinding toestaat als deze wordt gemaakt met SMB 3.0 + met versleuteling of HTTPS. Clients die geen ondersteuning bieden voor SMB 3,0 of clients die SMB 3,0 ondersteunen maar geen SMB-versleuteling, kunnen de Azure-bestands share niet koppelen als versleuteling in transit is ingeschakeld. Zie de gedetailleerde documentatie voor [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)en [Linux](storage-how-to-use-files-linux.md)voor meer informatie over welke besturings systemen SMB 3,0 met versleuteling ondersteunen. Alle huidige versies van de Power shell, CLI en Sdk's ondersteunen HTTPS.  

U kunt versleuteling voor een Azure Storage-account uitschakelen in door voer. Wanneer versleuteling is uitgeschakeld, wordt door Azure Files ook SMB 2,1, SMB 3,0 zonder versleuteling en niet-versleutelde FileREST-API-aanroepen via HTTP toegestaan. De belangrijkste reden om versleuteling in transit uit te scha kelen, is het ondersteunen van een verouderde toepassing die moet worden uitgevoerd op een ouder besturings systeem, zoals Windows Server 2008 R2 of een oudere Linux-distributie. Met Azure Files worden SMB 2,1-verbindingen alleen toegestaan binnen dezelfde Azure-regio als de Azure-bestands share. een SMB 2,1-client buiten de Azure-regio van de Azure-bestands share, zoals on-premises of in een andere Azure-regio, heeft geen toegang tot de bestands share.

Zie [veilige overdracht vereisen in azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)voor meer informatie over het door sturen van versleuteling.

## <a name="see-also"></a>Zie ook
- [Overzicht van Azure Files](storage-files-introduction.md)
- [Planning voor de implementatie van Azure Files](storage-files-planning.md)