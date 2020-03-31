---
title: Azure File Sync Cloud Tiering begrijpen | Microsoft Documenten
description: Meer informatie over de functie Cloud Tiering van Azure File Sync
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 11f9097fc4875f0a4300ac56dafe7af9a0b00c97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454615"
---
# <a name="cloud-tiering-overview"></a>Overzicht van cloudlagen
Cloudtiering is een optionele functie van Azure File Sync, waarbij vaak geopende bestanden lokaal op de server in de cache worden opgeslagen, terwijl alle andere bestanden zijn gelaagd tot Azure Files op basis van beleidsinstellingen. Wanneer een bestand is gelaagd, vervangt het azure file sync-bestandssysteemfilter (StorageSync.sys) het bestand lokaal door een aanwijzer of een reparsepunt. Het heespunt vertegenwoordigt een URL naar het bestand in Azure-bestanden. Een gelaagd bestand heeft zowel het kenmerk 'offline' als het kenmerk FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS dat is ingesteld in NTFS, zodat toepassingen van derden trapsgewijs gelaagde bestanden kunnen identificeren.
 
Wanneer een gebruiker een gelaagd bestand opent, roept Azure File Sync naadloos de bestandsgegevens van Azure Files terug zonder dat de gebruiker hoeft te weten dat het bestand is opgeslagen in Azure. 
 
 > [!Important]  
 > Cloudtiering wordt niet ondersteund op het Windows-systeemvolume.
    
 > [!Important]  
 > Als u bestanden wilt terugroepen die zijn gelaagd, moet de netwerkbandbreedte ten minste 1 Mbps bedragen. Als de netwerkbandbreedte minder dan 1 Mbps bedraagt, kunnen bestanden zich mogelijk niet herinneren met een time-outfout.

## <a name="cloud-tiering-faq"></a>Veelgestelde vragen over cloudlagen

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Hoe werkt cloudtiering?
Met het azure file sync-systeemfilter wordt een 'heatmap' van uw naamruimte op elk servereindpunt gemaakt. Het controleert toegangen (lees- en schrijfbewerkingen) in de loop van de tijd en wijst vervolgens, op basis van zowel de frequentie als de recentheid van de toegang, een warmtescore toe aan elk bestand. Een vaak geopend bestand dat onlangs is geopend, wordt als hot beschouwd, terwijl een bestand dat nauwelijks wordt aangeraakt en al enige tijd niet is geopend, als cool wordt beschouwd. Wanneer het bestandsvolume op een server de ingestelde volumedrempel voor vrije ruimte overschrijdt, worden de coolste bestanden in Azure-bestanden geklasseerd totdat aan uw percentage vrije ruimte is voldaan.

In versies 4.0 en hoger van de Azure File Sync-agent u bovendien een datumbeleid opgeven voor elk servereindpunt dat alle bestanden die niet binnen een bepaald aantal dagen zijn geopend of gewijzigd, laagbrengt.

