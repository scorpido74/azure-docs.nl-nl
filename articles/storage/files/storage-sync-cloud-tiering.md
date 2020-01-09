---
title: Wat is Azure File Sync Cloud lagen? Microsoft Docs
description: Meer informatie over de functie voor Cloud lagen van Azure File Sync
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 483f13f89acd1bce0ceb8486ac252e6f844d881f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431739"
---
# <a name="cloud-tiering-overview"></a>Overzicht van Cloud lagen
Cloud lagen is een optionele functie van Azure File Sync waarbij veelgebruikte bestanden lokaal op de server worden opgeslagen in de cache, terwijl alle andere bestanden worden gelaagd op Azure Files op basis van beleids instellingen. Wanneer een bestand wordt getierd, wordt het bestand met de Azure File Sync bestandssysteem filter (StorageSync. sys) vervangen door een aanwijzer of een reparsepunt. Het reparsepunt vertegenwoordigt een URL naar het bestand in Azure Files. Een gelaagd bestand heeft zowel het kenmerk ' offline ' als het kenmerk FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS in NTFS ingesteld, zodat toepassingen van derden veilig gelaagde bestanden kunnen identificeren.
 
Wanneer een gebruiker een gelaagd bestand opent, Azure File Sync de bestands gegevens naadloos terugroepen van Azure Files zonder dat de gebruiker moet weten dat het bestand daad werkelijk is opgeslagen in Azure. 
 
 > [!Important]  
 > Cloud lagen worden niet ondersteund voor Server eindpunten op de Windows-systeem volumes en alleen bestanden met een grootte van meer dan 64 KiB kunnen worden getierd naar Azure Files.
    
Azure File Sync biedt geen ondersteuning voor het laag maken van bestanden die kleiner zijn dan 64 KiB omdat de prestatie overhead van het trapsgewijs scha kelen van lagen en het terugtrekken van dergelijke kleine bestanden niet groter wordt dan de ruimte besparing.

 > [!Important]  
 > Als u bestanden wilt terughalen die zijn getierd, moet de netwerk bandbreedte ten minste 1 Mbps zijn. Als de netwerk bandbreedte kleiner is dan 1 Mbps, kunnen bestanden niet worden ingetrokken vanwege een time-outfout.

## <a name="cloud-tiering-faq"></a>Veelgestelde vragen over Cloud lagen

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Hoe werkt Cloud lagen?
Het Azure File Sync-systeem filter bouwt een ' heatmap ' van uw naam ruimte op elk server eindpunt. Het controleert de toegang (lees-en schrijf bewerkingen) in de loop van de tijd en wijst vervolgens, op basis van de frequentie en de Recency van toegang, een hitte Score toe aan elk bestand. Een veelgebruikt bestand dat onlangs is geopend, wordt beschouwd als hot, terwijl een bestand dat nauwelijks is gerakend en gedurende enige tijd niet toegankelijk is, als koud wordt beschouwd. Wanneer het bestands volume op een server de drempel waarde voor vrije ruimte van het volume overschrijdt dat u hebt ingesteld, worden de coole bestanden gelaagd tot Azure Files tot er aan het percentage van de vrije ruimte wordt voldaan.

