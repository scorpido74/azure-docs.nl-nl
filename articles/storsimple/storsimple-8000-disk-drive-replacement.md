---
title: Een schijfstation vervangen op een apparaat uit de StorSimple 8000-serie | Microsoft Documenten
description: Hier wordt uitgelegd hoe u een schijf station vervangen op een StorSimple primaire behuizing of een EBOD-behuizing.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 8/25/2017
ms.author: alkohli
ms.openlocfilehash: 3d6ef22e4df36996d68194589f43ea0f57def22c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267909"
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>Schijfstation vervangen van apparaat uit StorSimple 8000-serie

## <a name="overview"></a>Overzicht
In deze zelfstudie wordt uitgelegd hoe u een defecte of defecte harde schijf op een Microsoft Azure StorSimple-apparaat verwijderen en vervangen. Als u een schijfwilt vervangen, moet u het:

* Schakel het antitamperslot uit
* Het schijfstation verwijderen
* Het vervangende schijfstation installeren

> [!IMPORTANT]
> Controleer de veiligheidsinformatie in [StorSimple hardwarecomponentvervanging](storsimple-8000-hardware-component-replacement.md)voordat u een schijfstation verwijdert en vervangt.
 

## <a name="disengage-the-antitamper-lock"></a>Schakel het antitamperslot uit
In deze procedure wordt uitgelegd hoe de antitampervergrendelingen op uw StorSimple-apparaat kunnen worden ingeschakeld of uitgeschakeld wanneer u de schijven vervangt. De antitamper sloten zijn gemonteerd in de drive carrier handgrepen, en ze zijn toegankelijk via een klein diafragma in de vergrendeling gedeelte van het handvat. Schijven worden geleverd met de sloten ingesteld op de vergrendelde positie.

#### <a name="to-unlock-the-antitamper-lock"></a>Het antitamperslot ontgrendelen
1. Plaats voorzichtig de slotsleutel (een "tamperproof" T10 schroevendraaier die Microsoft verstrekt) in het diafragma in het handvat en in de socket. 
   
   Als het antitamperslot is geactiveerd, is de rode indicator zichtbaar in het diafragma.
  
    ![Vergrendeld schijfstation](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **Figuur 1** Anti-sabotage slot ingeschakeld
   
   | Label | Beschrijving |
   |:--- |:--- |
   | 1 |Indicatordiafragma |
   | 2 |Antitamper slot |
2. Draai de toets tegen de klok in totdat de rode indicator niet zichtbaar is in het diafragma boven de toets.
3. Verwijder de sleutel.
   
    ![Ontgrendeld schijfstation](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **Figuur 2** Ontgrendeld schijfstation
4. De schijf drive kan nu worden verwijderd.

Volg de stappen in omgekeerde richting om het slot in te schakelen.

## <a name="remove-the-disk-drive"></a>Het schijfstation verwijderen
Uw StorSimple-apparaat ondersteunt een RAID 10-achtige configuratie van opslagruimten. Dit houdt in dat het normaal kan werken met een defecte schijf, solid-state drive (SSD) of harde schijf (HDD).

> [!IMPORTANT]
> * Als uw systeem meer dan één defecte schijf heeft, verwijdert u op geen enkel moment meer dan één SSD of HDD uit het systeem. Dit kan leiden tot verlies van gegevens.
> * Zorg ervoor dat u een vervangende SSD in een sleuf plaatst die voorheen een SSD bevatte. Plaats ook een vervangende HDD in een sleuf die voorheen een HDD bevatte.
> * In de Azure-portal worden sleuven genummerd van 0 tot 11. Als de portal aangeeft dat een schijf in sleuf 2 is mislukt, zoekt u daarom op het apparaat naar de mislukte schijf in de derde sleuf linksboven.
> 
> 

Schijven kunnen worden verwijderd en vervangen terwijl het systeem werkt.

#### <a name="to-remove-a-drive"></a>Een station verwijderen
1. Als u de mislukte schijf wilt identificeren, gaat u in de Azure-portal naar de > de status van uw **apparaatinstellingen.** Omdat een schijf kan falen in de primaire behuizing en/of in een EBOD-behuizing (als u een 8600-model gebruikt), moet u de status van de schijven bekijken onder **Gedeelde componenten** en onder **Gedeelde EBOD-componenten**. Een defecte schijf in beide behuizingen wordt weergegeven met een rode status.
2. Zoek de stations aan de voorkant van de primaire behuizing of de EBOD-behuizing. 
3. Als de schijf is ontgrendeld, gaat u verder met de volgende stap. Als de schijf is vergrendeld, ontgrendelt u deze door de procedure in [Het antitamperslot los te maken.](#disengage-the-antitamper-lock)
4. Druk op de zwarte vergrendeling op de aandrijfdragermodule en trek de handgreep van de aandrijfdrager uit en uit de buurt van de voorkant van het chassis.
   
    ![Handgreep van schijfstations vrijgeven](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **Figuur 3** Het loslaten van de schijfgreep
5. Wanneer de handgreep van de aandrijfdrager volledig is verlengd, schuift u de aandrijfdrager uit het chassis. 
   
    ![Schijf uit de schijf schuiven](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **Figuur 4** De schijfschijf uit de drager schuiven

## <a name="install-the-replacement-disk-drive"></a>Het vervangende schijfstation installeren
Nadat een station is mislukt in uw StorSimple-apparaat en u deze hebt verwijderd, volgt u deze procedure om het te vervangen door een nieuw station.

#### <a name="to-insert-a-drive"></a>Een station invoegen
1. Zorg ervoor dat de handgreep van de aandrijfdrager volledig is uitgebreid, zoals in de volgende afbeelding wordt weergegeven.
   
    ![Schijfstation met handgreep verlengd](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **Figuur 5** Aandrijving met verlengde handgreep
2. Schuif de aandrijfdrager helemaal in het chassis.
   
    ![Schuifschijf in schijfstationdrager](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **Figuur 6**  Schuif de aandrijfdrager in het chassis
3. Als de aandrijfdrager is geplaatst, sluit u de handgreep van de aandrijfdrager terwijl u de aandrijfdrager in het chassis blijft duwen, totdat de handgreep van de aandrijfdrager in een vergrendelde positie klikt.
4. Gebruik de door Microsoft geleverde slotsleutel (tamperproof Torx-schroevendraaier) om de handgreep van de drager op zijn plaats te zetten door de slotschroef een kwartslag met de klok mee te draaien.
5. Controleer of de vervanging is geslaagd en of de schijf operationeel is. Toegang tot de Azure-portal en navigeer naar de**hardwarestatus van** **apparaatinstellingen** > . Onder **Gedeelde componenten** of Gedeelde **EBOD-onderdelen**moet de status van de schijf groen zijn, wat aangeeft dat deze in orde is.

   
   > [!NOTE]
   > Het kan enkele uren duren voordat de schijfstatus na de vervanging groen wordt.
  
## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het vervangen van StorSimple-hardwarecomponenten](storsimple-8000-hardware-component-replacement.md).