<a id="tiering-minimum-file-size"></a>
### <a name="what-is-the-minimum-file-size-for-a-file-to-tier"></a>Wat is de minimale bestandsgrootte voor een bestand tot laag?
Voor agentversies 9.x en nieuwer is de minimale bestandsgrootte voor een bestandstot laag gebaseerd op de clustergrootte van het bestandssysteem (het dubbele van de clustergrootte van het bestandssysteem). Als de clustergrootte van het NTFS-bestandssysteem bijvoorbeeld 4 KB is, is de resulterende minimale bestandsgrootte voor een bestand tot laag 8 KB. Voor agentversies 8.x en ouder is de minimale bestandsgrootte voor een bestand tot laag 64 KB.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Hoe werkt het opslaglaagbeleid voor beschikbare volumeruimte?
Volumevrije ruimte is de hoeveelheid vrije ruimte die u wilt reserveren op het volume waarop een servereindpunt zich bevindt. Als bijvoorbeeld volumevrije ruimte is ingesteld op 20% op een volume met één servereindpunt, wordt tot 80% van de volumeruimte ingenomen door de meest recent geopende bestanden, waarbij de resterende bestanden die niet in deze ruimte passen, zijn gelaagd tot Azure. Volumevrije ruimte is van toepassing op volumeniveau in plaats van op het niveau van individuele mappen of synchronisatiegroepen. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Hoe werkt het opslaglaagbeleid voor beschikbare volumeruimte met betrekking tot de nieuwe servereindpunten?
Wanneer een servereindpunt onlangs is ingericht en is verbonden met een Azure-bestandsshare, haalt de server eerst de naamruimte naar beneden en haalt vervolgens de werkelijke bestanden naar beneden totdat deze de volumevrije ruimtedrempel bereikt. Dit proces wordt ook wel snel herstel van de ramp of snelle naamruimte herstellen genoemd.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Hoe wordt vrije ruimte op een volume geïnterpreteerd wanneer ik meerdere servereindpunten heb op een volume?
Wanneer er meer dan één servereindpunt op een volume is, is de effectieve volumevrije ruimtedrempel de grootste volumevrije ruimte die is opgegeven voor een servereindpunt op dat volume. Bestanden worden gelaagd op basis van hun gebruikspatronen, ongeacht welk servereindpunt waartoe ze behoren. Als u bijvoorbeeld twee servereindpunten op een volume, Endpoint1 en Endpoint2 hebt, waarbij Endpoint1 een volumevrije ruimtedrempel van 25% heeft en Endpoint2 een volumevrije ruimtedrempel van 50%, is de volumevrije ruimtedrempel voor beide servereindpunten 50%. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Hoe werkt het opslaglaagbeleid voor datums in combinatie met het opslaglaagbeleid voor beschikbare volumeruimte? 
Wanneer u cloudlagen inschakelt op een servereindpunt, stelt u een beleid voor volumevrije ruimte in. Het heeft altijd voorrang op andere beleidsregels, inclusief het datumbeleid. Optioneel u een datumbeleid inschakelen voor elk servereindpunt op dat volume, wat betekent dat alleen bestanden die toegankelijk zijn (dat wil zeggen, lezen of geschreven zijn) binnen het bereik van dagen dat dit beleid beschrijft, lokaal worden bewaard, waarbij stalbestanden worden gelaagd. Houd er rekening mee dat het beleid voor volumevrije ruimte altijd voorrang heeft en als er niet genoeg vrije ruimte op het volume is om zoveel dagen aan bestanden te behouden als beschreven in het datumbeleid, blijft Azure File Sync de koudste bestanden tieren totdat het volume vrij is ruimtepercentage is voldaan.

Stel dat u een op datum gebaseerd tieringbeleid van 60 dagen en een volumevrije ruimtebeleid van 20% hebt. Als er na het toepassen van het datumbeleid minder dan 20% vrije ruimte op het volume is, zal het volumevrije ruimtebeleid het datumbeleid intrappen en overschrijven. Dit zal resulteren in meer bestanden worden tiered, zodanig dat de hoeveelheid gegevens bewaard op de server kan worden teruggebracht van 60 dagen van de gegevens tot 45 dagen. Omgekeerd zal dit beleid de gelaagdheid van bestanden die buiten uw tijdsbereik vallen, zelfs als u uw drempelruimtedrempel voor vrije ruimte niet hebt bereikt, afdwingen, zodat een bestand dat 61 dagen oud is, wordt gelaagd, zelfs als uw volume leeg is.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Hoe bepaal ik de juiste hoeveelheid beschikbare volumeruimte?
De hoeveelheid gegevens die u lokaal moet bewaren, wordt bepaald door een aantal factoren: uw bandbreedte, het toegangspatroon van uw gegevensset en uw budget. Als u een verbinding met een lage bandbreedte hebt, wilt u misschien meer van uw gegevens lokaal houden om ervoor te zorgen dat er een minimale vertraging is voor uw gebruikers. Anders u deze gedurende een bepaalde periode baseren op de churn rate. Als u bijvoorbeeld weet dat ongeveer 10% van uw 1 TB-gegevensset elke maand wordt gewijzigd of actief wordt geopend, wilt u misschien 100 GB lokaal houden, zodat u niet vaak bestanden terugbelt. Als uw volume 2TB is, wilt u 5% (of 100 GB) lokaal houden, wat betekent dat de resterende 95% uw volumevrije ruimtepercentage is. We raden u echter aan een buffer toe te voegen om rekening te houden met perioden met een hogere churn , met andere woorden, te beginnen met een lager volumevrije ruimtepercentage en deze vervolgens aan te passen indien nodig later. 