In versie 4,0 en hoger van de Azure File Sync-agent kunt u ook een datum beleid opgeven op elk server eindpunt dat bestanden die niet worden geopend of gewijzigd binnen een opgegeven aantal dagen worden getierd.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Hoe werkt het volume beschik bare ruimte voor volumes?
Beschik bare volume ruimte is de hoeveelheid vrije ruimte die u wilt reserveren op het volume waarop een server eindpunt zich bevindt. Als volume beschik bare ruimte bijvoorbeeld is ingesteld op 20% op een volume met één server eindpunt, wordt Maxi maal 80% van de volume ruimte ingen Omen door de meest recent geopende bestanden, waarbij alle resterende bestanden die niet in deze ruimte passen, naar Azure worden gelaagd. Volume beschik bare ruimte is van toepassing op volume niveau in plaats van op het niveau van afzonderlijke directory's of synchronisatie groepen. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Wat is het gebruik van het volume beschik bare ruimte voor volumes met betrekking tot nieuwe server eindpunten?
Wanneer een server-eind punt nieuw is ingericht en verbonden met een Azure-bestands share, wordt door de server eerst de naam ruimte opgehaald en worden de daad werkelijke bestanden uitgelicht totdat de drempel waarde voor de vrije ruimte van het volume wordt bereikt. Dit proces wordt ook wel snelle herstel na nood gevallen of snelle naam ruimte herstel genoemd.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Hoe wordt de beschik bare volume ruimte geïnterpreteerd wanneer ik meerdere server eindpunten op een volume heb?
Wanneer er meer dan één server eindpunt op een volume is, is de limiet voor de beschik bare volume ruimte het grootste volume dat is opgegeven voor elk server eindpunt op dat volume. Bestanden worden trapsgewijs gelaagd op basis van hun gebruiks patronen, ongeacht het server eindpunt waarvan ze deel uitmaken. Als u bijvoorbeeld twee server eindpunten hebt op een volume, Endpoint1 en Endpoint2, waarbij Endpoint1 een volume drempel waarde voor vrije ruimte heeft van 25% en Endpoint2 een volume drempel waarde voor vrije ruimte van 50% heeft, is de drempel waarde voor volume vrije ruimte voor beide server eindpunten 50%. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Hoe werkt het beleid voor de datum lagen in combi natie met het volume beschik bare ruimte-laag beleid? 
Wanneer u Cloud Tiering inschakelt op een server eindpunt, stelt u een volume beschik bare ruimte beleid in. Het heeft altijd voor rang op elk ander beleid, inclusief het datum beleid. U kunt desgewenst een datum beleid inschakelen voor elk server eindpunt op dat volume, wat betekent dat alleen bestanden die toegankelijk zijn (dat wil zeggen, gelezen of beschreven) binnen het bereik van de dagen dat dit beleid wordt beschreven, lokaal worden bewaard, met eventuele verouderde bestanden. Houd er rekening mee dat het volume beschik bare ruimte beleid altijd voor rang heeft en wanneer er niet voldoende vrije ruimte op het volume is om zoveel dagen aan bestanden te bewaren, zoals wordt beschreven in het datum beleid, Azure File Sync de Coldest-bestanden blijven door lopen totdat het volume vrij is Er wordt aan het ruimte percentage voldaan.

Stel dat u een op een datum gebaseerd laag beleid hebt van 60 dagen en een volume beschik bare ruimte op basis van 20%. Als na het Toep assen van het datum beleid minder dan 20% van de beschik bare ruimte op het volume is, wordt het volume beschik bare ruimte beleid in en wordt het datum beleid overschreven. Dit leidt ertoe dat er meer bestanden worden getierd, zodat de hoeveelheid gegevens die op de server wordt bewaard, van 60 dagen aan gegevens kan worden teruggebracht tot 45 dagen. Dit beleid zorgt er ook voor dat de lagen die buiten uw tijds bereik vallen, worden afgehandeld, zelfs als u niet de drempel waarde voor vrije ruimte hebt bereikt. een bestand dat 61 dagen oud is, wordt getiereerd, zelfs als uw volume leeg is.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Hoe kan ik de juiste hoeveelheid beschik bare volume ruimte bepalen?
De hoeveelheid gegevens die u moet blijven lokale, wordt bepaald door enkele factoren: uw band breedte, het toegangs patroon van uw gegevensset en uw budget. Als u een verbinding met een lage band breedte hebt, wilt u mogelijk meer van uw gegevens lokaal houden om ervoor te zorgen dat uw gebruikers een minimale vertraging hebben. Als dat niet het geval is, kunt u deze baseren op het verloop tempo tijdens een bepaalde periode. Als u bijvoorbeeld weet dat ongeveer 10% van uw gegevensset van 1 TB wordt gewijzigd of elke maand actief wordt gebruikt, is het raadzaam om 100 GB lokaal te houden, zodat u niet vaak bestanden terugroept. Als uw volume 2 TB is, moet u 5% (of 100 GB) lokaal blijven, wat betekent dat de resterende 95% uw volume beschik bare ruimte percentage is. We raden u echter aan een buffer toe te voegen voor Peri Oden met een hoger verloop, met andere woorden, beginnend met een lager volume beschik bare ruimte percentage en het vervolgens aanpassen als dat later nodig is. 

