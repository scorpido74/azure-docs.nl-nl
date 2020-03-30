---
title: On-premises NAS-migratie naar Azure File Sync
description: Meer informatie over het migreren van bestanden van een on-premises NAS-locatie (Network Attached Storage) naar een hybride cloudimplementatie met Azure File Sync en Azure-bestandsshares.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7b0c7a30580d3863a78e85b8b45287a598bbf394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247347"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Migreren van NAS (Network Attached Storage) naar een hybride cloudimplementatie met Azure File Sync

Azure File Sync werkt op DAS-locaties (Direct Attached Storage) en biedt geen ondersteuning voor synchronisatie met NAS-locaties (Network Attached Storage).
Dit feit maakt een migratie van uw bestanden noodzakelijk en dit artikel begeleidt u door de planning en uitvoering van een dergelijke migratie.

## <a name="migration-goals"></a>Migratiedoelen

Het doel is om de aandelen die u op uw NAS-toestel hebt, te verplaatsen naar een Windows Server. Gebruik vervolgens Azure File Sync voor een hybride cloudimplementatie. Deze migratie moet gebeuren op een manier die de integriteit van de productiegegevens en de beschikbaarheid tijdens de migratie garandeert. Dit laatste vereist het tot een minimum beperken van downtime, zodat het in of slechts een beetje hoger kan zijn dan reguliere onderhoudsvensters.

## <a name="migration-overview"></a>Overzicht migratie

Zoals vermeld in het [overzichtsoverzicht azure](storage-files-migration-overview.md)files, is het belangrijk om het juiste kopieergereedschap en de juiste aanpak te gebruiken. Uw NAS-toestel stelt smb-aandelen rechtstreeks bloot op uw lokale netwerk. RoboCopy, ingebouwd in Windows Server, is de beste manier om uw bestanden te verplaatsen in dit migratiescenario.

