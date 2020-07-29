---
title: Wat is Azure File Sync Cloud lagen? Microsoft Docs
description: Meer informatie over de functie voor Cloud lagen van Azure File Sync
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 23e98c40420a5f1ed9b048d5530eacfe5eedfb32
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85413974"
---
# <a name="cloud-tiering-overview"></a>Overzicht van Cloud lagen
Cloud lagen is een optionele functie van Azure File Sync waarbij veelgebruikte bestanden lokaal op de server worden opgeslagen in de cache, terwijl alle andere bestanden worden gelaagd op Azure Files op basis van beleids instellingen. Wanneer een bestand wordt getierd, wordt het bestand door de Azure File Sync File System filter (StorageSync.sys) lokaal vervangen door een aanwijzer of een reparsepunt. Het reparsepunt vertegenwoordigt een URL naar het bestand in Azure Files. Een gelaagd bestand heeft zowel het kenmerk ' offline ' als het kenmerk FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS in NTFS ingesteld, zodat toepassingen van derden veilig gelaagde bestanden kunnen identificeren.
 
Wanneer een gebruiker een gelaagd bestand opent, Azure File Sync de bestands gegevens naadloos terugroepen van Azure Files zonder dat de gebruiker moet weten dat het bestand is opgeslagen in Azure. 
 
 > [!Important]  
 > Cloud lagen worden niet ondersteund op het Windows-systeem volume.
    
 > [!Important]  
 > Als u bestanden wilt terughalen die zijn getierd, moet de netwerk bandbreedte ten minste 1 Mbps zijn. Als de netwerk bandbreedte kleiner is dan 1 Mbps, kunnen bestanden niet worden ingetrokken vanwege een time-outfout.

## <a name="cloud-tiering-faq"></a>Veelgestelde vragen over Cloud lagen

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Hoe werkt Cloud lagen?
Het Azure File Sync-systeem filter bouwt een ' heatmap ' van uw naam ruimte op elk server eindpunt. Het controleert de toegang (lees-en schrijf bewerkingen) in de loop van de tijd en wijst vervolgens, op basis van de frequentie en de Recency van toegang, een hitte Score toe aan elk bestand. Een veelgebruikt bestand dat onlangs is geopend, wordt beschouwd als hot, terwijl een bestand dat nauwelijks is gerakend en gedurende enige tijd niet toegankelijk is, als koud wordt beschouwd. Wanneer het bestands volume op een server de drempel waarde voor vrije ruimte van het volume overschrijdt dat u hebt ingesteld, worden de coole bestanden gelaagd tot Azure Files tot er aan het percentage van de vrije ruimte wordt voldaan.

Daarnaast kunt u een datum beleid opgeven op elk server eindpunt dat bestanden belaagt die niet toegankelijk zijn binnen een opgegeven aantal dagen, ongeacht de beschik bare lokale opslag capaciteit. Dit is een goede keuze voor het proactief vrijmaken van lokale schijf ruimte als u weet dat bestanden in dat Server-eind punt niet lokaal hoeven te worden bewaard buiten een bepaalde leeftijd. Dat beschik bare lokale schijf capaciteit voor andere eind punten op hetzelfde volume vrijmaakt, om meer bestanden in de cache op te slaan.

De heatmap in de Cloud is in feite een geordende lijst van alle bestanden die worden gesynchroniseerd en die zich bevinden op een locatie waarvoor Cloud lagen zijn ingeschakeld. Om de relatieve positie van een afzonderlijk bestand in die heatmap te bepalen, gebruikt het systeem het maximum van een van de volgende tijds tempels, in die volg orde: MAX (tijd van laatste toegang, tijd van laatste wijziging, aanmaak tijd). Normaal gesp roken wordt de tijd van laatste toegang bijgehouden en beschikbaar. Als er echter een nieuwe server-eind punt wordt gemaakt, terwijl Cloud lagen zijn ingeschakeld, is er niet voldoende tijd door gegeven om de toegang tot het bestand te observeren. Als er geen tijd van laatste toegang is, wordt het tijdstip waarop de tijd voor het laatst is gewijzigd, gebruikt om de relatieve positie in het heatmap te evalueren. Dezelfde terugval is van toepassing op het datum beleid. Zonder tijd van laatste toegang wordt het datum beleid toegepast op het tijdstip waarop het voor het laatst is gewijzigd. Als deze niet beschikbaar is, wordt deze terugvallen op de aanmaak tijd van een bestand. In de loop van de tijd worden er meer en meer aanvragen voor bestands toegang en draaiing uitgevoerd om de automatisch getraceerde tijd van laatste toegang te gebruiken.