Als u meer gegevens lokaal houdt, betekent dit lagere kosten voor uitgaand verkeer naarmate er minder bestanden worden ingetrokken van Azure, maar moet u ook een grotere hoeveelheid on-premises opslag onderhouden, die op eigen kosten wordt geleverd. Zodra u een exemplaar van Azure File Sync hebt geïmplementeerd, kunt u de uitvoer van uw opslag account op ongeveer meter bekijken, ongeacht of uw volume beschik bare ruimte-instellingen geschikt zijn voor uw gebruik. Ervan uitgaande dat het opslag account alleen uw Azure File Sync Cloud-eind punt bevat (dat wil zeggen, uw synchronisatie share), dan betekent hoge uitbrei ding dat veel bestanden worden ingetrokken vanuit de Cloud. u kunt overwegen uw lokale cache te verg Roten.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Ik heb een nieuw server eindpunt toegevoegd. Hoe lang duurt het tot mijn bestanden op deze server laag?
Wanneer de bestanden in versie 4,0 en hoger van de Azure File Sync-agent zijn geüpload naar de Azure-bestands share, worden ze op basis van uw beleid gelaagd zodra de volgende sessie wordt uitgevoerd. dit gebeurt eenmaal per uur. Bij oudere agents kan het tot 24 uur duren voordat lagen worden gelaagd.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Hoe kan ik zien of een bestand is getierd?
Er zijn verschillende manieren om te controleren of een bestand naar uw Azure-bestands share is getierd:
    
   *  **Controleer de bestands kenmerken van het bestand.**
     Klik met de rechter muisknop op een bestand, ga naar **Details**en schuif omlaag naar de eigenschap **Attributes** . Voor een gelaagd bestand zijn de volgende kenmerken ingesteld:     
        
        | Kenmerk letter | Kenmerk | Definitie |
        |:----------------:|-----------|------------|
        | A | Archief | Geeft aan dat er een back-up moet worden gemaakt van het bestand met back-upsoftware. Dit kenmerk is altijd ingesteld, ongeacht of het bestand op de schijf is gelaagd of volledig wordt opgeslagen. |
        | P | Sparse-bestand | Geeft aan dat het bestand een sparse-bestand is. Een sparse-bestand is een speciaal type bestand dat NTFS biedt voor efficiënt gebruik wanneer het bestand op de schijf stroom grotendeels leeg is. Azure File Sync maakt gebruik van verspreide bestanden omdat een bestand volledig is gelaagd of gedeeltelijk wordt ingetrokken. In een volledig gelaagd bestand wordt de bestands stroom opgeslagen in de Cloud. Het deel van het bestand bevindt zich al op schijf in een gedeeltelijk ingetrokken bestand. Als een bestand volledig wordt ingetrokken, wordt Azure File Sync geconverteerd van een sparse-bestand naar een gewoon bestand. Dit kenmerk wordt alleen ingesteld op Windows Server 2016 en ouder.|
        | mln. | Gegevens toegang intrekken | Geeft aan dat de gegevens van het bestand niet volledig aanwezig zijn in de lokale opslag. Als u het bestand leest, wordt er ten minste een deel van de bestands inhoud opgehaald van een Azure-bestands share waarop het server-eind punt is aangesloten. Dit kenmerk wordt alleen ingesteld op Windows Server 2019. |
        | L | Reparsepunt | Geeft aan dat het bestand een reparsepunt heeft. Een reparsepunt is een speciale verwijzing voor gebruik door een bestandssysteem filter. Azure File Sync maakt gebruik van reparsepunten om te definiëren dat de Azure File Sync bestandssysteem filter (StorageSync. sys) de locatie van de Cloud waar het bestand is opgeslagen. Dit biedt ondersteuning voor naadloze toegang. Gebruikers hoeven niet te weten dat Azure File Sync wordt gebruikt of hoe ze toegang krijgen tot het bestand in de Azure-bestands share. Wanneer een bestand volledig wordt ingetrokken, verwijdert Azure File Sync het reparsepunt uit het bestand. |
        | O | Offline | Geeft aan dat sommige of alle inhoud van het bestand niet op de schijf worden opgeslagen. Wanneer een bestand volledig is ingetrokken, Azure File Sync dit kenmerk verwijdert. |

        ![Het dialoog venster Eigenschappen voor een bestand, met het tabblad Details geselecteerd](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        U kunt de kenmerken van alle bestanden in een map weer geven door het veld **kenmerken** toe te voegen aan de tabel weergave van bestanden Verkenner. Als u dit wilt doen, klikt u met de rechter muisknop op een bestaande kolom (bijvoorbeeld **grootte**), selecteert u **meer**en selecteert u vervolgens **kenmerken** in de vervolg keuzelijst.
        
   * **Gebruik `fsutil` om op reparsepunten te controleren op een bestand.**
       Zoals beschreven in de voor gaande optie, bevat een gelaagd bestand altijd een reparsepunt set. Een reparse-wijzer is een speciale verwijzing voor het Azure File Sync bestandssysteem filter (StorageSync. sys). Als u wilt controleren of een bestand een reparsepunt heeft, voert u in een opdracht prompt met verhoogde bevoegdheid of een Power shell-venster het `fsutil`-hulp programma uit:
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Als het bestand een reparsepunt heeft, kunt u de waarde van het **reparse-label zien: 0x8000001e**. Deze hexadecimale waarde is de reparsepunt waarde die het eigendom is van Azure File Sync. De uitvoer bevat ook de reparsegegevens die het pad naar het bestand op uw Azure-bestands share vertegenwoordigt.

        > [!WARNING]  
        > De opdracht `fsutil reparsepoint` Utility kan ook een reparsepunt verwijderen. Voer deze opdracht niet uit, tenzij het Azure File Sync technisch team u vraagt. Als u deze opdracht uitvoert, kan dit leiden tot gegevens verlies. 

<a id="afs-recall-file"></a>

### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>Een bestand dat ik wil gebruiken, is gelaagd. Hoe kan ik het bestand intrekken op schijf om het lokaal te gebruiken?
De eenvoudigste manier om een bestand in te trekken op schijf is door het bestand te openen. Het Azure File Sync bestandssysteem filter (StorageSync. sys) downloadt het bestand naadloos van uw Azure-bestands share zonder uw eigen werk. Voor bestands typen die gedeeltelijk kunnen worden gelezen uit, zoals multi media-of zip-bestanden, wordt het hele bestand niet gedownload door een bestand te openen.

U kunt Power shell ook gebruiken om te forceren dat een bestand wordt ingetrokken. Deze optie kan nuttig zijn als u meerdere bestanden tegelijk wilt intrekken, bijvoorbeeld alle bestanden in een map. Open een Power shell-sessie met het server knooppunt waar Azure File Sync is geïnstalleerd en voer vervolgens de volgende Power shell-opdrachten uit:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -Order CloudTieringPolicy
```

Als u `-Order CloudTieringPolicy` opgeeft, worden de meest recent gewijzigde bestanden eerst ingetrokken.
Andere optionele para meters:
* `-ThreadCount` bepaalt hoeveel bestanden parallel kunnen worden ingetrokken.
* `-PerFileRetryCount`bepaalt hoe vaak een terugroep bewerking wordt uitgevoerd voor een bestand dat momenteel is geblokkeerd.
* `-PerFileRetryDelaySeconds`bepaalt de tijd in seconden tussen nieuwe pogingen en moet altijd worden gebruikt in combi natie met de vorige para meter.

> [!Note]  
> Als het lokale volume dat als host fungeert voor de server onvoldoende beschik bare ruimte heeft om alle gelaagde gegevens in te trekken, mislukt de `Invoke-StorageSyncFileRecall`-cmdlet.  

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Waarom wordt de eigenschap *grootte op schijf* voor een bestand niet overeenkomen met de eigenschap *grootte* na gebruik van Azure file sync? 
In Windows Verkenner worden twee eigenschappen beschikbaar gesteld om de grootte van een bestand weer te geven: **grootte** en **grootte op schijf**. Deze eigenschappen wijken in betekenis. **Grootte** vertegenwoordigt de volledige grootte van het bestand. **Grootte op schijf** staat voor de grootte van de bestands stroom die op de schijf is opgeslagen. De waarden voor deze eigenschappen kunnen verschillende oorzaken hebben, zoals compressie, het gebruik van Gegevensontdubbeling of Cloud lagen met Azure File Sync. Als een bestand wordt gelaagd voor een Azure-bestands share, is de grootte op de schijf nul, omdat de bestands stroom wordt opgeslagen in de Azure-bestands share en niet op de schijf. Het is ook mogelijk dat een bestand gedeeltelijk wordt gelaagd (of gedeeltelijk wordt ingetrokken). In een gedeeltelijk gelaagd bestand bevindt zich een deel van het bestand op schijf. Dit kan gebeuren wanneer bestanden gedeeltelijk worden gelezen door toepassingen als multimedia spelers of zip-hulpprogram ma's. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Hoe kan ik moet een bestand of map geforceerd worden getierd?
Wanneer de functie voor Cloud lagen is ingeschakeld, worden bestanden in Cloud lagen automatisch gelaagd op basis van de laatste toegangs-en wijzigings tijden om het percentage beschik bare ruimte op het Cloud eindpunt te verkrijgen. Soms wilt u een bestand op laag hand matig geforceerd afdwingen. Dit kan handig zijn als u een groot bestand opslaat dat u niet langer wilt gebruiken gedurende een lange periode en u de beschik bare ruimte op uw volume nu wilt gebruiken voor andere bestanden en mappen. U kunt de lagen afdwingen met behulp van de volgende Power shell-opdrachten:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="next-steps"></a>Volgende stappen
* [Een Azure File Sync-implementatie plannen](storage-sync-files-planning.md)
