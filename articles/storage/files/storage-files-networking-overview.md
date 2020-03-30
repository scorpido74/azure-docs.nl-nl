---
title: Overwegingen voor Azure Files-netwerken | Microsoft Documenten
description: Een overzicht van netwerkopties voor Azure Files.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 02/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 383ad5e5063a0a207320a517c34f3b41cc57804a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067155"
---
# <a name="azure-files-networking-considerations"></a>Overwegingen voor Azure Files-netwerken 
U op twee manieren verbinding maken met een Azure-bestandsshare:

- Rechtstreeks toegang tot het aandeel via de SMB- of FileREST-protocollen. Dit toegangspatroon wordt voornamelijk gebruikt wanneer u zoveel mogelijk on-premises servers elimineert.
- Een cache van de Azure-bestandsshare maken op een on-premises server met Azure File Sync en toegang krijgen tot de gegevens van de bestandsshare van de on-premises server met uw protocol naar keuze (SMB, NFS, FTPS, enz.) voor uw use case. Dit toegangspatroon is handig omdat het het beste van zowel on-premises prestaties als cloudschaal en serverloze gekoppelde services combineert, zoals Azure Backup.

In dit artikel wordt gefocusd op het configureren van netwerken voor wanneer uw use case-oproepen om rechtstreeks toegang te krijgen tot de Azure-bestandsshare in plaats van Azure File Sync te gebruiken. Zie [Azure File Sync-proxy en firewall-instellingen configureren](storage-sync-files-firewall-and-proxy.md)voor meer informatie over netwerkoverwegingen voor een Azure File Sync-implementatie.

Netwerkconfiguratie voor Azure-bestandsshares wordt uitgevoerd op het Azure-opslagaccount. Een opslagaccount is een beheerconstructie die een gedeelde opslaggroep vertegenwoordigt waarin u meerdere bestandsshares implementeren, evenals andere opslagbronnen, zoals blobcontainers of wachtrijen. Opslagaccounts leggen meerdere instellingen bloot waarmee u netwerktoegang tot uw bestandsshares beveiligen: netwerkeindpunten, firewall-instellingen voor opslagaccounts en versleuteling onderweg. 

We raden u aan [Planning voor een Azure-bestanden-implementatie](storage-files-planning.md) te lezen voordat u deze conceptuele handleiding leest.

## <a name="accessing-your-azure-file-shares"></a>Toegang tot uw Azure-bestandsshares
Wanneer u een Azure-bestandsshare implementeert in een opslagaccount, is uw bestandsshare onmiddellijk toegankelijk via het openbare eindpunt van het opslagaccount. Dit betekent dat geverifieerde aanvragen, zoals aanvragen die zijn geautoriseerd door de aanmeldingsidentiteit van een gebruiker, veilig kunnen afkomstig zijn van binnen of buiten Azure. 

In veel klantomgevingen mislukt een eerste bevestiging van het Azure-bestandsaandeel op uw on-premises werkstation, ook al slagen mounts van Azure VM's. De reden hiervoor is dat veel organisaties en internet service providers (ISP's) blokkeren de poort die SMB gebruikt om te communiceren, poort 445. Deze praktijk is afkomstig van beveiligingsrichtlijnen over verouderde en afgeschafte versies van het SMB-protocol. Hoewel SMB 3.0 een internetveilig protocol is, zijn oudere versies van SMB, met name SMB 1.0 niet. Azure-bestandsshares zijn alleen extern toegankelijk via SMB 3.0 en het FileREST-protocol (dat ook een internetveilig protocol is) via het openbare eindpunt.

Aangezien de eenvoudigste manier om toegang te krijgen tot uw Azure-bestandsshare van on-premises is om uw on-premises netwerk te openen naar poort 445, raadt Microsoft de volgende stappen aan om SMB 1.0 uit uw omgeving te verwijderen:

1. Zorg ervoor dat SMB 1.0 wordt verwijderd of uitgeschakeld op de apparaten van uw organisatie. Alle momenteel ondersteunde versies van Windows en Windows Server ondersteunen het verwijderen of uitschakelen van SMB 1.0, en vanaf Windows 10, versie 1709, is SMB 1.0 standaard niet geïnstalleerd op Windows. Zie onze OS-specifieke pagina's voor meer informatie over het uitschakelen van SMB 1.0:
    - [Windows/Windows Server beveiligen](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Linux beveiligen](storage-how-to-use-files-linux.md#securing-linux)
2. Zorg ervoor dat er geen producten binnen uw organisatie SMB 1.0 nodig hebben en verwijder de producten die dat wel doen. We onderhouden een [SMB1 Product Clearinghouse](https://aka.ms/stillneedssmb1), dat alle eerste en externe producten bevat die bekend zijn bij Microsoft om SMB 1.0 te vereisen. 
3. (Optioneel) Gebruik een firewall van derden met het on-premises netwerk van uw organisatie om te voorkomen dat smb 1.0-verkeer uw organisatiegrens verlaat.

Als uw organisatie vereist dat poort 445 wordt geblokkeerd per beleid of regelgeving, of als uw organisatie verkeer naar Azure nodig heeft om een deterministisch pad te volgen, u Azure VPN Gateway of ExpressRoute gebruiken om verkeer naar uw Azure-bestandsshares te tunnelen.

> [!Important]  
> Zelfs als u besluit een alternatieve methode te gebruiken om toegang te krijgen tot uw Azure-bestandsshares, raadt Microsoft nog steeds aan om SMB 1.0 uit uw omgeving te verwijderen.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Tunneling verkeer over een virtueel privénetwerk of ExpressRoute
Wanneer u een netwerktunnel maakt tussen uw on-premises netwerk en Azure, kijkt u met uw on-premises netwerk met een of meer virtuele netwerken in Azure. Een [virtueel netwerk](../../virtual-network/virtual-networks-overview.md)of VNet is vergelijkbaar met een traditioneel netwerk dat u on-premises zou exploiteren. Net als een Azure-opslagaccount of een Azure VM is een VNet een Azure-bron die is geïmplementeerd in een brongroep. 

Azure Files ondersteunt de volgende mechanismen voor het tunnelverkeer tussen uw on-premises werkstations en servers en Azure:

- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): Een VPN-gateway is een specifiek type virtuele netwerkgateway dat wordt gebruikt om versleuteld verkeer tussen een virtueel Azure-netwerk en een alternatieve locatie (zoals on-premises) via internet te verzenden. Een Azure VPN Gateway is een Azure-bron die kan worden geïmplementeerd in een brongroep naast een opslagaccount of andere Azure-bronnen. VPN-gateways leggen twee verschillende soorten verbindingen bloot:
    - [Point-to-Site (P2S) VPN-gatewayverbindingen,](../../vpn-gateway/point-to-site-about.md) vpn-verbindingen tussen Azure en een afzonderlijke client. Deze oplossing is vooral handig voor apparaten die geen deel uitmaken van het on-premises netwerk van uw organisatie, zoals teleforenzen die onderweg hun Azure-bestandsshare willen kunnen monteren vanuit huis, een coffeeshop of hotel. Als u een P2S VPN-verbinding met Azure Files wilt gebruiken, moet een P2S VPN-verbinding worden geconfigureerd voor elke client die verbinding wil maken. Zie [Een Point-to-Site (P2S)-VPN op Windows configureren voor gebruik met Azure Files](storage-files-configure-p2s-vpn-windows.md) en Configureren van een [Point-to-Site (P2S) VPN op Linux configureren voor gebruik met Azure Files.](storage-files-configure-p2s-vpn-linux.md)
    - [Site-to-Site (S2S) VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti), die VPN-verbindingen zijn tussen Azure en het netwerk van uw organisatie. Met een S2S VPN-verbinding u één keer een VPN-verbinding configureren voor een VPN-server of -apparaat dat wordt gehost op het netwerk van uw organisatie, in plaats van te doen voor elk clientapparaat dat toegang nodig heeft om toegang te krijgen tot uw Azure-bestandsshare. Zie [Een Site-to-Site (S2S) VPN configureren voor gebruik met Azure Files](storage-files-configure-s2s-vpn.md)om de implementatie van een S2S VPN-verbinding te vereenvoudigen.
- [ExpressRoute](../../expressroute/expressroute-introduction.md), waarmee u een gedefinieerde route maken tussen Azure en uw on-premises netwerk dat het internet niet doorkruist. Omdat ExpressRoute een speciaal pad biedt tussen uw on-premises datacenter en Azure, kan ExpressRoute nuttig zijn wanneer netwerkprestaties een overweging zijn. ExpressRoute is ook een goede optie wanneer de beleids- of regelgevingsvereisten van uw organisatie een deterministisch pad naar uw resources in de cloud vereisen.

Ongeacht welke tunnelmethode u gebruikt om toegang te krijgen tot uw Azure-bestandsshares, hebt u een mechanisme nodig om ervoor te zorgen dat het verkeer naar uw opslagaccount over de tunnel gaat in plaats van uw gewone internetverbinding. Het is technisch mogelijk om naar het openbare eindpunt van het opslagaccount te routeren, maar dit vereist harde codering van alle IP-adressen voor de Azure-opslagclusters in een regio, omdat opslagaccounts op elk gewenst moment tussen opslagclusters kunnen worden verplaatst. Dit vereist ook het voortdurend bijwerken van de IP-adrestoewijzingen, omdat er voortdurend nieuwe clusters worden toegevoegd.

In plaats van het IP-adres van uw opslagaccounts hard te coderen in uw VPN-routeringsregels, raden we u aan privéeindpunten te gebruiken, die uw opslagaccount een IP-adres geven vanuit de adresruimte van een virtueel Azure-netwerk. Aangezien het maken van een tunnel naar Azure peering tussen uw on-premises netwerk en een of meer virtuele netwerk vestigt, maakt dit de juiste routering op een duurzame manier mogelijk.

### <a name="private-endpoints"></a>Privéeindpunten
Naast het standaard openbare eindpunt voor een opslagaccount biedt Azure Files de mogelijkheid om een of meer privéeindpunten te hebben. Een privéeindpunt is een eindpunt dat alleen toegankelijk is binnen een virtueel Azure-netwerk. Wanneer u een privéeindpunt voor uw opslagaccount maakt, krijgt uw opslagaccount een privé-IP-adres binnen de adresruimte van uw virtuele netwerk, net zoals hoe een on-premises bestandsserver of NAS-apparaat een IP-adres ontvangt binnen het specifieke adres ruimte van uw on-premises netwerk. 

Een afzonderlijk privéeindpunt is gekoppeld aan een specifiek subnet voor een Extern Azure-netwerk. Een opslagaccount kan privéeindpunten hebben in meer dan één virtueel netwerk.

Met het gebruik van privéeindpunten met Azure Files u:
- Maak veilig verbinding met uw Azure-bestandsshares van on-premises netwerken via een VPN- of ExpressRoute-verbinding met private-peering.
- Beveilig uw Azure-bestandsshares door de firewall voor het opslagaccount te configureren om alle verbindingen op het openbare eindpunt te blokkeren. Standaard blokkeert het maken van een privéeindpunt geen verbindingen met het openbare eindpunt.
- Verhoog de beveiliging van het virtuele netwerk door dat u exfiltratie van gegevens uit het virtuele netwerk (en peering-grenzen) blokkeren.

Zie [Privéeindpunten configureren voor Azure-bestanden](storage-files-networking-endpoints.md)als u een privéeindpunt wilt maken.

### <a name="private-endpoints-and-dns"></a>Privéeindpunten en DNS
Wanneer u een privéeindpunt maakt, maken we standaard ook een (of bijwerken `privatelink` van een bestaande) privé-DNS-zone die overeenkomt met het subdomein. Strikt genomen is het maken van een privé-DNS-zone niet vereist om een privéeindpunt voor uw opslagaccount te gebruiken, maar het wordt in het algemeen ten zeerste aanbevolen en expliciet vereist bij het monteren van uw Azure-bestandsshare met een Active Directory-gebruikersprincipal of van de FileREST API.

> [!Note]  
> In dit artikel wordt het DNS-achtervoegsel `core.windows.net`van het opslagaccount voor de azure public-regio's gebruikt. Dit commentaar is ook van toepassing op Azure Sovereign-clouds zoals de Azure Us Government-cloud en de Azure China-cloud - vervang gewoon de juiste achtervoegsels voor uw omgeving. 

In uw privé-DNS-zone maken `storageaccount.privatelink.file.core.windows.net` we een A-record voor en een CNAME-record `storageaccount.file.core.windows.net`voor de normale naam van het opslagaccount, die het patroon volgt. Aangezien uw Azure private DNS-zone is verbonden met het virtuele netwerk met het privéeindpunt, u de DNS-configuratie observeren wanneer u de `Resolve-DnsName` cmdlet van PowerShell in een Azure VM aanroept (afwisselend `nslookup` in Windows en Linux):

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

In dit voorbeeld wordt `storageaccount.file.core.windows.net` het opslagaccount ophet privé-IP-adres van `192.168.0.4`het privéeindpunt opgelost, wat toevallig .

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

Als u dezelfde opdracht van on-premises uitvoert, ziet u dat dezelfde naam van het opslagaccount wordt opgelost op het openbare IP-adres van het opslagaccount. `storageaccount.file.core.windows.net` is een CNAME-record voor `storageaccount.privatelink.file.core.windows.net`, die op zijn beurt een CNAME-record is voor het Azure-opslagcluster dat het opslagaccount host:

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

Dit weerspiegelt het feit dat het opslagaccount zowel het openbare eindpunt als een of meer privéeindpunten kan blootleggen. Als u ervoor wilt zorgen dat de naam van het opslagaccount wordt opgelost op het privé-IP-adres van het privé-eindpunt, moet u de configuratie op uw on-premises DNS-servers wijzigen. Dit kan op verschillende manieren worden bereikt:

- Het wijzigen van het hosts-bestand op uw clients om een oplossing te vinden `storageaccount.file.core.windows.net` voor het privé-IP-adres van het gewenste privé-eindpunt. Dit wordt sterk afgeraden voor productieomgevingen, omdat u deze wijzigingen moet aanbrengen in elke client die uw Azure-bestandsshares wil monteren en wijzigingen in het opslagaccount of privéeindpunt niet automatisch worden verwerkt.
- Een A-record `storageaccount.file.core.windows.net` maken voor in uw on-premises DNS-servers. Dit heeft als voordeel dat clients in uw on-premises omgeving het opslagaccount automatisch kunnen oplossen zonder dat elke client hoeft te worden geconfigureerd, maar deze oplossing is ook broos voor het wijzigen van het hosts-bestand omdat wijzigingen niet Weerspiegeld. Hoewel deze oplossing broos is, kan het de beste keuze zijn voor sommige omgevingen.
- Stuur `core.windows.net` de zone door van uw on-premises DNS-servers naar uw privé-DNS-zone in Azure. De Azure private DNS-host is bereikbaar`168.63.129.16`via een speciaal IP-adres ( ) dat alleen toegankelijk is binnen virtuele netwerken die zijn gekoppeld aan de Private DNS-zone van Azure. Als u deze beperking wilt oplossen, u extra `core.windows.net` DNS-servers binnen uw virtuele netwerk uitvoeren die worden doorgestuurd naar de privé-DNS-zone van Azure. Om deze set-up te vereenvoudigen, hebben we PowerShell-cmdlets geleverd waarmee DNS-servers automatisch worden geïmplementeerd in uw virtuele Azure-netwerk en deze naar wens worden geconfigureerd. Zie DNS configureren met Azure Files voor meer informatie over het instellen [van DNS-forwarding.](storage-files-networking-dns.md)

## <a name="storage-account-firewall-settings"></a>Firewall-instellingen voor opslagaccount
Een firewall is een netwerkbeleid dat bepaalt welke aanvragen toegang hebben tot het openbare eindpunt voor een opslagaccount. Met behulp van de firewall voor het opslagaccount u de toegang tot het openbare eindpunt van het opslagaccount beperken tot bepaalde IP-adressen of -bereiken of tot een virtueel netwerk. In het algemeen beperkt het meeste firewallbeleid voor een opslagaccount de toegang tot netwerken tot een of meer virtuele netwerken. 

Er zijn twee benaderingen om de toegang tot een opslagaccount te beperken tot een virtueel netwerk:
- Maak een of meer privéeindpunten voor het opslagaccount en beperk alle toegang tot het openbare eindpunt. Dit zorgt ervoor dat alleen verkeer dat afkomstig is van de gewenste virtuele netwerken toegang heeft tot de Azure-bestandsshares binnen het opslagaccount.
- Beperk het openbare eindpunt tot een of meer virtuele netwerken. Dit werkt met behulp van een mogelijkheid van het virtuele netwerk genaamd *service eindpunten*. Wanneer u het verkeer via een serviceeindpunt beperkt tot een opslagaccount, hebt u nog steeds toegang tot het opslagaccount via het openbare IP-adres.

Zie [Azure Storage Firewalls en virtuele netwerken configureren](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)voor meer informatie over het configureren van de firewall van het opslagaccount.

## <a name="encryption-in-transit"></a>Versleuteling 'in transit'
Standaard hebben alle Azure-opslagaccounts versleuteling in transit ingeschakeld. Dit betekent dat wanneer u een bestandsshare monteert via SMB of deze opent via het FileREST-protocol (bijvoorbeeld via de Azure-portal, PowerShell/CLI of Azure SDKs), Azure Files de verbinding alleen toestaat als deze is gemaakt met SMB 3.0+ met versleuteling of HTTPS. Clients die geen SMB 3.0 ondersteunen of clients die SMB 3.0 ondersteunen, maar geen SMB-versleuteling, kunnen het Azure-bestandsaandeel niet monteren als versleuteling tijdens het transport is ingeschakeld. Voor meer informatie over welke besturingssystemen SMB 3.0 ondersteunen met versleuteling, zie onze gedetailleerde documentatie voor [Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)en [Linux](storage-how-to-use-files-linux.md). Alle huidige versies van de PowerShell-, CLI- en SDK's ondersteunen HTTPS.  

U versleuteling tijdens het transport uitschakelen voor een Azure-opslagaccount. Wanneer versleuteling is uitgeschakeld, staat Azure Files ook SMB 2.1, SMB 3.0 zonder versleuteling en niet-versleutelde FileREST API-aanroepen via HTTP toe. De belangrijkste reden om versleuteling tijdens het transport uit te schakelen, is het ondersteunen van een verouderde toepassing die moet worden uitgevoerd op een ouder besturingssysteem, zoals Windows Server 2008 R2 of oudere Linux-distributie. Azure Files staat alleen SMB 2.1-verbindingen toe binnen hetzelfde Azure-gebied als de Azure-bestandsshare. een SMB 2.1-client buiten het Azure-gebied van het Azure-bestandsaandeel, zoals on-premises of in een andere Azure-regio, heeft geen toegang tot het bestandsshare.

Zie [beveiligde overdracht in Azure-opslag vereisen](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)voor meer informatie over versleuteling tijdens het transport.

## <a name="see-also"></a>Zie ook
- [Overzicht van Azure Files](storage-files-introduction.md)
- [Implementatie van Azure Files plannen](storage-files-planning.md)