Cloud lagen zijn niet afhankelijk van de NTFS-functie voor het bijhouden van de tijd van de laatste toegang. Deze NTFS-functie is standaard uitgeschakeld en vanwege prestatie overwegingen wordt u niet aangeraden deze functie hand matig in te scha kelen. Met Cloud lagen wordt de tijd van laatste toegang afzonderlijk en zeer efficiënt bijgehouden.

<a id="tiering-minimum-file-size"></a>
### <a name="what-is-the-minimum-file-size-for-a-file-to-tier"></a>Wat is de minimale bestands grootte voor een bestand dat moet worden gelaagd?

Voor Agent versie 9 en hoger is de minimale bestands grootte voor een bestand op laag gebaseerd op de grootte van het bestandssysteem cluster. In de volgende tabel ziet u de minimale bestands grootte die kan worden getierd op basis van de grootte van het volume cluster:

|Volume cluster grootte (bytes) |Bestanden van deze grootte of groter kunnen worden getierd  |
|----------------------------|---------|
|4 KB (4096)                 | 8 kB    |
|8 KB (8192)                 | 16 kB   |
|16 KB (16384)               | 32 KB   |
|32 KB (32768) en groter    | 64 kB   |

Alle bestands systemen die worden gebruikt door Windows, organiseren uw vaste schijf op basis van de cluster grootte (ook wel bekend als Allocation Unit Size). Cluster grootte vertegenwoordigt de kleinste hoeveelheid schijf ruimte die kan worden gebruikt om een bestand te bewaren. Wanneer de bestands grootte niet van een even veelvoud van de cluster grootte komt, moet er extra ruimte worden gebruikt om het bestand te bewaren (tot het volgende veelvoud van de cluster grootte).

Azure File Sync wordt ondersteund op NTFS-volumes met Windows Server 2012 R2 en nieuwer. De volgende tabel beschrijft de standaard cluster grootten wanneer u een nieuw NTFS-volume maakt. 

|Volume grootte    |Windows Server 2012R2 en hoger |
|---------------|---------------|
|7 MB – 16 TB   | 4 kB          |
|16TB – 32 TB   | 8 kB          |
|32 TB – 64 TB   | 16 kB         |
|64 TB – 128 TB  | 32 KB         |
|128TB – 256 TB | 64 kB         |
|> 256 TB       | Niet ondersteund |

