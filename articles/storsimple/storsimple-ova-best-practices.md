---
title: Aanbevolen procedures voor StorSimple Virtual Array | Microsoft Documenten
description: Beschrijft de aanbevolen procedures voor het implementeren en beheren van de StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 57ac6eeb-c47c-442d-a5f4-b360d81a76a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 5da0297dd97c8263bdc47f1d5a3d7d2d1f835e4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298836"
---
# <a name="storsimple-virtual-array-best-practices"></a>Best practices virtuele array van StorSimple

## <a name="overview"></a>Overzicht

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Microsoft Azure StorSimple Virtual Array is een geïntegreerde opslagoplossing die opslagtaken beheert tussen een on-premises virtueel apparaat dat wordt uitgevoerd in een hypervisor en Microsoft Azure-cloudopslag. StorSimple Virtual Array is een efficiënt, kosteneffectief alternatief voor de 8000-serie fysieke array. De virtuele array kan worden uitgevoerd op uw bestaande hypervisor-infrastructuur, ondersteunt zowel de iSCSI- als de SMB-protocollen en is zeer geschikt voor externe kantoor- en filiaalscenario's. Ga voor meer informatie over de StorSimple-oplossingen naar [Microsoft Azure StorSimple Overview](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

In dit artikel worden de aanbevolen procedures besproken die zijn geïmplementeerd tijdens de eerste installatie, implementatie en beheer van de Virtuele Array van StorSimple. Deze aanbevolen procedures bieden gevalideerde richtlijnen voor het instellen en beheren van uw virtuele array. Dit artikel is gericht op de IT-beheerders die de virtuele arrays in hun datacenters implementeren en beheren.

We raden u aan de aanbevolen procedures periodiek te bekijken om ervoor te zorgen dat uw apparaat nog steeds in overeenstemming is wanneer wijzigingen worden aangebracht in de installatie- of bewerkingsstroom. Als u problemen ondervindt bij het implementeren van deze aanbevolen procedures op uw virtuele array, neemt u contact op [met Microsoft Support](storsimple-virtual-array-log-support-ticket.md) voor hulp.

## <a name="configuration-best-practices"></a>Aanbevolen procedures voor configuratie
Deze aanbevolen procedures hebben betrekking op de richtlijnen die moeten worden gevolgd tijdens de eerste installatie en implementatie van de virtuele arrays. Deze aanbevolen procedures omvatten die met betrekking tot de inrichting van de virtuele machine, groepsbeleidsinstellingen, grootte, het opzetten van de netwerken, het configureren van opslagaccounts en het maken van aandelen en volumes voor de virtuele array. 

### <a name="provisioning"></a>Inrichten
StorSimple Virtual Array is een virtuele machine (VM) ingericht op de hypervisor (Hyper-V of VMware) van uw hostserver. Zorg er bij het inrichten van de virtuele machine voor dat uw host voldoende resources kan inzetten. Ga voor meer informatie naar [minimale resourcevereisten](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) om een array in te richten.

Implementeer de volgende aanbevolen procedures bij het inrichten van de virtuele array:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Type virtuele machine** |**Generatie 2** VM voor gebruik met Windows Server 2012 of hoger en een *.vhdx-afbeelding.* <br></br> **Generatie 1** VM voor gebruik met een Windows Server 2008 of hoger en een *.vhd-afbeelding.* |Gebruik versie 8 van de virtuele machine bij gebruik van *.vmdk-afbeelding.* |
| **Geheugentype** |Configureren als **statisch geheugen**. <br></br> Gebruik de optie **dynamisch geheugen** niet. | |
| **Gegevensschijftype** |Voorziening als **dynamisch uitbreidend**.<br></br> **Vaste grootte** duurt lang. <br></br> Gebruik de **optie differencing** niet. |Gebruik de **dunne voorzieningsoptie.** |
| **Wijziging van gegevensschijf** |Uitbreiding of krimp is niet toegestaan. Een poging om dit te doen resulteert in het verlies van alle lokale gegevens op het apparaat. |Uitbreiding of krimp is niet toegestaan. Een poging om dit te doen resulteert in het verlies van alle lokale gegevens op het apparaat. |

### <a name="sizing"></a>Grootte aanpassen
Houd bij het dimensioneren van uw StorSimple Virtual Array rekening met de volgende factoren:

* Lokale reservering voor volumes of aandelen. Ongeveer 12% van de ruimte is gereserveerd op de lokale laag voor elk ingerichte gelaagd volume of aandeel. Ongeveer 10% van de ruimte is ook gereserveerd voor een lokaal vastgemaakt volume voor bestandssysteem.
* Momentopname overhead. Ongeveer 15% ruimte op de lokale laag is gereserveerd voor momentopnamen.
* Behoefte aan herstel. Als u herstellen doet als een nieuwe bewerking, moet de grootte rekening houden met de ruimte die nodig is voor herstel. Herstellen gebeurt naar een aandeel of volume van dezelfde grootte.
* Sommige buffer moet worden toegewezen voor een onverwachte groei.

Op basis van de voorgaande factoren kunnen de maatvereisten worden weergegeven door de volgende vergelijking:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

In de volgende voorbeelden ziet u hoe u een virtuele array vergroten op basis van uw vereisten.

#### <a name="example-1"></a>Voorbeeld 1:
Op uw virtuele array wilt u in staat zijn om

* een 2-TB tiered volume of aandeel.
* een niveau of aandeel van 1 TB.
* een 300 GB lokaal vastgemaakt volume of aandeel intedelen.

Voor de voorgaande volumes of aandelen, laten we de ruimtevereisten op de lokale laag berekenen.

Ten eerste zou voor elk gedifferentieerd volume/aandeel de lokale reservering gelijk zijn aan 12% van het volume/aandeel. Voor het lokaal vastgemaakte volume/aandeel is de lokale reservering 10 % van het lokaal vastgemaakte volume/aandeelgrootte (naast de ingerichte grootte). In dit voorbeeld moet u

* 240 GB lokale reservering (voor een 2 TB gelaagd volume/aandeel)
* Lokale reservering van 120 GB (voor een niveau/aandeel van 1 TB)
* 330 GB voor lokaal vastgemaakt volume of aandeel (10 % van de lokale reservering toevoegen aan de 300 GB ingerichte grootte)

De totale benodigde ruimte op de lokale laag tot nu toe is: 240 GB + 120 GB + 330 GB = 690 GB.

Ten tweede hebben we minstens evenveel ruimte nodig op het lokale niveau als de grootste enkele reservering. Dit extra bedrag wordt gebruikt voor het geval u moet herstellen vanuit een cloudmomentopname. In dit voorbeeld is de grootste lokale reservering 330 GB (inclusief reservering voor bestandssysteem), dus dat zou u toevoegen aan de 690 GB: 690 GB + 330 GB = 1020 GB.
Als we volgende extra herstelwerkzaamheden hebben uitgevoerd, kunnen we altijd de ruimte vrijmaken van de vorige herstelbewerking.

