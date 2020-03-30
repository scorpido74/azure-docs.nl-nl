---
title: StorSimple Snapshot Manager back-upcatalogus | Microsoft Documenten
description: Beschrijft hoe u de Module StorSimple Snapshot Manager MMC gebruiken om de back-upcatalogus weer te geven en te beheren.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 38ef7774263e4b28b7c316fd0870ca8f7b89d6b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931711"
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>StorSimple Snapshot Manager gebruiken om de back-upcatalogus te beheren

## <a name="overview"></a>Overzicht
De primaire functie van StorSimple Snapshot Manager is om u in staat te stellen toepassingsconsistente back-upkopieën van StorSimple-volumes te maken in de vorm van momentopnamen. Momentopnamen worden vervolgens weergegeven in een XML-bestand dat een *back-upcatalogus*wordt genoemd. De back-upcatalogus organiseert momentopnamen per volumegroep en vervolgens op lokale momentopname of cloudmomentopname.

In deze zelfstudie wordt beschreven hoe u het knooppunt **Back-upcatalogus** gebruiken om de volgende taken uit te voeren:

* Een volume herstellen
* Een volume- of volumegroep klonen
* Een back-up verwijderen
* Een bestand herstellen
* De Database van Storsimple Snapshot Manager herstellen

U de back-upcatalogus bekijken door het knooppunt **Back-upcatalogus** uit te vouwen in het deelvenster **Bereik** en vervolgens de volumegroep uit te breiden.

