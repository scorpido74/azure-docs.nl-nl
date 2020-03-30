---
title: Overzicht van de StorSimple 8000-serie oplossing | Microsoft Documenten
description: Beschrijft StorSimple-lagen, het apparaat, virtueel apparaat, services en opslagbeheer en introduceert belangrijke termen die in StorSimple worden gebruikt.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 7144d218-db21-4495-88fb-e3b24bbe45d1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/10/2017
ms.author: timlt
ms.openlocfilehash: 2a6650cac975c575415a329361da00d4fbfcaa9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68965119"
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>StorSimple 8000-serie: een hybride cloudopslagoplossing

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Overzicht
Welkom bij Microsoft Azure StorSimple, een geïntegreerde opslagoplossing die opslagtaken tussen on-premises apparaten en Microsoft Azure-cloudopslag beheert. StorSimple is een efficiënte, kosteneffectieve en gemakkelijk beheerbare Storage Area Network (SAN)-oplossing die veel van de problemen en uitgaven in verband met bedrijfsopslag en gegevensbescherming elimineert. StorSimple gebruikt het eigen apparaat uit de StorSimple 8000-reeks, integreert met cloudservices en biedt een reeks hulpprogramma’s voor beheer voor een naadloze weergave van alle Enterprise-opslag, waaronder cloudopslag. (De StorSimple-implementatiegegevens die op de Microsoft Azure-website worden gepubliceerd, zijn alleen van toepassing op apparaten uit de StorSimple 8000-serie. Als u een StorSimple 5000/7000-serie gebruikt, gaat u naar [StorSimple Help](http://onlinehelp.storsimple.com/).)

StorSimple gebruikt [opslaglagen](#automatic-storage-tiering) om opgeslagen gegevens te beheren in verschillende opslagmedia. De huidige werkset wordt on-premises opgeslagen op solid state drives (SSD's), gegevens die minder vaak worden gebruikt worden opgeslagen op harde schijven (HDD's) en archiefgegevens worden naar de cloud gepusht. Bovendien gebruikt StorSimple ontdubbeling en compressie om de hoeveelheid opslagruimte die de gegevens verbruiken te verminderen. Ga voor meer informatie naar [Deduplicatie en compressie.](#deduplication-and-compression) Voor definities van andere belangrijke termen en concepten die worden gebruikt in de StorSimple 8000 serie documentatie, ga naar [StorSimple terminologie](#storsimple-terminology) aan het einde van dit artikel.

Naast opslagbeheer u met de functies voor gegevensbescherming van StorSimple on-demand en geplande back-ups maken en deze vervolgens lokaal of in de cloud opslaan. Back-ups worden genomen in de vorm van incrementele momentopnamen, wat betekent dat ze snel kunnen worden gemaakt en hersteld. Cloudmomentopnamen kunnen van cruciaal belang zijn in scenario's voor noodherstel, omdat ze secundaire opslagsystemen (zoals tapeback-up) vervangen en u in staat stellen gegevens naar uw datacenter te herstellen of indien nodig naar alternatieve locaties.

![videopictogram](./media/storsimple-overview/video_icon.png) Bekijk de video voor een snelle introductie tot Microsoft Azure StorSimple.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>Waarom StorSimple gebruiken?
In de volgende tabel worden enkele van de belangrijkste voordelen beschreven die Microsoft Azure StorSimple biedt.

| Functie | Voordeel |
| --- | --- |
| Transparante integratie |Gebruikt het iSCSI-protocol om gegevensopslagfaciliteiten onzichtbaar te koppelen. Dit zorgt ervoor dat gegevens die zijn opgeslagen in de cloud, in het datacenter of op externe servers op één locatie lijken te worden opgeslagen. |
| Lagere opslagkosten |Wijst voldoende lokale of cloudopslag toe om aan de huidige eisen te voldoen en breidt cloudopslag alleen uit wanneer dat nodig is. Het vermindert verder opslagvereisten en kosten door overtollige versies van dezelfde gegevens (ontdubbeling) te elimineren en compressie te gebruiken. |
| Vereenvoudigd opslagbeheer |Biedt hulpprogramma's voor systeembeheer voor het configureren en beheren van on-premises opgeslagen gegevens, op een externe server en in de cloud. Bovendien u back-up- en herstelfuncties beheren via een MMC-module (Microsoft Management Console).|
| Verbeterde herstel en naleving van rampen |Vereist geen langere hersteltijd. In plaats daarvan worden gegevens hersteld wanneer dat nodig is. Dit betekent dat normale bewerkingen kunnen doorgaan met minimale verstoring. Daarnaast u beleidsregels configureren om back-upschema's en gegevensbewaring op te geven. |
| Datamobiliteit |Gegevens die zijn geüpload naar Microsoft Azure-cloudservices, kunnen vanaf andere sites worden geopend voor herstel- en migratiedoeleinden. Bovendien u StorSimple gebruiken om StorSimple Cloud Appliances te configureren op virtuele machines (VM's) die worden uitgevoerd in Microsoft Azure. De VM's kunnen vervolgens virtuele apparaten gebruiken om toegang te krijgen tot opgeslagen gegevens voor test- of hersteldoeleinden. |
| Bedrijfscontinuïteit |Hiermee kunnen gebruikers uit de StorSimple 5000-7000-serie hun gegevens migreren naar een apparaat uit de StorSimple 8000-serie. |
| Beschikbaarheid in de Azure Government Portal |StorSimple is beschikbaar in de Azure Government Portal. Zie [Uw on-premises StorSimple-apparaat implementeren in het overheidsportaal voor](storsimple-8000-deployment-walkthrough-gov-u2.md)meer informatie. |
| Gegevensbescherming en beschikbaarheid |De StorSimple 8000-serie ondersteunt Zone Redundant Storage (ZRS), naast Locally Redundant Storage (LRS) en Geo-redundante opslag (GRS). Raadpleeg [dit artikel over redundantieopties](https://azure.microsoft.com/documentation/articles/storage-redundancy/) voor Azure Storage voor ZRS-details. |
| Ondersteuning voor kritieke toepassingen |Met StorSimple u de juiste volumes identificeren als lokaal vastgemaakt, wat er op zijn beurt voor zorgt dat gegevens die vereist zijn voor kritieke toepassingen niet worden gelaagd naar de cloud. Lokaal vastgemaakte volumes zijn niet onderhevig aan problemen met latencies in de cloud of connectiviteit. Zie [De StorSimple Device Manager-service gebruiken om volumes te beheren voor](storsimple-8000-manage-volumes-u2.md)meer informatie over lokaal vastgemaakte volumes. |
| Lage latentie en hoge prestaties |U cloudapparaten maken die profiteren van de krachtige, lage latentiefuncties van Azure premium-opslag. Zie [Een StorSimple Cloud Appliance implementeren en beheren in Azure voor](storsimple-8000-cloud-appliance-u2.md)meer informatie over Premium Cloud appliances van StorSimple. |


## <a name="storsimple-components"></a>StorSimple-componenten
De Microsoft Azure StorSimple-oplossing bevat de volgende onderdelen:

* **Microsoft Azure StorSimple-apparaat** – een on-premises hybride opslagarray die SED's en HDD's bevat, samen met redundante controllers en automatische failovermogelijkheden. De controllers beheren opslaglagen en plaatsen momenteel gebruikte (of hete) gegevens op lokale opslag (in het apparaat of on-premises servers), terwijl minder vaak gebruikte gegevens naar de cloud worden verplaatst.
* **StorSimple Cloud Appliance** - ook wel bekend als de StorSimple Virtual Appliance, dit is een softwareversie van de StorSimple apparaat dat de architectuur en de meeste mogelijkheden van de fysieke hybride opslag apparaat repliceert. Het StorSimple Cloud Appliance draait op één knooppunt in een virtuele Azure-machine. Premium virtuele apparaten, die profiteren van Azure premium storage, zijn beschikbaar in Update 2 en hoger.
* **StorSimple Device Manager-service** – een uitbreiding van de Azure-portal waarmee u een StorSimple-apparaat of StorSimple Cloud Appliance beheren vanuit één webinterface. U de StorSimple Device Manager-service gebruiken om services te maken en te beheren, apparaten te bekijken en te beheren, waarschuwingen te bekijken, volumes te beheren en back-upbeleid en de back-upcatalogus te bekijken en te beheren.
* **Windows PowerShell voor StorSimple** – een command-line interface die u gebruiken om het StorSimple-apparaat te beheren. Windows PowerShell voor StorSimple heeft functies waarmee u uw StorSimple-apparaat registreren, de netwerkinterface op uw apparaat configureren, bepaalde typen updates installeren, uw apparaat oplossen door toegang te krijgen tot de ondersteuningssessie en het apparaat wijzigen Staat. U hebt toegang tot Windows PowerShell voor StorSimple door verbinding te maken met de seriële console of door Windows PowerShell remoting te gebruiken.
* **Azure PowerShell StorSimple-cmdlets** - een verzameling Windows PowerShell-cmdlets waarmee u service- en migratietaken vanaf de opdrachtregel automatiseren. Ga voor meer informatie over de Azure PowerShell-cmdlets voor StorSimple naar de [verwijzing naar cmdlet](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0#azure).
* **StorSimple Snapshot Manager** - een MMC-module die volumegroepen en de Windows Volume Shadow Copy Service gebruikt om toepassingsconsistente back-ups te genereren. Daarnaast u StorSimple Snapshot Manager gebruiken om back-upschema's te maken en volumes te klonen of te herstellen.
* **StorSimple-adapter voor SharePoint** - een tool die microsoft Azure StorSimple-opslag- en gegevensbeveiliging op transparante wijze uitbreidt naar SharePoint Server-farms, terwijl storSimple-opslag zichtbaar en beheersbaar wordt gemaakt vanuit de SharePoint Central Administration-portal.

Het onderstaande diagram biedt een weergave op hoog niveau van de Microsoft Azure StorSimple-architectuur en -componenten.

![StorSimple architectuur](./media/storsimple-overview/overview-big-picture.png)

In de volgende secties worden elk van deze componenten gedetailleerder beschreven en wordt uitgelegd hoe de oplossing gegevens rangschikt, opslag toewijst en opslagbeheer en gegevensbescherming vergemakkelijkt. Het laatste deel bevat definities voor enkele van de belangrijke termen en concepten met betrekking tot StorSimple componenten en hun beheer.

## <a name="storsimple-device"></a>StorSimple-apparaat
Het Microsoft Azure StorSimple-apparaat is een on-premises hybride opslagarray die primaire opslag- en iSCSI-toegang biedt tot gegevens die erop zijn opgeslagen. Het beheert de communicatie met cloudopslag en helpt de veiligheid en vertrouwelijkheid te garanderen van alle gegevens die zijn opgeslagen in de Microsoft Azure StorSimple-oplossing.

Het StorSimple-apparaat bevat HDD's en harde schijven, evenals ondersteuning voor clustering en automatische failover. Het bevat een gedeelde processor, gedeelde opslag en twee gespiegelde controllers. Elke controller geeft het volgende:

* Verbinding met een hostcomputer
* Maximaal zes netwerkpoorten om verbinding te maken met het LAN (Local Area Network)
* Hardwarebewaking
* Niet-vluchtig random access memory (NVRAM), dat informatie bewaart, zelfs als de stroom wordt onderbroken
* Clusterbewuste updates voor het beheren van software-updates op servers in een failovercluster, zodat de updates minimaal of geen effect hebben op de beschikbaarheid van de service
* Clusterservice, die functioneert als een back-endcluster, die hoge beschikbaarheid biedt en eventuele nadelige effecten minimaliseert als een HDD of SSD uitvalt of offline wordt gehaald

Er is slechts één controller actief op enig moment. Als de actieve controller uitvalt, wordt de tweede controller automatisch actief.

Ga voor meer informatie naar [StorSimple-hardwarecomponenten en -status.](storsimple-8000-monitor-hardware-status.md)

## <a name="storsimple-cloud-appliance"></a>StorSimple Cloud Appliance
U StorSimple gebruiken om een cloudtoestel te maken dat de architectuur en mogelijkheden van het fysieke hybride opslagapparaat nabootst. De StorSimple Cloud Appliance (ook bekend als de StorSimple Virtual Appliance) draait op één knooppunt in een Azure virtuele machine. (Een cloudtoestel kan alleen worden gemaakt op een virtuele Azure-machine. U er geen maken op een StorSimple-apparaat of een on-premises server.)

Het cloudtoestel heeft de volgende functies:

* Het gedraagt zich als een fysiek apparaat en kan een iSCSI-interface bieden aan virtuele machines in de cloud.
* U een onbeperkt aantal cloudapparaten in de cloud maken en deze zo nodig in- en uitschakelen.
* Het kan helpen bij het simuleren van on-premises omgevingen in scenario's voor noodherstel, ontwikkeling en testen en kan helpen bij het ophalen op itemniveau van back-ups.

De StorSimple Cloud Appliance is verkrijgbaar in twee modellen: het 8010-apparaat (voorheen bekend als het 1100-model) en het 8020-apparaat. Het 8010-apparaat heeft een maximale capaciteit van 30 TB. Het 8020-apparaat, dat gebruikmaakt van Azure premium-opslag, heeft een maximale capaciteit van 64 TB. (In lokale lagen slaat Azure premium-opslag gegevens op in SSD's, terwijl standaardopslag gegevens op HDD's opslaat.) Houd er rekening mee dat u een Azure Premium-opslagaccount moet hebben om premium opslag te kunnen gebruiken.

Ga voor meer informatie over het StorSimple Cloud Appliance naar [Implementeren en beheren van een StorSimple Cloud Appliance in Azure.](storsimple-8000-cloud-appliance-u2.md)

## <a name="storsimple-device-manager-service"></a>StorSimple-apparaatbeheerfunctie
Microsoft Azure StorSimple biedt een webgebaseerde gebruikersinterface (de StorSimple Device Manager-service) waarmee u datacenter- en cloudopslag centraal beheren. U de StorSimple Device Manager-service gebruiken om de volgende taken uit te voeren:

* Systeeminstellingen configureren voor StorSimple-apparaten.
* Beveiligingsinstellingen configureren en beheren voor StorSimple-apparaten.
* Cloudreferenties en -eigenschappen configureren.
* Volumes configureren en beheren op een server.
* Volumegroepen configureren.
* Back-ups maken en gegevens herstellen.
* Controleer de prestaties.
* Controleer systeeminstellingen en identificeer mogelijke problemen.

U de StorSimple Device Manager-service gebruiken om alle beheertaken uit te voeren, behalve die waarvoor systeemdowntime nodig is, zoals het in- en installeren van updates.

Ga voor meer informatie naar [De StorSimple Device Manager-service gebruiken om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell voor StorSimple
Windows PowerShell voor StorSimple biedt een command-line interface die u gebruiken om de Microsoft Azure StorSimple-service te maken en te beheren en StorSimple-apparaten in te stellen en te controleren. Het is een Op Windows PowerShell gebaseerde, command-line interface met speciale cmdlets voor het beheer van uw StorSimple-apparaat. Windows PowerShell voor StorSimple heeft functies waarmee u:

* Registreer een apparaat.
* Configureer de netwerkinterface op een apparaat.
* Installeer bepaalde soorten updates.
* Problemen met uw apparaat oplossen door toegang te krijgen tot de ondersteuningssessie.
* De apparaatstatus wijzigen.

U hebt toegang tot Windows PowerShell voor StorSimple vanaf een seriële console (op een hostcomputer die rechtstreeks op het apparaat is aangesloten) of op afstand met Windows PowerShell remoting. Houd er rekening mee dat sommige Windows PowerShell-taken voor StorSimple-taken, zoals de eerste apparaatregistratie, alleen op de seriële console kunnen worden uitgevoerd.

Ga voor meer informatie naar [Windows PowerShell gebruiken voor StorSimple om uw apparaat te beheren.](storsimple-8000-windows-powershell-administration.md)

## <a name="azure-powershell-storsimple-cmdlets"></a>Azure PowerShell StorSimple-cmdlets
De Azure PowerShell StorSimple-cmdlets zijn een verzameling Windows PowerShell-cmdlets waarmee u service- en migratietaken vanaf de opdrachtregel automatiseren. Ga voor meer informatie over de Azure PowerShell-cmdlets voor StorSimple naar de [verwijzing naar cmdlet](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0).

## <a name="storsimple-snapshot-manager"></a>StorSimple Snapshot Manager
StorSimple Snapshot Manager is een MMC-module (Microsoft Management Console) waarmee u consistente, point-in-time back-upkopieën van lokale en cloudgegevens maken. De module wordt uitgevoerd op een windows server-gebaseerde host. U StorSimple Snapshot Manager gebruiken om:

* Volumes configureren, een back-up maken en verwijderen.
* Configureer volumegroepen om ervoor te zorgen dat back-upgegevens toepassingsconsistent zijn.
* Beheer back-upbeleid, zodat er een back-up wordt gemaakt van gegevens volgens een vooraf bepaalde planning en wordt opgeslagen op een aangewezen locatie (lokaal of in de cloud).
* Volumes en afzonderlijke bestanden herstellen.

Back-ups worden vastgelegd als momentopnamen, die alleen de wijzigingen opnemen sinds de laatste momentopname is gemaakt en veel minder opslagruimte vereisen dan volledige back-ups. U back-upschema's maken of direct back-ups maken als dat nodig is. Daarnaast u StorSimple Snapshot Manager gebruiken om bewaarbeleid op te stellen dat bepaalt hoeveel momentopnamen worden opgeslagen. Als u vervolgens gegevens uit een back-up moet herstellen, u met StorSimple Snapshot Manager kiezen uit de catalogus met lokale of cloudmomentopnamen. 

Als er een ramp optreedt of als u gegevens om een andere reden moet herstellen, herstelt StorSimple Snapshot Manager deze stapsgewijs als nodig is. Voor het herstellen van gegevens hoeft u het hele systeem niet af te sluiten terwijl u een bestand herstelt, apparatuur vervangt of bewerkingen naar een andere site verplaatst.

Ga voor meer informatie naar [Wat is StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>StorSimple Adapter voor SharePoint
Microsoft Azure StorSimple bevat de StorSimple-adapter voor SharePoint, een optioneel onderdeel dat op transparante wijze storSimple-opslag- en gegevensbeveiligingsfuncties uitbreidt naar SharePoint-serverfarms. De adapter werkt met een RBS-provider (Remote Blob Storage) en de SQL Server RBS-functie, zodat u BLOB's verplaatsen naar een server die wordt ondersteund door het Microsoft Azure StorSimple-systeem. Microsoft Azure StorSimple slaat de BLOB-gegevens vervolgens lokaal of in de cloud op op basis van het gebruik.

De StorSimple-adapter voor SharePoint wordt beheerd vanuit de SharePoint Central Administration-portal. SharePoint-beheer blijft dus gecentraliseerd en alle opslag lijkt zich in het SharePoint-farm te bevindt.

Ga voor meer informatie naar [StorSimple Adapter voor SharePoint.](storsimple-adapter-for-sharepoint.md) 

## <a name="storage-management-technologies"></a>Opslagbeheertechnologieën
Naast het speciale StorSimple-apparaat, virtueel apparaat en andere componenten, gebruikt Microsoft Azure StorSimple de volgende softwaretechnologieën om snelle toegang tot gegevens te bieden en het opslagverbruik te verminderen:

* [Automatische opslaglaag](#automatic-storage-tiering) 
* [Thin provisioning](#thin-provisioning) 
* [Ontdubbeling en compressie](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Automatische opslaglaag
Microsoft Azure StorSimple rangschikt gegevens automatisch in logische lagen op basis van huidig gebruik, leeftijd en relatie met andere gegevens. Gegevens die het meest actief zijn, worden lokaal opgeslagen, terwijl minder actieve en inactieve gegevens automatisch naar de cloud worden gemigreerd. Het volgende diagram illustreert deze opslagbenadering.

![StorSimple-opslaglagen](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Om snelle toegang mogelijk te maken, slaat StorSimple zeer actieve gegevens (hot data) op op ssd's in het StorSimple-apparaat. Het slaat gegevens op die af en toe (warme gegevens) worden gebruikt op hdd's in het apparaat of op servers in het datacenter. Het verplaatst inactieve gegevens, back-upgegevens en gegevens die worden bewaard voor archiverings- of nalevingsdoeleinden naar de cloud. 

> [!NOTE]
> In Update 2 of hoger u een volume opgeven als lokaal vastgemaakt, in welk geval de gegevens op het lokale apparaat blijven en niet naar de cloud zijn gegelaagd. 


StorSimple past gegevens- en opslagtoewijzingen aan en herschikt deze naarmate de gebruikspatronen veranderen. Sommige informatie kan bijvoorbeeld na verloop van tijd minder actief worden. Als het steeds minder actief wordt, wordt het gemigreerd van SSD's naar HDD's en vervolgens naar de cloud. Als diezelfde gegevens weer actief worden, wordt deze teruggemigreerd naar het opslagapparaat.

Het opslaglaagproces vindt als volgt plaats:

1. Een systeembeheerder stelt een Microsoft Azure-cloudopslagaccount in.
2. De beheerder gebruikt de seriële console en de StorSimple Device Manager-service (uitgevoerd in de Azure-portal) om het apparaat en de bestandsserver te configureren, waardoor volumes en gegevensbeschermingsbeleid worden gemaakt. On-premises machines (zoals bestandsservers) gebruiken de Internet Small Computer System Interface (iSCSI) om toegang te krijgen tot het StorSimple-apparaat.
3. In eerste instantie slaat StorSimple gegevens op over de snelle SSD-laag van het apparaat.
4. Terwijl de SSD-laag de capaciteit benadert, dedupeert en comprimeert StorSimple de oudste gegevensblokken en verplaatst deze naar de HDD-laag.
5. Terwijl de HDD-laag de capaciteit benadert, versleutelt StorSimple de oudste gegevensblokken en stuurt deze veilig naar het Microsoft Azure-opslagaccount via HTTPS.
6. Microsoft Azure maakt meerdere replica's van de gegevens in het datacenter en in een extern datacenter, zodat de gegevens kunnen worden hersteld als zich een ramp voordoet.
7. Wanneer de bestandsserver gegevens opvraagt die in de cloud zijn opgeslagen, retourneert StorSimple deze naadloos en slaat een kopie op de SSD-laag van het StorSimple-apparaat op.

#### <a name="how-storsimple-manages-cloud-data"></a>Hoe StorSimple cloudgegevens beheert

StorSimple dedupeert klantgegevens voor alle momentopnamen en de primaire gegevens (gegevens geschreven door hosts). Hoewel ontdubbeling geweldig is voor opslagefficiëntie, maakt het de vraag "wat er in de cloud" zit ingewikkeld. De gelaagde primaire gegevens en de momentopnamegegevens overlappen elkaar. Een enkel stuk gegevens in de cloud kan worden gebruikt als gelaagde primaire gegevens en ook worden verwezen door verschillende momentopnamen. Elke cloudmomentopname zorgt ervoor dat een kopie van alle point-in-time gegevens in de cloud wordt vergrendeld totdat die momentopname is verwijderd.

Gegevens worden alleen uit de cloud verwijderd als er geen verwijzingen naar die gegevens zijn. Als we bijvoorbeeld een cloudmomentopname maken van alle gegevens die zich in het StorSimple-apparaat bevinden en vervolgens bepaalde primaire gegevens verwijderen, zien we de _primaire gegevens_ onmiddellijk dalen. De _cloudgegevens_ die de gelaagde gegevens en de back-ups bevatten, blijven hetzelfde. Dit komt omdat er een momentopname nog steeds verwijst naar de cloudgegevens. Nadat de cloudmomentopname is verwijderd (en elke andere momentopname waarnaar naar dezelfde gegevens verwijst), daalt het cloudverbruik. Voordat we cloudgegevens verwijderen, controleren we of er geen momentopnamen nog steeds verwijzen naar die gegevens. Dit proces wordt _garbage collection_ genoemd en is een achtergrondservice die op het apparaat wordt uitgevoerd. Verwijdering van cloudgegevens is niet onmiddellijk omdat de garbage collection-service controleert op andere verwijzingen naar die gegevens voordat deze worden verwijderd. De snelheid van het ophalen van afval is afhankelijk van het totale aantal momentopnamen en de totale gegevens. Doorgaans worden de cloudgegevens in minder dan een week opgeschoond.


### <a name="thin-provisioning"></a>Thin provisioning
Dunne inrichting is een virtualisatietechnologie waarbij de beschikbare opslag de fysieke resources lijkt te overschrijden. In plaats van vooraf voldoende opslagruimte te reserveren, gebruikt StorSimple dunne provisioning om net genoeg ruimte toe te wijzen om aan de huidige vereisten te voldoen. Het elastische karakter van cloudopslag vergemakkelijkt deze aanpak omdat StorSimple de cloudopslag kan verhogen of verlagen om aan veranderende eisen te voldoen.

> [!NOTE]
> Lokaal vastgemaakte volumes zijn niet dun ingericht. Opslag die wordt toegewezen aan een lokaal volume, wordt in zijn geheel ingericht wanneer het volume wordt gemaakt.


### <a name="deduplication-and-compression"></a>Ontdubbeling en compressie
Microsoft Azure StorSimple gebruikt ontdubbeling en gegevenscompressie om de opslagvereisten verder te verminderen.

Ontdubbeling vermindert de totale hoeveelheid gegevens die is opgeslagen door redundantie in de opgeslagen gegevensset te elimineren. Als de informatie verandert, negeert StorSimple de ongewijzigde gegevens en legt alleen de wijzigingen vast. Bovendien vermindert StorSimple de hoeveelheid opgeslagen gegevens door onnodige informatie te identificeren en te verwijderen. 

> [!NOTE]
> Gegevens over lokaal vastgemaakte volumes worden niet gededupliceerd of gecomprimeerd. Back-ups van lokaal vastgemaakte volumes worden echter gededupliceerd en gecomprimeerd.


## <a name="storsimple-workload-summary"></a>Overzicht van de StorSimple-werkbelasting
Hieronder vindt u een overzicht van de ondersteunde StorSimple-workloads.

| Scenario | Workload | Ondersteund | Beperkingen | Versie |
| --- | --- | --- | --- | --- |
| Samenwerking |Het delen van bestanden |Ja | |Alle versies |
| Samenwerking |Gedistribueerde bestandsdeling |Ja | |Alle versies |
| Samenwerking |SharePoint |Ja* |Alleen ondersteund met lokaal vastgemaakte volumes |Update 2 en hoger |
| Archivering |Eenvoudige bestandsarchivering |Ja | |Alle versies |
| Virtualisatie |Virtuele machines |Ja* |Alleen ondersteund met lokaal vastgemaakte volumes |Update 2 en hoger |
| Database |SQL |Ja* |Alleen ondersteund met lokaal vastgemaakte volumes |Update 2 en hoger |
| Videobewaking |Videobewaking |Ja* |Ondersteund wanneer storSimple-apparaat alleen is gewijd aan deze werkbelasting |Update 2 en hoger |
| Back-up |Primaire doelback-up |Ja* |Ondersteund wanneer storSimple-apparaat alleen is gewijd aan deze werkbelasting |Update 3 en hoger |
| Back-up |Secundaire doelback-up |Ja* |Ondersteund wanneer storSimple-apparaat alleen is gewijd aan deze werkbelasting |Update 3 en hoger |

*Ja&#42; - Richtlijnen en beperkingen van oplossingen moeten worden toegepast.*

De volgende workloads worden niet ondersteund door apparaten uit de StorSimple 8000-serie. Als deze workloads worden geïmplementeerd op StorSimple, resulteren deze workloads in een niet-ondersteunde configuratie.

* Medische beeldvorming
* Exchange
* Vdi
* Oracle
* SAP
* Big Data
* Inhoudsdistributie
* Opstarten vanuit SCSI

Hieronder volgt een lijst van de storSimple ondersteunde infrastructuurcomponenten.

| Scenario | Workload | Ondersteund | Beperkingen | Versie |
| --- | --- | --- | --- | --- |
| Algemeen |ExpressRoute |Ja | |Alle versies |
| Algemeen |DataCore FC |Ja* |Ondersteund met DataCore SANsymphony |Alle versies |
| Algemeen |DFSR |Ja* |Alleen ondersteund met lokaal vastgemaakte volumes |Alle versies |
| Algemeen |Indexeren |Ja* |Voor gelaagde volumes wordt alleen metadataindexering ondersteund (geen gegevens).<br>Voor lokaal vastgemaakte volumes wordt volledige indexering ondersteund. |Alle versies |
| Algemeen |Antivirus |Ja* |Voor gelaagde volumes wordt alleen scannen op openen en sluiten ondersteund.<br> Voor lokaal vastgemaakte volumes wordt volledige scan ondersteund. |Alle versies |

*Ja&#42; - Richtlijnen en beperkingen van oplossingen moeten worden toegepast.*

Hieronder volgt een lijst van andere software die met StorSimple wordt gebruikt om oplossingen te bouwen.

| Werkbelastingtype | Software die wordt gebruikt met StorSimple | Ondersteunde versies|Koppeling naar oplossingshandleiding| 
| --- | --- | --- | --- |
| Back-updoel |Veeam Veeam |Veeam v 9 en later |[StorSimple als back-up doel met Veaam](storsimple-configure-backup-target-veeam.md)|
| Back-updoel |Veritas Back-up Exec |Back-up van Exec 16 en hoger |[StorSimple als back-updoel met Backup Exec](storsimple-configure-backup-target-using-backup-exec.md)|
| Back-updoel |Veritas NetBackup |NetBackup 7.7.x en hoger  |[StorSimple als back-updoel met NetBackup](storsimple-configure-backuptarget-netbackup.md)|
| Wereldwijd delen van bestanden <br></br> Samenwerking |Talon  |[StorSimple met Talon](https://www.talonstorage.com/products/archive/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>StorSimple terminologie
Voordat u uw Microsoft Azure StorSimple-oplossing implementeert, raden we u aan de volgende termen en definities te bekijken.

### <a name="key-terms-and-definitions"></a>Belangrijkste termen en definities
| Term (Acroniem of afkorting) | Beschrijving |
| --- | --- |
| Access control record (ACR) |Een record dat is gekoppeld aan een volume op uw Microsoft Azure StorSimple-apparaat dat bepaalt welke hosts er verbinding mee kunnen maken. De bepaling is gebaseerd op de iSCSI Qualified Name (IQN) van de hosts (opgenomen in de ACR) die verbinding maken met uw StorSimple-apparaat. |
| AES-256 |Een 256-bits Advanced Encryption Standard (AES) algoritme voor het versleutelen van gegevens als het beweegt van en naar de cloud. |
| toewijzingseenheidsgrootte (AUS) |De kleinste hoeveelheid schijfruimte die kan worden toegewezen om een bestand in uw Windows-bestandssystemen te bewaren. Als een bestandsgrootte niet een even veelvoud van de clustergrootte is, moet extra ruimte worden gebruikt om het bestand vast te houden (tot het volgende veelvoud van de clustergrootte), wat resulteert in verloren ruimte en fragmentatie van de harde schijf. <br>De aanbevolen AUS voor Azure StorSimple-volumes is 64 KB omdat het goed werkt met de ontdubbelingsalgoritmen. |
| geautomatiseerde opslaglaag |Verplaats automatisch minder actieve gegevens van SD's naar HDD's en vervolgens naar een laag in de cloud en schakel vervolgens het beheer van alle opslag vanuit een centrale gebruikersinterface in. |
| back-upcatalogus |Een verzameling back-ups, meestal gerelateerd aan het toepassingstype dat is gebruikt. Deze verzameling wordt weergegeven in het blade back-upcatalogus van de gebruikersinterface van de StorSimple Device Manager-service. |
| back-upcatalogusbestand |Een bestand met een lijst met beschikbare momentopnamen die momenteel zijn opgeslagen in de back-updatabase van StorSimple Snapshot Manager. |
| back-upbeleid |Een selectie van volumes, type back-up en een tijdschema waarmee u back-ups maken volgens een vooraf gedefinieerd schema. |
| binaire grote objecten (BLOBs) |Een verzameling van binaire gegevens die als één entiteit in een databasebeheersysteem zijn opgeslagen. BLOB's zijn meestal afbeeldingen, audio of andere multimediaobjecten, hoewel soms binaire uitvoerbare code wordt opgeslagen als een BLOB. |
| Challenge Handshake Authentication Protocol (CHAP) |Een protocol dat wordt gebruikt om de peer van een verbinding te verifiëren, op basis van het delen van een wachtwoord of geheim door peer. CHAP kan eenrichtingsverkeer of wederzijds zijn. Met eenrichtingsCHAP verifieert het doel een initiator. Mutual CHAP vereist dat het doel de initiator verifieert en dat de initiator het doel verifieert. |
| clone |Een dubbele kopie van een volume. |
| Cloud as a Tier (CaaT) |Cloudopslag geïntegreerd als een laag binnen de opslagarchitectuur, zodat alle opslag deel lijkt uit te maken van één bedrijfsopslagnetwerk. |
| cloudserviceprovider (CSP) |Een leverancier van cloud computing-diensten. |
| cloudmomentopname |Een point-in-time kopie van volumegegevens die in de cloud is opgeslagen. Een cloudmomentopname is gelijk aan een momentopname die wordt gerepliceerd op een ander, off-site opslagsysteem. Cloudsnapshots zijn vooral handig in scenario's voor noodherstel. |
| versleutelingssleutel voor cloudopslag |Een wachtwoord of een sleutel die door uw StorSimple-apparaat wordt gebruikt om toegang te krijgen tot de versleutelde gegevens die door uw apparaat naar de cloud worden verzonden. |
| clusterbewuste updates |Software-updates beheren op servers in een failovercluster, zodat de updates minimaal of geen effect hebben op de beschikbaarheid van de service. |
| gegevenspad |Een verzameling functionele eenheden die onderling verbonden gegevensverwerkingen uitvoeren. |
| deactiveren |Een permanente actie die de verbinding tussen het StorSimple-apparaat en de bijbehorende cloudservice doorbreekt. Cloudsnapshots van het apparaat blijven na dit proces en kunnen worden gekloond of gebruikt voor noodherstel. |
| schijfspiegeling |Replicatie van logische schijfvolumes op afzonderlijke harde schijven in realtime om continue beschikbaarheid te garanderen. |
| dynamische schijfmirroring |Replicatie van logische schijfvolumes op dynamische schijven. |
| dynamische schijven |Een schijfvolumeindeling die gebruikmaakt van de LDM (Logical Disk Manager) om gegevens op meerdere fysieke schijven op te slaan en te beheren. Dynamische schijven kunnen worden vergroot om meer vrije ruimte te bieden. |
| Uitgebreide groep schijven (EBOD) behuizing |Een secundaire behuizing van uw Microsoft Azure StorSimple-apparaat met extra harde schijfschijven voor extra opslag. |
| vetvoorziening |Een conventionele opslagvoorziening waarin opslagruimte wordt toegewezen op basis van verwachte behoeften (en is meestal buiten de huidige behoefte). Zie ook *dunne provisioning*. |
| harde schijf (HDD) |Een station dat roterende platters gebruikt om gegevens op te slaan. |
| hybride cloudopslag |Een opslagarchitectuur die lokale en off-site bronnen gebruikt, waaronder cloudopslag. |
| Internet Small Computer System Interface (iSCSI) |Een op internetprotocol gebaseerde opslagnetwerkstandaard voor het koppelen van gegevensopslagapparatuur of -faciliteiten. |
| iSCSI-initiator |Een softwarecomponent waarmee een hostcomputer met Windows verbinding kan maken met een extern iSCSI-gebaseerd opslagnetwerk. |
| iSCSI-gekwalificeerde naam (IQN) |Een unieke naam die een iSCSI-doel of initiator identificeert. |
| iSCSI-doel |Een softwarecomponent die gecentraliseerde iSCSI-schijfsubsystemen biedt in opslagnetwerken. |
| live archivering |Een opslagbenadering waarbij archiefgegevens altijd toegankelijk zijn (deze worden bijvoorbeeld niet off-site op tape opgeslagen). Microsoft Azure StorSimple maakt gebruik van live archivering. |
| lokaal vastgemaakt volume |een volume dat zich op het apparaat bevindt en nooit naar de cloud is gegelaagd. |
| lokale momentopname |Een point-in-time kopie van volumegegevens die is opgeslagen op het Microsoft Azure StorSimple-apparaat. |
| Microsoft Azure StorSimple |Een krachtige oplossing bestaande uit een datacenteropslagtoestel en software waarmee IT-organisaties gebruik kunnen maken van cloudopslag alsof het datacenteropslag is. StorSimple vereenvoudigt gegevensbescherming en gegevensbeheer en verlaagt tegelijkertijd de kosten. De oplossing consolideert primaire opslag, archief, back-up en disaster recovery (DR) door naadloze integratie met de cloud. Door SAN-opslag en cloudgegevensbeheer te combineren op een platform van bedrijfsniveau, maken StorSimple-apparaten snelheid, eenvoud en betrouwbaarheid mogelijk voor alle opslaggerelateerde behoeften. |
| Stroom- en koelmodule (PCM) |Hardware componenten van uw StorSimple apparaat bestaande uit de voedingen en de koelventilator, vandaar de naam Power and Cooling module. De primaire behuizing van het apparaat heeft twee 764W PCM's, terwijl de EBOD-behuizing twee 580W-PCM's heeft. |
| primaire behuizing |Hoofdbehuizing van uw StorSimple-apparaat met de controllers van het toepassingsplatform. |
| hersteltijddoelstelling (RTO) |De maximale hoeveelheid tijd die moet worden besteed voordat een bedrijfsproces of systeem volledig is hersteld na een ramp. |
| seriële bijgevoegde SCSI (SAS) |Een type harde schijf (HDD). |
| versleutelingssleutel voor servicegegevens |Een sleutel die beschikbaar wordt gesteld aan elk nieuw StorSimple-apparaat dat zich registreert met de StorSimple Device Manager-service. De configuratiegegevens die worden overgedragen tussen de StorSimple Device Manager-service en het apparaat worden versleuteld met behulp van een openbare sleutel en kunnen vervolgens alleen op het apparaat worden gedecodeerd met een privésleutel. Met de versleutelingssleutel van servicegegevens kan de service deze privésleutel verkrijgen voor decryptie. |
| serviceregistratiesleutel |Een sleutel waarmee het StorSimple-apparaat kan worden geregistreerd bij de StorSimple Device Manager-service, zodat deze wordt weergegeven in de Azure-portal voor verdere beheeracties. |
| SCSI (Small Computer System Interface) |Een reeks standaarden voor het fysiek verbinden van computers en het doorgeven van gegevens tussen hen. |
| solid state drive (SSD) |Een schijf die geen bewegende delen bevat; bijvoorbeeld een flashdrive. |
| opslagaccount |Een set toegangsreferenties die zijn gekoppeld aan uw opslagaccount voor een bepaalde cloudserviceprovider. |
| StorSimple Adapter voor SharePoint |Een Microsoft Azure StorSimple-component die StorSimple-opslag- en gegevensbescherming op transparante wijze uitbreidt naar SharePoint-serverfarms. |
| StorSimple-apparaatbeheerfunctie |Een uitbreiding van de Azure-portal waarmee u uw Azure StorSimple on-premises en virtuele apparaten beheren. |
| StorSimple Snapshot Manager |Een MMC-module (Microsoft Management Console) voor het beheren van back-up- en herstelbewerkingen in Microsoft Azure StorSimple. |
| maak een back-up |Een functie waarmee de gebruiker een interactieve back-up van een volume kan maken. Het is een alternatieve manier om een handmatige back-up van een volume te nemen in plaats van een geautomatiseerde back-up te maken via een gedefinieerd beleid. |
| dunne inrichting |Een methode om de efficiëntie waarmee de beschikbare opslagruimte wordt gebruikt in opslagsystemen te optimaliseren. In dunne inrichting, de opslag wordt toegewezen aan meerdere gebruikers op basis van de minimale ruimte die nodig is door elke gebruiker op een bepaald moment. Zie ook *vetvoorziening*. |
| tiering |Gegevens in logische groeperingen rangschikken op basis van huidig gebruik, leeftijd en relatie tot andere gegevens. StorSimple rangschikt automatisch gegevens in lagen. |
| volume |Logische opslagruimten gepresenteerd in de vorm van stations. StorSimple volumes komen overeen met de volumes die door de host zijn gemonteerd, inclusief de volumes die zijn ontdekt door het gebruik van iSCSI en een StorSimple-apparaat. |
| volumecontainer |Een groepering van volumes en de instellingen die daarop van toepassing zijn. Alle volumes in uw StorSimple-apparaat zijn gegroepeerd in volumecontainers. Volumecontainer-instellingen omvatten opslagaccounts, versleutelingsinstellingen voor gegevens die naar de cloud worden verzonden met bijbehorende versleutelingssleutels en bandbreedte die wordt verbruikt voor bewerkingen met betrekking tot de cloud. |
| volumegroep |In StorSimple Snapshot Manager is een volumegroep een verzameling volumes die zijn geconfigureerd om back-upverwerking te vergemakkelijken. |
| Vss (Volume Shadow Copy Service) |Een Windows Server-besturingssysteemservice die de consistentie van toepassingen vergemakkelijkt door te communiceren met VSS-bewuste toepassingen om het maken van incrementele momentopnamen te coördineren. VSS zorgt ervoor dat de toepassingen tijdelijk inactief zijn wanneer momentopnamen worden gemaakt. |
| Windows PowerShell voor StorSimple |Een op Windows PowerShell gebaseerde command-line interface die wordt gebruikt om uw StorSimple-apparaat te bedienen en te beheren. Met behoud van een aantal van de basismogelijkheden van Windows PowerShell, deze interface heeft extra speciale cmdlets die zijn gericht op het beheer van een StorSimple-apparaat. |

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [Beveiliging van StorSimple](storsimple-8000-security.md).