Het lokaal houden van meer gegevens betekent lagere uitgangskosten omdat er minder bestanden worden teruggeroepen uit Azure, maar u moet ook een grotere hoeveelheid on-premises opslag onderhouden, wat op eigen kosten gebeurt. Zodra u een exemplaar van Azure File Sync hebt geïmplementeerd, u de uitgang van uw opslagaccount bekijken om ruwweg te meten of uw instellingen voor volumevrije ruimte geschikt zijn voor uw gebruik. Ervan uitgaande dat het opslagaccount alleen uw Azure File Sync Cloud Endpoint bevat (dat wil zeggen uw synchronisatieaandeel), betekent een hoge uitgang dat veel bestanden uit de cloud worden teruggeroepen en u moet overwegen uw lokale cache te vergroten.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Ik heb een nieuw servereindpunt toegevoegd. Hoe lang duurt het voordat mijn bestanden op deze serverlaag staan?
In versie 4.0 en hoger van de Azure File Sync-agent worden deze, zodra uw bestanden zijn geüpload naar het Azure-bestandsaandeel, op basis van uw beleid gelaagd zodra de volgende gelaagdheidssessie wordt uitgevoerd, die eenmaal per uur plaatsvindt. Op oudere agenten kan tiering tot 24 uur duren.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Hoe weet ik of een bestand is trapsgewijs?
Er zijn verschillende manieren om te controleren of een bestand is gelaagd voor uw Azure-bestandsshare:
    
   *  **Controleer de bestandskenmerken in het bestand.**
     Klik met de rechtermuisknop op een bestand, ga naar **Details**en blader naar beneden naar de eigenschap **Kenmerken.** Een gelaagd bestand heeft de volgende kenmerken:     
        
        | Kenmerkletter | Kenmerk | Definitie |
        |:----------------:|-----------|------------|
        | A | Archiveren | Geeft aan dat er een back-up van het bestand moet worden gemaakt door back-upsoftware. Dit kenmerk is altijd ingesteld, ongeacht of het bestand is gelaagd of volledig op schijf is opgeslagen. |
        | P | Spaarzaam bestand | Geeft aan dat het bestand een schaars bestand is. Een schaars bestand is een gespecialiseerd type bestand dat NTFS biedt voor efficiënt gebruik wanneer het bestand op de schijfstroom grotendeels leeg is. Azure File Sync maakt gebruik van schaarse bestanden omdat een bestand volledig gelaagd is of gedeeltelijk wordt teruggeroepen. In een volledig gelaagd bestand wordt de bestandsstream opgeslagen in de cloud. In een gedeeltelijk teruggeroepen bestand staat dat deel van het bestand al op schijf. Als een bestand volledig wordt teruggeroepen naar de schijf, converteert Azure File Sync het van een schaars bestand naar een normaal bestand. Dit kenmerk is alleen ingesteld op Windows Server 2016 en ouder.|
        | M | Terugroepen op gegevenstoegang | Geeft aan dat de gegevens van het bestand niet volledig aanwezig zijn op lokale opslag. Als u het bestand leest, wordt ten minste een deel van de bestandsinhoud opgehaald uit een Azure-bestandsshare waarmee het servereindpunt is verbonden. Dit kenmerk is alleen ingesteld op Windows Server 2019. |
        | L | Reparsepunt | Geeft aan dat het bestand een reparsepunt heeft. Een reparsepunt is een speciale aanwijzer voor gebruik door een filter van het bestandssysteem. Azure File Sync gebruikt reparsepunten om te definiëren op het Azure File Sync-bestandssysteemfilter (StorageSync.sys) de cloudlocatie waar het bestand is opgeslagen. Dit ondersteunt naadloze toegang. Gebruikers hoeven niet te weten dat Azure File Sync wordt gebruikt of hoe ze toegang krijgen tot het bestand in uw Azure-bestandsshare. Wanneer een bestand volledig wordt teruggeroepen, verwijdert Azure File Sync het reparsepunt uit het bestand. |
        | O | Offline | Geeft aan dat de inhoud van het bestand niet op de schijf is opgeslagen of dat sommige of alle inhoud van het bestand niet is opgeslagen. Wanneer een bestand volledig wordt teruggeroepen, verwijdert Azure File Sync dit kenmerk. |

        ![Het dialoogvenster Eigenschappen voor een bestand, waarbij het tabblad Details is geselecteerd](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        U de kenmerken voor alle bestanden in een map bekijken door het veld **Kenmerken** toe te voegen aan de tabelweergave van Verkenner. Klik hiervoor met de rechtermuisknop op een bestaande kolom (bijvoorbeeld **Grootte),** selecteer **Meer**en selecteer **vervolgens Kenmerken** in de vervolgkeuzelijst.
        
   * **Met `fsutil` gebruiken om te controleren op hees punten op een bestand.**
       Zoals beschreven in de vorige optie, heeft een gelaagd bestand altijd een hees puntset. Een hearse aanwijzer is een speciale aanwijzer voor het azure file sync-bestandssysteemfilter (StorageSync.sys). Voer het `fsutil` hulpprogramma uit om te controleren of een bestand een reparsepunt heeft:
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Als het bestand een reparse punt heeft, u **reparse tagwaarde verwachten: 0x8000001e**. Deze hexadecimale waarde is de reparse puntwaarde die eigendom is van Azure File Sync. De uitvoer bevat ook de hearse gegevens die het pad naar uw bestand op uw Azure-bestandsshare vertegenwoordigen.

        > [!WARNING]  
        > De `fsutil reparsepoint` utility opdracht heeft ook de mogelijkheid om een reparse punt te verwijderen. Voer deze opdracht niet uit, tenzij het azure file sync-engineeringteam u daarom vraagt. Als u deze opdracht uitvoert, kan dit leiden tot gegevensverlies. 

<a id="afs-recall-file"></a>

### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>Een bestand dat ik wil gebruiken is gelaagd. Hoe kan ik het bestand op schijf herinneren om het lokaal te gebruiken?
De eenvoudigste manier om een bestand terug te roepen naar de schijf is door het bestand te openen. Het azure file sync-bestandssysteemfilter (StorageSync.sys) downloadt het bestand naadloos uit uw Azure-bestandsshare zonder dat u werkt. Voor bestandstypen waarvan gedeeltelijk kan worden gelezen, zoals multimedia- of .zip-bestanden, wordt het openen van een bestand niet het hele bestand gedownload.

U PowerShell ook gebruiken om een bestand terug te roepen. Deze optie kan handig zijn als u meerdere bestanden tegelijk wilt terugroepen, zoals alle bestanden in een map. Open een PowerShell-sessie op het serverknooppunt waar Azure File Sync is geïnstalleerd en voer de volgende PowerShell-opdrachten uit:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Optionele parameters:
* `-Order CloudTieringPolicy`herinnert zich eerst de meest recent gewijzigde bestanden.  
* `-ThreadCount`bepaalt hoeveel bestanden parallel kunnen worden teruggeroepen.
* `-PerFileRetryCount`hiermee wordt bepaald hoe vaak een terugroepactie wordt geprobeerd van een bestand dat momenteel is geblokkeerd.
* `-PerFileRetryDelaySeconds`bepaalt de tijd in seconden tussen het opnieuw inroepen van pogingen en moet altijd worden gebruikt in combinatie met de vorige parameter.

> [!Note]  
> Als het lokale volume dat de server host niet genoeg vrije `Invoke-StorageSyncFileRecall` ruimte heeft om alle gelaagde gegevens terug te roepen, mislukt de cmdlet.  

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Waarom komt de *eigenschap Grootte op schijf* voor een bestand niet overeen met de eigenschap *Grootte* na het gebruik van Azure File Sync? 
In Windows Verkenner worden twee eigenschappen weergegeven die de grootte van een bestand weergeven: **grootte** en **grootte op schijf**. Deze eigenschappen verschillen subtiel in betekenis. **Grootte** vertegenwoordigt de volledige grootte van het bestand. **Grootte op schijf** vertegenwoordigt de grootte van de bestandsstream die op de schijf is opgeslagen. De waarden voor deze eigenschappen kunnen om verschillende redenen verschillen, zoals compressie, gebruik van gegevensdeduplicatie of cloudgelaagdheid met Azure File Sync. Als een bestand is gelaagd tot een Azure-bestandsshare, is de grootte op de schijf nul, omdat de bestandsstroom is opgeslagen in uw Azure-bestandsshare en niet op de schijf. Het is ook mogelijk dat een bestand gedeeltelijk wordt gerijstoond (of gedeeltelijk wordt teruggeroepen). In een gedeeltelijk gelaagd bestand bevindt een deel van het bestand zich op schijf. Dit kan gebeuren wanneer bestanden gedeeltelijk worden gelezen door toepassingen zoals multimediaspelers of zip-hulpprogramma's. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Hoe dwing ik een bestand of directory te laten tieren?
Wanneer de functie voor cloudgelaagdheid is ingeschakeld, worden bestanden met cloudlagen automatisch laagbestandbestanden op basis van laatste toegang en worden de tijden aangepast om het volumevrije ruimtepercentage te bereiken dat is opgegeven op het eindpunt van de cloud. Soms wilt u een bestand echter handmatig naar laag dwingen. Dit kan handig zijn als u een groot bestand opslaat dat u lange tijd niet meer wilt gebruiken en u wilt dat de vrije ruimte op uw volume nu wordt gebruikt voor andere bestanden en mappen. U tiering forceren met behulp van de volgende PowerShell-opdrachten:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

<a id="afs-image-thumbnail"></a>
### <a name="why-are-my-tiered-files-not-showing-thumbnails-or-previews-in-windows-explorer"></a>Waarom worden in mijn gelaagde bestanden geen miniaturen of voorvertoningen weergegeven in Windows Verkenner?
Voor gelaagde bestanden zijn miniaturen en voorvertoningen niet zichtbaar op het eindpunt van de server. Dit gedrag wordt verwacht omdat de miniatuurcachefunctie in Windows opzettelijk het lezen van bestanden met het offline kenmerk overslaat. Als Cloud Tiering is ingeschakeld, zou het lezen van gelaagde bestanden ertoe leiden dat deze worden gedownload (teruggeroepen).

Dit gedrag is niet specifiek voor Azure File Sync, Windows Explorer geeft een 'grijze X' weer voor bestanden met de offline attribuutset. U ziet het X-pictogram wanneer u bestanden via SMB opent. Voor een gedetailleerde uitleg van dit gedrag, verwijzen naar[https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)


## <a name="next-steps"></a>Volgende stappen
* [Planning voor een Azure File Sync Deployment](storage-sync-files-planning.md)
