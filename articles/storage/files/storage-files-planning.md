---
title: Planning voor een Azure Files-implementatie | Microsoft Documenten
description: Lees waar u rekening mee moet houden bij het plannen van een Azure-bestandenimplementatie.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d5bf3a6df9d7292c18a93737fb7dea5d8c91f984
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536478"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planning voor de implementatie van Azure Files
[Azure-bestanden](storage-files-introduction.md) kunnen op twee belangrijke manieren worden geïmplementeerd: door de serverloze Azure-bestandsshares rechtstreeks te monteren of door Azure-bestandsshares on-premises te plaatsen met Azure File Sync. Welke implementatieoptie u kiest, verandert de dingen waarmee u rekening moet houden bij het plannen van uw implementatie. 

- **Directe bevestiging van een Azure-bestandsshare:** Aangezien Azure Files SMB-toegang biedt, u Azure-bestandsshares on-premises of in de cloud monteren met behulp van de standaard SMB-client die beschikbaar is in Windows, macOS en Linux. Omdat Azure-bestandsshares serverloos zijn, hoeft u geen bestandsserver of NAS-apparaat te beheren voor productiescenario's. Dit betekent dat u geen softwarepatches hoeft toe te passen of fysieke schijven hoeft uit te wisselen. 

- **On-premises cache Azure-bestandsshare met Azure File Sync:** met Azure File Sync u de bestandsshares van uw organisatie centraliseren in Azure-bestanden, terwijl de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver behouden blijft. Azure File Sync transformeert een on-premises (of cloud) Windows Server in een snelle cache van uw Azure-bestandsshare. 

In dit artikel worden voornamelijk implementatieoverwegingen voor het implementeren van een Azure-bestandsshare besproken die rechtstreeks worden gestart door een on-premises of cloudclient. Zie Plannen voor een Azure [File Sync-implementatie](storage-sync-files-planning.md)als u een Azure File Sync-implementatie wilt plannen.

## <a name="management-concepts"></a>Managementconcepten
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Wanneer u Azure-bestandsshares implementeert in opslagaccounts, raden we u aan:

- Alleen Azure-bestandsshares implementeren in opslagaccounts met andere Azure-bestandsshares. Hoewel met GPv2-opslagaccounts u opslagaccounts voor gemengde doeleinden hebben, kunnen opslagbronnen zoals Azure-bestandsshares en blob-containers de limieten van het opslagaccount delen, waardoor het later moeilijker kan worden om prestatieproblemen op te lossen. 

- Let op de IOPS-beperkingen van een opslagaccount bij het implementeren van Azure-bestandsshares. Idealiter zou u bestandsshares 1:1 toewijzen aan opslagaccounts, maar dit is mogelijk niet altijd mogelijk vanwege verschillende beperkingen en beperkingen, zowel van uw organisatie als vanuit Azure. Wanneer het niet mogelijk is om slechts één bestandsshare in één opslagaccount te laten implementeren, moet u overwegen welke aandelen zeer actief zullen zijn en welke aandelen minder actief zullen zijn om ervoor te zorgen dat de heetste bestandsshares niet samen in dezelfde opslagaccount worden geplaatst.

- Implementeer alleen GPv2- en FileStorage-accounts en upgrade GPv1- en klassieke opslagaccounts wanneer u ze in uw omgeving vindt. 

