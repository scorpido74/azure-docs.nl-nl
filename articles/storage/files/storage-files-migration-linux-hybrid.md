---
title: Linux-migratie naar Azure File Sync
description: Meer informatie over het migreren van bestanden van een locatie van een Linux-server naar een hybride Cloud implementatie met Azure File Sync en Azure-bestands shares.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: c80f43d61aeea8aba803267b7908831209812d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247715"
---
# <a name="migrate-from-linux-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Migreren van Linux naar een hybride Cloud implementatie met Azure File Sync

Azure File Sync werkt op Windows-servers met Direct Attached Storage (DAS). De functie biedt geen ondersteuning voor synchronisatie van en naar Linux of een externe SMB-share.
Als gevolg hiervan is het transformeren van uw bestands Services naar een hybride implementatie een migratie naar een Windows-Server nodig. Dit artikel begeleidt u bij het plannen en uitvoeren van een dergelijke migratie.

## <a name="migration-goals"></a>Migratiedoelen

Het doel is om de shares die u op uw Linux Samba-server hebt geplaatst, te verplaatsen naar een Windows-Server. Gebruik vervolgens Azure File Sync voor een hybride Cloud implementatie. Deze migratie moet zo worden uitgevoerd dat de integriteit van de productie gegevens en de beschik baarheid tijdens de migratie gewaarborgd is. Ten laatste moet de downtime tot een minimum worden beperkt, zodat deze kan worden aangepast aan of slechts een beetje meer regel matig onderhouds Vensters kan hebben.

## <a name="migration-overview"></a>Overzicht van Migratie

Zoals vermeld in het [overzichts artikel](storage-files-migration-overview.md)over Azure files migratie, met het juiste Kopieer programma en de aanpak is belang rijk. Uw Linux Samba-server maakt SMB-shares rechtstreeks beschikbaar op uw lokale netwerk. RoboCopy, ingebouwde Windows Server, is de beste manier om uw bestanden in dit migratie scenario te verplaatsen.

Als u samba niet uitvoert op uw Linux-server en liever mappen wilt migreren naar een hybride implementatie op een Windows-Server, kunt u Linux-Kopieer hulpprogramma's gebruiken in plaats van RoboCopy. Als u dit doet, moet u rekening houden met de betrouw baarheid van uw hulp programma voor het kopiëren van bestanden. Raadpleeg de [sectie basis beginselen](storage-files-migration-overview.md#migration-basics) van migratie in het artikel migratie overzicht voor meer informatie over wat u moet zoeken in een kopieer programma.