Ten derde hebben we 15 % van uw totale lokale ruimte tot nu toe nodig om lokale momentopnamen op te slaan, zodat slechts 85% ervan beschikbaar is. In dit voorbeeld zou dat ongeveer 1020 GB&ast;= 0,85 ingerichte dataschijf TB zijn. Dus, de ingerichte data schijf zou&ast;zijn (1020 (1/0,85)=1200 GB = 1,20 TB ~ 1,25 TB (afronding naar het dichtstbijzijnde kwartiel)

Rekening houden met onverwachte groei en nieuwe herstelt, moet u een lokale schijf van ongeveer 1,25 - 1,5 TB.

> [!NOTE]
> We raden ook aan dat de lokale schijf dun is ingericht. Deze aanbeveling is omdat de herstelruimte alleen nodig is wanneer u gegevens wilt herstellen die ouder zijn dan vijf dagen. Met itemniveauherstel u gegevens van de afgelopen vijf dagen herstellen zonder dat u de extra ruimte nodig hebt om te herstellen.


#### <a name="example-2"></a>Voorbeeld 2:
Op uw virtuele array wilt u in staat zijn om

* voorzien in een 2 TB tiered volume
* een lokaal vastgemaakt volume van 300 GB

Op basis van 12 % van de lokale ruimtereservering voor gelaagde volumes/aandelen en 10 % voor lokaal vastgepinde volumes/aandelen, hebben we

* 240 GB lokale reservering (voor 2 TB gelaagd volume/aandeel)
* 330 GB voor lokaal vastgemaakt volume of aandeel (10% van de lokale reservering toevoegen aan de 300 GB ingerichte ruimte)

Totale benodigde ruimte op de lokale laag is: 240 GB + 330 GB = 570 GB

De minimale lokale ruimte die nodig is voor herstel is 330 GB.

15 % van uw totale schijf wordt gebruikt om momentopnamen op te slaan, zodat er slechts 0,85 beschikbaar is. Dus, de schijf grootte is&ast;(900 (1/0,85)) = 1,06 TB ~ 1,25 TB (afronding naar dichtstbijzijnde kwartiel)

Rekening houden met onverwachte groei, u een 1,25 - 1,5 TB lokale schijf.

### <a name="group-policy"></a>Groepsbeleid
Groepsbeleid is een infrastructuur waarmee u specifieke configuraties voor gebruikers en computers implementeren. Groepsbeleidsinstellingen zijn opgenomen in Groepsbeleidsobjecten (GPO's), die zijn gekoppeld aan de volgende AD DS-containers (Active Directory Domain Services): sites, domeinen of organisatie-eenheden (OE's). 

Als uw virtuele array is verbonden met een domein, kunnen GPO's erop worden toegepast. Deze GPO's kunnen toepassingen installeren, zoals een antivirussoftware die een negatieve invloed kan hebben op de werking van de StorSimple Virtual Array.

Daarom raden wij u aan:

* Controleer of uw virtuele array zich in de eigen organisatie-eenheid (OU) voor Active Directory bevindt.
* Zorg ervoor dat er geen groepsbeleidsobjecten (GPO's) worden toegepast op uw virtuele array. U overerving blokkeren om ervoor te zorgen dat de virtuele array (onderliggend knooppunt) niet automatisch gPO's van de ouder overneemt. Ga voor meer informatie naar [het blokkeren van overerving.](https://technet.microsoft.com/library/cc731076.aspx)

### <a name="networking"></a>Netwerken
De netwerkconfiguratie voor uw virtuele array gebeurt via de lokale web-gebruikersinterface. Een virtuele netwerkinterface is ingeschakeld via de hypervisor waarin de virtuele array is ingericht. Gebruik de pagina [Netwerkinstellingen](storsimple-virtual-array-deploy3-fs-setup.md) om het IP-adres, subnet en gateway voor virtuele netwerkinterface te configureren.  U ook de primaire en secundaire DNS-server, tijdinstellingen en optionele proxy-instellingen voor uw apparaat configureren. Het grootste deel van de netwerkconfiguratie is een eenmalige installatie. Bekijk de [StorSimple-netwerkvereisten](storsimple-ova-system-requirements.md#networking-requirements) voordat u de virtuele array implementeert.

Bij het implementeren van uw virtuele array raden we u aan de volgende aanbevolen procedures te volgen:

* Zorg ervoor dat het netwerk waarin de virtuele array wordt geïmplementeerd altijd de capaciteit heeft om 5 Mbps internetbandbreedte (of meer) te besteden.
  
  * De behoefte aan internetbandbreedte is afhankelijk van uw werkbelastingkenmerken en de snelheid van gegevenswijziging.
  * De gegevenswijziging die kan worden verwerkt, is rechtstreeks evenredig met uw internetbandbreedte. Als voorbeeld bij het nemen van een back-up, kan een bandbreedte van 5 Mbps geschikt voor een gegevenswijziging van ongeveer 18 GB in 8 uur. Met vier keer meer bandbreedte (20 Mbps) u vier keer meer gegevenswijzigen (72 GB) aan.
* Zorg ervoor dat er altijd verbinding met het internet beschikbaar is. Sporadische of onbetrouwbare internetverbindingen met de apparaten kunnen leiden tot een verlies van toegang tot gegevens in de cloud en kunnen resulteren in een niet-ondersteunde configuratie.
* Als u van plan bent uw apparaat als iSCSI-server te implementeren:
  
  * We raden u aan de optie **IP-adres automatisch opvragen** (DHCP) (Get IP-adres).
  * Statische IP-adressen configureren. U moet een primaire en een secundaire DNS-server configureren.
  * Als u meerdere netwerkinterfaces op uw virtuele array definieert, kan alleen de eerste netwerkinterface (standaard is deze interface **Ethernet)** de cloud bereiken. Als u het type verkeer wilt beheren, u meerdere virtuele netwerkinterfaces op uw virtuele array maken (geconfigureerd als iSCSI-server) en deze interfaces verbinden met verschillende subnetten.
* Als u alleen de bandbreedte van de cloud wilt beperken (gebruikt door de virtuele array), configureert u beperking op de router of de firewall. Als u beperking in uw hypervisor definieert, wordt alle protocollen, waaronder iSCSI en SMB, beperkt in plaats van alleen de bandbreedte in de cloud.
* Zorg ervoor dat tijdsynchronisatie voor hypervisors is ingeschakeld. Als u Hyper-V gebruikt, selecteert u uw virtuele array in Hyper-V Manager, gaat u naar **Instellingenintegratieservices &gt; **en zorgt u ervoor dat de **synchronisatie tijd** is ingeschakeld.

### <a name="storage-accounts"></a>Opslagaccounts
StorSimple Virtual Array kan worden gekoppeld aan één opslagaccount. Dit opslagaccount kan een automatisch gegenereerd opslagaccount zijn, een account in hetzelfde abonnement als de service of een opslagaccount met betrekking tot een ander abonnement. Zie voor meer informatie hoe [u opslagaccounts voor uw virtuele array beheert.](storsimple-virtual-array-manage-storage-accounts.md)

Gebruik de volgende aanbevelingen voor opslagaccounts die zijn gekoppeld aan uw virtuele array.

* Wanneer u meerdere virtuele arrays koppelt aan één opslagaccount, moet u rekening houden met de maximale capaciteit (64 TB) voor een virtuele array en de maximale grootte (500 TB) voor een opslagaccount. Dit beperkt het aantal virtuele arrays op volledige grootte dat aan dat opslagaccount kan worden gekoppeld tot ongeveer 7.
* Bij het maken van een nieuw opslagaccount
  
  * We raden u aan deze te maken in de regio die het dichtst bij het externe kantoor/filiaal ligt waar uw StorSimple Virtual Array wordt geïmplementeerd om latencies te minimaliseren.
  * Houd er rekening mee dat u een opslagaccount niet over verschillende regio's verplaatsen. Ook u een service niet over abonnementen verplaatsen.
  * Gebruik een opslagaccount dat redundantie tussen de datacenters implementeert. Geo-Redundant Storage (GRS), Zone Redundant Storage (ZRS) en LocalLy Redundant Storage (LRS) worden allemaal ondersteund voor gebruik met uw virtuele array. Ga voor meer informatie over de verschillende typen opslagaccounts naar [Azure-opslagreplicatie](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Aandelen en volumes
Voor uw StorSimple Virtual Array u shares inrichten wanneer deze is geconfigureerd als bestandsserver en volumes wanneer deze zijn geconfigureerd als een iSCSI-server. De aanbevolen procedures voor het maken van aandelen en volumes zijn gerelateerd aan de grootte en het type geconfigureerd.

#### <a name="volumeshare-size"></a>Volume/aandeelgrootte
Op uw virtuele array u aandelen inrichten wanneer deze is geconfigureerd als bestandsserver en volumes wanneer deze zijn geconfigureerd als een iSCSI-server. De aanbevolen procedures voor het maken van aandelen en volumes hebben betrekking op de grootte en het geconfigureerde type. 

Houd rekening met de volgende aanbevolen procedures bij het inrichten van aandelen of volumes op uw virtuele apparaat.

* De bestandsgrootte ten opzichte van de ingerichte grootte van een gelaagd aandeel kan van invloed zijn op de tieringprestaties. Werken met grote bestanden kan resulteren in een langzame laag uit. Bij het werken met grote bestanden raden we aan dat het grootste bestand kleiner is dan 3% van de grootte van het aandeel.
* Er kunnen maximaal 16 volumes/shares worden gemaakt op de virtuele array. Voor de groottelimieten van de lokaal vastgemaakte en gelaagde volumes/shares raadpleegt u altijd de [Limieten voor Virtuele Array storSimple.](storsimple-ova-limits.md)
* Let bij het maken van een volume op het verwachte dataverbruik en de toekomstige groei. Het volume kan later niet worden uitgebreid.
* Zodra het volume is gemaakt, u de grootte van het volume op StorSimple niet verkleinen.
* Wanneer u naar een gelaagd volume op StorSimple schrijft, wordt de IO beperkt wanneer de volumegegevens een bepaalde drempelwaarde bereiken (ten opzichte van de lokale ruimte die is gereserveerd voor het volume. Doorgaan met schrijven naar dit volume vertraagt de IO aanzienlijk. Hoewel u schrijven naar een gelaagd volume buiten de ingerichte capaciteit (we stoppen de gebruiker niet actief om te schrijven buiten de ingerichte capaciteit), ziet u een waarschuwingsmelding met het effect dat u bent overschreven. Zodra u de waarschuwing ziet, is het noodzakelijk dat u corrigerende maatregelen neemt, zoals het verwijderen van de volumegegevens (volumeuitbreiding wordt momenteel niet ondersteund).
* Voor gebruiksgevallen voor noodherstel, aangezien het aantal toegestane aandelen/volumes 16 is en het maximum aantal aandelen/volumes dat parallel kan worden verwerkt ook 16 is, heeft het aantal aandelen/volumes geen invloed op uw RPO's en RPO's.

#### <a name="volumeshare-type"></a>Volume/sharetype
StorSimple ondersteunt twee volume-/sharetypen op basis van het gebruik: lokaal vastgemaakt en gelaagd. Lokaal vastgemaakte volumes/aandelen zijn dik ingericht, terwijl de gelaagde volumes/aandelen dun zijn ingericht. U een lokaal vastgemaakt volume/aandeel niet converteren naar gelaagd of *omgekeerd* nadat deze is gemaakt.

We raden u aan de volgende best practices te implementeren bij het configureren van StorSimple-volumes/shares:

* Identificeer het volumetype op basis van de workloads die u wilt implementeren voordat u een volume maakt. Gebruik lokaal vastgemaakte volumes voor workloads waarvoor lokale garanties van gegevens nodig zijn (zelfs tijdens een cloudstoring) en die lage cloudlatencies vereisen. Zodra u een volume op uw virtuele array hebt gemaakt, u het volumetype niet wijzigen van lokaal vastgemaakt in gelaagd of *vice versa.* Maak bijvoorbeeld lokaal vastgemaakte volumes bij het implementeren van SQL-workloads of workloads die virtuele machines hosten (VM's); trapsgewijze volumes gebruiken voor bestandsshare-workloads.


#### <a name="volume-format"></a>Volumenotatie
Nadat u StorSimple-volumes op uw iSCSI-server hebt gemaakt, moet u de volumes initialiseren, monteren en opmaken. Deze bewerking wordt uitgevoerd op de host die is aangesloten op uw StorSimple-apparaat. Het volgen van aanbevolen procedures wordt aanbevolen bij het monteren en opmaken van volumes op de StorSimple-host.

* Voer een snelle indeling uit op alle StorSimple-volumes.
* Wanneer u een StorSimple-volume opmaakt, gebruikt u een aus (toewijzingseenheidsgrootte) van 64 KB (standaard is 4 KB). De 64 KB AUS is gebaseerd op interne tests voor veelvoorkomende StorSimple-workloads en andere workloads.
* Gebruik bij het gebruik van de StorSimple Virtual Array die is geconfigureerd als een iSCSI-server, geen overspannen volumes of dynamische schijven, omdat deze volumes of schijven niet worden ondersteund door StorSimple.

#### <a name="share-access"></a>Toegang delen
Volg de volgende richtlijnen wanneer u shares maakt op uw virtuele arraybestandsserver:

* Wanneer u een share maakt, wijst u een gebruikersgroep toe als sharebeheerder in plaats van één gebruiker.
* U de NTFS-machtigingen beheren nadat het aandeel is gemaakt door de shares te bewerken via Windows Explorer.

#### <a name="volume-access"></a>Toegang tot volume
Bij het configureren van de iSCSI-volumes op uw StorSimple Virtual Array is het belangrijk om de toegang waar nodig te beheren. Als u wilt bepalen welke hostservers toegang hebben tot volumes, maakt en koppelt u toegangscontrolerecords (ABE's) aan StorSimple-volumes.

Gebruik de volgende aanbevolen procedures bij het configureren van ADR's voor StorSimple-volumes:

* Associeer altijd ten minste één ACR met een volume.

* Wanneer u meer dan één ACR aan een volume toekent, moet u ervoor zorgen dat het volume niet wordt blootgesteld op een manier waar het gelijktijdig toegankelijk is voor meer dan één niet-geclusterde host. Als u meerdere ACL's aan een volume hebt toegewezen, verschijnt er een waarschuwingsbericht dat u uw configuratie controleren.

### <a name="data-security-and-encryption"></a>Gegevensbeveiliging en -versleuteling
Uw StorSimple Virtual Array heeft functies voor gegevensbeveiliging en versleuteling die de vertrouwelijkheid en integriteit van uw gegevens garanderen. Wanneer u deze functies gebruikt, wordt u aangeraden deze aanbevolen procedures te volgen: 

* Definieer een versleutelingssleutel voor cloudopslag om AES-256-versleuteling te genereren voordat de gegevens van uw virtuele array naar de cloud worden verzonden. Deze sleutel is niet vereist als uw gegevens om te beginnen zijn versleuteld. De sleutel kan worden gegenereerd en veilig worden bewaard met behulp van een sleutelbeheersysteem, zoals [Azure-sleutelkluis.](../key-vault/key-vault-overview.md)
* Wanneer u het opslagaccount configureert via de StorSimple Manager-service, moet u ervoor zorgen dat u de TLS-modus inschakelt om een beveiligd kanaal te maken voor netwerkcommunicatie tussen uw StorSimple-apparaat en de cloud.
* Regenereert de sleutels voor uw opslagaccounts (door periodiek toegang te krijgen tot de Azure Storage-service) om rekening te houden met eventuele wijzigingen in de toegang op basis van de gewijzigde lijst met beheerders.
* Gegevens op uw virtuele array worden gecomprimeerd en gededuplicatevoordat deze naar Azure worden verzonden. We raden u af de functieservice Gegevensdeduplicatie te gebruiken op uw Windows Server-host.

## <a name="operational-best-practices"></a>Aanbevolen operationele procedures
De operationele best practices zijn richtlijnen die moeten worden gevolgd tijdens het dagelijks beheer of de werking van de virtuele array. Deze praktijken hebben betrekking op specifieke beheertaken, zoals het maken van back-ups, herstellen van een back-upset, het uitvoeren van een failover, het deactiveren en verwijderen van de array, het bewaken van het systeemgebruik en de gezondheid en het uitvoeren van virusscans op uw virtuele array.

### <a name="backups"></a>Back-ups
De gegevens op uw virtuele array worden op twee manieren geback-upt naar de cloud, een standaard automatische dagelijkse back-up van het hele apparaat vanaf 22:30 uur of via een handmatige on-demand back-up. Standaard maakt het apparaat automatisch dagelijkse cloudsnapshots van alle gegevens die erop staan. Ga voor meer informatie een [back-up van uw StorSimple Virtual Array.](storsimple-virtual-array-backup.md)

De frequentie en retentie die aan de standaardback-ups zijn gekoppeld, kunnen niet worden gewijzigd, maar u het tijdstip configureren waarop de dagelijkse back-ups elke dag worden gestart. Bij het configureren van de begintijd voor de geautomatiseerde back-ups raden we u aan:

* Plan uw back-ups in voor daluren. De begintijd van de back-up mag niet samenvallen met een groot aantal host IO.
* Start een handmatige on-demand back-up wanneer u van plan bent een apparaatfailover uit te voeren of voorafgaand aan het onderhoudsvenster, om de gegevens op uw virtuele array te beschermen.

### <a name="restore"></a>Herstellen
U op twee manieren herstellen vanuit een back-upset: herstellen naar een ander volume of een herstel op itemniveau delen of uitvoeren (alleen beschikbaar op een virtuele array die is geconfigureerd als bestandsserver). Met itemniveauherstel u bestanden en mappen gedetailleerd herstellen vanuit een cloudback-up van alle shares op het StorSimple-apparaat. Ga voor meer informatie naar [herstellen vanuit een back-up.](storsimple-virtual-array-clone.md)

Houd bij het uitvoeren van een herstel rekening met de volgende richtlijnen:

* Uw StorSimple Virtual Array biedt geen ondersteuning voor herstel op zijn plaats. Dit kan echter gemakkelijk worden bereikt door een proces in twee stappen: ruimte maken op de virtuele array en vervolgens herstellen naar een ander volume / aandeel.
* Houd er bij het herstellen van een lokaal volume rekening mee dat het herstel een langdurige bewerking is. Hoewel het volume snel online kan komen, worden de gegevens nog steeds gehydrateerd op de achtergrond.
* Het volumetype blijft hetzelfde tijdens het herstelproces. Een gelaagd volume wordt hersteld naar een ander gelaagd volume en een lokaal vastgemaakt volume naar een ander lokaal vastgemaakt volume.
* Wanneer u een volume of een aandeel probeert te herstellen vanaf een back-upset, kan er in de portal nog steeds een doelvolume of aandeel worden gemaakt als de hersteltaak mislukt. Het is belangrijk dat u dit ongebruikte doelvolume verwijdert of deelt in de portal om eventuele toekomstige problemen die uit dit element voortvloeien, te minimaliseren.

### <a name="failover-and-disaster-recovery"></a>Failover en disaster recovery
Met een apparaatfailover u uw gegevens migreren van een *bronapparaat* in het datacenter naar een ander *doelapparaat* op dezelfde of een andere geografische locatie. De failover van het apparaat is voor het hele apparaat. Tijdens failover worden de cloudgegevens voor het bronapparaat van eigenaar veranderd naar dat van het doelapparaat.

Voor uw StorSimple Virtual Array u alleen mislukken naar een andere virtuele array die wordt beheerd door dezelfde StorSimple Manager-service. Een failover naar een apparaat uit de 8000-serie of een array die wordt beheerd door een andere StorSimple Manager-service (dan die voor het bronapparaat) is niet toegestaan. Ga voor meer informatie over de failoveroverwegingen naar [vereisten voor de failover](storsimple-virtual-array-failover-dr.md)van het apparaat.

Houd bij het uitvoeren van een fail-over voor uw virtuele array rekening met het volgende:

* Voor een geplande failover is het een aanbevolen aanbevolen best practice om alle volumes/aandelen offline te halen voordat de failover wordt gestart. Volg de instructies voor het besturingssysteem om de volumes/aandelen eerst offline te halen op de host en deze vervolgens offline te halen op uw virtuele apparaat.
* Voor een bestandsserver disaster recovery (DR) raden we u aan het doelapparaat samen te voegen met hetzelfde domein als de bron, zodat de machtigingen voor delen automatisch worden opgelost. Alleen de failover naar een doelapparaat in hetzelfde domein wordt ondersteund in deze release.
* Zodra de DR is voltooid, wordt het bronapparaat automatisch verwijderd. Hoewel het apparaat niet meer beschikbaar is, verbruikt de virtuele machine die u op het hostsysteem hebt ingericht, nog steeds resources. We raden u aan deze virtuele machine uit uw hostsysteem te verwijderen om te voorkomen dat er kosten worden gemaakt.
* Houd er rekening mee dat zelfs als de failover niet succesvol is, **de gegevens altijd veilig zijn in de cloud.** Houd rekening met de volgende drie scenario's waarin de failover niet is voltooid:
  
  * Er is een fout opgetreden in de beginfase van de failover, zoals wanneer de DR-voorcontroles worden uitgevoerd. In deze situatie is uw doelapparaat nog steeds bruikbaar. U de failover opnieuw proberen op hetzelfde doelapparaat.
  * Er is een fout opgetreden tijdens het eigenlijke failoverproces. In dit geval is het doelapparaat gemarkeerd als onbruikbaar. U moet een andere virtuele array van een doel inrichten en configureren en deze gebruiken voor failover.
  * De failover was voltooid waarna het bronapparaat is verwijderd, maar het doelapparaat problemen heeft en u geen toegang hebt tot gegevens. De gegevens zijn nog steeds veilig in de cloud en kunnen gemakkelijk worden opgehaald door een andere virtuele array te maken en deze vervolgens te gebruiken als doelapparaat voor de DR.

### <a name="deactivate"></a>Deactiveren
Wanneer u een StorSimple Virtual Array deactiveert, verpersoonlijkt u de verbinding tussen het apparaat en de bijbehorende StorSimple Manager-service. Deactivering is een **permanente** bewerking en kan niet ongedaan worden gemaakt. Een gedeactiveerd apparaat kan niet opnieuw worden geregistreerd bij de StorSimple Manager-service. Ga voor meer informatie naar [deactiveren en verwijderen van uw StorSimple Virtual Array.](storsimple-virtual-array-deactivate-and-delete-device.md)

Houd rekening met de volgende aanbevolen procedures bij het deactiveren van uw virtuele array:

* Maak een cloudmomentopname van alle gegevens voordat u een virtueel apparaat deactiveert. Wanneer u een virtuele array deactiveert, gaan alle lokale apparaatgegevens verloren. Als u een cloudmomentopname maakt, u gegevens in een later stadium herstellen.
* Voordat u een StorSimple Virtual Array deactiveert, moet u clients en hosts die afhankelijk zijn van dat apparaat stoppen of verwijderen.
* Verwijder een gedeactiveerd apparaat als u niet meer gebruikt, zodat er geen kosten in rekening worden gebracht.

### <a name="monitoring"></a>Bewaking
Om ervoor te zorgen dat uw StorSimple Virtual Array in een continue gezonde staat verkeert, moet u de array controleren en ervoor zorgen dat u informatie van het systeem ontvangt, inclusief waarschuwingen. Als u de algehele status van de virtuele array wilt controleren, implementeert u de volgende aanbevolen procedures:

* Configureer bewaking om het schijfgebruik van uw virtuele arraygegevensschijf en de OS-schijf bij te houden. Als hyper-v wordt uitgevoerd, u een combinatie van System Center Virtual Machine Manager (SCVMM) en System Center Operations Manager gebruiken om uw virtualisatiehosts te controleren.
* Configureer e-mailmeldingen op uw virtuele array om waarschuwingen te verzenden op bepaalde gebruiksniveaus.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Index search en virus scan toepassingen
Een StorSimple Virtual Array kan automatisch gegevens van de lokale laag naar de Microsoft Azure-cloud brengen. Wanneer een toepassing zoals een indexzoekopdracht of een virusscan wordt gebruikt om de gegevens te scannen die zijn opgeslagen op StorSimple, moet u ervoor zorgen dat de cloudgegevens niet worden geopend en terugnaar de lokale laag worden teruggetrokken.

We raden u aan de volgende aanbevolen procedures te implementeren bij het configureren van de indexzoekopdracht of virusscan op uw virtuele array:

* Schakel automatisch geconfigureerde volledige scanbewerkingen uit.
* Configureer de indexzoek- of virusscantoepassing voor gelaagde volumes om een incrementele scan uit te voeren. Dit zou scannen alleen de nieuwe gegevens waarschijnlijk woonachtig op de lokale laag. De gegevens die naar de cloud zijn gegelaagdeerd, worden niet geopend tijdens een incrementele bewerking.
* Zorg ervoor dat de juiste zoekfilters en -instellingen zo zijn geconfigureerd dat alleen de beoogde typen bestanden worden gescand. Afbeeldingsbestanden (JPEG, GIF en TIFF) en technische tekeningen mogen bijvoorbeeld niet worden gescand tijdens de incrementele of volledige indexreconstructie.

Als u het Indexeringsproces van Windows gebruikt, volgt u de volgende richtlijnen:

* Gebruik de Windows Indexer niet voor gelaagde volumes, omdat deze grote hoeveelheden gegevens (TBS's) uit de cloud terugroept als de index regelmatig moet worden herbouwd. Als u de index opnieuw opbouwt, worden alle bestandstypen opgehaald om de inhoud ervan te indexeren.
* Gebruik het Windows-indexeringsproces voor lokaal vastgemaakte volumes, omdat dit alleen toegang zou krijgen tot gegevens op de lokale lagen om de index op te bouwen (de cloudgegevens worden niet geopend).

### <a name="byte-range-locking"></a>Byte bereik vergrendeling
Toepassingen kunnen een bepaald bereik van bytes binnen de bestanden vergrendelen. Als byte-bereikvergrendeling is ingeschakeld op de toepassingen die naar uw StorSimple schrijven, werkt tiering niet op uw virtuele array. Om de gelaagdheid te laten werken, moeten alle gebieden van de geopende bestanden worden ontgrendeld. Byte-bereikvergrendeling wordt niet ondersteund met gelaagde volumes op uw virtuele array.

Aanbevolen maatregelen om de vergrendeling van het bytebereik te verlichten zijn:

* Schakel de vergrendeling van het bytebereik in uw toepassingslogica uit.
* Gebruik lokaal vastgemaakte volumes (in plaats van trapsgewijze) voor de gegevens die aan deze toepassing zijn gekoppeld. Lokaal vastgemaakte volumes worden niet in de cloud geklasseerd.
* Wanneer lokaal vastgemaakte volumes worden gebruikt met de vergrendeling van het bytebereik ingeschakeld, kan het volume online komen voordat het herstel is voltooid. In deze gevallen moet u wachten tot het herstel is voltooid.

## <a name="multiple-arrays"></a>Meerdere arrays
Mogelijk moeten meerdere virtuele arrays worden geïmplementeerd om rekening te houden met een groeiende werkset gegevens die op de cloud kunnen lekken, waardoor de prestaties van het apparaat worden beïnvloed. In deze gevallen is het het beste om apparaten te schalen naarmate de werkset groeit. Hiervoor moeten een of meer apparaten worden toegevoegd in het on-premises datacenter. Bij het toevoegen van de apparaten u het als nog maar beter maken:

* Splits de huidige set gegevens.
* Nieuwe workloads implementeren op nieuwe apparaten.
* Als u meerdere virtuele arrays implementeert, raden we u aan de array vanuit het perspectief van loadbalancing te verdelen over verschillende hypervisorhosts.
* Meerdere virtuele arrays (wanneer geconfigureerd als bestandsserver of een iSCSI-server) kunnen worden geïmplementeerd in een naamruimte voor gedistribueerd bestandssysteem. Ga voor gedetailleerde stappen naar [Distributed File System Namespace Solution met Hybrid Cloud Storage Deployment Guide](https://www.microsoft.com/download/details.aspx?id=45507). Gedistribueerde bestandssysteemreplicatie wordt momenteel niet aanbevolen voor gebruik met de virtuele array. 

## <a name="see-also"></a>Zie ook
Meer informatie over het [beheren van uw StorSimple Virtual Array](storsimple-virtual-array-manager-service-administration.md) via de StorSimple Manager-service.