* Als u op de naam van de volumegroep klikt, wordt in het deelvenster **Resultaten** het aantal lokale momentopnamen en cloudmomentopnamen weergegeven dat beschikbaar is voor de volumegroep. 
* Als u op **Lokale momentopname of** **Cloudmomentopname**klikt, worden in het deelvenster **Resultaten** de volgende informatie over elke momentopname weergegeven (afhankelijk van uw **weergave-instellingen):**
  
  * **Naam** – de tijd dat de momentopname is gemaakt.
  * **Typ** : of dit nu een lokale momentopname of een cloudmomentopname is.
  * **Eigenaar** – de eigenaar van de inhoud. 
  * **Beschikbaar** – of de momentopname momenteel beschikbaar is. **True** geeft aan dat de momentopname beschikbaar is en kan worden hersteld; **False** geeft aan dat de momentopname niet meer beschikbaar is. 
  * **Geïmporteerd** : of de back-up is geïmporteerd. **True** geeft aan dat de back-up is geïmporteerd uit de StorSimple Device Manager-service op het moment dat het apparaat is geconfigureerd in StorSimple Snapshot Manager; **False** geeft aan dat het niet is geïmporteerd, maar is gemaakt door StorSimple Snapshot Manager. (U eenvoudig een geïmporteerde volumegroep identificeren omdat er een achtervoegsel wordt toegevoegd dat het apparaat identificeert waaruit de volumegroep is geïmporteerd.)
    
    ![Back-upcatalogus](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* Als u **Lokale momentopname of** cloudmomentopname uitvouwt en vervolgens op een afzonderlijke momentopnamenaam klikt, wordt in het deelvenster **Resultaten** de volgende informatie weergegeven over de momentopname die u hebt geselecteerd: **Cloud Snapshot**
  
  * **Naam** – het volume dat per stationsletter wordt aangegeven. 
  * **Lokale naam** – de lokale naam van het station (indien beschikbaar). 
  * **Apparaat** – de naam van het apparaat waarop het volume zich bevindt. 
  * **Beschikbaar** – of de momentopname momenteel beschikbaar is. **True** geeft aan dat de momentopname beschikbaar is en kan worden hersteld; **False** geeft aan dat de momentopname niet meer beschikbaar is. 

## <a name="restore-a-volume"></a>Een volume herstellen
Gebruik de volgende procedure om een volume van back-up te herstellen.

#### <a name="prerequisites"></a>Vereisten
Als u dit nog niet hebt gedaan, maakt u een volume- en volumegroep en verwijdert u het volume. StorSimple Snapshot Manager maakt standaard een back-up van een volume voordat het kan worden verwijderd. Deze voorzorgsmaatregel kan gegevensverlies voorkomen als het volume onbedoeld wordt verwijderd of als de gegevens om welke reden dan ook moeten worden hersteld. 

StorSimple Snapshot Manager geeft het volgende bericht weer terwijl het de voorzorgsmaatregel maakt.

![Automatisch momentopnamebericht](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> U een volume dat deel uitmaakt van een volumegroep niet verwijderen. De optie verwijderen is niet beschikbaar. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>Een volume herstellen
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten. 
2. Vouw **in** het deelvenster Bereik het knooppunt **Back-upcatalogus** uit, vouw een volumegroep uit en klik vervolgens op **Lokale momentopnamen** of **Cloudmomentopnamen**. Er wordt een lijst met back-upmomentopnamen weergegeven in het deelvenster **Resultaten.**
3. Zoek de back-up die u wilt herstellen, klik met de rechtermuisknop en klik op **Herstellen**.
   
    ![Back-upcatalogus herstellen](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. Controleer op de bevestigingspagina de details, typ **Bevestigen**en klik op **OK**. StorSimple Snapshot Manager gebruikt de back-up om het volume te herstellen.
   
    ![Bevestigingsbericht herstellen](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. U de herstelactie controleren terwijl deze wordt uitgevoerd. Vouw in het deelvenster **Bereik** het knooppunt **Taken** uit en klik vervolgens op **Uitvoeren**. De taakgegevens worden weergegeven in het deelvenster **Resultaten.** Wanneer de hersteltaak is voltooid, worden de taakgegevens overgebracht naar de lijst **Laatste 24 uur.**

## <a name="clone-a-volume-or-volume-group"></a>Een volume- of volumegroep klonen
Gebruik de volgende procedure om een duplicaat (kloon) van een volume- of volumegroep te maken.

#### <a name="to-clone-a-volume-or-volume-group"></a>Een volume- of volumegroep klonen
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
2. Vouw **in** het deelvenster Bereik het knooppunt **Back-upcatalogus** uit, vouw een volumegroep uit en klik vervolgens op **Cloudsnapshots**. Er wordt een lijst met back-ups weergegeven in het deelvenster **Resultaten.**
3. Zoek de volume- of volumegroep die u wilt klonen, klik met de rechtermuisknop op de naam van de volume- of volumegroep en klik op **Kloon**. Het dialoogvenster **Momentopname kloonwolk** wordt weergegeven.
   
    ![Een cloudmomentopname klonen](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Vul het dialoogvenster **Clone Cloud Snapshot** als volgt in: 
   
   1. Typ in het tekstvak **Naam** een naam voor het gekloonde volume. Deze naam wordt weergegeven in het knooppunt **Volumes.** 
   2. (Optioneel) selecteer **Station**en selecteer vervolgens een stationsletter in de vervolgkeuzelijst.
   3. (Optioneel) selecteer **Map (NTFS)** en typ een mappad of klik op Bladeren en selecteer een locatie voor de map. 
   4. Klik **op Maken**.
5. Wanneer het kloonproces is voltooid, moet u het gekloonde volume initialiseren. Start Serverbeheer en start schijfbeheer. Zie [Volumes monteren voor](storsimple-snapshot-manager-manage-volumes.md#mount-volumes)gedetailleerde instructies. Nadat het is geïnitialiseerd, wordt het volume weergegeven onder het knooppunt **Volumes** in het deelvenster **Scope.** Als u het vermelde volume niet ziet, vernieuwt u de lijst met volumes (klik met de rechtermuisknop op het knooppunt **Volumes** en klikt u vervolgens op **Vernieuwen).**

## <a name="delete-a-backup"></a>Een back-up verwijderen
Gebruik de volgende procedure om een momentopname uit de back-upcatalogus te verwijderen. 

> [!NOTE]
> Als u een momentopname verwijdert, worden de back-upgegevens verwijderd die aan de momentopname zijn gekoppeld. Het verwerken van gegevens uit de cloud kan echter enige tijd in beslag nemen.<br>


#### <a name="to-delete-a-backup"></a>Een back-up verwijderen
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
2. Vouw **in** het deelvenster Bereik het knooppunt **Back-upcatalogus** uit, vouw een volumegroep uit en klik vervolgens op **Lokale momentopnamen** of **Cloudmomentopnamen**. Er wordt een lijst met momentopnamen weergegeven in het deelvenster **Resultaten.**
3. Klik met de rechtermuisknop op de momentopname die u wilt verwijderen en klik vervolgens op **Verwijderen**.
4. Klik op **OK**wanneer het bevestigingsbericht wordt weergegeven.

## <a name="recover-a-file"></a>Een bestand herstellen
Als een bestand per ongeluk uit een volume wordt verwijderd, u het bestand herstellen door een momentopname op te halen die voorafgaat aan de verwijdering, de momentopname gebruikt om een kloon van het volume te maken en vervolgens het bestand van het gekloonde volume naar het oorspronkelijke volume te kopiëren.

#### <a name="prerequisites"></a>Vereisten
Voordat u begint, moet u ervoor zorgen dat u een huidige back-up van de volumegroep hebt. Verwijder vervolgens een bestand dat is opgeslagen op een van de volumes in die volumegroep. Gebruik ten slotte de volgende stappen om het verwijderde bestand uit uw back-up te herstellen. 

#### <a name="to-recover-a-deleted-file"></a>Een verwijderd bestand herstellen
1. Klik op het pictogram StorSimple Snapshot Manager op uw bureaublad. Het consolevenster Van StorSimple Snapshot Manager wordt weergegeven. 
2. Vouw **in** het deelvenster Bereik het knooppunt **Back-upcatalogus** uit en blader naar een momentopname die het verwijderde bestand bevat. Meestal moet u een momentopname selecteren die vlak voor de verwijdering is gemaakt.
3. Zoek het volume dat u wilt klonen, klik met de rechtermuisknop en klik op **Klonen**. Het dialoogvenster **Momentopname kloonwolk** wordt weergegeven.
   
    ![Een cloudmomentopname klonen](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Vul het dialoogvenster **Clone Cloud Snapshot** als volgt in: 
   
   1. Typ in het tekstvak **Naam** een naam voor het gekloonde volume. Deze naam wordt weergegeven in het knooppunt **Volumes.** 
   2. (Optioneel) Selecteer **Station**en selecteer vervolgens een stationsletter in de vervolgkeuzelijst. 
   3. (Optioneel) Selecteer **Map (NTFS)** en typ een mappad of klik op **Bladeren** en selecteer een locatie voor de map. 
   4. Klik **op Maken**. 
5. Wanneer het kloonproces is voltooid, moet u het gekloonde volume initialiseren. Start Serverbeheer en start schijfbeheer. Zie [Volumes monteren voor](storsimple-snapshot-manager-manage-volumes.md#mount-volumes)gedetailleerde instructies. Nadat het is geïnitialiseerd, wordt het volume weergegeven onder het knooppunt **Volumes** in het deelvenster **Scope.** 
   
    Als u het vermelde volume niet ziet, vernieuwt u de lijst met volumes (klik met de rechtermuisknop op het knooppunt **Volumes** en klikt u vervolgens op **Vernieuwen).**
6. Open de NTFS-map die het gekloonde volume bevat, vouw het knooppunt **Volumes** uit en open het gekloonde volume. Zoek het bestand dat u wilt herstellen en kopieer het naar het primaire volume.
7. Nadat u het bestand hebt hersteld, u de NTFS-map verwijderen die het gekloonde volume bevat.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>De Database van StorSimple Snapshot Manager herstellen
U moet regelmatig een back-up maken van de StorSimple Snapshot Manager-database op de hostcomputer. Als er zich een ramp voordoet of als de hostcomputer om welke reden dan ook uitvalt, u deze herstellen vanuit de back-up. Het maken van de database back-up is een handmatig proces.

#### <a name="to-back-up-and-restore-the-database"></a>Een back-up maken en de database herstellen
1. Stop de Microsoft StorSimple-beheerservice:
   
   1. Start Serverbeheer.
   2. Selecteer Services in het dashboard Serverbeheer in **het** menu **Extra**.
   3. Selecteer in het venster **Services** de **Microsoft StorSimple Management Service**.
   4. Klik in het rechterdeelvenster onder **Microsoft StorSimple Management Service**op De service **stoppen**.
2. Blader op de hostcomputer naar C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData is een verborgen map.
   > 
   > 
3. Zoek het XML-bestand van de catalogus, kopieer het bestand en sla de kopie op een veilige locatie of in de cloud op. Als de host uitvalt, u dit back-upbestand gebruiken om het back-upbeleid te herstellen dat u hebt gemaakt in StorSimple Snapshot Manager.
   
    ![Azure StorSimple-back-upcatalogusbestand](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Start de Microsoft StorSimple-beheerservice opnieuw: 
   
   1. Selecteer Services in het dashboard Serverbeheer in **het** menu **Extra**.
   2. Selecteer in het venster **Services** de **Microsoft StorSimple Management Service**.
   3. Klik in het rechterdeelvenster onder **Microsoft StorSimple Management Service**op De service opnieuw **starten**.
5. Blader op de hostcomputer naar C:\ProgramData\Microsoft\StorSimple\BACatalog. 
6. Verwijder het XML-bestand van de catalogus en vervang het door de back-upversie die u hebt gemaakt. 
7. Klik op het pictogram StorSimple Snapshot Manager op het bureaublad om StorSimple Snapshot Manager te starten. 

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [het gebruik van StorSimple Snapshot Manager om uw StorSimple-oplossing te beheren.](storsimple-snapshot-manager-admin.md)
* Meer informatie over [taken en werkstromen van StorSimple Snapshot Manager](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).