- Fase 1: [bepalen hoeveel Azure-bestands shares u nodig hebt](#phase-1-identify-how-many-azure-file-shares-you-need)
- Fase 2: [een geschikte Windows Server on-premises inrichten](#phase-2-provision-a-suitable-windows-server-on-premises)
- Fase 3: [de Azure file sync Cloud resource implementeren](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- Fase 4: [Azure storage-resources implementeren](#phase-4-deploy-azure-storage-resources)
- Fase 5: [de Azure file sync-agent implementeren](#phase-5-deploy-the-azure-file-sync-agent)
- Fase 6: [Azure file sync configureren op de Windows-Server](#phase-6-configure-azure-file-sync-on-the-windows-server)
- Fase 7: [Robocopy](#phase-7-robocopy)
- Fase 8: [gebruikers knippen](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fase 1: bepalen hoeveel Azure-bestands shares u nodig hebt

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>Fase 2: een geschikte Windows Server on-premises inrichten

* Maak een Windows Server 2019-met een minimale 2012R2-als een virtuele machine of fysieke server. Een failover-cluster van Windows Server wordt ook ondersteund.
* Het inrichten of toevoegen van direct gekoppelde opslag (DAS in vergelijking met NAS), wat niet wordt ondersteund.

    De hoeveelheid opslag ruimte die u hebt ingericht, kan kleiner zijn dan wat u momenteel gebruikt op uw Linux Samba-server, als u Azure-bestands synchronisaties functie voor [Cloud lagen](storage-sync-cloud-tiering.md) gebruikt.
    Wanneer u echter uw bestanden vanuit de grotere Linux Samba-server ruimte naar het kleinere Windows Server-volume in een latere fase kopieert, moet u in batches werken:

    1. Verplaats een reeks bestanden die op de schijf passen.
    2. Bestands synchronisatie en Cloud lagen laten deel nemen.
    3. Wanneer er meer vrije ruimte op het volume wordt gemaakt, gaat u door met de volgende batch bestanden. 
    
    U kunt deze methode voor batch verwerking vermijden door de gelijkwaardige ruimte op de Windows-Server in te richten die uw bestanden op de Linux Samba-server innemen. Overweeg om ontdubbeling in Windows in te scha kelen. Als u deze hoge hoeveelheid opslag ruimte niet permanent wilt door voeren naar uw Windows-Server, kunt u de volume grootte na de migratie verkleinen en voordat u het beleid voor Cloud lagen hebt aangepast. Hiermee maakt u een kleinere on-premises cache van uw Azure-bestands shares.

De resource configuratie (reken kracht en RAM) van de Windows-Server die u implementeert, is vooral afhankelijk van het aantal items (bestanden en mappen) dat u wilt synchroniseren. Als u problemen hebt, kunt u het beste een configuratie met hogere prestaties uitvoeren.

[Meer informatie over het aanpassen van de grootte van een Windows-Server op basis van het aantal items (bestanden en mappen) dat u wilt synchroniseren.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> Het eerder gekoppelde artikel bevat een tabel met een bereik voor Server geheugen (RAM). U kunt naar het kleinere nummer voor uw server richten, maar u kunt verwachten dat initiële synchronisatie aanzienlijk meer tijd kan duren.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>Fase 3: de Azure File Sync Cloud resource implementeren

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>Fase 4: Azure storage-resources implementeren

In deze fase raadpleegt u de toewijzings tabel uit fase 1 en gebruikt u deze om het juiste aantal Azure-opslag accounts en bestands shares in te richten.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>Fase 5: de Azure File Sync-agent implementeren

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>Fase 6: Azure File Sync configureren op de Windows-Server

Uw geregistreerde on-premises Windows-Server moet gereed zijn en verbonden zijn met internet voor dit proces.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Cloud lagen is de AFS-functie waarmee de lokale server minder opslag capaciteit kan hebben dan is opgeslagen in de Cloud, maar die de volledige naam ruimte beschikbaar heeft. Lokaal interessante gegevens worden ook lokaal in de cache opgeslagen voor snelle toegang tot de prestaties. Cloud lagen is een optionele functie per Azure File Sync server-eind punt.

> [!WARNING]
> Als u minder opslag ruimte op uw Windows Server-volume (s) hebt ingericht dan uw gegevens die op de Linux Samba-server worden gebruikt, is Cloud lagen verplicht. Als u Cloud lagen niet inschakelt, maakt de server geen ruimte vrij om alle bestanden op te slaan. Stel het beleid voor lagen tijdelijk in voor de migratie tot 99% beschik bare ruimte op het volume. Ga terug naar de instellingen voor de Cloud lagen nadat de migratie is voltooid en stel deze in op een meer lange termijn handig niveau.

Herhaal de stappen voor het maken van de synchronisatie groep en het toevoegen van de overeenkomende servermap als server-eind punt voor alle Azure-bestands shares/server locaties die moeten worden geconfigureerd voor synchronisatie.

Na het maken van alle server eindpunten werkt synchronisatie. U kunt een test bestand maken en het synchroniseren vanuit uw server locatie naar de verbonden Azure-bestands share (zoals beschreven in het Cloud-eind punt in de synchronisatie groep).

Beide locaties, de Server mappen en de Azure-bestands shares zijn op een andere locatie leeg en wachten op gegevens. In de volgende stap gaat u bestanden kopiëren naar de Windows-Server voor Azure File Sync om ze naar de cloud te verplaatsen. Als u Cloud lagen hebt ingeschakeld, begint de server vervolgens met het trapsgewijs delen van bestanden. Als u geen capaciteit meer hebt op de lokale volume (s).

## <a name="phase-7-robocopy"></a>Fase 7: RoboCopy

De basis benadering voor migratie is een RoboCopy van uw Linux Samba-server naar uw Windows-Server en Azure File Sync naar Azure-bestands shares.

Voer de eerste lokale kopie uit naar de doelmap van Windows Server:

1. Bepaal de eerste locatie op de Linux Samba-server.
1. Identificeer de overeenkomende map op de Windows-Server, waarop al Azure File Sync zijn geconfigureerd.
1. Start de kopie met behulp van RoboCopy.

Met de volgende RoboCopy-opdracht worden bestanden van uw Linux Samba-servers opslag gekopieerd naar de doelmap van uw Windows-Server. De Windows-Server synchroniseert deze met de Azure-bestands share (s). 

Als u minder opslag ruimte op uw Windows-Server hebt ingericht dan uw bestanden op de Linux Samba-server innemen, hebt u Cloud lagen geconfigureerd. Omdat het lokale Windows Server-volume vol is, worden er [Cloud lagen](storage-sync-cloud-tiering.md) in gemaakt en lagen die al zijn gesynchroniseerd. Cloud-lagen genereren voldoende ruimte om de kopie van de Linux Samba-server voort te zetten. Controles voor Cloud lagen worden één keer per uur uitgevoerd om te zien wat er is gesynchroniseerd en om schijf ruimte vrij te maken voor het bereiken van de beschik bare ruimte van 99% volume.
Het is mogelijk dat RoboCopy bestanden sneller verplaatst dan u naar de Cloud en laag lokaal kunt synchroniseren, waardoor er geen lokale schijf ruimte meer beschikbaar is. RoboCopy mislukt. Het wordt aanbevolen dat u de shares in een volg orde doorloopt, waardoor dat niet mogelijk is. U kunt bijvoorbeeld geen RoboCopy-taken voor alle shares tegelijk starten of alleen shares verplaatsen die passen bij de huidige hoeveelheid beschik bare ruimte op de Windows-Server, om een paar te vermelden.

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Achtergrondbitmap

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Hiermee kan RoboCopy meerdere threads uitvoeren. De standaard waarde is 8, maximum is 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:\<bestands naam\>
   :::column-end:::
   :::column span="1":::
      Hiermee wordt de status van de uitvoer naar een logboek bestand opgeslagen als UNICODE (het bestaande logboek wordt overschreven).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Uitvoer naar console venster. Wordt gebruikt in combi natie met uitvoer naar een logboek bestand.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Voert RoboCopy uit in dezelfde modus als een back-uptoepassing zou gebruiken. Hiermee kan RoboCopy bestanden verplaatsen waarnaar de huidige gebruiker geen machtigingen heeft.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Hiermee kan deze RoboCopy-opdracht verschillende keren worden uitgevoerd, opeenvolgend op hetzelfde doel/dezelfde bestemming. Hiermee wordt aangegeven wat er eerder is gekopieerd en wordt deze wegge laten. Alleen wijzigingen, toevoegingen en '*verwijderingen*' worden verwerkt, die zijn opgetreden sinds de laatste uitvoering. Als de opdracht nog niet eerder is uitgevoerd, wordt er niets wegge laten. De vlag */Mir* is een uitstekende optie voor bron locaties die nog steeds worden gebruikt en worden gewijzigd.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      betrouw baarheid van het bestand kopiëren (standaard is/COPY: DAT), kopieer vlaggen: D = gegevens, A = kenmerken, T = tijds tempels, S = Security = NTFS Acl's, O = eigenaargegevens, U = controle-informatie
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      / COPYALL
   :::column-end:::
   :::column span="1":::
      ALLE bestands gegevens kopiëren (gelijk aan/COPY: DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      betrouw baarheid voor het kopiëren van mappen (standaard is/DCOPY: DA), kopieer vlaggen: D = gegevens, A = kenmerken, T = tijds tempels
   :::column-end:::
:::row-end:::

## <a name="phase-8-user-cut-over"></a>Fase 8: gebruikers knippen

Wanneer u de RoboCopy-opdracht voor de eerste keer uitvoert, hebben uw gebruikers en toepassingen nog steeds toegang tot bestanden op de Linux Samba-server en kunnen ze worden gewijzigd. Het is mogelijk dat RoboCopy een map heeft verwerkt, naar de volgende gaat en vervolgens een gebruiker op de bron locatie (Linux) een bestand toevoegt, wijzigt of verwijdert dat nu niet wordt verwerkt in deze huidige RoboCopy-uitvoering. Dit gedrag is verwacht.

De eerste keer dat u het meren deel van de gegevens naar uw Windows-Server en naar de Cloud verplaatst via Azure File Sync. Het eerste exemplaar kan enige tijd in beslag nemen, afhankelijk van:

* De Download bandbreedte.
* De upload bandbreedte.
* De snelheid van het lokale netwerk en het aantal hoe optimaal het aantal RoboCopy-threads overeenkomt met het.
* Het aantal items (bestanden en mappen) dat moet worden verwerkt door RoboCopy en Azure File Sync.

Zodra de eerste uitvoering is voltooid, voert u de opdracht opnieuw uit.

De tweede keer dat deze sneller wordt voltooid, hoeft alleen wijzigingen te worden transporteren die sinds de laatste uitvoering zijn opgetreden. Tijdens deze tweede uitvoering kunnen er nog steeds nieuwe wijzigingen worden verzameld.

Herhaal dit proces totdat u tevreden bent over de hoeveelheid tijd die nodig is voor het volt ooien van een RoboCopy voor een specifieke locatie binnen een aanvaardbaar venster voor uitval tijd.

Wanneer u de beschik bare downtime in overweging neemt en u bent voor bereid de Linux-locatie offline te halen: als u de gebruikers toegang offline wilt maken, kunt u de Acl's op de hoofdmap van de share wijzigen, zodat gebruikers geen toegang meer hebben tot de locatie of een andere geschikte stap kunnen ondernemen die voor komt dat inhoud in deze map op uw Linux-server kan worden gewijzigd.

Voer een laatste RoboCopy-ronde uit. Er worden wijzigingen opgehaald, die mogelijk zijn gemist.
Hoe lang deze laatste stap duurt, is afhankelijk van de snelheid van de RoboCopy-scan. U kunt een schatting maken van de tijd (die gelijk is aan uw downtime) door te meten hoe lang de vorige uitvoering heeft geduurd.

Maak een share op de Windows Server-map en stel eventueel uw DFS-N-implementatie zodanig in dat deze ernaar verwijst. Zorg ervoor dat u dezelfde machtigingen op share niveau hebt ingesteld als voor de SMB-shares van de Linux Samba-server. Als u lokale gebruikers hebt gebruikt op uw Linux Samba-server, moet u deze gebruikers opnieuw maken als lokale gebruikers van Windows Server en de bestaande Sid's van de nieuwe Windows Server-server naar de Sid's van uw nieuw, lokale gebruikers van micro Point verplaatsen. Als u AD-accounts en-Acl's hebt gebruikt, worden deze als zodanig door RoboCopy verplaatst en is er geen verdere actie nodig.

U bent klaar met het migreren van een share/groep shares naar een gemeen schappelijke hoofdmap of volume. (Afhankelijk van uw toewijzing vanuit fase 1)

U kunt proberen enkele van deze kopieën parallel uit te voeren. Het is raadzaam om het bereik van een Azure-bestands share tegelijk te verwerken.

> [!WARNING]
> Wanneer u alle gegevens van de Linux Samba-server naar de Windows-Server hebt verplaatst en uw migratie is voltooid, gaat u terug naar ***alle*** synchronisatie groepen in het Azure Portal en past u de waarde voor het percentage beschik bare ruimte in de Cloud-laag aan om iets beter geschikt te maken voor het gebruik van de cache, bijvoorbeeld 20%. 

Het beleid voor beschik bare ruimte op het niveau van de Cloud-laag is een volume dat mogelijk meerdere server eindpunten synchroniseert. Als u vergeet de beschik bare ruimte op zelfs één server eindpunt aan te passen, blijft synchronisatie de meest beperkende regel Toep assen en wordt geprobeerd om 99% vrije schijf ruimte te houden, waardoor de lokale cache niet wordt uitgevoerd zoals u zou verwachten. Tenzij het uw doel is om alleen de naam ruimte te hebben voor een volume dat slechts zelden wordt gebruikt, worden de archiverings gegevens opgeslagen en moet u de rest van de opslag ruimte voor een ander scenario reserveren.

## <a name="troubleshoot"></a>Problemen oplossen

Het meest waarschijnlijke probleem dat u kunt uitvoeren in, is dat de RoboCopy-opdracht mislukt met *' volume vol '* aan de Windows Server-zijde. Cloud lagen reageert eenmaal elk uur om inhoud te verlaten van de lokale Windows Server-schijf, die is gesynchroniseerd. Het doel is om uw 99% beschik bare ruimte op het volume te bereiken.

Laat de voortgang van de synchronisatie en Cloud lagen vrij schijf ruimte vrijmaken. U ziet dat in Verkenner op uw Windows-Server.

Als uw Windows-Server voldoende beschik bare capaciteit heeft, wordt het probleem opgelost door de opdracht opnieuw uit te voeren. Er zijn geen onderbrekingen wanneer u deze situatie krijgt en u kunt door gaan met vertrouwen. Het ongemak van het uitvoeren van de opdracht is het enige gevolg.

Controleer de koppeling in de volgende sectie voor het oplossen van problemen met Azure File Sync.

## <a name="next-steps"></a>Volgende stappen

Er is meer informatie over Azure-bestands shares en Azure File Sync. De volgende artikelen helpen u bij het begrijpen van geavanceerde opties, aanbevolen procedures en ook voor het oplossen van problemen. Deze artikelen maken een koppeling naar de [documentatie van Azure file share](storage-files-introduction.md) .

* [Overzicht van AFS](https://aka.ms/AFS)
* [AFS-implementatie handleiding](storage-files-deployment-guide.md)
* [Problemen met AFS oplossen](storage-sync-files-troubleshoot.md)
