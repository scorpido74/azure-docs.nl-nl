---
title: Een Azure Files-implementatie plannen | Microsoft Docs
description: Meer informatie over hoe u rekening moet houden bij het plannen van een Azure Files-implementatie.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 5356ff0ac165deefc5053cf4faa40c1159e98678
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856896"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planning voor de implementatie van Azure Files
[Azure files](storage-files-introduction.md) kunnen op twee manieren worden geïmplementeerd: door de Serverloze Azure-bestands shares rechtstreeks te koppelen of door Azure-bestands shares on-premises in de cache op te maken met behulp van Azure file sync. Welke implementatie optie u kiest, wijzigt de dingen die u moet overwegen bij het plannen van uw implementatie. 

- **Directe koppeling van een Azure-bestands share**: omdat Azure files SMB-toegang biedt, kunt u Azure-bestands shares on-premises of in de Cloud koppelen met behulp van de standaard-SMB-client die beschikbaar is in Windows, MacOS en Linux. Omdat Azure-bestands shares serverloos zijn, is voor het implementeren van productie scenario's geen bestands server of een NAS-apparaat nodig. Dit betekent dat u geen software patches hoeft toe te passen of fysieke schijven hoeft uit te wisselen. 

- **Azure-bestands share op locatie opslaan in de cache met Azure file sync**: Azure File Sync kunt u de bestands shares van uw organisatie in azure files centraliseren en tegelijkertijd de flexibiliteit, prestaties en compatibiliteit van een on-premises Bestands server behouden. Azure File Sync transformeert een on-premises Windows-Server (of Cloud) naar een snelle cache van uw Azure-bestands share. 

Dit artikel heeft voornamelijk betrekking op implementatie overwegingen voor het implementeren van een Azure-bestands share om rechtstreeks te worden gekoppeld door een on-premises of cloud-client. Zie [planning voor een implementatie van een Azure file sync](storage-sync-files-planning.md)om een Azure file sync implementatie te plannen.

## <a name="management-concepts"></a>Beheer concepten
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Wanneer u Azure-bestands shares in opslag accounts implementeert, kunt u het volgende doen:

- Implementeer alleen Azure-bestands shares in opslag accounts met andere Azure-bestands shares. Hoewel u met GPv2-opslag accounts gebruik kunt maken van gemengde opslag accounts, omdat opslag resources zoals Azure-bestands shares en BLOB-containers de limieten van het opslag account delen, kan het lastiger zijn om sneller problemen op te lossen. 

- Let op de limieten voor de IOPS van een opslag account bij het implementeren van Azure-bestands shares. In het ideale geval wijst u bestands shares 1:1 met opslag accounts toe, maar dit is mogelijk niet altijd mogelijk als gevolg van verschillende limieten en beperkingen, zowel vanuit uw organisatie als vanuit Azure. Wanneer het niet mogelijk is om slechts één bestands share in één opslag account te implementeren, moet u overwegen welke shares Maxi maal actief zullen zijn en welke shares minder actief zijn om ervoor te zorgen dat de meest uitgebreide bestands shares niet samen in hetzelfde opslag account worden geplaatst.

- Implementeer alleen GPv2-en FileStorage-accounts en werk GPv1 en klassieke opslag accounts bij wanneer u deze in uw omgeving vindt. 