## <a name="identity"></a>Identiteit
Als u toegang wilt krijgen tot een Azure-bestandsshare, moet de gebruiker van het bestandsaandeel worden geverifieerd en toestemming hebben om toegang te krijgen tot het aandeel. Dit gebeurt op basis van de identiteit van de gebruiker die toegang heeft tot het bestandsshare. Azure Files integreert met drie belangrijke identiteitsproviders:
- **On-premises Active Directory Domain Services (AD DS of on-premises AD DS)** (voorbeeld): Azure-opslagaccounts kunnen worden samengevoegd met een klantdie eigendom is van Active Directory Domain Services, net als een Windows Server-bestandsserver of NAS-apparaat. U een domeincontroller on-premises, in een Azure VM of zelfs als VM implementeren in een andere cloudprovider. Azure Files is agnostisch voor waar uw domeincontroller wordt gehost. Zodra een opslagaccount is gekoppeld aan een domein, kan de eindgebruiker een bestandsshare monteren met het gebruikersaccount waarmee hij zich op zijn pc heeft aangemeld. Verificatie op basis van AD maakt gebruik van het Kerberos-verificatieprotocol.
- **Azure Active Directory Domain Services (Azure AD DS):** Azure AD DS biedt een door Microsoft beheerde domeincontroller die kan worden gebruikt voor Azure-resources. Domein dat lid wordt van uw opslagaccount met Azure AD DS biedt vergelijkbare voordelen als domeintoetreding tot een Active Directory die eigendom is van een klant. Deze implementatieoptie is het handigst voor toepassingslift- en shiftscenario's waarvoor ad-gebaseerde machtigingen nodig zijn. Aangezien Azure AD DS verificatie op basis van AD biedt, maakt deze optie ook gebruik van het Kerberos-verificatieprotocol.
- **Azure-opslagaccountsleutel**: Azure-bestandsshares kunnen ook worden gemonteerd met een Azure-opslagaccountsleutel. Om een bestandsshare op deze manier te monteren, wordt de naam van het opslagaccount gebruikt als gebruikersnaam en wordt de opslagaccountsleutel als wachtwoord gebruikt. Het gebruik van de opslagaccountsleutel om het Azure-bestandsaandeel te monteren is in feite een beheerbewerking van beheerders, omdat de gemonteerde bestandsshare volledige machtigingen heeft voor alle bestanden en mappen op het aandeel, zelfs als ze ACL's hebben. Wanneer u de opslagaccountsleutel gebruikt om over SMB te monteren, wordt het NTLMv2-verificatieprotocol gebruikt.

Voor klanten die migreren van on-premises bestandsservers of nieuwe bestandsshares maken in Azure-bestanden die bedoeld zijn om zich te gedragen als Windows-bestandsservers of NAS-apparaten, is domein dat uw opslagaccount aansluit bij **Active Directory,** de aanbevolen optie. Zie Azure [Files Active Directory-overzicht](storage-files-active-directory-overview.md)voor meer informatie over het samenvoegen van uw opslagaccount door een domein met een Active Directory van de klant.

Als u de opslagaccountsleutel wilt gebruiken om toegang te krijgen tot uw Azure-bestandsshares, raden we u aan serviceeindpunten te gebruiken zoals beschreven in de sectie [Netwerken.](#networking)

## <a name="networking"></a>Netwerken
Azure-bestandsshares zijn overal toegankelijk via het openbare eindpunt van het opslagaccount. Dit betekent dat geverifieerde aanvragen, zoals aanvragen die zijn geautoriseerd door de aanmeldingsidentiteit van een gebruiker, veilig kunnen afkomstig zijn van binnen of buiten Azure. In veel klantomgevingen mislukt een eerste bevestiging van het Azure-bestandsaandeel op uw on-premises werkstation, ook al slagen mounts van Azure VM's. De reden hiervoor is dat veel organisaties en internet service providers (ISP's) blokkeren de poort die SMB gebruikt om te communiceren, poort 445. Ga naar [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) voor een overzicht van welke internetproviders toegang via poort 445 toestaan en welke niet.

Als u de toegang tot uw Azure-bestandsshare wilt deblokkeren, hebt u twee belangrijke opties:

- Blokkeer poort 445 voor het on-premises netwerk van uw organisatie. Azure-bestandsshares zijn alleen extern toegankelijk via het openbare eindpunt via internetveilige protocollen zoals SMB 3.0 en de FileREST API. Dit is de eenvoudigste manier om toegang te krijgen tot uw Azure-bestandsshare van on-premises, omdat hiervoor geen geavanceerde netwerkconfiguratie nodig is die verder gaat dan het wijzigen van de uitgaande poortregels van uw organisatie, maar we raden u aan oudere en afgeschafte versies van het SMB-protocol, namelijk SMB 1.0, te verwijderen. Zie [Windows/Windows Server beveiligen](storage-how-to-use-files-windows.md#securing-windowswindows-server) en Linux [beveiligen](storage-how-to-use-files-linux.md#securing-linux)voor meer informatie.

- Toegang tot Azure-bestand deelt via een ExpressRoute- of VPN-verbinding. Wanneer u uw Azure-bestandsshare opent via een netwerktunnel, u uw Azure-bestandsshare als een on-premises bestandsshare monteren, omdat smb-verkeer uw organisatiegrens niet overschrijdt.   

Hoewel het vanuit technisch oogpunt aanzienlijk eenvoudiger is om uw Azure-bestandsshares te monteren via het openbare eindpunt, verwachten we dat de meeste klanten ervoor zullen kiezen om hun Azure-bestandsshares te monteren via een ExpressRoute- of VPN-verbinding. Hiervoor moet u het volgende configureren voor uw omgeving:  

- **Netwerktunneling met ExpressRoute, Site-to-Site of Point-to-Site VPN:** Tunneling in een virtueel netwerk maakt toegang tot Azure-bestandsshares van on-premises mogelijk, zelfs als poort 445 is geblokkeerd.
- **Privéeindpunten**: Privéeindpunten geven uw opslagaccount een speciaal IP-adres vanuit de adresruimte van het virtuele netwerk. Hierdoor kunnen netwerktunneling zonder on-premises netwerken te openen tot alle IP-adresbereiken die eigendom zijn van de Azure-opslagclusters. 
- **DNS forwarding**: Configureer uw on-premises DNS om de naam `storageaccount.file.core.windows.net` van uw opslagaccount (d.w.z. voor de openbare cloudregio's) op te lossen om het IP-adres van uw privéeindpunten op te lossen.

Zie [Azure Files-netwerkoverwegingen](storage-files-networking-overview.md)voor het plannen van de netwerken die zijn gekoppeld aan het implementeren van een Azure-bestandsshare.

## <a name="encryption"></a>Versleuteling
Azure Files ondersteunt twee verschillende soorten versleuteling: versleuteling tijdens het transport, die betrekking heeft op de versleuteling die wordt gebruikt bij het monteren/openen van de Azure-bestandsshare, en versleuteling in rust, die betrekking heeft op de manier waarop de gegevens worden versleuteld wanneer deze op schijf worden opgeslagen. 

### <a name="encryption-in-transit"></a>Versleuteling 'in transit'
Standaard hebben alle Azure-opslagaccounts versleuteling in transit ingeschakeld. Dit betekent dat wanneer u een bestandsshare monteert via SMB of deze opent via het FileREST-protocol (bijvoorbeeld via de Azure-portal, PowerShell/CLI of Azure SDKs), Azure Files de verbinding alleen toestaat als deze is gemaakt met SMB 3.0+ met versleuteling of HTTPS. Clients die geen SMB 3.0 ondersteunen of clients die SMB 3.0 ondersteunen, maar geen SMB-versleuteling, kunnen het Azure-bestandsaandeel niet monteren als versleuteling tijdens het transport is ingeschakeld. Voor meer informatie over welke besturingssystemen SMB 3.0 ondersteunen met versleuteling, zie onze gedetailleerde documentatie voor [Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)en [Linux](storage-how-to-use-files-linux.md). Alle huidige versies van de PowerShell-, CLI- en SDK's ondersteunen HTTPS.  

U versleuteling tijdens het transport uitschakelen voor een Azure-opslagaccount. Wanneer versleuteling is uitgeschakeld, staat Azure Files ook SMB 2.1, SMB 3.0 zonder versleuteling en onversleutelde FileREST API-aanroepen via HTTP toe. De belangrijkste reden om versleuteling tijdens het transport uit te schakelen, is het ondersteunen van een verouderde toepassing die moet worden uitgevoerd op een ouder besturingssysteem, zoals Windows Server 2008 R2 of oudere Linux-distributie. Azure Files staat alleen SMB 2.1-verbindingen toe binnen hetzelfde Azure-gebied als de Azure-bestandsshare. een SMB 2.1-client buiten het Azure-gebied van het Azure-bestandsaandeel, zoals on-premises of in een andere Azure-regio, heeft geen toegang tot het bestandsshare.

We raden u ten zeerste aan ervoor te zorgen dat versleuteling van gegevens tijdens het transport is ingeschakeld.

Zie [beveiligde overdracht in Azure-opslag vereisen](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)voor meer informatie over versleuteling tijdens het transport.

### <a name="encryption-at-rest"></a>Versleuteling 'at rest'
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Opslaglagen
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

In het algemeen zijn azure-bestandenfuncties en interoperabiliteit met andere services hetzelfde tussen premium bestandsshares en standaardbestandsshares, maar er zijn een paar belangrijke verschillen:
- **Factureringsmodel**
    - Premium bestandsaandelen worden gefactureerd met behulp van een ingericht factureringsmodel, wat betekent dat u betaalt voor hoeveel opslagruimte u indient in plaats van hoeveel opslagruimte u daadwerkelijk vraagt. 
    - Standaardbestandsshares worden gefactureerd met behulp van een pay-as-you-go-model, dat een basiskosten van opslag bevat voor hoeveel opslagruimte u daadwerkelijk verbruikt en vervolgens een extra transactiekosten op basis van hoe u het aandeel gebruikt. Met standaard bestandsshares wordt uw factuur verhoogd als u de Azure-bestandsshare meer gebruikt (lezen/schrijven/monteren).
- **Redundantieopties**
    - Premium bestandsshares zijn alleen beschikbaar voor lokaal redundante (LRS) en zone redundante (ZRS) opslag. 
    - Standaard bestandsshares zijn beschikbaar voor lokaal redundante, zone redundante, geo-redundante (GRS) en geo-zone redundante (GZRS) opslag.
- **Maximale grootte van bestandsshare**
    - Premium bestandsaandelen kunnen worden ingericht voor maximaal 100 TiB zonder extra werk.
    - Standaard kunnen standaard bestandsshares slechts tot 5 TiB omvatten, hoewel de limiet voor delen kan worden verhoogd tot 100 TiB door in te kiezen voor de vlag van de *grote bestandsshare-account.* Standaardbestandsshares mogen maximaal 100 TiB omvatten voor lokaal redundante of zoneredundante opslagaccounts. Zie [Grote bestandsshares inschakelen en maken](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share)voor meer informatie over het vergroten van bestandsgroottes.
- **Regionale beschikbaarheid**
    - Premium bestandsshares zijn niet in elke regio beschikbaar en zoneredundante ondersteuning is beschikbaar in een kleinere subset van regio's. Zie de producten die beschikbaar zijn [op regiopagina](https://azure.microsoft.com/global-infrastructure/services/?products=storage) voor Azure om erachter te komen of er momenteel premium bestandsshares beschikbaar zijn in uw regio. Zie [Ondersteuning voor Azure Availability Zone per regio](../../availability-zones/az-overview.md#services-support-by-region)voor meer informatie over welke regio's ZRS ondersteunen. Vul deze [enquête](https://aka.ms/pfsfeedback)in om ons te helpen prioriteit te geven aan nieuwe regio's en functies van de premiumlaag.
    - Standaardbestandsshares zijn beschikbaar in elke Azure-regio.
- Azure Kubernetes Service (AKS) ondersteunt premium bestandsshares in versie 1.13 en hoger.

Zodra een bestandsshare is gemaakt als een premium of een standaard bestandsshare, u deze niet automatisch converteren naar de andere laag. Als u wilt overschakelen naar de andere laag, moet u een nieuwe bestandsshare in die laag maken en de gegevens van uw oorspronkelijke share handmatig kopiëren naar het nieuwe aandeel dat u hebt gemaakt. We raden `robocopy` u `rsync` aan om deze kopie te gebruiken voor Windows of voor macOS en Linux.

### <a name="understanding-provisioning-for-premium-file-shares"></a>Inzicht in de inrichting van premium bestandsaandelen
Premium bestandsaandelen worden ingericht op basis van een vaste GiB/IOPS/throughput ratio. Voor elke gib-voorziening wordt het aandeel één IOPS en 0,1 MiB/s-doorvoer tot de maximale limieten per aandeel uitgegeven. De minimaal toegestane provisioning is 100 GiB met min IOPS/throughput.

Op basis van de beste inspanning kunnen alle aandelen tot drie IOPS per GiB van ingerichte opslag gedurende 60 minuten of langer barsten, afhankelijk van de grootte van het aandeel. Nieuwe aandelen beginnen met het volledige burst-krediet op basis van de ingerichte capaciteit.

Aandelen moeten worden ingericht in 1 GiB stappen. Minimale grootte is 100 GiB, volgende maat is 101 GiB en ga zo maar door.

> [!TIP]
> Baseline IOPS = 1 * ingerichte GiB. (Tot een maximum van 100.000 IOPS).
>
> Burst Limit = 3 * Baseline IOPS. (Tot een maximum van 100.000 IOPS).
>
> uitgangssnelheid = 60 MiB/s + 0,06 * ingerichte GiB
>
> invallen = 40 MiB/s + 0,04 * ingerichte GiB

De ingerichte aandelenomvang wordt gespecificeerd op aandelenquotum. Het quotum voor delen kan op elk moment worden verhoogd, maar kan pas na 24 uur sinds de laatste verhoging worden verlaagd. Nadat u 24 uur hebt gewacht zonder een quotumverhoging, u het quotum voor delen zo vaak verlagen als u wilt, totdat u het opnieuw verhoogt. Veranderingen in de IOPS/Doorvoerschaal zijn binnen enkele minuten na de wijziging van de grootte van kracht.

Het is mogelijk om de grootte van uw ingerichte aandeel onder uw gebruikte GiB te verkleinen. Als u dit doet, verliest u geen gegevens, maar wordt u nog steeds gefactureerd voor de gebruikte grootte en ontvangt u de prestaties (baseline IOPS, doorvoer en burst IOPS) van het ingerichte aandeel, niet de gebruikte grootte.

In de volgende tabel worden enkele voorbeelden van deze formules weergegeven voor de ingerichte delen:

|Capaciteit (GiB) | IOPS basislijn | Burst IOPS | Uitgang (MiB/s) | Ingress (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Maximaal 300     | 66   | 44   |
|500         | 500     | Maximaal 1.500   | 90   | 60   |
|1,024       | 1,024   | Maximaal 3.072   | 122   | 81   |
|5,120       | 5,120   | Maximaal 15.360  | 368   | 245   |
|10,240      | 10,240  | Maximaal 30.720  | 675 | 450   |
|33,792      | 33,792  | Tot 100.000 | 2,088 | 1,392   |
|51,200      | 51,200  | Tot 100.000 | 3,132 | 2,088   |
|102,400     | 100.000 | Tot 100.000 | 6,204 | 4,136   |

> [!NOTE]
> Bestandsshares prestaties is onderworpen aan machine netwerk grenzen, beschikbare netwerkbandbreedte, IO-formaten, parallellisme, onder vele andere factoren. Bijvoorbeeld, op basis van interne testen met 8 KiB lees / schrijven IO-formaten, een enkele Windows virtuele machine, *Standard F16s_v2*, aangesloten op premium bestandsaandeel over SMB zou kunnen bereiken 20K lees IOPS en 15K schrijven IOPS. Met 512 MiB lees/schrijf IO-formaten kan dezelfde VM 1,1 GiB/s uitgang bereiken en 370 MiB/s invallen doorvoer. Als u een maximale prestatieschaal wilt bereiken, spreidt u de belasting over meerdere VM's. Raadpleeg de handleiding voor [probleemoplossing](storage-troubleshooting-files-performance.md) voor een aantal veelvoorkomende prestatieproblemen en tijdelijke oplossingen.

#### <a name="bursting"></a>Barsten
Premium bestandsaandelen kunnen hun IOPS tot een factor drie laten barsten. Bursting is geautomatiseerd en werkt op basis van een kredietsysteem. Bursting werkt op een best effort basis en de burst limiet is geen garantie, bestand aandelen kunnen barsten *tot* het uiterste.

Credits accumuleren in een burst bucket wanneer het verkeer voor uw bestandsaandeel lager is dan baseline IOPS. Een 100 GiB-aandeel heeft bijvoorbeeld 100 IOPS per basislijn. Als het werkelijke verkeer op het aandeel was 40 IOPS voor een specifieke 1-seconden interval, dan is de 60 ongebruikte IOPS worden bijgeschreven op een burst emmer. Deze credits worden later gebruikt wanneer bewerkingen de baseline IOPs zouden overschrijden.

> [!TIP]
> Grootte van de burst bucket = Baseline IOPS * 2 * 3600.

Wanneer een aandeel hoger is dan de baseline IOPS en heeft credits in een burst emmer, zal barsten. Aandelen kunnen blijven barsten zolang de credits overblijven, hoewel aandelen kleiner dan 50 TiB slechts tot een uur aan de burst-limiet blijven. Aandelen groter dan 50 TiB kunnen technisch gezien deze limiet van één uur overschrijden, tot twee uur, maar dit is gebaseerd op het aantal opgebouwde burstcredits. Elke IO voorbij baseline IOPS verbruikt een krediet en zodra alle credits zijn verbruikt het aandeel zou terugkeren naar baseline IOPS.

Aandelencredits hebben drie staten:

- Accruing, wanneer de bestandsshare minder gebruikt dan de baseline IOPS.
- Dalend, wanneer het bestandsaandeel barst.
- Constant blijven, wanneer er geen credits of baseline IOPS in gebruik zijn.

Nieuwe bestandsaandelen beginnen met het volledige aantal credits in de burst-bucket. Burst-credits worden niet gegenereerd als de iOPS van het aandeel onder de IOPS van de basislijn valt als gevolg van beperking door de server.

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Standaardbestandsshares inschakelen tot 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="limitations"></a>Beperkingen
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Redundantie
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migratie
In veel gevallen maakt u geen nieuwe bestandsshare voor uw organisatie, maar migreert u een bestaande bestandsshare van een on-premises bestandsserver of NAS-apparaat naar Azure Files. Er zijn veel tools, die zowel door Microsoft als door derden worden geleverd, om een migratie naar een bestandsshare uit te brengen, maar ze kunnen grofweg worden onderverdeeld in twee categorieën:

- **Hulpprogramma's die bestandssysteemkenmerken zoals ACL's en tijdstempels bijhouden:**
    - **[Azure File Sync](storage-sync-files-planning.md)**: Azure File Sync kan worden gebruikt als een methode om gegevens in te nemen in een Azure-bestandsshare, zelfs wanneer de gewenste eindimplementatie niet is om een on-premises aanwezigheid te behouden. Azure File Sync kan op zijn plaats worden geïnstalleerd op bestaande Windows Server 2012 R2-, Windows Server 2016- en Windows Server 2019-implementaties. Een voordeel van het gebruik van Azure File Sync als innamemechanisme is dat eindgebruikers de bestaande bestandsshare kunnen blijven gebruiken. cut-over naar de Azure-bestandsshare kan optreden nadat alle gegevens zijn voltooid met het uploaden op de achtergrond.
    - **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: Robocopy is een bekende kopieertool die wordt geleverd met Windows en Windows Server. Robocopy kan worden gebruikt om gegevens over te zetten naar Azure-bestanden door het bestandsaandeel lokaal te monteren en vervolgens de gemonteerde locatie als bestemming te gebruiken in de opdracht Robocopy.

- **Hulpprogramma's die geen bestandssysteemkenmerken bijhouden:**
    - **Gegevensvak:** Gegevensvak biedt een offline mechanisme voor gegevensoverdracht om gegevens fysiek naar Azure te verzenden. Deze methode is ontworpen om de doorvoer te verhogen en bandbreedte te besparen, maar ondersteunt momenteel geen bestandssysteemkenmerken zoals tijdstempels en ACL's.
    - **[AzCopy:](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** AzCopy is een command-line hulpprogramma ontworpen voor het kopiëren van gegevens van en naar Azure Files, evenals Azure Blob-opslag, met behulp van eenvoudige opdrachten met optimale prestaties.

## <a name="next-steps"></a>Volgende stappen
* [Planning voor een Azure File Sync Deployment](storage-sync-files-planning.md)
* [Azure-bestanden implementeren](storage-files-deployment-guide.md)
* [Azure-bestandssynchronisatie implementeren](storage-sync-files-deployment-guide.md)
