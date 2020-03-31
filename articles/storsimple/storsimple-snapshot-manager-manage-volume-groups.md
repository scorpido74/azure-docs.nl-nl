---
title: StorSimple Snapshot Manager volumegroepen | Microsoft Documenten
description: Beschrijft hoe u de MMC-module Van StorSimple Snapshot Manager gebruiken om volumegroepen te maken en te beheren.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 5198729cf96fb48c5dcd05096c04ea4d77c26de5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931498"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>StorSimple Snapshot Manager gebruiken om volumegroepen te maken en te beheren
## <a name="overview"></a>Overzicht
U het knooppunt **Volumegroepen** in het deelvenster **Bereik** gebruiken om volumes toe te wijzen aan volumegroepen, informatie over een volumegroep weer te geven, back-ups te plannen en volumegroepen te bewerken.

Volumegroepen zijn groepen met gerelateerde volumes die worden gebruikt om ervoor te zorgen dat back-ups consistent zijn. Zie [Volumes en volumegroepen en](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) Integratie met Windows Volume Shadow Copy [Service](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service)voor meer informatie.

> [!IMPORTANT]
> * Alle volumes in een volumegroep moeten afkomstig zijn van één cloudserviceprovider.
> * Wanneer u volumegroepen configureert, worden clustergedeelde volumes (CsVs) en niet-CSV's niet in dezelfde volumegroep gemengd. StorSimple Snapshot Manager ondersteunt geen mix van CSV's en niet-CSV's in dezelfde momentopname.