## <a name="identity"></a>Identiteit
Om toegang te krijgen tot een Azure-bestands share, moet de gebruiker van de bestands share worden geverifieerd en moeten ze toegang hebben tot de share. Dit wordt gedaan op basis van de identiteit van de gebruiker die toegang heeft tot de bestands share. Azure Files integreert met drie belangrijkste id-providers:
- **On-premises Active Directory Domain Services (AD DS, of on-premises AD DS)** (preview): Azure-opslag accounts kunnen lid zijn van een domein, Active Directory Domain Services, net als een Windows Server-Bestands server of een NAS-apparaat. U kunt een domein controller on-premises, in een Azure-VM of zelfs als een virtuele machine in een andere Cloud provider implementeren. Azure Files is neutraal waar uw domein controller wordt gehost. Als een opslag account is toegevoegd aan een domein, kan de eind gebruiker een bestands share koppelen met het gebruikers account waarmee ze zijn aangemeld bij hun PC. Op AD gebaseerde verificatie maakt gebruik van het Kerberos-verificatie protocol.
- **Azure Active Directory Domain Services (azure AD DS)**: Azure AD DS biedt een door micro soft beheerde domein controller die kan worden gebruikt voor Azure-resources. Een domein dat wordt toegevoegd aan uw opslag account voor Azure AD DS biedt vergelijk bare voor delen als het domein wordt toegevoegd aan een Active Directory van een klant. Deze implementatie optie is het nuttigst voor toepassingen die zijn gebaseerd op AD-machtigingen. Omdat Azure AD DS op AD gebaseerde verificatie biedt, gebruikt deze optie ook het Kerberos-verificatie protocol.
- **Azure Storage-account sleutel**: Azure-bestands shares kunnen ook worden gekoppeld met een Azure Storage-account sleutel. Als u een bestands share op deze manier wilt koppelen, wordt de naam van het opslag account gebruikt als de gebruikers naam en de sleutel voor het opslag account als wacht woord. Het gebruik van de sleutel van het opslag account voor het koppelen van de Azure-bestands share is in feite een beheerders bewerking, omdat de gekoppelde bestands share volledige machtigingen heeft voor alle bestanden en mappen op de share, zelfs als deze Acl's hebben. Wanneer u de sleutel voor het opslag account gebruikt om te koppelen via SMB, wordt het NTLMv2-verificatie protocol gebruikt.

Voor klanten die migreren van on-premises bestands servers of het maken van nieuwe bestands shares in Azure Files bedoeld om te fungeren als Windows-bestands servers of NAS-apparaten, is het domein dat wordt toegevoegd aan de door de **klant Active Directory** de aanbevolen optie. Zie [Azure Files Active Directory Overview](storage-files-active-directory-overview.md)voor meer informatie over het toevoegen van uw opslag account aan een Active Directory van de klant.

Als u van plan bent om de sleutel van het opslag account te gebruiken voor toegang tot uw Azure-bestands shares, kunt u het beste service-eind punten gebruiken zoals beschreven in de sectie [netwerken](#networking) .

## <a name="networking"></a>Netwerken
Azure-bestands shares zijn overal toegankelijk via het open bare eind punt van het opslag account. Dit betekent dat geverifieerde aanvragen, zoals aanvragen die zijn geautoriseerd door de aanmeldings identiteit van een gebruiker, veilig kunnen zijn van binnen of buiten Azure. In veel klanten omgevingen kan een initiële koppeling van de Azure-bestands share op uw on-premises werk station mislukken, zelfs als de koppeling van Azure-Vm's slaagt. De reden hiervoor is dat veel organisaties en Internet serviceproviders (Isp's) de poort blok keren die door SMB wordt gebruikt om te communiceren, poort 445. Ga naar [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) voor een overzicht van welke internetproviders toegang via poort 445 toestaan en welke niet.

Als u de toegang tot uw Azure-bestands share wilt blok keren, hebt u twee belang rijke opties:

- Deblokkeren van poort 445 voor het on-premises netwerk van uw organisatie. Azure-bestands shares zijn mogelijk alleen extern toegankelijk via het open bare eind punt met behulp van veilige protocollen voor Internet, zoals SMB 3,0 en de FileREST-API. Dit is de eenvoudigste manier om toegang te krijgen tot uw Azure-bestands share vanaf een on-premises omdat er geen geavanceerde netwerk configuratie nodig is dan het wijzigen van de uitgaande poort regels van uw organisatie, maar we raden u echter aan verouderde en verouderde versies van het SMB-protocol te verwijderen, namelijk SMB 1,0. Zie [Windows/Windows Server beveiligen](storage-how-to-use-files-windows.md#securing-windowswindows-server) en [Linux beveiligen](storage-how-to-use-files-linux.md#securing-linux)voor meer informatie over hoe u dit doet.

- Toegang tot Azure-bestands shares via een ExpressRoute of een VPN-verbinding. Wanneer u toegang hebt tot uw Azure-bestands share via een netwerk tunnel, kunt u uw Azure-bestands share koppelen zoals een on-premises bestands share omdat het SMB-verkeer uw organisatie grens niet overschrijdt.   

Hoewel het vanuit een technisch perspectief aanzienlijk eenvoudiger is om uw Azure-bestands shares te koppelen via het open bare eind punt, zullen de meeste klanten ervoor kiezen hun Azure-bestands shares te koppelen via een ExpressRoute of een VPN-verbinding. Hiervoor moet u het volgende configureren voor uw omgeving:  

- **Netwerk tunneling met behulp van ExpressRoute, site-naar-site-of punt-naar-site-VPN**: tunneling naar een virtueel netwerk maakt toegang tot Azure-bestands shares van on-premises mogelijk, zelfs als poort 445 wordt geblokkeerd.
- **Persoonlijke eind punten**: persoonlijke eind punten geven uw opslag account een toegewezen IP-adres binnen de adres ruimte van het virtuele netwerk. Hierdoor kan netwerk tunneling worden ingeschakeld zonder dat u on-premises netwerken hoeft te openen tot alle IP-adresbereiken die eigendom zijn van de Azure Storage-clusters. 
- **Door sturen via DNS**: Configureer uw on-premises DNS om de naam van uw opslag account ( `storageaccount.file.core.windows.net` bijvoorbeeld voor de open bare Cloud regio's) om te zetten in het IP-adres van uw privé-eind punten.

Zie [Azure files netwerk overwegingen](storage-files-networking-overview.md)voor het plannen van de netwerken die zijn gekoppeld aan de implementatie van een Azure-bestands share.

## <a name="encryption"></a>Versleuteling
Azure Files ondersteunt twee verschillende soorten versleuteling: versleuteling in transit, dat betrekking heeft op de versleuteling die wordt gebruikt bij het koppelen/openen van de Azure-bestands share, en versleuteling op rest, die betrekking heeft op hoe de gegevens worden versleuteld wanneer deze op schijf worden opgeslagen. 

### <a name="encryption-in-transit"></a>Versleuteling 'in transit'
Standaard hebben alle Azure Storage-accounts versleuteling in transit ingeschakeld. Dit betekent dat wanneer u een bestands share koppelt via SMB of toegang krijgt via het FileREST-Protocol (zoals via de Azure Portal, Power shell/CLI of Azure Sdk's), Azure Files alleen de verbinding toestaat als deze wordt gemaakt met SMB 3.0 + met versleuteling of HTTPS. Clients die geen ondersteuning bieden voor SMB 3,0 of clients die SMB 3,0 ondersteunen maar geen SMB-versleuteling, kunnen de Azure-bestands share niet koppelen als versleuteling in transit is ingeschakeld. Zie de gedetailleerde documentatie voor [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)en [Linux](storage-how-to-use-files-linux.md)voor meer informatie over welke besturings systemen SMB 3,0 met versleuteling ondersteunen. Alle huidige versies van de Power shell, CLI en Sdk's ondersteunen HTTPS.  

U kunt versleuteling voor een Azure Storage-account uitschakelen in door voer. Wanneer versleuteling is uitgeschakeld, staat Azure Files ook toe dat SMB 2,1, SMB 3,0 zonder versleuteling en niet-versleutelde API-aanroepen van FileREST via HTTP. De belangrijkste reden om versleuteling in transit uit te scha kelen, is het ondersteunen van een verouderde toepassing die moet worden uitgevoerd op een ouder besturings systeem, zoals Windows Server 2008 R2 of een oudere Linux-distributie. Met Azure Files worden SMB 2,1-verbindingen alleen toegestaan binnen dezelfde Azure-regio als de Azure-bestands share. een SMB 2,1-client buiten de Azure-regio van de Azure-bestands share, zoals on-premises of in een andere Azure-regio, heeft geen toegang tot de bestands share.

We raden u ten zeerste aan te zorgen dat de versleuteling van gegevens in-transit is ingeschakeld.

Zie [veilige overdracht vereisen in azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)voor meer informatie over het door sturen van versleuteling.

### <a name="encryption-at-rest"></a>Versleuteling 'at rest'
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Opslaglagen
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

Over het algemeen zijn Azure Files functies en interoperabiliteit met andere services hetzelfde als voor Premium-bestands shares en standaard bestands shares, maar er zijn echter enkele belang rijke verschillen:
- **Factureringsmodel**
    - Premium-bestands shares worden gefactureerd met behulp van een ingericht facturerings model. Dit betekent dat u betaalt voor de hoeveelheid opslag ruimte die u in plaats van de hoeveelheid opslag die u daad werkelijk vraagt. 
    - Standaard bestands shares worden gefactureerd op basis van een betalen per gebruik-model, dat een basis kosten voor opslag bevat voor de hoeveelheid opslag die u daad werkelijk verbruikt en vervolgens een extra transactie kosten op basis van hoe u de share gebruikt. Bij standaard bestands shares neemt uw factuur toe als u de Azure-bestands share gebruikt (lezen/schrijven/koppelen).
- **Redundantie opties**
    - Premium-bestands shares zijn alleen beschikbaar voor lokaal redundante (LRS) en zone redundante opslag (ZRS).
    - Standaard bestands shares zijn beschikbaar voor lokaal redundante, zone redundante, geo-redundante (GRS) en geo-zone redundante opslag (GZRS).
- **Maximale grootte van bestands share**
    - Premium-bestands shares kunnen Maxi maal 100 TiB worden ingericht zonder verdere werkzaamheden.
    - Standaard kunnen standaard bestands shares slechts tot 5 TiB groot zijn, hoewel de limiet voor delen kan worden verhoogd tot 100 TiB door u aan te melden bij de functie vlag voor het opslag account voor *grote bestands shares* . Standaard bestands shares kunnen Maxi maal 100 TiB zijn voor lokaal redundante of zone redundante opslag accounts. Zie [grote bestands shares inschakelen en maken](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share)voor meer informatie over het verhogen van de grootte van de bestands share.
- **Regionale beschikbaarheid**
    - Premium-bestands shares zijn niet beschikbaar in elke regio en zone redundante ondersteuning is beschikbaar in een kleinere subset van regio's. Zie de pagina [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=storage) voor Azure als u wilt weten of Premium-bestands shares op dit moment beschikbaar zijn in uw regio. Zie [Azure Availability zone Support per regio](../../availability-zones/az-region.md)als u wilt weten welke regio's ZRS ondersteunen. Vul deze [enquête](https://aka.ms/pfsfeedback)in om u te helpen bij het bepalen van de prioriteit van nieuwe regio's en de functies van de Premium-laag.
    - Standaard bestands shares zijn beschikbaar in elke Azure-regio.
- Azure Kubernetes service (AKS) biedt ondersteuning voor Premium-bestands shares in versie 1,13 en hoger.

Als een bestands share is gemaakt als een Premium-of een standaard bestands share, kunt u deze niet automatisch converteren naar de andere laag. Als u wilt overschakelen naar de andere laag, moet u een nieuwe bestands share in die laag maken en de gegevens van de oorspronkelijke share hand matig kopiëren naar de nieuwe share die u hebt gemaakt. U kunt het `robocopy` beste voor Windows `rsync` of voor macOS en Linux gebruiken om die kopie uit te voeren.

### <a name="understanding-provisioning-for-premium-file-shares"></a>Informatie over het inrichten voor Premium-bestands shares
Premium-bestands shares worden ingericht op basis van een vaste GiB/IOPS/doorvoer ratio. Voor elke GiB die is ingericht, wordt de share één IOPS en 0,1 MiB/s-door Voer verleend tot het maximum aantal limieten per share. De mini maal toegestane inrichting is 100 GiB met een minimale IOPS/door voer.

Op basis van de beste inspanningen kunnen alle shares Maxi maal drie IOPS per GiB aan ingerichte opslag gedurende 60 minuten of langer, afhankelijk van de grootte van de share. Nieuwe shares beginnen met het volledige burst-tegoed op basis van de ingerichte capaciteit.

Shares moeten worden ingericht in 1 GiB-stappen. De minimale grootte is 100 GiB, de volgende grootte is 101 GiB, enzovoort.

> [!TIP]
> Basis lijn IOPS = 1 * ingerichte GiB. (Maxi maal 100.000 IOPS).
>
> Burst-limiet = 3 * IOPS basis lijn. (Maxi maal 100.000 IOPS).
>
> uitgangs bedrag = 60 MiB/s + 0,06 * ingerichte GiB
>
> Ingangs frequentie = 40 MiB/s + 0,04 * ingerichte GiB

De inrichtings share grootte wordt opgegeven door het share quotum. Share quotum kan op elk moment worden verhoogd, maar kan pas na 24 uur na de laatste toename worden verlaagd. Nadat u 24 uur hebt gewacht zonder een quota verhoging, kunt u het share quotum zo vaak als u wilt verlagen totdat u het opnieuw verhoogt. Wijzigingen in IOPS/doorvoer schaal worden binnen een paar minuten na de grootte gewijzigd.

Het is mogelijk om de grootte van uw ingerichte share onder uw gebruikte GiB te verkleinen. Als u dit doet, verliest u geen gegevens, maar worden er nog steeds kosten in rekening gebracht voor de gebruikte grootte en worden de prestaties (de basis-IOPS, door Voer en burst IOPS) van de ingerichte share ontvangen, niet de gebruikte grootte.

In de volgende tabel ziet u enkele voor beelden van deze formules voor de ingerichte grootte van de shares:

|Capaciteit (GiB) | IOPS basis lijn | Burst IOPS | Uitgang (MiB/s) | Ingangs (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Maxi maal 300     | 66   | 44   |
|500         | 500     | Maxi maal 1.500   | 90   | 60   |
|1.024       | 1.024   | Maxi maal 3.072   | 122   | 81   |
|5.120       | 5.120   | Maxi maal 15.360  | 368   | 245   |
|10.240      | 10.240  | Maxi maal 30.720  | 675 | 450   |
|33.792      | 33.792  | Maxi maal 100.000 | 2.088 | 1.392   |
|51.200      | 51.200  | Maxi maal 100.000 | 3.132 | 2.088   |
|102.400     | 100.000 | Maxi maal 100.000 | 6.204 | 4.136   |

> [!NOTE]
> De prestaties van bestands shares zijn afhankelijk van de netwerk limieten van de computer, de beschik bare netwerk bandbreedte, i/o-grootte, de parallelle uitvoering, onder andere factoren. Een voor beeld: op basis van een interne test met 8 KiB i/o-schrijf grootten, kan één virtuele Windows-machine, *standaard F16s_v2*, die is verbonden met Premium file share over SMB, 20.000 Read IOPS en Maxi maal 15.000 schrijf-IOPS behaalt. Met 512 MiB lees/schrijf-i/o-groottes kan dezelfde virtuele machine 1,1 GiB/s uitgaand worden gemaakt en 370 MiB/s door voer. U kunt de maximale prestaties schalen door de belasting over meerdere virtuele machines uit te breiden. Raadpleeg de [gids voor probleem oplossing](storage-troubleshooting-files-performance.md) voor enkele veelvoorkomende prestatie problemen en tijdelijke oplossingen.

#### <a name="bursting"></a>Toepassingen
Premium-bestands shares kunnen de IOPS opbursten tot een factor van drie. Bursting wordt geautomatiseerd en werkt op basis van een tegoed systeem. Bursting werkt op basis van de beste inspanningen en de burst-limiet is geen garantie. bestands shares *kunnen de limiet* overschrijden.

De tegoeden worden in een burst-Bucket verzameld wanneer het verkeer voor uw bestands share onder IOPS voor de basis lijn valt. Een GiB-share van 100 heeft bijvoorbeeld 100 Baseline IOPS. Als het werkelijke verkeer op de share 40 IOPS is voor een specifiek interval van 1 seconde, worden 60 de ongebruikte IOPS gecrediteerd op een burst-Bucket. Deze tegoeden worden vervolgens later gebruikt wanneer bewerkingen de basis lijn van IOPs overschrijden.

> [!TIP]
> Grootte van de burst Bucket = basislijn IOPS * 2 * 3600.

Wanneer een share de limiet van de basis lijn overschrijdt en er tegoed in een burst-Bucket staat, wordt deze gesplitst. Shares kunnen blijven worden opgedeeld zolang de tegoeden resteren, terwijl de shares die kleiner zijn dan 50 TiB alleen tot een uur in de burst-limiet blijven. Shares groter dan 50 TiB kan technisch deze limiet van één uur overschrijden, tot twee uur, maar dit is gebaseerd op het aantal burst-tegoeden dat is samengevoegd. Elke i/o-limiet van meer dan Baseline IOPS maakt gebruik van één tegoed en wanneer alle tegoeden worden verbruikt, wordt de share teruggestuurd naar de limiet van de basis lijn

Aandelen tegoeden hebben drie statussen:

- Samen, wanneer de bestands share minder dan de basis lijn voor IOPS gebruikt.
- Weigeren, wanneer de bestands share bursting is.
- De resterende constante wanneer er geen tegoeden of basis-IOPS in gebruik zijn.

Nieuwe bestands shares beginnen met het volledige aantal tegoeden in de burst-Bucket. Burst-tegoed wordt niet in rekening gebracht als de delen IOPS onder de basis lijn IOPS vallen vanwege het beperken van de server.

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Schakel standaard bestands shares in om Maxi maal 100 TiB te beslaan
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="limitations"></a>Beperkingen
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Redundantie
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migratie
In veel gevallen is het niet mogelijk om een net nieuwe bestands share te maken voor uw organisatie, maar om een bestaande bestands share te migreren van een on-premises Bestands server of een NAS-apparaat naar Azure Files. Er zijn veel hulpprogram ma's, zowel door micro soft als derden, voor het uitvoeren van een migratie naar een bestands share, maar ze kunnen ongeveer worden onderverdeeld in twee categorieën:

- **Hulpprogram ma's voor het onderhouden van bestandssysteem kenmerken, zoals acl's en tijds tempels**:
    - **[Azure file sync](storage-sync-files-planning.md)**: Azure File Sync kan worden gebruikt als methode om gegevens op te nemen in een Azure-bestands share, zelfs wanneer de gewenste end-implementatie geen on-premises aanwezigheid houdt. Azure File Sync kunnen worden geïnstalleerd in plaats van bestaande Windows Server 2012 R2-, Windows Server 2016-en Windows Server 2019-implementaties. Een voor deel van het gebruik van Azure File Sync als een opname mechanisme is dat eind gebruikers de bestaande bestands share op locatie kunnen blijven gebruiken. knippen naar de Azure-bestands share kan zich voordoen nadat alle gegevens zijn geüpload op de achtergrond.
    - **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: Robocopy is een bekend Kopieer hulpprogramma dat wordt geleverd bij Windows en Windows Server. Robocopy kan worden gebruikt om gegevens over te brengen naar Azure Files door de bestands share lokaal te koppelen en vervolgens de gekoppelde locatie als bestemming in de Robocopy-opdracht te gebruiken.

- **Hulpprogram ma's die geen kenmerken van bestands systeem onderhouden**:
    - **Data Box**: data Box biedt een mechanisme voor het overdragen van offline gegevens om gegevens fysiek naar Azure te verzenden. Deze methode is ontworpen om de door voer te verbeteren en band breedte te besparen, maar biedt momenteel geen ondersteuning voor bestandssysteem kenmerken zoals tijds tempels en Acl's.
    - **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: AzCopy is een opdracht regel programma dat is ontworpen voor het kopiëren van gegevens van en naar Azure files, evenals Azure Blob-opslag, met behulp van eenvoudige opdrachten met optimale prestaties.

## <a name="next-steps"></a>Volgende stappen
* [Een Azure File Sync-implementatie plannen](storage-sync-files-planning.md)
* [Azure Files implementeren](storage-files-deployment-guide.md)
* [Azure File Sync implementeren](storage-sync-files-deployment-guide.md)
