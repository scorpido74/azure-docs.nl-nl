---
title: StorSimple 1200-migratie naar Azure File Sync
description: Meer informatie over het migreren van een virtueel toestel uit de StorSimple 1200-serie naar Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 69225da1506ced879363b10b098d939df93cbfba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502370"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>StorSimple 1200-migratie naar Azure File Sync

StorSimple 1200-serie is een virtueel apparaat dat wordt uitgevoerd in een on-premises datacenter. Het is mogelijk om de gegevens van dit toestel te migreren naar een Azure File Sync-omgeving. Azure File Sync is de standaard- en strategische Azure-service voor de lange termijn waarnaar StorSimple-apparaten kunnen worden gemigreerd.

De StorSimple 1200-serie bereikt in december 2022 zijn [einde van de levensduur.](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series)  Het is belangrijk om zo snel mogelijk te beginnen met het plannen van uw migratie. In dit artikel vindt u de nodige achtergrondkennis en migratiestappen voor een succesvolle migratie naar Azure File Sync. 

## <a name="azure-file-sync"></a>Azure File Sync

> [!IMPORTANT]
> Microsoft zet zich in om klanten te helpen bij hun migratie. E-mail AzureFilesMigration@microsoft .com voor een aangepast migratieplan en hulp tijdens de migratie.

Azure File Sync is een Microsoft-cloudservice, gebaseerd op twee hoofdcomponenten:

* Bestandssynchronisatie en cloudtiering.
* Bestandsshares als native storage in Azure, die toegankelijk is via meerdere protocollen zoals SMB en file REST. Een Azure-bestandsshare is vergelijkbaar met een bestandsshare op een Windows Server, die u native monteren als netwerkstation. Het ondersteunt belangrijke bestandsgetrouwheidsaspecten zoals kenmerken, machtigingen en tijdstempels. In tegenstelling tot StorSimple is er geen toepassing/service nodig om de bestanden en mappen die in de cloud zijn opgeslagen, te interpreteren. De ideale en meest flexibele aanpak om bestandsservergegevens voor algemene doeleinden op te slaan, evenals enkele toepassingsgegevens, in de cloud.

Dit artikel richt zich op de migratiestappen. Als u voor de migratie meer wilt weten over Azure File Sync, raden we de volgende artikelen aan:

* [Azure File Sync - overzicht](https://aka.ms/AFS "Overzicht")
* [Azure File Sync - implementatiehandleiding](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Migratiedoelen

Het doel is om de integriteit van de productiegegevens te garanderen en de beschikbaarheid te garanderen. Dit laatste vereist het tot een minimum beperken van downtime, zodat het in of slechts een beetje hoger kan zijn dan reguliere onderhoudsvensters.

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>StorSimple 1200-migratiepad naar Azure File Sync

Een lokale Windows Server is vereist om een Azure File Sync-agent uit te voeren. De Windows Server kan minimaal een 2012R2-server zijn, maar is idealiter een Windows Server 2019.

Er zijn tal van, alternatieve migratiepaden en het zou te lang van een artikel om ze allemaal te documenteren en illustreren waarom ze risico's of nadelen dragen over de route die we aanbevelen als een beste praktijk in dit artikel.

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="Migratieroute overzicht van de onderstaande stappen in dit artikel.":::

De vorige afbeelding toont stappen die overeenkomen met secties in dit artikel.

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>Stap 1: Uw on-premises Windows Server en opslag inrichten

1. Maak een Windows Server 2019 - minimaal 2012R2 - als virtuele machine of fysieke server. Een fail-overcluster van Windows Server wordt ook ondersteund.
2. Direct Attached Storage inrichten of toevoegen (DAS in vergelijking met NAS, die niet wordt ondersteund). De grootte van de Windows Server-opslag moet gelijk zijn aan of groter zijn dan de grootte van de beschikbare capaciteit van uw virtuele StorSimple 1200-toestel.

### <a name="step-2-configure-your-windows-server-storage"></a>Stap 2: Uw Windows Server-opslag configureren

In deze stap brengt u uw StorSimple-opslagstructuur (volumes en shares) in kaart met uw Windows Server-opslagstructuur.
Als u van plan bent wijzigingen aan te brengen in uw opslagstructuur, wat het aantal volumes, de koppeling van gegevensmappen in volumes of de submapstructuur boven of onder uw huidige SMB/NFS-shares betekent, dan is het nu tijd om met deze wijzigingen rekening te houden.
Het wijzigen van uw bestands- en mapstructuur nadat Azure File Sync is geconfigureerd, is omslachtig en moet worden vermeden.
In dit artikel wordt ervan uitgegaan dat u 1:1 in kaart brengt, dus u moet rekening houden met uw kaartwijzigingen wanneer u de stappen in dit artikel volgt.

* Geen van uw productiegegevens mag op het systeemvolume van Windows Server terechtkomen. Cloudtiering wordt niet ondersteund op systeemvolumes. Deze functie is echter vereist voor de migratie en continue bewerkingen als een StorSimple-vervanging.
* Informeer hetzelfde aantal volumes op uw Windows Server als op uw StorSimple 1200 virtuele toestel.
* Configureer alle Rollen, functies en instellingen van Windows Server die u nodig hebt. We raden u aan zich aan te melden voor Windows Server-updates om uw besturingssysteem veilig en up-to-date te houden. We raden u ook aan om u aan te melden voor Microsoft Update om Microsoft-toepassingen up-to-date te houden, inclusief de Azure File Sync-agent.
* Configureer geen mappen of shares voordat u de volgende stappen leest.

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>Stap 3: De eerste Azure File Sync-cloudbron implementeren

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>Stap 4: Uw lokale volume- en mapstructuur afstemmen op Azure File Sync en Azure-bronnen voor bestandsshare

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>Stap 5: Azure-bestandsshares inrichten

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>Stap 6: Doelmappen van Windows Server configureren

In eerdere stappen hebt u alle aspecten overwogen die de onderdelen van uw synchronisatietopologieën bepalen. Het is nu tijd om de server voor te bereiden om bestanden te ontvangen voor upload.

Maak **alle** mappen die elk met zijn eigen Azure-bestandsshare synchroniseren.
Het is belangrijk dat u de mapstructuur volgt die u eerder hebt gedocumenteerd. Als u bijvoorbeeld hebt besloten om meerdere lokale SMB-shares samen te synchroniseren met één Azure-bestandsshare, moet u deze onder een gemeenschappelijke hoofdmap op het volume plaatsen. Maak nu deze doelhoofdmap op het volume.

Het aantal Azure-bestandsshares dat u hebt ingericht, moet overeenkomen met het aantal mappen dat u in deze stap hebt gemaakt + het aantal volumes dat u op basisniveau synchroniseert.

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>Stap 7: De Azure File Sync-agent implementeren

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>Stap 8: Synchronisatie configureren

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Zorg ervoor dat u cloud tiering inschakelt!** Dit is vereist als uw lokale server niet genoeg ruimte heeft om de totale grootte van uw gegevens op te slaan in de StorSimple-cloudopslag. Stel uw gelaagdbeleid tijdelijk in voor de migratie op 99% volumevrije ruimte.

Herhaal de stappen van het maken van synchronisatiegroepen en de toevoeging van de overeenkomende servermap als een servereindpunt voor alle Azure-bestandsshares / serverlocaties, die moeten worden geconfigureerd voor synchronisatie.

### <a name="step-9-copy-your-files"></a>Stap 9: Uw bestanden kopiëren

De basismigratiebenadering is een RoboCopy van uw virtuele storSimple-toestel naar uw Windows Server en Azure File Sync naar Azure-bestandsshares.

Voer de eerste lokale kopie uit naar uw windows server-doelmap:

* Identificeer de eerste locatie op uw virtuele StorSimple-toestel.
* Identificeer de overeenkomende map op de Windows Server, waarop al Azure File Sync is geconfigureerd.
* De kopie starten met RoboCopy

Met de volgende opdracht RoboCopy worden bestanden uit uw StorSimple Azure-opslag teruggeroepen naar uw lokale StorSimple en vervolgens naar de doelmap van Windows Server verplaatst. De Windows Server synchroniseert deze met de Azure-bestandsshare(s). Als het lokale Windows Server-volume vol raakt, worden cloudlagen in- en laagbestanden opgenomen die al zijn gesynchroniseerd. Cloudtiering genereert voldoende ruimte om de kopie van het virtuele storSimple-toestel voort te zetten. Cloud tiering controleert eenmaal per uur om te zien wat er is gesynchroniseerd en om schijfruimte vrij te maken om de 99% volumevrije ruimte te bereiken.

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
      /UNILOG:<file name>
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
      Maakt het mogelijk om deze RoboCopy-opdracht meerdere keren uit te voeren, achtereenvolgens op hetzelfde doel / doel. Het identificeert wat eerder is gekopieerd en laat het weg. Alleen wijzigingen, toevoegingen en "*deletes*" worden verwerkt, die zich hebben voorgedaan sinds de laatste run. Als de opdracht niet eerder is uitgevoerd, wordt er niets weggelaten. Dit is een uitstekende optie voor bronlocaties die nog steeds actief worden gebruikt en gewijzigd.
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

Wanneer u de robocopy-opdracht voor de eerste keer uitvoert, hebben uw gebruikers en toepassingen nog steeds toegang tot de StorSimple-bestanden en -mappen en wijzigen deze mogelijk. Het is mogelijk, dat RoboCopy een directory heeft verwerkt, naar de volgende is gegaan en vervolgens een gebruiker op de bronlocatie (StorSimple) een bestand toevoegt, wijzigt of verwijdert dat nu niet wordt verwerkt in deze huidige RoboCopy-run. Dat is prima.

De eerste run gaat over het verplaatsen van het grootste deel van de gegevens terug naar on-premises, over naar uw Windows Server en back-up naar de cloud via Azure File Sync. Dit kan lang duren, afhankelijk van:

* uw downloadbandbreedte
* de terugroepsnelheid van de StorSimple-cloudservice
* de uploadbandbreedte
* het aantal items (bestanden en mappen), die moeten worden verwerkt door een van beide diensten

Zodra de eerste run is voltooid, voert u de opdracht opnieuw uit.

De tweede keer zal het sneller eindigen, omdat het alleen maar veranderingen hoeft te vervoeren die sinds de laatste run zijn gebeurd. Deze veranderingen zijn waarschijnlijk lokaal aan StorSimple reeds, omdat zij recent zijn. Dat is verder het verminderen van de tijd, omdat de behoefte aan terugroepen uit de cloud wordt verminderd. Tijdens deze tweede run kunnen zich nog steeds nieuwe veranderingen ophopen.

Herhaal dit proces totdat u ervan overtuigd bent dat de hoeveelheid tijd die nodig is om te voltooien een aanvaardbare downtime is.

Als u de downtime acceptabel vindt en u bereid bent om de StorSimple-locatie offline te halen, doe dit dan nu: Verwijder bijvoorbeeld het SMB-aandeel zodat geen enkele gebruiker toegang heeft tot de map of een andere passende stap neemt die voorkomt dat inhoud in deze map op StorSimple.

Voer nog een laatste RoboCopy-ronde uit. Dit zal pick-up eventuele wijzigingen, die kunnen zijn gemist.
Hoe lang deze laatste stap duurt, is afhankelijk van de snelheid van de RoboCopy-scan. U de tijd (die gelijk is aan uw downtime) schatten door te meten hoe lang de vorige run heeft geduurd.

Maak een aandeel in de Windows Server-map en pas eventueel uw DFS-N-implementatie aan om erop te wijzen. Zorg ervoor dat u dezelfde machtigingen op aandelenniveau instelt als op uw StorSimple SMB-aandeel.

U bent klaar met het migreren van een aandeel / groep aandelen naar een gemeenschappelijke basis of volume. (Afhankelijk van wat u in kaart hebt gebracht en hebt besloten dat nodig was om in dezelfde Azure-bestandsshare te gaan.)

U proberen om een paar van deze kopieën parallel uit te voeren. We raden u aan het bereik van één Azure-bestandsshare tegelijk te verwerken.

> [!WARNING]
> Zodra u alle gegevens van u StorSimple naar de Windows Server hebt verplaatst en uw migratie is voltooid: keer terug naar ***alle*** synchronisatiegroepen in de Azure-portal en pas de waarde van de volumevrije ruimte voor cloudlagen aan om iets dat beter geschikt is voor cachegebruik, bijvoorbeeld 20%. 

Het beleid voor cloudtiering-volumevrije ruimte werkt op volumeniveau met mogelijk meerdere servereindpunten die ermee worden gesynchroniseerd. Als u vergeet de vrije ruimte op zelfs één servereindpunt aan te passen, blijft synchronisatie de meest beperkende regel toepassen en probeert u 99% vrije schijfruimte te behouden, waardoor de lokale cache niet presteert zoals u zou verwachten. Tenzij het uw doel is om alleen de naamruimte te hebben voor een volume dat slechts zelden geopende archiefgegevens bevat.

## <a name="troubleshoot"></a>Problemen oplossen

Het meest waarschijnlijke probleem dat u tegenkomen, is dat de robocopy-opdracht mislukt met *'Volume vol'* aan de Windows Server-kant. Als dat het geval is, dan is uw downloadsnelheid waarschijnlijk beter dan uw uploadsnelheid. Cloudtiering werkt eenmaal per uur om inhoud te evacueren van de lokale Windows Server-schijf, die is gesynchroniseerd.

Laat voortgang synchroniseren en cloudtiering schijfruimte vrijmaken. Dat zie je in Verkenner op je Windows Server.

Wanneer uw Windows Server over voldoende beschikbare capaciteit beschikt, wordt het probleem opgelost door de opdracht opnieuw uit te voeren. Niets breekt als je in deze situatie en je verder gaan met vertrouwen. Ongemak van het uitvoeren van het commando opnieuw is het enige gevolg.

U ook andere Azure File Sync-problemen tegenkomen.
Hoe onwaarschijnlijk ze ook zijn, als dat gebeurt, kijk dan naar de handleiding voor het **oplossen van problemen met het oplossen van problemen met HET PROBLEEMMETAAL voor azure voor bestandssynchronisatie**.

## <a name="relevant-links"></a>Relevante links

Migratie-inhoud:

* [StorSimple 8000 serie migratiegids](storage-files-migration-storsimple-8000.md)

Inhoud voor azure-bestandssynchronisatie:

* [AFS-overzicht](https://aka.ms/AFS)
* [AFS-implementatiehandleiding](storage-files-deployment-guide.md)
* [AFS-probleemoplossing](storage-sync-files-troubleshoot.md)