![Knooppunt volumegroepen](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Afbeelding 1: Knooppunt storSimple-momentopnamebeheer** 

In deze zelfstudie wordt uitgelegd hoe u StorSimple Snapshot Manager gebruiken om:

* Informatie over uw volumegroepen weergeven
* Een volumegroep maken
* Een back-up maken van een volumegroep
* Een volumegroep bewerken
* Een volumegroep verwijderen

Al deze acties zijn ook beschikbaar in het deelvenster **Handelingen.**

## <a name="view-volume-groups"></a>Volumegroepen weergeven
Als u op het knooppunt **Volumegroepen** klikt, worden in het deelvenster **Resultaten** de volgende informatie over elke volumegroep weergegeven, afhankelijk van de kolomselecties die u maakt. (De kolommen in het deelvenster **Resultaten** zijn configureerbaar. Klik met de rechtermuisknop op het knooppunt **Volumes,** selecteer **Weergave**en selecteer **Kolommen toevoegen/verwijderen**.)

| Resultatenkolom | Beschrijving |
|:--- |:--- |
| Name |De kolom **Naam** bevat de naam van de volumegroep. |
| Toepassing |In de kolom **Toepassingen** wordt het aantal VSS-schrijvers weergegeven dat momenteel is geïnstalleerd en op de Windows-host wordt uitgevoerd. |
| Geselecteerd |De **kolom Geselecteerd** geeft het aantal volumes weer dat in de volumegroep is opgenomen. Een nul (0) geeft aan dat er geen toepassing is gekoppeld aan de volumes in de volumegroep. |
| Geïmporteerd |De **kolom Geïmporteerd toont** het aantal geïmporteerde volumes. Wanneer deze kolom is ingesteld op **True,** geeft deze kolom aan dat een volumegroep is geïmporteerd uit de Azure-portal en niet is gemaakt in StorSimple Snapshot Manager. |

> [!NOTE]
> StorSimple Snapshot Manager-volumegroepen worden ook weergegeven op het tabblad **Back-upbeleid** in de Azure-portal.
> 
> 

## <a name="create-a-volume-group"></a>Een volumegroep maken
Gebruik de volgende procedure om een volumegroep te maken.

#### <a name="to-create-a-volume-group"></a>Een volumegroep maken
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
2. Klik **in** het deelvenster Bereik met de rechtermuisknop op **Volumegroepen**en klik vervolgens op **Volumegroep maken**.
   
    ![Volumegroep maken](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    Het dialoogvenster **Een volumegroep maken** wordt weergegeven.
   
    ![Een dialoogvenster volumegroep maken](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Voer de volgende informatie in:
   
   1. Typ **in** het vak Naam een unieke naam voor de nieuwe volumegroep.
   2. Selecteer **in** het vak Toepassingen toepassingen die zijn gekoppeld aan de volumes die u aan de volumegroep toevoegt.
      
       In het vak **Toepassingen** worden alleen toepassingen vermeld die StorSimple-volumes gebruiken en VSS-schrijvers voor hen hebben ingeschakeld. Een VSS schrijver is alleen ingeschakeld als alle volumes die de schrijver zich bewust is van zijn StorSimple volumes. Als het vak Toepassingen leeg is, worden er geen toepassingen geïnstalleerd die Azure StorSimple-volumes gebruiken en ondersteunde VSS-schrijvers hebben geïnstalleerd. (Momenteel ondersteunt Azure StorSimple Microsoft Exchange en SQL Server.) Zie [Integratie met Windows Volume Shadow Copy Service](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service)voor meer informatie over VSS-schrijvers.
      
       Als u een toepassing selecteert, worden alle volumes die eraan zijn gekoppeld, automatisch geselecteerd. Als u daarentegen volumes selecteert die zijn gekoppeld aan een specifieke toepassing, wordt de toepassing automatisch geselecteerd in het vak **Toepassingen.** 
   3. Selecteer **in** het vak Volumes de optie StorSimple-volumes die u wilt toevoegen aan de volumegroep. 
      
      * U volumes opnemen met enkele of meerdere partities. (Meerdere partitievolumes kunnen dynamische schijven of basisschijven met meerdere partities zijn.) Een volume dat meerdere partities bevat, wordt behandeld als één eenheid. Als u dus slechts één van de partities aan een volumegroep toevoegt, worden alle andere partities tegelijkertijd automatisch aan die volumegroep toegevoegd. Nadat u een meervoudig partitievolume aan een volumegroep hebt toegevoegd, wordt het volume van de meervoudige partitie nog steeds als één eenheid behandeld.
      * U lege volumegroepen maken door er geen volumes aan toe te wijsen. 
      * Meng geen clustergedeelde volumes (CSV's) en niet-CSV's in dezelfde volumegroep. StorSimple Snapshot Manager ondersteunt geen mix van CSV-volumes en niet-CSV-volumes in dezelfde momentopname.
4. Klik op **OK** om de volumegroep op te slaan.

## <a name="back-up-a-volume-group"></a>Een back-up maken van een volumegroep
Gebruik de volgende procedure om een back-up van een volumegroep te maken.

#### <a name="to-back-up-a-volume-group"></a>Een back-up maken van een volumegroep
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
2. Vouw **in** het deelvenster Bereik het knooppunt **Volumegroepen** uit, klik met de rechtermuisknop op een naam van een volumegroep en klik vervolgens op **Back-up maken**.
   
    ![Direct een back-up van volumegroep maken](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. Selecteer in het dialoogvenster **Back-up maken** de optie **Lokale momentopname** of **Cloudmomentopname**en klik vervolgens op **Maken**.
   
    ![Dialoogvenster Back-up maken](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Als u wilt bevestigen dat de back-up wordt uitgevoerd, vouwt u het knooppunt **Taken** uit en klikt u op **Uitvoeren**. De back-up moet worden vermeld.
5. Als u de voltooide momentopname wilt weergeven, vouwt u het knooppunt **Back-upcatalogus** uit, vouwt u de naam van de volumegroep uit en klikt u vervolgens op **Lokale momentopname of** Momentopname van de **cloud.** De back-up wordt weergegeven als deze is voltooid.

## <a name="edit-a-volume-group"></a>Een volumegroep bewerken
Gebruik de volgende procedure om een volumegroep te bewerken.

#### <a name="to-edit-a-volume-group"></a>Een volumegroep bewerken
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
2. Vouw **in** het deelvenster Bereik het knooppunt **Volumegroepen** uit, klik met de rechtermuisknop op een naam van een volumegroep en klik vervolgens op **Bewerken**.
3. Het dialoogvenster **Een volumegroep maken **wordt weergegeven. U de **vermeldingen Naam**, **Toepassingen**en **Volumes** wijzigen.
4. Klik op **OK** om uw wijzigingen op te slaan.

## <a name="delete-a-volume-group"></a>Een volumegroep verwijderen
Gebruik de volgende procedure om een volumegroep te verwijderen. 

> [!WARNING]
> Hiermee worden ook alle back-ups verwijderd die zijn gekoppeld aan de volumegroep.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Een volumegroep verwijderen
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
2. Vouw **in** het deelvenster Bereik het knooppunt **Volumegroepen** uit, klik met de rechtermuisknop op een naam van een volumegroep en klik vervolgens op **Verwijderen**.
3. Het dialoogvenster **Volumegroep verwijderen** wordt weergegeven. Typ **Bevestigen** in het tekstvak en klik op **OK**.
   
    De verwijderde volumegroep verdwijnt uit de lijst in het deelvenster **Resultaten** en alle back-ups die aan die volumegroep zijn gekoppeld, worden uit de back-upcatalogus verwijderd.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [gebruik van StorSimple Snapshot Manager om uw StorSimple-oplossing te beheren.](storsimple-snapshot-manager-admin.md)
* Meer informatie over het [gebruik van StorSimple Snapshot Manager om back-upbeleid te maken en te beheren.](storsimple-snapshot-manager-manage-backup-policies.md)