Het is mogelijk dat u na het maken van het volume hand matig het volume met een andere grootte van een cluster (toewijzings eenheid) hebt geformatteerd. Als uw volume is afgeleid van een oudere versie van Windows, kunnen de standaard cluster grootten ook verschillen. [In dit artikel vindt u meer informatie over de standaard cluster groottes.](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat)

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Hoe werkt het opslaglaagbeleid voor beschikbare volumeruimte?
Beschik bare volume ruimte is de hoeveelheid vrije ruimte die u wilt reserveren op het volume waarop een server eindpunt zich bevindt. Als volume beschik bare ruimte bijvoorbeeld is ingesteld op 20% op een volume met één server eindpunt, wordt Maxi maal 80% van de volume ruimte ingen Omen door de meest recent geopende bestanden, waarbij alle resterende bestanden die niet in deze ruimte passen, naar Azure worden gelaagd. Volume beschik bare ruimte is van toepassing op volume niveau in plaats van op het niveau van afzonderlijke directory's of synchronisatie groepen. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Hoe werkt het opslaglaagbeleid voor beschikbare volumeruimte met betrekking tot de nieuwe servereindpunten?
Wanneer een server-eind punt nieuw is ingericht en verbonden met een Azure-bestands share, wordt door de server eerst de naam ruimte opgehaald en worden de daad werkelijke bestanden uitgelicht totdat de drempel waarde voor de vrije ruimte van het volume wordt bereikt. Dit proces wordt ook wel snelle herstel na nood gevallen of snelle naam ruimte herstel genoemd.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Hoe wordt vrije ruimte op een volume geïnterpreteerd wanneer ik meerdere servereindpunten heb op een volume?
Wanneer er meer dan één server eindpunt op een volume is, is de limiet voor de beschik bare volume ruimte het grootste volume dat is opgegeven voor elk server eindpunt op dat volume. Bestanden worden trapsgewijs gelaagd op basis van hun gebruiks patronen, ongeacht het server eindpunt waarvan ze deel uitmaken. Als u bijvoorbeeld twee server eindpunten hebt op een volume, Endpoint1 en Endpoint2, waarbij Endpoint1 een volume drempel waarde voor vrije ruimte heeft van 25% en Endpoint2 een volume drempel waarde voor vrije ruimte van 50% heeft, is de drempel waarde voor volume vrije ruimte voor beide server eindpunten 50%. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Hoe werkt het opslaglaagbeleid voor datums in combinatie met het opslaglaagbeleid voor beschikbare volumeruimte? 
Wanneer u Cloud Tiering inschakelt op een server eindpunt, stelt u een volume beschik bare ruimte beleid in. Het heeft altijd voor rang op elk ander beleid, inclusief het datum beleid. U kunt desgewenst een datum beleid inschakelen voor elk server eindpunt op dat volume. Dit beleid beheert dat alleen de bestanden die zijn geopend (dat wil zeggen, gelezen of beschreven) binnen het bereik van dagen dat dit beleid wordt beschreven, lokaal worden bewaard. Bestanden die niet toegankelijk zijn met het opgegeven aantal dagen, worden trapsgewijs gelaagd. 

Cloud lagen maakt gebruik van de tijd van laatste toegang om te bepalen welke bestanden moeten worden gelaagd. Het filter stuur programma voor Cloud lagen (storagesync.sys) traceert de tijd van laatste toegang en registreert de informatie in de opslag van de Cloud-laag. U kunt de opslag voor hitte bekijken met een lokale Power shell-cmdlet.

```powershell
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'
Get-StorageSyncHeatStoreInformation '<LocalServerEndpointPath>'
```

> [!IMPORTANT]
> De laatst geopende-time stamp is geen eigenschap die wordt bijgehouden door NTFS en daarom niet standaard zichtbaar is in Verkenner. Gebruik de tijds tempel van de laatste wijziging niet voor een bestand om te controleren of het datum beleid werkt zoals verwacht. Deze tijds tempel houdt alleen schrijf bewerkingen bij, niet lees bewerkingen. Gebruik de cmdlet die wordt weer gegeven om de laatst gebruikte time stamp voor deze evaluatie op te halen.

> [!WARNING]
> Schakel de NTFS-functie voor het bijhouden van de laatste toegang-tijds tempel voor bestanden en mappen niet in. Deze functie is standaard uitgeschakeld omdat deze een grote invloed heeft op de prestaties. Azure File Sync wordt automatisch en zeer efficiënt bijgehouden tijden en maakt geen gebruik van deze NTFS-functie.

Houd er rekening mee dat het volume beschik bare ruimte beleid altijd voor rang heeft, en wanneer er niet voldoende vrije ruimte op het volume is om zoveel dagen aan bestanden te bewaren, zoals wordt beschreven in het datum beleid, dan Azure File Sync de Coldest-bestanden blijven door lopen tot het percentage beschik bare ruimte aan het volume wordt bereikt.