- Fase 1: [bepalen hoeveel Azure-bestandsshares u nodig hebt](#phase-1-identify-how-many-azure-file-shares-you-need)
- Fase 2: [On-premises een geschikte Windows Server inrichten](#phase-2-provision-a-suitable-windows-server-on-premises)
- Fase 3: [De Azure File Sync-cloudbron implementeren](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- Fase 4: [Azure-opslagbronnen implementeren](#phase-4-deploy-azure-storage-resources)
- Fase 5: [De Azure File Sync-agent implementeren](#phase-5-deploy-the-azure-file-sync-agent)
- Fase 6: [Azure-bestandssynchronisatie configureren op de Windows Server](#phase-6-configure-azure-file-sync-on-the-windows-server)
- Fase 7: [RoboCopy](#phase-7-robocopy)
- Fase 8: [Gebruiker cut-over](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fase 1: bepalen hoeveel Azure-bestandsshares u nodig hebt

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>Fase 2: On-premises een geschikte Windows Server inrichten

* Maak een Windows Server 2019 - minimaal 2012R2 - als virtuele machine of fysieke server. Een fail-overcluster van Windows Server wordt ook ondersteund.
* Direct Attached Storage inrichten of toevoegen (DAS in vergelijking met NAS, die niet wordt ondersteund).

    De hoeveelheid opslagruimte die u indient, kan kleiner zijn dan wat u momenteel gebruikt op uw NAS-toestel, als u de functie Azure File Syncs [cloudtiering](storage-sync-cloud-tiering.md) gebruikt.
    Wanneer u uw bestanden echter in een latere fase kopieert van de grotere NAS-ruimte naar het kleinere Windows Server-volume, moet u in batches werken:

    1. Een set bestanden verplaatsen die op de schijf past
    2. bestandssynchronisatie en cloudtiering in gebruik laten nemen
    3. wanneer er meer vrije ruimte wordt gecreëerd op het volume, gaat u verder met de volgende batch bestanden. 
    
    U deze batching-benadering voorkomen door de equivalente ruimte op de Windows Server in te richten die uw bestanden innemen op het NAS-toestel. Overweeg deduplicatie op NAS / Windows. Als u deze hoge hoeveelheid opslagruimte niet permanent aan uw Windows Server wilt verbinden, u de volumegrootte na de migratie verkleinen en voordat u het beleid voor cloudlagen aanpast. Hierdoor wordt een kleinere on-premises cache van uw Azure-bestandsshares mogelijk.

De bronconfiguratie (rekenkracht en RAM) van de Windows Server die u implementeert, is grotendeels afhankelijk van het aantal items (bestanden en mappen) dat u synchroniseert. We raden u aan om met een hogere prestatieconfiguratie te gaan als u zich zorgen maakt.

[Meer informatie over het vergroten van een Windows Server op basis van het aantal items (bestanden en mappen) dat u moet synchroniseren.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> Het eerder gekoppelde artikel presenteert een tabel met een bereik voor servergeheugen (RAM). U zich oriënteren op het kleinere aantal voor uw server, maar verwachten dat de eerste synchronisatie aanzienlijk meer tijd in beslag kan nemen.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>Fase 3: De Azure File Sync-cloudbron implementeren

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>Fase 4: Azure-opslagbronnen implementeren

Raadpleeg in deze fase de toewijzingstabel van fase 1 en gebruikt deze om het juiste aantal Azure-opslagaccounts en bestandsshares in deze fase in te richten.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>Fase 5: De Azure File Sync-agent implementeren

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>Fase 6: Azure-bestandssynchronisatie configureren op de Windows Server

Uw geregistreerde on-premises Windows Server moet voor dit proces klaar zijn en verbonden zijn met internet.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Cloudtiering is de AFS-functie waarmee de lokale server minder opslagcapaciteit heeft dan in de cloud is opgeslagen, maar toch de volledige naamruimte beschikbaar heeft. Lokaal interessante gegevens worden ook lokaal opgeslagen voor snelle toegangsprestaties. Cloudtiering is een optionele functie per Azure File Sync "server eindpunt".

> [!WARNING]
> Als u minder opslagruimte op uw Windows-servervolume(s) hebt ingericht dan uw gegevens die op het NAS-toestel worden gebruikt, is cloudtiering verplicht. Als u cloudtiering niet inschakelt, maakt uw server geen ruimte vrij om alle bestanden op te slaan. Stel uw gelaagdbeleid tijdelijk in voor de migratie op 99% volumevrije ruimte. Zorg ervoor dat u terugkeert naar uw cloud-gelaagdheidinstellingen nadat de migratie is voltooid en stel deze in op een nuttiger niveau op lange termijn.

Herhaal de stappen van het maken van synchronisatiegroepen en de toevoeging van de overeenkomende servermap als een servereindpunt voor alle Azure-bestandsshares / serverlocaties, die moeten worden geconfigureerd voor synchronisatie.

Na het maken van alle servereindpunten werkt de synchronisatie. U een testbestand maken en het synchroniseren vanaf uw serverlocatie naar de verbonden Azure-bestandsshare (zoals beschreven door het cloudeindpunt in de synchronisatiegroep).

Beide locaties, de servermappen en de Azure-bestandsshares zijn anders leeg en wachten op gegevens op beide locaties. In de volgende stap begint u bestanden te kopiëren naar de Windows Server voor Azure File Sync om ze naar de cloud te verplaatsen. In het geval dat u cloudtiering hebt ingeschakeld, begint de server bestanden te tieren, mocht u geen capaciteit meer hebben op het lokale volume(en).

## <a name="phase-7-robocopy"></a>Fase 7: RoboCopy

De basismigratiebenadering is een RoboCopy van uw NAS-toestel naar uw Windows Server en Azure File Sync naar Azure-bestandsshares.

Voer de eerste lokale kopie uit naar uw windows server-doelmap:

* Identificeer de eerste locatie op uw NAS-toestel.
* Identificeer de overeenkomende map op de Windows Server, waarop al Azure File Sync is geconfigureerd.
* De kopie starten met RoboCopy

Met de volgende opdracht RoboCopy worden bestanden uit uw NAS-opslag naar uw doelmap van Windows Server gekopieerd. De Windows Server synchroniseert deze met de Azure-bestandsshare(s). 

Als u minder opslagruimte op uw Windows Server hebt ingericht dan uw bestanden op het NAS-toestel, hebt u cloudlagen geconfigureerd. Als het lokale Windows Server-volume vol raakt, worden [cloudlagen](storage-sync-cloud-tiering.md) in- en laagbestanden opgenomen die al zijn gesynchroniseerd. Cloudtiering genereert voldoende ruimte om de kopie van het NAS-toestel voort te zetten. Cloud tiering controleert eenmaal per uur om te zien wat er is gesynchroniseerd en om schijfruimte vrij te maken om de 99% volumevrije ruimte te bereiken.
Het is mogelijk dat RoboCopy bestanden sneller verplaatst dan u lokaal synchroniseren met de cloud en de laag, waardoor de lokale schijfruimte opraakt. RoboCopy zal mislukken. Het wordt aanbevolen dat u de aandelen in een reeks doorneemt die dat voorkomt. Bijvoorbeeld, niet het starten van RoboCopy-taken voor alle aandelen op hetzelfde moment, of alleen het verplaatsen van aandelen die passen op de huidige hoeveelheid vrije ruimte op de Windows Server, om er een paar te noemen.

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Achtergrond:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Hiermee kan RoboCopy multithread-reeksen uitvoeren. Standaard is 8, max is 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:\<bestandsnaam\>
   :::column-end:::
   :::column span="1":::
      Uitvoerstatus naar LOG-bestand als UNICODE (overschrijft bestaande logboek).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Uitgangen naar het consolevenster. Wordt gebruikt in combinatie met uitvoer naar een logboekbestand.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Draait RoboCopy in dezelfde modus die een back-uptoepassing zou gebruiken. Hiermee kan RoboCopy bestanden verplaatsen waar de huidige gebruiker geen machtigingen voor heeft.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Maakt het mogelijk om deze RoboCopy-opdracht meerdere keren uit te voeren, achtereenvolgens op hetzelfde doel / doel. Het identificeert wat eerder is gekopieerd en laat het weg. Alleen wijzigingen, toevoegingen en "*deletes*" worden verwerkt, die zich hebben voorgedaan sinds de laatste run. Als de opdracht niet eerder is uitgevoerd, wordt er niets weggelaten. De */MIR-vlag* is een uitstekende optie voor bronlocaties die nog steeds actief worden gebruikt en gewijzigd.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      getrouwheid van de bestandskopie (standaard is /COPY:DAT), copy flags: D=Data, A=Attributes, T=Timestamps, S=Security=NTFS ATFS' s, O=Owner info, U=aUditing info
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      / COPYALL
   :::column-end:::
   :::column span="1":::
      ALLE bestandsgegevens kopiëren (gelijk aan /COPY:DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      getrouwheid voor de kopie van mappen (standaard is /DCOPY:DA), copy flags: D=Data, A=Attributes, T=Timestamps
   :::column-end:::
:::row-end:::

## <a name="phase-8-user-cut-over"></a>Fase 8: Gebruiker cut-over

Wanneer u de RoboCopy-opdracht voor de eerste keer uitvoert, hebben uw gebruikers en toepassingen nog steeds toegang tot bestanden op de NAS en wijzigen ze mogelijk. Het is mogelijk dat RoboCopy een directory heeft verwerkt, naar de volgende is gegaan en vervolgens een gebruiker op de bronlocatie (NAS) een bestand toevoegt, wijzigt of verwijdert dat nu niet wordt verwerkt in deze huidige RoboCopy-run. Dit gedrag is verwacht.

De eerste run gaat over het verplaatsen van het grootste deel van de gegevens naar uw Windows Server en naar de cloud via Azure File Sync. Dit eerste exemplaar kan lang duren, afhankelijk van:

* uw downloadbandbreedte
* de uploadbandbreedte
* de lokale netwerksnelheid en het aantal hoe optimaal het aantal RoboCopy-threads overeenkomt
* het aantal items (bestanden en mappen), dat moet worden verwerkt door RoboCopy en Azure File Sync

Zodra de eerste run is voltooid, voert u de opdracht opnieuw uit.

De tweede keer zal het sneller eindigen, omdat het alleen maar veranderingen hoeft te vervoeren die sinds de laatste run zijn gebeurd. Tijdens deze tweede run kunnen zich nog steeds nieuwe veranderingen ophopen.

Herhaal dit proces totdat u ervan overtuigd bent dat de hoeveelheid tijd die nodig is om een RoboCopy voor een specifieke locatie te voltooien binnen een aanvaardbaar venster voor downtime ligt.

Wanneer u de downtime acceptabel vindt en u bereid bent om de NAS-locatie offline te halen: Om de gebruikerstoegang offline te halen, hebt u de mogelijkheid om ACL's op de shareroot te wijzigen, zodat gebruikers geen toegang meer hebben tot de locatie of een andere passende stap kunnen zetten dat voorkomt dat inhoud wordt gewijzigd in deze map op uw NAS.

Voer nog een laatste RoboCopy-ronde uit. Het zal pick-up eventuele wijzigingen, die kunnen zijn gemist.
Hoe lang deze laatste stap duurt, is afhankelijk van de snelheid van de RoboCopy-scan. U de tijd (die gelijk is aan uw downtime) schatten door te meten hoe lang de vorige run heeft geduurd.

Maak een aandeel in de Windows Server-map en pas eventueel uw DFS-N-implementatie aan om erop te wijzen. Zorg ervoor dat u dezelfde machtigingen op aandelenniveau instelt als op uw NAS MKB-aandeel. Als u een nas met een domein van bedrijfsklasse had, komen de ID's van de gebruiker automatisch overeen als de gebruikers in Active Directory bestaan en RoboCopy bestanden en metagegevens met volledige getrouwheid kopieert. Als u lokale gebruikers op uw NAS hebt gebruikt, moet u deze gebruikers opnieuw maken als lokale gebruikers van Windows Server en de bestaande ID's die RoboCopy naar uw Windows Server verplaatst, toewijzen aan de Id's van uw nieuwe lokale gebruikers van Windows Server.

U bent klaar met het migreren van een aandeel / groep aandelen naar een gemeenschappelijke basis of volume. (Afhankelijk van uw toewijzing van fase 1)

U proberen om een paar van deze kopieën parallel uit te voeren. We raden u aan het bereik van één Azure-bestandsshare tegelijk te verwerken.

> [!WARNING]
> Zodra u alle gegevens van u NAS naar de Windows Server hebt verplaatst en uw migratie is voltooid: Keer terug naar ***alle*** synchronisatiegroepen in de Azure-portal en pas de waarde van de volumevrije ruimte voor cloudlagen aan op iets dat beter geschikt is voor cachegebruik, bijvoorbeeld 20%. 

Het beleid voor cloudtiering-volumevrije ruimte werkt op volumeniveau met mogelijk meerdere servereindpunten die ermee worden gesynchroniseerd. Als u vergeet de vrije ruimte op zelfs één servereindpunt aan te passen, blijft synchronisatie de meest beperkende regel toepassen en probeert u 99% vrije schijfruimte te behouden, waardoor de lokale cache niet presteert zoals u zou verwachten. Tenzij het uw doel is om alleen de naamruimte te hebben voor een volume dat slechts zelden toegankelijk is, archiveringsgegevens en u de rest van de opslagruimte reserveert voor een ander scenario.

## <a name="troubleshoot"></a>Problemen oplossen

Het meest waarschijnlijke probleem dat u tegenkomen, is dat de robocopy-opdracht mislukt met *'Volume vol'* aan de Windows Server-kant. Cloudtiering werkt eenmaal per uur om inhoud te evacueren van de lokale Windows Server-schijf, die is gesynchroniseerd. Het doel is om uw 99% vrije ruimte op het volume te bereiken.

Laat voortgang synchroniseren en cloudtiering schijfruimte vrijmaken. Dat zie je in Verkenner op je Windows Server.

Wanneer uw Windows Server over voldoende beschikbare capaciteit beschikt, wordt het probleem opgelost door de opdracht opnieuw uit te voeren. Niets breekt als je in deze situatie en je verder gaan met vertrouwen. Ongemak van het uitvoeren van het commando opnieuw is het enige gevolg.

Controleer de koppeling in de volgende sectie voor het oplossen van Azure File Sync-problemen.

## <a name="next-steps"></a>Volgende stappen

Er is meer te ontdekken over Azure-bestandsshares en Azure File Sync. In de volgende artikelen u inzicht krijgen in geavanceerde opties, aanbevolen procedures en helpen bij het oplossen van problemen. Deze artikelen koppelen naar [Azure file share documentatie](storage-files-introduction.md) naar gelang van het geval.

* [AFS-overzicht](https://aka.ms/AFS)
* [AFS-implementatiehandleiding](storage-files-deployment-guide.md)
* [AFS-probleemoplossing](storage-sync-files-troubleshoot.md)
