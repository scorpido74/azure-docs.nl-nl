---
title: StorSimple Snapshot Manager en volumes | Microsoft Documenten
description: Beschrijft hoe u de Module StorSimple Snapshot Manager-module gebruiken om volumes weer te geven en te beheren en back-ups te configureren.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: twooley
ms.openlocfilehash: f09d4dd46a50f1794e51342a939b8919c5c523ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254649"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>StorSimple Snapshot Manager gebruiken om volumes weer te geven en te beheren
## <a name="overview"></a>Overzicht
U het knooppunt Volumes **van** StorSimple Snapshot Manager (in het deelvenster **Bereik)** gebruiken om volumes te selecteren en informatie over deze objecten weer te geven. De volumes worden gepresenteerd als stations die overeenkomen met de volumes die door de host zijn gemonteerd. Het knooppunt **Volumes** toont lokale volumes en volumetypen die worden ondersteund door StorSimple, inclusief volumes die zijn ontdekt door het gebruik van iSCSI en een apparaat. 

Ga voor meer informatie over ondersteunde volumes naar [Ondersteuning voor meerdere volumetypen](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Volumelijst in deelvenster Resultaten](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

Met het knooppunt **Volumes** u ook volumes opnieuw scannen of verwijderen nadat StorSimple Snapshot Manager ze hebt ontdekt. 

In deze zelfstudie wordt uitgelegd hoe u volumes monteren, initialiseren en opmaken en vervolgens StorSimple Snapshot Manager gebruiken om:

* Informatie over volumes weergeven 
* Volumes verwijderen
* Volumes opnieuw scannen 
* Een basisvolume configureren en een back-up maken
* Een dynamisch gespiegeld volume configureren en een back-up maken

> [!NOTE]
> Alle acties met **het volumeknooppunt** zijn ook beschikbaar in het deelvenster **Handelingen.**
> 
> 

## <a name="mount-volumes"></a>Monteer volumes
Gebruik de volgende procedure om StorSimple-volumes te monteren, te initialiseren en op te maken. Deze procedure maakt gebruik van Disk Management, een systeemhulpprogramma voor het beheren van harde schijven en de bijbehorende volumes of partities. Ga voor meer informatie over Schijfbeheer naar [Schijfbeheer](https://technet.microsoft.com/library/cc770943.aspx) op de website van Microsoft TechNet.

#### <a name="to-mount-volumes"></a>Volumes monteren
1. Start op uw hostcomputer de Microsoft iSCSI-initiator.
2. Lever een van de IP-adressen van de interface als het doelportaal of detectie-IP-adres en maak verbinding met het apparaat. Nadat het apparaat is aangesloten, zijn de volumes toegankelijk voor uw Windows-systeem. Ga voor meer informatie over het gebruik van de Microsoft iSCSI-initiator naar de sectie 'Verbinding maken met een iSCSI-doelapparaat' [in Microsoft iSCSI Initiator installeren en configureren.][1]
3. Gebruik een van de volgende opties om Schijfbeheer te starten:
   
   * Typ Diskmgmt.msc in het vak **Uitvoeren.**
   * Start Serverbeheer, vouw het **opslagknooppunt** uit en selecteer **Schijfbeheer**.
   * Gebruik **Systeembeheer,** vouw het knooppunt **Computerbeheer** uit en selecteer **Schijfbeheer**. 
     
     > [!NOTE]
     > U moet beheerdersbevoegdheden gebruiken om Schijfbeheer uit te voeren.
     > 
     > 
4. Neem het volume(en) online:
   
   1. Klik in Schijfbeheer met de rechtermuisknop op een volume dat is gemarkeerd **als Offline.**
   2. Klik **op Schijf opnieuw activeren**. De schijf moet **online** worden gemarkeerd nadat de schijf opnieuw is geactiveerd.
5. Initialiseer het volume(en):
   
   1. Klik met de rechtermuisknop op de gedetecteerde volumes.
   2. Selecteer **Schijf initialiseren**in het menu .
   3. Selecteer in het dialoogvenster **Schijf initialiseren** de schijven die u wilt initialiseren en klik op **OK**.
6. Eenvoudige volumes opmaken:
   
   1. Klik met de rechtermuisknop op een volume dat u wilt opmaken.
   2. Selecteer **nieuw eenvoudig volume**in het menu .
   3. Gebruik de wizard Nieuw eenvoudig volume om het volume op te maken:
      
      * Geef de volumegrootte op.
      * Lever een drive letter.
      * Selecteer het NTFS-bestandssysteem.
      * Geef een clustergrootte van 64 kB op.
      * Voer een snelle formattering uit.
7. Multipartitievolumes opmaken. Ga voor instructies naar de sectie 'Partities en volumes' in [het implementeren van schijfbeheer.](https://msdn.microsoft.com/library/dd163556.aspx)

## <a name="view-information-about-your-volumes"></a>Informatie over uw volumes weergeven
Gebruik de volgende procedure om informatie over lokale en Azure StorSimple-volumes weer te geven.

#### <a name="to-view-volume-information"></a>Volume-informatie weergeven
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten. 
2. Klik **in** het deelvenster Bereik op het knooppunt **Volumes.** Een lijst met lokale en gemonteerde volumes, inclusief alle Azure StorSimple-volumes, wordt weergegeven in het deelvenster **Resultaten.** De kolommen in het deelvenster **Resultaten** kunnen worden geconfigureerd. (Klik met de rechtermuisknop op het knooppunt **Volumes,** selecteer **Weergave**en selecteer **Kolommen toevoegen/verwijderen**.)
   
    ![De kolommen configureren](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Resultatenkolom | Beschrijving |
   |:--- |:--- |
   |  Name |De kolom **Naam** bevat de stationsletter die is toegewezen aan elk gedetecteerd volume. |
   |  Apparaat |De kolom **Apparaat** bevat het IP-adres van het apparaat dat is aangesloten op de hostcomputer. |
   |  Naam apparaatvolume |De kolom **Apparaatvolumenaam** bevat de naam van het apparaatvolume waartoe het geselecteerde volume behoort. Dit is de volumenaam die is gedefinieerd in de Azure-portal voor dat specifieke volume. |
   |  Toegangspaden |In de kolom **Toegangspaden** wordt het toegangspad naar het volume weergegeven. Dit is de drive letter of mount point waarop het volume toegankelijk is op de host computer. |

## <a name="delete-a-volume"></a>Een volume verwijderen
Gebruik de volgende procedure om een volume uit StorSimple Snapshot Manager te verwijderen.

> [!NOTE]
> U een volume niet verwijderen als het deel uitmaakt van een volumegroep. (De optie verwijderen is niet beschikbaar voor volumes die lid zijn van een volumegroep.) U moet de hele volumegroep verwijderen om het volume te verwijderen.

#### <a name="to-delete-a-volume"></a>Een volume verwijderen
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
2. Klik **in** het deelvenster Bereik op het knooppunt **Volumes.** 
3. Klik in het deelvenster **Resultaten** met de rechtermuisknop op het volume dat u wilt verwijderen.
4. Klik in het menu op **Verwijderen**. 
   
    ![Een volume verwijderen](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. Het dialoogvenster **Volume verwijderen** wordt weergegeven. Typ **Bevestigen** in het tekstvak en klik op **OK**.
6. StorSimple Snapshot Manager maakt standaard een back-up van een volume voordat u het verwijderd. Deze voorzorgsmaatregel kan u beschermen tegen gegevensverlies als de verwijdering onbedoeld was. StorSimple Snapshot Manager geeft een **voortgangsbericht voor automatische momentopname weer** terwijl het een back-up maakt van het volume. 
   
    ![Automatisch momentopnamebericht](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Volumes opnieuw scannen
Gebruik de volgende procedure om de volumes die zijn verbonden met StorSimple Snapshot Manager opnieuw te scannen.

#### <a name="to-rescan-the-volumes"></a>De volumes opnieuw scannen
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
2. Klik **in** het deelvenster Bereik met de rechtermuisknop op **Volumes**en klik vervolgens op **Volumes opnieuw scannen**.
   
    ![Volumes opnieuw scannen](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Met deze procedure wordt de volumelijst gesynchroniseerd met StorSimple Snapshot Manager. Eventuele wijzigingen, zoals nieuwe volumes of verwijderde volumes, worden in de resultaten weergegeven.

## <a name="configure-and-back-up-a-basic-volume"></a>Een basisvolume configureren en een back-up maken
Gebruik de volgende procedure om een back-up van een basisvolume te configureren en start vervolgens onmiddellijk een back-up of maak een beleid voor geplande back-ups.

### <a name="prerequisites"></a>Vereisten
Voordat u begint:

* Zorg ervoor dat het StorSimple-apparaat en de hostcomputer correct zijn geconfigureerd. Ga voor meer informatie naar [Uw on-premises StorSimple-apparaat implementeren.](storsimple-deployment-walkthrough-u2.md)
* Installeer en configureer StorSimple Snapshot Manager. Ga voor meer informatie naar [StorSimple Snapshot Manager implementeren](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Back-up van een basisvolume configureren
1. Maak een basisvolume op het StorSimple-apparaat.
2. Monteer, initialiseer en opmaak het volume zoals beschreven in [De volumes van de berg](#mount-volumes). 
3. Klik op het pictogram StorSimple Snapshot Manager op uw bureaublad. Het venster StorSimple Snapshot Manager wordt weergegeven. 
4. Klik **in** het deelvenster Bereik met de rechtermuisknop op het knooppunt **Volumes** en selecteer **Vervolgens Volumes opnieuw scannen**. Wanneer de scan is voltooid, moet een lijst met volumes worden weergegeven in het deelvenster **Resultaten.** 
5. Klik in het deelvenster **Resultaten** met de rechtermuisknop op het volume en selecteer **Volumegroep maken**. 
   
    ![Volumegroep maken](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. Typ in het dialoogvenster **Volumegroep maken** een naam voor de volumegroep, wijs er volumes aan toe en klik op **OK**.
7. Vouw in het deelvenster **Bereik** het knooppunt **Volumegroepen** uit. De nieuwe volumegroep moet worden weergegeven onder het knooppunt **Volumegroepen.** 
8. Klik met de rechtermuisknop op de naam van de volumegroep.
   
   * Als u een interactieve (on-demand) back-uptaak wilt starten, klikt u op **Back-up maken.** 
   * Als u een automatische back-up wilt plannen, klikt u op **Back-upbeleid maken**. Selecteer **op** de pagina Algemeen een volumegroep in de lijst. Voer **op** de pagina Schema de planningsgegevens in. Klik op **OK**als u klaar bent. 
9. Als u wilt controleren of de back-uptaak is gestart, vouwt u het knooppunt **Taken** uit in het deelvenster **Bereik** en klikt u op het knooppunt **Uitvoeren.** De lijst met momenteel lopende taken wordt weergegeven in het deelvenster **Resultaten.** 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Een dynamisch gespiegeld volume configureren en een back-up maken
Voer de volgende stappen uit om een back-up van een dynamisch gespiegeld volume te configureren:

* Stap 1: Gebruik Schijfbeheer om een dynamisch gespiegeld volume te maken. 
* Stap 2: Gebruik StorSimple Snapshot Manager om back-ups te configureren.

### <a name="prerequisites"></a>Vereisten
Voordat u begint:

* Zorg ervoor dat het StorSimple-apparaat en de hostcomputer correct zijn geconfigureerd. Ga voor meer informatie naar [Uw on-premises StorSimple-apparaat implementeren.](storsimple-8000-deployment-walkthrough-u2.md)
* Installeer en configureer StorSimple Snapshot Manager. Ga voor meer informatie naar [StorSimple Snapshot Manager implementeren](storsimple-snapshot-manager-deployment.md).
* Configureer twee volumes op het StorSimple-apparaat. (In de voorbeelden zijn de beschikbare volumes **Schijf 1** en **Schijf 2**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Stap 1: Schijfbeheer gebruiken om een dynamisch gespiegeld volume te maken
Disk Management is een systeemhulpprogramma voor het beheren van harde schijven en de volumes of partities die ze bevatten. Ga voor meer informatie over Schijfbeheer naar [Schijfbeheer](https://technet.microsoft.com/library/cc770943.aspx) op de website van Microsoft TechNet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Een dynamisch gespiegeld volume maken
1. Gebruik een van de volgende opties om Schijfbeheer te starten: 
   
   * Open het vak **Uitvoeren,** typ **Diskmgmt.msc**en druk op Enter.
   * Start Serverbeheer, vouw het **opslagknooppunt** uit en selecteer **Schijfbeheer**. 
   * Gebruik **Systeembeheer,** vouw het knooppunt **Computerbeheer** uit en selecteer **Schijfbeheer**. 
2. Zorg ervoor dat u twee volumes beschikbaar hebt op het StorSimple-apparaat. (In het voorbeeld zijn de beschikbare volumes **Schijf 1** en **Schijf 2**.) 
3. Klik in het venster Schijfbeheer in de rechterkolom van het onderste deelvenster met de rechtermuisknop op **Schijf 1** en selecteer **Nieuw gespiegeld volume**. 
   
    ![Nieuw gespiegeld volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. Klik op de wizard **Nieuw gespiegeld volume** op **Volgende**.
5. Selecteer **op** de pagina Schijven selecteren **schijf 2** in het **geselecteerde** deelvenster, klik op **Toevoegen**en klik vervolgens op **Volgende**. 
6. Accepteer op de pagina **Stationsletter of Pad toewijzen** de standaardinstellingen en klik op **Volgende**. 
7. Selecteer **64K**op de pagina **Volume opmaken** in het vak **Toewijzingseenheid** . Schakel het selectievakje **Een snelle indeling uitvoeren** in en klik op **Volgende**. 
8. Controleer op de pagina Nieuw **gespiegeld volume** de instellingen en klik op **Voltooien**. 
9. Er verschijnt een bericht dat de basisschijf wordt geconverteerd naar een dynamische schijf. Klik **op Ja**.
   
    ![Dynamisch schijfconversiebericht](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. Controleer in Schijfbeheer of schijf 1 en schijf 2 worden weergegeven als dynamische gespiegelde volumes. (**Dynamisch** moet worden weergegeven in de statuskolom en de kleur van de capaciteitsbalk moet in rood veranderen, met vermelding van een gespiegeld volume.) 
    
    ![Gespiegelde dynamische schijven voor schijfbeheer](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Stap 2: StorSimple Snapshot Manager gebruiken om back-ups te configureren
Gebruik de volgende procedure om een dynamisch gespiegeld volume te configureren en start vervolgens onmiddellijk een back-up of maak een beleid voor geplande back-ups.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Back-up van een dynamisch gespiegeld volume configureren
1. Klik op het pictogram StorSimple Snapshot Manager op uw bureaublad. Het venster StorSimple Snapshot Manager wordt weergegeven. 
2. Klik **in** het deelvenster Bereik met de rechtermuisknop op het knooppunt **Volumes** en selecteer **Volumes opnieuw scannen**. Wanneer de scan is voltooid, moet een lijst met volumes worden weergegeven in het deelvenster **Resultaten.** Het dynamische gespiegelde volume wordt weergegeven als één volume. 
3. Klik in het deelvenster **Resultaten** met de rechtermuisknop op het dynamische gespiegelde volume en klik vervolgens op **Volumegroep maken**. 
4. Typ in het dialoogvenster **Volumegroep maken** een naam voor de volumegroep, wijs het dynamische gespiegelde volume toe aan deze groep en klik op **OK**. 
5. Vouw in het deelvenster **Bereik** het knooppunt **Volumegroepen** uit. De nieuwe volumegroep moet worden weergegeven onder het knooppunt **Volumegroepen.** 
6. Klik met de rechtermuisknop op de naam van de volumegroep. 
   
   * Als u een interactieve (on-demand) back-uptaak wilt starten, klikt u op **Back-up maken.** 
   * Als u een automatische back-up wilt plannen, klikt u op **Back-upbeleid maken**. Selecteer **op** de pagina Algemeen de volumegroep in de lijst. Voer **op** de pagina Schema de planningsgegevens in. Klik op **OK**als u klaar bent. 
7. U de back-uptaak tijdens het gebruik controleren. Vouw **in** het deelvenster Bereik het knooppunt **Taken** uit en klik vervolgens op **Uitvoeren**, De taakgegevens worden weergegeven in het deelvenster **Resultaten.** Wanneer de back-uptaak is voltooid, worden de details overgebracht naar de **taaklijst van 24** uur in de afgelopen uren. 

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [gebruik van StorSimple Snapshot Manager om uw StorSimple-oplossing te beheren.](storsimple-snapshot-manager-admin.md)
* Meer informatie over het [gebruik van StorSimple Snapshot Manager om volumegroepen te maken en te beheren.](storsimple-snapshot-manager-manage-volume-groups.md)

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