Stel dat u een op een datum gebaseerd laag beleid hebt van 60 dagen en een volume beschik bare ruimte op basis van 20%. Als na het Toep assen van het datum beleid minder dan 20% van de beschik bare ruimte op het volume is, wordt het volume beschik bare ruimte beleid in en wordt het datum beleid overschreven. Dit leidt ertoe dat er meer bestanden worden getierd, zodat de hoeveelheid gegevens die op de server wordt bewaard, van 60 dagen aan gegevens kan worden teruggebracht tot 45 dagen. Dit beleid zorgt er ook voor dat de lagen die buiten uw tijds bereik vallen, worden afgehandeld, zelfs als u niet de drempel waarde voor vrije ruimte hebt bereikt. een bestand dat 61 dagen oud is, wordt getiereerd, zelfs als uw volume leeg is.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Hoe bepaal ik de juiste hoeveelheid beschikbare volumeruimte?
De hoeveelheid gegevens die u moet blijven lokale, wordt bepaald door enkele factoren: uw band breedte, het toegangs patroon van uw gegevensset en uw budget. Als u een verbinding met een lage band breedte hebt, wilt u mogelijk meer van uw gegevens lokaal houden om ervoor te zorgen dat uw gebruikers een minimale vertraging hebben. Als dat niet het geval is, kunt u deze baseren op het verloop tempo tijdens een bepaalde periode. Als u bijvoorbeeld weet dat ongeveer 10% van uw gegevensset van 1 TB wordt gewijzigd of elke maand actief wordt gebruikt, is het raadzaam om 100 GB lokaal te houden, zodat u niet vaak bestanden terugroept. Als uw volume 2 TB is, moet u 5% (of 100 GB) lokaal blijven, wat betekent dat de resterende 95% uw volume beschik bare ruimte percentage is. We raden u echter aan een buffer toe te voegen voor Peri Oden met een hoger verloop, met andere woorden, beginnend met een lager volume beschik bare ruimte percentage en het vervolgens aanpassen als dat later nodig is. 

Als u meer gegevens lokaal houdt, betekent dit lagere kosten voor uitgaand verkeer naarmate er minder bestanden worden ingetrokken van Azure, maar moet u ook een grotere hoeveelheid on-premises opslag onderhouden, die op eigen kosten wordt geleverd. Zodra u een exemplaar van Azure File Sync hebt geïmplementeerd, kunt u de uitvoer van uw opslag account op ongeveer meter bekijken, ongeacht of uw volume beschik bare ruimte-instellingen geschikt zijn voor uw gebruik. Ervan uitgaande dat het opslag account alleen uw Azure File Sync Cloud-eind punt bevat (dat wil zeggen, uw synchronisatie share), dan betekent hoge uitbrei ding dat veel bestanden worden ingetrokken vanuit de Cloud. u kunt overwegen uw lokale cache te verg Roten.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Ik heb een nieuw server eindpunt toegevoegd. Hoe lang duurt het tot mijn bestanden op deze server laag?

Hiermee wordt één keer per uur geëvalueerd of bestanden moeten worden getierd op basis van de ingestelde beleids regels. U kunt twee situaties tegen komen wanneer een nieuw server eindpunt wordt gemaakt:

1. Wanneer u een nieuw server eindpunt toevoegt, bestaan er vaak bestanden op die server locatie. Ze moeten eerst worden geüpload voordat cloud lagen kunnen beginnen. Het volume beschik bare ruimte beleid begint niet met zijn werk totdat de initiële upload van alle bestanden is voltooid. Het optionele datum beleid begint echter met het uitvoeren van een afzonderlijk bestand, zodra een bestand is geüpload. Het interval van één uur is hier ook van toepassing. 
2. Wanneer u een nieuw server eindpunt toevoegt, is het mogelijk dat u een lege server locatie in een Azure-bestands share verbindt met uw gegevens. Of dat voor een tweede server of tijdens een herstel na nood geval is. Als u ervoor kiest om de naam ruimte te downloaden en de inhoud op te halen tijdens de eerste down load naar uw server, worden de bestanden op basis van de laatst gewijzigd tijds tempel ingetrokken wanneer de naam ruimte niet beschikbaar is. Er worden slechts zoveel bestanden ingetrokken als binnen het volume beschik bare ruimte beleid en het optionele datum beleid.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Hoe kan ik zien of een bestand is getierd?
Er zijn verschillende manieren om te controleren of een bestand naar uw Azure-bestands share is getierd:
    
   *  **Controleer de bestands kenmerken van het bestand.**
     Klik met de rechter muisknop op een bestand, ga naar **Details**en schuif omlaag naar de eigenschap **Attributes** . Voor een gelaagd bestand zijn de volgende kenmerken ingesteld:     
        
        | Kenmerk letter | Kenmerk | Definitie |
        |:----------------:|-----------|------------|
        | A | Archiveren | Geeft aan dat er een back-up moet worden gemaakt van het bestand met back-upsoftware. Dit kenmerk is altijd ingesteld, ongeacht of het bestand op de schijf is gelaagd of volledig wordt opgeslagen. |
        | P | Sparse-bestand | Geeft aan dat het bestand een sparse-bestand is. Een sparse-bestand is een speciaal type bestand dat NTFS biedt voor efficiënt gebruik wanneer het bestand op de schijf stroom grotendeels leeg is. Azure File Sync maakt gebruik van verspreide bestanden omdat een bestand volledig is gelaagd of gedeeltelijk wordt ingetrokken. In een volledig gelaagd bestand wordt de bestands stroom opgeslagen in de Cloud. Het deel van het bestand bevindt zich al op schijf in een gedeeltelijk ingetrokken bestand. Als een bestand volledig wordt ingetrokken, wordt Azure File Sync geconverteerd van een sparse-bestand naar een gewoon bestand. Dit kenmerk wordt alleen ingesteld op Windows Server 2016 en ouder.|
        | M | Gegevens toegang intrekken | Geeft aan dat de gegevens van het bestand niet volledig aanwezig zijn in de lokale opslag. Als u het bestand leest, wordt er ten minste een deel van de bestands inhoud opgehaald van een Azure-bestands share waarop het server-eind punt is aangesloten. Dit kenmerk wordt alleen ingesteld op Windows Server 2019. |
        | L | Reparsepunt | Geeft aan dat het bestand een reparsepunt heeft. Een reparsepunt is een speciale verwijzing voor gebruik door een bestandssysteem filter. Azure File Sync maakt gebruik van reparsepunten om te definiëren of het Azure File Sync bestandssysteem filter (StorageSync.sys) de locatie van de Cloud waar het bestand is opgeslagen. Dit biedt ondersteuning voor naadloze toegang. Gebruikers hoeven niet te weten dat Azure File Sync wordt gebruikt of hoe ze toegang krijgen tot het bestand in de Azure-bestands share. Wanneer een bestand volledig wordt ingetrokken, verwijdert Azure File Sync het reparsepunt uit het bestand. |
        | O | Offline | Geeft aan dat sommige of alle inhoud van het bestand niet op de schijf worden opgeslagen. Wanneer een bestand volledig is ingetrokken, Azure File Sync dit kenmerk verwijdert. |

        ![Het dialoog venster Eigenschappen voor een bestand, met het tabblad Details geselecteerd](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        U kunt de kenmerken van alle bestanden in een map weer geven door het veld **kenmerken** toe te voegen aan de tabel weergave van bestanden Verkenner. Als u dit wilt doen, klikt u met de rechter muisknop op een bestaande kolom (bijvoorbeeld **grootte**), selecteert u **meer**en selecteert u vervolgens **kenmerken** in de vervolg keuzelijst.
        
   * **Gebruiken `fsutil` om te controleren op reparsepunten voor een bestand.**
       Zoals beschreven in de voor gaande optie, bevat een gelaagd bestand altijd een reparsepunt set. Een reparse-wijzer is een speciale verwijzing voor de Azure File Sync File System filter (StorageSync.sys). Voer het hulp programma uit om te controleren of een bestand een reparsepunt heeft, in een opdracht prompt met verhoogde bevoegdheid of een Power shell-venster `fsutil` :
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Als het bestand een reparsepunt heeft, kunt u de waarde van het **reparse-label zien: 0x8000001e**. Deze hexadecimale waarde is de reparsepunt waarde die het eigendom is van Azure File Sync. De uitvoer bevat ook de reparsegegevens die het pad naar het bestand op uw Azure-bestands share vertegenwoordigt.

        > [!WARNING]  
        > De `fsutil reparsepoint` opdracht voor het hulp programma heeft ook de mogelijkheid om een reparsepunt te verwijderen. Voer deze opdracht niet uit, tenzij het Azure File Sync technisch team u vraagt. Als u deze opdracht uitvoert, kan dit leiden tot gegevens verlies. 

<a id="afs-recall-file"></a>
### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>Een bestand dat ik wil gebruiken, is gelaagd. Hoe kan ik het bestand intrekken op schijf om het lokaal te gebruiken?
De eenvoudigste manier om een bestand in te trekken op schijf is door het bestand te openen. Het Azure File Sync bestandssysteem filter (StorageSync.sys) downloadt het bestand naadloos van uw Azure-bestands share zonder uw eigen werk. Voor bestands typen die gedeeltelijk kunnen worden gelezen uit, zoals multi media-of zip-bestanden, wordt het hele bestand niet gedownload door een bestand te openen.

U kunt Power shell ook gebruiken om te forceren dat een bestand wordt ingetrokken. Deze optie kan nuttig zijn als u meerdere bestanden tegelijk wilt intrekken, bijvoorbeeld alle bestanden in een map. Open een Power shell-sessie met het server knooppunt waar Azure File Sync is geïnstalleerd en voer vervolgens de volgende Power shell-opdrachten uit:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Optionele para meters:
* `-Order CloudTieringPolicy`roept eerst de meest recent gewijzigde of geopende bestanden aan en is toegestaan door het huidige laag beleid. 
    * Als het volume beschik bare ruimte beleid is geconfigureerd, worden de bestanden ingetrokken totdat de beleids instelling volume beschik bare ruimte is bereikt. Als de waarde voor het gratis volume beleid bijvoorbeeld 20% is, wordt intrekken gestopt zodra de beschik bare ruimte van het volume 20% bedraagt.  
    * Als volume beschik bare ruimte en datum beleid is geconfigureerd, worden de bestanden ingetrokken totdat de instelling volume beschik bare ruimte of datum beleid is bereikt. Als de instelling voor het volume gratis beleid bijvoorbeeld 20% is en het datum beleid 7 dagen is, wordt intrekken gestopt zodra de beschik bare ruimte van het volume 20% bedraagt of alle bestanden die worden geopend of gewijzigd binnen 7 dagen, lokaal zijn.
* `-ThreadCount`Hiermee wordt bepaald hoeveel bestanden parallel kunnen worden ingetrokken.
* `-PerFileRetryCount`bepaalt hoe vaak een terugroep bewerking wordt uitgevoerd voor een bestand dat momenteel is geblokkeerd.
* `-PerFileRetryDelaySeconds`bepaalt de tijd in seconden tussen nieuwe pogingen en moet altijd worden gebruikt in combi natie met de vorige para meter.

Voorbeeld:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
> - De cmdlet invoke-StorageSyncFileRecall kan ook worden gebruikt om de prestaties van het bestand te downloaden wanneer u een nieuw server eindpunt toevoegt aan een bestaande synchronisatie groep.  
>- Als het lokale volume dat als host fungeert voor de server onvoldoende beschik bare ruimte heeft om alle gelaagde gegevens in te trekken, `Invoke-StorageSyncFileRecall` mislukt de cmdlet.  

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Waarom wordt de eigenschap *grootte op schijf* voor een bestand niet overeenkomen met de eigenschap *grootte* na gebruik van Azure file sync? 
In Windows Verkenner worden twee eigenschappen beschikbaar gesteld om de grootte van een bestand weer te geven: **grootte** en **grootte op schijf**. Deze eigenschappen wijken in betekenis. **Grootte** vertegenwoordigt de volledige grootte van het bestand. **Grootte op schijf** staat voor de grootte van de bestands stroom die op de schijf is opgeslagen. De waarden voor deze eigenschappen kunnen verschillende oorzaken hebben, zoals compressie, het gebruik van Gegevensontdubbeling of Cloud lagen met Azure File Sync. Als een bestand wordt gelaagd voor een Azure-bestands share, is de grootte op de schijf nul, omdat de bestands stroom wordt opgeslagen in de Azure-bestands share en niet op de schijf. Het is ook mogelijk dat een bestand gedeeltelijk wordt gelaagd (of gedeeltelijk wordt ingetrokken). In een gedeeltelijk gelaagd bestand bevindt zich een deel van het bestand op schijf. Dit kan gebeuren wanneer bestanden gedeeltelijk worden gelezen door toepassingen als multimedia spelers of zip-hulpprogram ma's. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Hoe kan ik moet een bestand of map geforceerd worden getierd?

> [!NOTE]
> Wanneer u een map selecteert die u wilt gelaagd, worden alleen de bestanden die zich momenteel in de directory bevinden gelaagd gelaagd. Bestanden die zijn gemaakt na die tijd, worden niet automatisch gelaagd.

Wanneer de functie voor Cloud lagen is ingeschakeld, worden bestanden in Cloud lagen automatisch gelaagd op basis van de laatste toegangs-en wijzigings tijden om het percentage beschik bare ruimte op het Cloud eindpunt te verkrijgen. Soms wilt u een bestand op laag hand matig geforceerd afdwingen. Dit kan handig zijn als u een groot bestand opslaat dat u niet langer wilt gebruiken gedurende een lange periode en u de beschik bare ruimte op uw volume nu wilt gebruiken voor andere bestanden en mappen. U kunt de lagen afdwingen met behulp van de volgende Power shell-opdrachten:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

<a id="afs-image-thumbnail"></a>
### <a name="why-are-my-tiered-files-not-showing-thumbnails-or-previews-in-windows-explorer"></a>Waarom worden mijn gelaagde bestanden niet weer gegeven als miniaturen of voor beelden in Windows Verkenner?
Voor gelaagde bestanden zijn miniaturen en voor beelden niet zichtbaar op het server eindpunt. Dit gedrag wordt verwacht omdat de functie van de miniatuur cache in Windows het lezen van bestanden met het kenmerk offline overs Laan. Als Cloud lagen zijn ingeschakeld, kan de Lees bewerking door gelaagde bestanden worden gedownload (ingetrokken).

Dit gedrag is niet specifiek voor Azure File Sync. in Windows Verkenner wordt een ' grijze X ' weer gegeven voor bestanden waarvoor het kenmerk offline is ingesteld. Het pictogram X wordt weer geven bij het openen van bestanden via SMB. Raadpleeg voor een gedetailleerde uitleg van dit gedrag[https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)

<a id="afs-tiering-disabled"></a>
### <a name="i-have-cloud-tiering-disabled-why-are-there-tiered-files-in-the-server-endpoint-location"></a>Ik heb Cloud lagen uitgeschakeld, waarom zijn er gelaagde bestanden op de locatie van het server eindpunt?

Er zijn twee redenen waarom gelaagde bestanden zich op de locatie van het server eindpunt bevinden:

- Wanneer een nieuw server-eind punt aan een bestaande synchronisatie groep wordt toegevoegd, worden de meta gegevens eerst gesynchroniseerd met de server en worden de bestanden vervolgens op de achtergrond gedownload naar de server. De bestanden worden weer gegeven als gelaagd totdat ze lokaal worden gedownload. Als u de prestaties van het bestand wilt downloaden wanneer u een nieuwe server aan een synchronisatie groep toevoegt, gebruikt u de cmdlet [invoke-StorageSyncFileRecall](storage-sync-cloud-tiering.md#afs-recall-file) .

- Als Cloud lagen zijn ingeschakeld op het server eindpunt en vervolgens is uitgeschakeld, blijven de bestanden gelaagd totdat ze worden geopend.


## <a name="next-steps"></a>Volgende stappen
* [Een Azure File Sync-implementatie plannen](storage-sync-files-planning.md)
