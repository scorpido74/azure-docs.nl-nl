---
title: StorSimple-adapter voor SharePoint installeren | Microsoft Documenten
description: Beschrijft hoe u de StorSimple-adapter voor SharePoint in een SharePoint-serverfarm installeert en configureert of verwijdert.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: twooley
ms.openlocfilehash: a841ce8b664389ccd8fdf55de9965f09412fecf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75930208"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>De StorSimple-adapter voor SharePoint installeren en configureren
## <a name="overview"></a>Overzicht
De StorSimple-adapter voor SharePoint is een onderdeel waarmee u flexibele opslag en gegevensbescherming van Microsoft Azure StorSimple bieden aan SharePoint-serverfarms. U de adapter gebruiken om BLOB-inhoud (Binary Large Object) te verplaatsen van de SQL Server-inhoudsdatabases naar het hybride cloudopslagapparaat van Microsoft Azure StorSimple.

De StorSimple-adapter voor SharePoint fungeert als een RBS-provider (Remote BLOB Storage) en gebruikt de SQL Server Remote BLOB Storage-functie om ongestructureerde SharePoint-inhoud (in de vorm van BLOB's) op te slaan op een bestandsserver die wordt ondersteund door een StorSimple-apparaat.

> [!NOTE]
> De StorSimple-adapter voor SharePoint ondersteunt SharePoint Server 2010 Remote BLOB Storage (RBS). Het ondersteunt geen Externe BLOB Storage (SharePoint Server 2010) (EBS).


* Als u de StorSimple-adapter voor SharePoint wilt downloaden, gaat u naar [StorSimple Adapter voor SharePoint][1] in het Microsoft Downloadcentrum.
* Ga voor informatie over planning voor RBS- en RBS-beperkingen naar [Besluiten om RBS te gebruiken in SharePoint 2013][2] of [Plan for RBS (SharePoint Server 2010).][3]

In de rest van dit overzicht wordt kort de rol beschreven van de StorSimple-adapter voor SharePoint en de capaciteit- en prestatielimieten van SharePoint waarvan u op de hoogte moet zijn voordat u de adapter installeert en configureert. Nadat u deze informatie hebt gecontroleerd, gaat u naar [StorSimple-adapter voor SharePoint-installatie](#storsimple-adapter-for-sharepoint-installation) om te beginnen met het instellen van de adapter.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>StorSimple-adapter voor SharePoint-voordelen
In een SharePoint-site wordt inhoud opgeslagen als ongestructureerde BLOB-gegevens in een of meer inhoudsdatabases. Deze databases worden standaard gehost op computers waarop SQL Server wordt uitgevoerd en bevinden zich in de SharePoint-serverfarm. BLOB's kunnen snel in omvang toenemen en grote hoeveelheden on-premises opslag verbruiken. Om deze reden wilt u misschien een andere, goedkopere opslagoplossing vinden. SQL Server biedt een technologie genaamd Remote Blob Storage (RBS) waarmee u BLOB-inhoud opslaan in het bestandssysteem, buiten de SQL Server-database. Met RBS kunnen BLOB's zich in het bestandssysteem bevinden op de computer waarop SQL Server wordt uitgevoerd, of kunnen ze worden opgeslagen in het bestandssysteem op een andere servercomputer.

RBS vereist dat u een RBS-provider gebruikt, zoals de StorSimple-adapter voor SharePoint, om RBS in SharePoint in te schakelen. De StorSimple-adapter voor SharePoint werkt met RBS, zodat u BLOBs verplaatsen naar een server die wordt ondersteund door het Microsoft Azure StorSimple-systeem. Microsoft Azure StorSimple slaat de BLOB-gegevens vervolgens lokaal of in de cloud op op basis van het gebruik. BLOB's die zeer actief zijn (meestal aangeduid als Tier 1 of hot data) wonen lokaal. Minder actieve gegevens en archiefgegevens bevinden zich in de cloud. Nadat u RBS in een inhoudsdatabase hebt ingeschakeld, wordt elke nieuwe BLOB-inhoud die in SharePoint is gemaakt, opgeslagen op het StorSimple-apparaat en niet in de inhoudsdatabase.

De Microsoft Azure StorSimple-implementatie van RBS biedt de volgende voordelen:

* Door BLOB-inhoud naar een afzonderlijke server te verplaatsen, u de querybelasting op SQL Server verminderen, wat de responsiviteit van SQL Server kan verbeteren. 
* Azure StorSimple gebruikt ontdubbeling en compressie om de gegevensgrootte te verkleinen.
* Azure StorSimple biedt gegevensbescherming in de vorm van lokale en cloudsnapshots. Ook als u de database zelf op het StorSimple-apparaat plaatst, u samen een back-up maken van de inhoudsdatabase en BLOB's op een crashconsistente manier. (Het verplaatsen van de inhoudsdatabase naar het apparaat wordt alleen ondersteund voor het apparaat uit de StorSimple 8000-serie. Deze functie wordt niet ondersteund voor de 5000- of 7000-serie.)
* Azure StorSimple bevat functies voor noodherstel, waaronder failover, bestands- en volumeherstel (inclusief testherstel) en snel herstel van gegevens.
* U datarecoverysoftware, zoals Kroll Ontrack PowerControls, gebruiken met StorSimple-momentopnamen van BLOB-gegevens om itemniveauherstel van SharePoint-inhoud uit te voeren. (Deze data recovery software is een aparte aankoop.)
* De StorSimple-adapter voor SharePoint wordt aangesloten op de SharePoint Central Administration-portal, zodat u uw volledige SharePoint-oplossing vanaf een centrale locatie beheren.

Het verplaatsen van BLOB-inhoud naar het bestandssysteem kan andere kostenbesparingen en voordelen opleveren. Met rbs kan bijvoorbeeld de behoefte aan dure Tier 1-opslag verminderen en omdat rbs hierdoor de inhoudsdatabase krimpt, kan het aantal databases dat nodig is in de SharePoint-serverfarm worden verminderd. Andere factoren, zoals limieten voor databasegrootte en de hoeveelheid niet-RBS-inhoud, kunnen echter ook van invloed zijn op de opslagvereisten. Zie [Voor RBS (SharePoint Foundation 2010)][4] en [Besluit om RBS te gebruiken in SharePoint 2013][5]voor meer informatie over de kosten en baten van het gebruik van RBS .

### <a name="capacity-and-performance-limits"></a>Capaciteits- en prestatielimieten
Voordat u rbs in uw SharePoint-oplossing gaat gebruiken, moet u zich bewust zijn van de geteste prestatie- en capaciteitslimieten van SharePoint Server 2010 en SharePoint Server 2013 en hoe deze limieten zich verhouden tot acceptabele prestaties. Zie [Softwaregrenzen en -limieten voor SharePoint 2013 voor](https://technet.microsoft.com/library/cc262787.aspx)meer informatie.

Bekijk het volgende voordat u RBS configureert:

* Zorg ervoor dat de totale grootte van de inhoud (de grootte van een inhoudsdatabase plus de grootte van de bijbehorende geexternaliseerde BLOB's) de RBS-groottelimiet die door SharePoint wordt ondersteund, niet overschrijdt. Deze limiet is 200 GB. 
  
    **Inhoudsdatabase en BLOB-grootte meten**
  
  1. Voer deze query uit op de WFE centrale administratie. Start de SharePoint Management Shell en voer de volgende Opdracht Windows PowerShell in om de grootte van de inhoudsdatabases op te halen:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Met deze stap wordt de grootte van de inhoudsdatabase op de schijf.
  2. Voer een van de volgende SQL-query's uit in SQL Management Studio in het SQL-servervak in elke inhoudsdatabase en voeg het resultaat toe aan het getal dat in stap 1 is verkregen.
     
     Voer in inhoudsdatabases van SharePoint 2013 het als:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     Voer in SharePoint 2010-inhoudsdatabases het als:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Deze stap krijgt de grootte van de BLOBs die zijn extern.
* We raden u aan alle BLOB- en database-inhoud lokaal op te slaan op het StorSimple-apparaat. Het StorSimple-apparaat is een cluster met twee plaatsen voor hoge beschikbaarheid. Het plaatsen van de inhoud databases en BLOBs op de StorSimple apparaat biedt hoge beschikbaarheid.
  
    Gebruik de aanbevolen procedures voor traditionele SQL Server-migratieom de inhoudsdatabase naar het StorSimple-apparaat te verplaatsen. Verplaats de database pas nadat alle BLOB-inhoud uit de database is verplaatst naar de bestandsshare via RBS. Als u ervoor kiest de inhoudsdatabase naar het StorSimple-apparaat te verplaatsen, raden we u aan de opslag van de inhoudsdatabase op het apparaat als primair volume te configureren.
* Als u in Microsoft Azure StorSimple gelaagde volumes gebruikt, u niet garanderen dat inhoud die lokaal op het StorSimple-apparaat is opgeslagen, niet wordt gelaagd naar Microsoft Azure-cloudopslag. Daarom raden we aan om StorSimple lokaal vastgemaakte volumes te gebruiken in combinatie met SharePoint RBS. Dit zorgt ervoor dat alle BLOB-inhoud lokaal op het StorSimple-apparaat blijft en niet wordt verplaatst naar Microsoft Azure.
* Als u de inhoudsdatabases niet op het StorSimple-apparaat opslaat, gebruikt u traditionele best practices met hoge beschikbaarheid van SQL Server met hoge beschikbaarheid die RBS ondersteunen. SQL Server-clustering ondersteunt RBS, terwijl SQL Server-mirroring dat niet doet. 

> [!WARNING]
> Als u RBS niet hebt ingeschakeld, raden we u aan de inhoudsdatabase naar het StorSimple-apparaat te verplaatsen. Dit is een niet-geteste configuratie.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>StorSimple-adapter voor SharePoint-installatie
Voordat u de StorSimple-adapter voor SharePoint installeren, moet u het StorSimple-apparaat configureren en ervoor zorgen dat de SharePoint-serverfarm en SQL Server-instantiatie aan alle vereisten voldoen. In deze zelfstudie worden configuratievereisten beschreven, evenals procedures voor het installeren en upgraden van de StorSimple-adapter voor SharePoint.

## <a name="configure-prerequisites"></a>Vereisten configureren
Voordat u de StorSimple-adapter voor SharePoint installeren, moet u ervoor zorgen dat het StorSimple-apparaat, de SharePoint-serverfarm en sql server-instantiatie voldoen aan de volgende vereisten.

### <a name="system-requirements"></a>Systeemvereisten
De StorSimple-adapter voor SharePoint werkt met de volgende hardware en software:

* Ondersteund besturingssysteem – Windows Server 2008 R2 SP1, Windows Server 2012 of Windows Server 2012 R2
* Ondersteunde SharePoint-versies – SharePoint Server 2010 of SharePoint Server 2013
* Ondersteunde SQL Server-versies – SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition of SQL Server 2012 Enterprise Edition
* Ondersteunde StorSimple-apparaten - StorSimple 8000-serie, StorSimple 7000-serie of StorSimple 5000-serie.

### <a name="storsimple-device-configuration-prerequisites"></a>Vereisten voor storSimple-apparaatconfiguratie
Het StorSimple-apparaat is een blokapparaat en vereist als zodanig een bestandsserver waarop de gegevens kunnen worden gehost. We raden u aan een aparte server te gebruiken in plaats van een bestaande server uit de SharePoint-farm. Deze bestandsserver moet zich op hetzelfde LAN (Local Area Network) bevinden als de SQL Server-computer die de inhoudsdatabases host.

> [!TIP]
> * Als u uw SharePoint-farm configureert voor hoge beschikbaarheid, moet u de bestandsserver ook implementeren voor hoge beschikbaarheid.
> * Als u de inhoudsdatabase niet op slaat op het StorSimple-apparaat, gebruikt u traditionele aanbevolen procedures met hoge beschikbaarheid die RBS ondersteunen. SQL Server-clustering ondersteunt RBS, terwijl SQL Server-mirroring dat niet doet. 


Zorg ervoor dat uw StorSimple-apparaat correct is geconfigureerd en dat de juiste volumes ter ondersteuning van uw SharePoint-implementatie zijn geconfigureerd en toegankelijk zijn vanaf uw SQL Server-computer. Ga naar [Uw on-premises StorSimple-apparaat implementeren](storsimple-8000-deployment-walkthrough-u2.md) als u uw StorSimple-apparaat nog niet hebt geïmplementeerd en geconfigureerd. Let op het IP-adres van het StorSimple-apparaat; je hebt het nodig tijdens storSimple Adapter voor SharePoint installatie.

Zorg er bovendien voor dat het volume dat moet worden gebruikt voor BLOB-externalisatie voldoet aan de volgende vereisten:

* Het volume moet worden opgemaakt met een grootte van 64 KB toewijzingseenheden.
* Uw web front end (WFE) en applicatieservers moeten toegang hebben tot het volume via een UNC-pad (Universal Naming Convention).
* De SharePoint-serverfarm moet zijn geconfigureerd om naar het volume te schrijven.

> [!NOTE]
> Nadat u de adapter hebt geïnstalleerd en geconfigureerd, moet alle BLOB-externalisatie via het StorSimple-apparaat gaan (het apparaat presenteert de volumes aan SQL Server en beheert de opslaglagen). U geen andere doelen gebruiken voor BLOB-externalisatie.


Als u StorSimple Snapshot Manager wilt gebruiken om snapshots van de BLOB- en databasegegevens te maken, moet u StorSimple Snapshot Manager op de databaseserver installeren, zodat de SQL Writer-service kan worden gebruikt om de WINDOWS Volume Shadow Copy Service (VSS) te implementeren.

> [!IMPORTANT]
> StorSimple Snapshot Manager ondersteunt de SharePoint VSS Writer niet en kan geen toepassingsconsistente momentopnamen van SharePoint-gegevens maken. In een SharePoint-scenario biedt StorSimple Snapshot Manager alleen crashconsistente back-ups.


## <a name="sharepoint-farm-configuration-prerequisites"></a>Vereisten voor configuratie van SharePoint-farm
Zorg ervoor dat uw SharePoint-serverfarm als volgt correct is geconfigureerd:

* Controleer of uw SharePoint-serverfarm in orde is en controleer het volgende:
* Alle SharePoint WFE- en toepassingsservers die in de farm zijn geregistreerd, worden uitgevoerd en kunnen worden gepingd vanaf de server waarop u de StorSimple-adapter voor SharePoint installeert.
* De SharePoint Timer-service (SPTimerV3 of SPTimerV4) wordt uitgevoerd op elke WFE-server en toepassingsserver.
* Zowel de SharePoint Timer-service als de IIS-toepassingsgroep waaronder de SharePoint Central Administration-site wordt uitgevoerd, hebben beheerdersbevoegdheden.
* Zorg ervoor dat de verbeterde beveiligingscontext van Internet Explorer (IE ESC) is uitgeschakeld. Volg de volgende stappen om IE ESC uit te schakelen:
  
  1. Sluit alle exemplaren van Internet Explorer.
  2. Serverbeheer starten.
  3. Klik in het linkerdeelvenster op **Lokale server**.
  4. Klik in het rechterdeelvenster naast **IE Enhanced Security Configuration**op **Aan**.
  5. Klik **onder Administrators**op Uit **.**
  6. Klik op **OK**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Vereisten voor externe BLOB-opslag (RBS)
Zorg ervoor dat u een ondersteunde versie van SQL Server gebruikt. Alleen de volgende versies worden ondersteund en kunnen RBS gebruiken:

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

BLOB's kunnen alleen worden geexternaliseerd op de volumes die het StorSimple-apparaat aan SQL Server presenteert. Er worden geen andere doelen voor BLOB-externalisatie ondersteund.

Wanneer u alle configuratiestappen voor vereiste hebt voltooid, gaat u naar [De StorSimple-adapter voor SharePoint installeren.](#install-the-storsimple-adapter-for-sharepoint)

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>De StorSimple-adapter voor SharePoint installeren
Gebruik de volgende stappen om de StorSimple-adapter voor SharePoint te installeren. Zie [De StorSimple-adapter voor SharePoint bijwerken of opnieuw installeren](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint)als u de software opnieuw installeert. De tijd die nodig is voor de installatie is afhankelijk van het totale aantal SharePoint-databases in uw SharePoint-serverfarm.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>RBS configureren
Nadat u de StorSimple-adapter voor SharePoint hebt geïnstalleerd, configureert u RBS zoals beschreven in de volgende procedure.

> [!TIP]
> De StorSimple-adapter voor SharePoint wordt aangesloten op de pagina Centraal beheer van SharePoint, zodat RBS kan worden ingeschakeld of uitgeschakeld in elke inhoudsdatabase in de SharePoint-farm. Het in- of uitschakelen van RBS in de inhoudsdatabase zorgt echter voor een IIS-reset, die, afhankelijk van uw bedrijfsconfiguratie, de beschikbaarheid van de SharePoint-webfront-end (WFE) tijdelijk kan verstoren. (Factoren zoals het gebruik van een front-end load balancer, de huidige serverworkload, enzovoort, kunnen deze onderbreking beperken of elimineren.) Om gebruikers te beschermen tegen een onderbreking, raden we u aan RBS alleen in te schakelen of uit te schakelen tijdens een gepland onderhoudsvenster.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>Garbage collection configureren
Wanneer objecten van een SharePoint-site worden verwijderd, worden ze niet automatisch verwijderd uit het volume van het RBS-archief. In plaats daarvan verwijdert een asynchrone, achtergrondonderhoudsprogramma verweesde BLOB's uit het bestandsarchief. Systeembeheerders kunnen dit proces plannen om periodiek uit te voeren of ze kunnen het starten wanneer dat nodig is.

Dit onderhoudsprogramma (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) wordt automatisch geïnstalleerd op alle SharePoint WFE-servers en toepassingsservers wanneer u RBS inschakelt. Het programma is geïnstalleerd op de volgende locatie: *opstartstation*:\Program Files\Microsoft SQL Remote Blob Storage 10.50\Maintainer\

Zie [RBS in SharePoint Server 2013 onderhouden voor][8]informatie over het configureren en gebruiken van het onderhoudsprogramma.

> [!IMPORTANT]
> Het RBS-onderhoudsprogramma is resource-intensief. U moet plannen dat deze alleen wordt uitgevoerd tijdens perioden van lichte activiteit op de SharePoint-farm.


### <a name="delete-orphaned-blobs-immediately"></a>Verwijder verweesde BLOBs onmiddellijk
Als u verweesde BLOB's onmiddellijk moet verwijderen, u de volgende instructies gebruiken. Houd er rekening mee dat deze instructies een voorbeeld zijn van hoe dit kan worden gedaan in een SharePoint 2013-omgeving met de volgende componenten:

* De naam van de inhoudsdatabase is WSS_Content.
* De SQL Server-naam is SHRPT13-SQL12\SHRPT13.
* De naam van de webtoepassing is SharePoint – 80.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>De StorSimple-adapter voor SharePoint upgraden of opnieuw installeren
Gebruik de volgende procedure om sharepoint-server te upgraden en vervolgens de StorSimple-adapter voor SharePoint opnieuw te installeren of om de adapter in een bestaande SharePoint-serverfarm te upgraden of opnieuw te installeren.

> [!IMPORTANT]
> Controleer de volgende gegevens voordat u probeert uw SharePoint-software te upgraden en/of de StorSimple-adapter voor SharePoint te upgraden of opnieuw te installeren:
> 
> * Bestanden die eerder via RBS naar externe opslag zijn verplaatst, zijn pas beschikbaar nadat de herinstallatie is voltooid en de RBS-functie opnieuw is ingeschakeld. Als u de impact van de gebruiker wilt beperken, voert u een upgrade of herinstallatie uit tijdens een gepland onderhoudsvenster.
> * De tijd die nodig is voor de upgrade/herinstallatie kan variëren, afhankelijk van het totale aantal SharePoint-databases in de SharePoint-serverfarm.
> * Nadat de upgrade/herinstallatie is voltooid, moet u RBS inschakelen voor de inhoudsdatabases. Zie [RBS configureren](#configure-rbs) voor meer informatie.
> * Als u RBS configureert voor een SharePoint-farm met een zeer groot aantal databases (meer dan 200), kan de pagina **SharePoint Central Administration** een time-out krijgen. Als dat gebeurt, vernieuwt u de pagina. Dit heeft geen invloed op het configuratieproces.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>StorSimple-adapter voor SharePoint-verwijdering
In de volgende procedures wordt beschreven hoe u de BLOB's terugverplaatst naar de SQL Server-inhoudsdatabases en vervolgens de StorSimple-adapter voor SharePoint verwijdert. 

> [!IMPORTANT]
> U moet de BLOB's terug naar de inhouddatabases verplaatsen voordat u de adaptersoftware verwijdert.


### <a name="before-you-begin"></a>Voordat u begint
Verzamel de volgende informatie voordat u de gegevens terugverplaatst naar de SQL Server-inhoudsdatabases en start het verwijderingsproces van de adapter:

* De namen van alle databases waarvoor RBS is ingeschakeld
* Het UNC-pad van het geconfigureerde BLOB-archief

### <a name="move-the-blobs-back-to-the-content-databases"></a>Verplaats de BLOBs terug naar de inhoudsdatabases
Voordat u de StorSimple-adapter voor SharePoint-software verwijdert, moet u alle BLOB's die zijn geëternaliseerd, migreren naar de SQL Server-inhoudsdatabases. Als u probeert de StorSimple-adapter voor SharePoint te verwijderen voordat u alle BLOB's terugverplaatst naar de inhoudsdatabases, ziet u het volgende waarschuwingsbericht.

![Waarschuwingsbericht](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>De BLOB's terug naar de inhoudsdatabases verplaatsen
1. Download elk van de geexternaliseerde objecten.
2. Open de pagina **Centraal Beheer van SharePoint** en blader naar **Systeeminstellingen**.
3. Klik **onder Azure StorSimple**op **StorSimple-adapter configureren**.
4. Klik op de pagina **StorSimple-adapter configureren** op de knop **Uitschakelen** onder elk van de inhoudsdatabases die u uit externe BLOB-opslag wilt verwijderen. 
5. Verwijder de objecten uit SharePoint en upload ze opnieuw.

U ook de `RBS Migrate()` Microsoft PowerShell-cmdlet gebruiken die bij SharePoint is meegeleverd. Zie [Inhoud migreren naar of uit RBS voor](https://technet.microsoft.com/library/ff628255.aspx)meer informatie.

Nadat u de BLOB's terug naar de inhoudsdatabase hebt verplaatst, gaat u naar de volgende stap: [Verwijder de adapter.](#uninstall-the-adapter)

### <a name="uninstall-the-adapter"></a>De adapter verwijderen
Nadat u de BLOB's terug hebt geplaatst naar de SQL Server-inhoudsdatabases, gebruikt u een van de volgende opties om de StorSimple-adapter voor SharePoint te verwijderen.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Het installatieprogramma gebruiken om de adapter te verwijderen
1. Gebruik een account met beheerdersrechten om u aan te melden bij de WFE-server (Web Front-End).
2. Dubbelklik op de StorSimple-adapter voor SharePoint-installatieprogramma. De wizard Instellen wordt gestart.
   
    ![Wizard Setup](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Klik op **Volgende**. De volgende pagina wordt weergegeven.
   
    ![Pagina Pagina Installatieverwijderen](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Klik **op Verwijderen** om het verwijderingsproces te selecteren. De volgende pagina wordt weergegeven.
   
    ![Bevestigingspagina van de wizard instellen](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Klik **op Verwijderen** om de verwijdering te bevestigen. De volgende voortgangspagina wordt weergegeven.
   
    ![Voortgangspagina van de wizard instellen](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. Wanneer de verwijdering is voltooid, wordt de finishpagina weergegeven. Klik **op Voltooien** om de wizard Setup te sluiten.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Het Configuratiescherm gebruiken om de adapter te verwijderen
1. Open het Configuratiescherm en klik op **Programma's en onderdelen**.
2. Selecteer **StorSimple-adapter voor SharePoint**en klik op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/library/ff943565.aspx
