---
title: StorSimple Snapshot Manager implementeren | Microsoft Documenten
description: Meer informatie over het downloaden en installeren van de StorSimple Snapshot Manager, een MMC-module voor het beheren van StorSimple-gegevensbescherming en back-upfuncties.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: f0128f57-519e-49ec-9187-23575809cdbe
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 6d3e3d6cdf7a831bf09d9c4709c1a60d27683438
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933369"
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>De Module StorSimple Snapshot Manager implementeren

## <a name="overview"></a>Overzicht
De StorSimple Snapshot Manager is een MMC-module (Microsoft Management Console) die gegevensbescherming en back-upbeheer in een Microsoft Azure StorSimple-omgeving vereenvoudigt. Met StorSimple Snapshot Manager u Microsoft Azure StorSimple on-premises en cloudopslag beheren alsof het een volledig geïntegreerd opslagsysteem is, waardoor back-up- en herstelprocessen worden vereenvoudigd en de kosten worden verlaagd. 

In deze zelfstudie worden configuratievereisten beschreven, evenals procedures voor het installeren, verwijderen en upgraden van StorSimple Snapshot Manager.

> [!NOTE]
> * U StorSimple Snapshot Manager niet gebruiken om Microsoft Azure StorSimple Virtual Arrays (ook bekend als StorSimple on-premises virtuele apparaten) te beheren.
> * Als u van plan bent StorSimple Update 2 op uw StorSimple-apparaat te installeren, moet u de nieuwste versie van StorSimple Snapshot Manager downloaden en installeren **voordat u StorSimple Update 2 installeert.** De nieuwste versie van StorSimple Snapshot Manager is achterwaarts compatibel en werkt met alle vrijgegeven versies van Microsoft Azure StorSimple. Als u de vorige versie van StorSimple Snapshot Manager gebruikt, moet u deze bijwerken (u hoeft de vorige versie niet te verwijderen voordat u de nieuwe versie installeert).


## <a name="storsimple-snapshot-manager-installation"></a>StorSimple Snapshot Manager installatie
StorSimple Snapshot Manager kan worden geïnstalleerd op computers waarop het Windows Server 2008 R2 SP1-, Windows Server 2012- of Windows Server 2012 R2-besturingssysteem wordt uitgevoerd. Op servers met Windows 2008 R2 moet u ook Windows Server 2008 SP1 en Windows Management Framework 3.0 installeren.

Voordat u de module StorSimple Snapshot Manager voor de Microsoft Management Console (MMC) installeert of upgradet, moet u ervoor zorgen dat het Microsoft Azure StorSimple-apparaat en de hostserver correct zijn geconfigureerd.

## <a name="configure-prerequisites"></a>Vereisten configureren
De volgende stappen bieden een overzicht op hoog niveau van configuratietaken die u moet voltooien voordat u de StorSimple Snapshot Manager installeert. Zie [Uw on-premises StorSimple-apparaat implementeren](storsimple-8000-deployment-walkthrough-u2.md)voor volledige configuratie- en installatiegegevens van Microsoft Azure, inclusief systeemvereisten en stapsgewijze instructies.

> [!IMPORTANT]
> Voordat u begint, controleert u de [checklist voor implementatieconfiguratie](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) en [implementatievereisten](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites) in [Uw on-premises StorSimple-apparaat implementeren.](storsimple-8000-deployment-walkthrough-u2.md)
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>Voordat u StorSimple Snapshot Manager installeert
1. Pak het Microsoft Azure StorSimple-apparaat uit, monteer en sluit deze aan zoals beschreven in [Het installeren van uw StorSimple 8100-apparaat](storsimple-8100-hardware-installation.md) of installeer uw [StorSimple 8600-apparaat.](storsimple-8600-hardware-installation.md)
2. Controleer of op uw hostcomputer een van de volgende besturingssystemen wordt uitgevoerd:
   
   * Windows Server 2008 R2 (op servers met Windows 2008 R2 moet u ook Windows Server 2008 SP1 en Windows Management Framework 3.0 installeren)
   * Windows Server 2012
   * Windows Server 2012 R2
     
     Voor een virtueel StorSimple-apparaat moet de host een Virtuele Microsoft Azure-machine zijn.
3. Zorg ervoor dat u voldoet aan alle Microsoft Azure StorSimple-configuratievereisten. Ga voor meer informatie naar [Implementatievereisten](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites).
4. Sluit het apparaat aan op de host en voer de eerste configuratie uit. Ga voor meer informatie naar [Implementatiestappen](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps).
5. Maak volumes op het apparaat, wijs ze toe aan de host en controleer of de host ze kan monteren en gebruiken. StorSimple Snapshot Manager ondersteunt de volgende typen volumes:
   
   * Basisvolumes
   * Eenvoudige volumes
   * Dynamische volumes
   * Gespiegelde dynamische volumes (RAID 1)
   * Gedeelde volumes voor clusterdelen
     
     Ga voor informatie over het maken van volumes op het StorSimple-apparaat of het virtuele StorSimple-apparaat naar [Stap 6: Een volume maken](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume)in Uw [on-premises StorSimple-apparaat implementeren.](storsimple-8000-deployment-walkthrough-u2.md)

## <a name="install-a-new-storsimple-snapshot-manager"></a>Installeer een nieuwe StorSimple Snapshot Manager
Voordat u StorSimple Snapshot Manager installeert, moet u ervoor zorgen dat de volumes die u hebt gemaakt op het StorSimple-apparaat of het virtuele storsimple-apparaat zijn gemonteerd, geïnitialiseerd en opgemaakt zoals beschreven in [Vereisten configureren.](#configure-prerequisites)

> [!IMPORTANT]
> * Voor een virtueel StorSimple-apparaat moet de host een Virtuele Microsoft Azure-machine zijn. 
> * De host moet Windows 2008 R2, Windows Server 2012 of Windows Server 2012 R2 gebruiken. Als op uw server Windows Server 2008 R2 wordt uitgevoerd, moet u ook Windows Server 2008 SP1 en Windows Management Framework 3.0 installeren.
> * U moet een iSCSI-verbinding configureren van de host met het StorSimple-apparaat voordat u het apparaat verbinden met StorSimple Snapshot Manager.

Volg deze stappen om een nieuwe installatie van StorSimple Snapshot Manager te voltooien. Als u een upgrade installeert, gaat u naar [StorSimple Snapshot Manager upgraden of opnieuw installeren.](#upgrade-or-reinstall-storsimple-snapshot-manager)

* Stap 1: Installeer StorSimple Snapshot Manager 
* Stap 2: StorSimple Snapshot Manager verbinden met een apparaat 
* Stap 3: De verbinding met het apparaat verifiëren 

### <a name="step-1-install-storsimple-snapshot-manager"></a>Stap 1: Installeer StorSimple Snapshot Manager
Gebruik de volgende stappen om StorSimple Snapshot Manager te installeren.

#### <a name="to-install-storsimple-snapshot-manager"></a>StorSimple Snapshot Manager installeren
1. Download de StorSimple Snapshot Manager-software (ga naar [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) in het Microsoft Download Center) en sla deze lokaal op de host op.
2. Klik in Verkenner met de rechtermuisknop op de gecomprimeerde map en klik vervolgens op **Alles uitpakken**.
3. Typ of blader in het venster **Gecomprimeerde mappen (Gezipte) mappen** in het vak **Een bestemming selecteren en bestanden extraheren,** typ of blader naar het pad waar u wilt bestand en wilt worden geëxtraheerd.
   
    > [!IMPORTANT]
    > U moet StorSimple Snapshot Manager installeren op de C: drive.
    
4. Schakel het selectievakje **Uitgepakte bestanden weergeven wanneer voltooid** is in en klik op **Uitpakken**.
   
    ![Dialoogvenster Bestanden extraheren](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. Wanneer de extractie is voltooid, wordt de doelmap geopend. Dubbelklik op het pictogram voor het instellen van toepassingen dat wordt weergegeven in de doelmap.
6. Wanneer het bericht **Setup Succesvol** wordt weergegeven, klikt u op **Sluiten**. U ziet het pictogram StorSimple Snapshot Manager op uw bureaublad.
   
    ![bureaubladpictogram](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Stap 2: StorSimple Snapshot Manager verbinden met een apparaat
Gebruik de volgende stappen om StorSimple Snapshot Manager aan te sluiten op een StorSimple-apparaat.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>StorSimple Snapshot Manager verbinden met een apparaat
1. Klik op het pictogram StorSimple Snapshot Manager op uw bureaublad. Het venster StorSimple Snapshot Manager wordt weergegeven. Het venster bevat een **deelvenster Bereik,** een deelvenster **Resultaten** en een **deelvenster Handelingen.** 
   
    ![StorSimple Snapshot Manager-gebruikersinterface](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * Het deelvenster **Bereik** (het linkerdeelvenster) bevat een lijst met knooppunten die zijn georganiseerd in een structuur structuur van een boom. U sommige knooppunten uitbreiden om een weergave of specifieke gegevens met betrekking tot dat knooppunt te selecteren. Klik op het pijlpictogram om een knooppunt uit te vouwen of samen te vouwen. Klik met de rechtermuisknop op een item in het deelvenster **Bereik** om een lijst met beschikbare acties voor dat item weer te geven.
   * Het deelvenster **Resultaten** (het middelste deelvenster) bevat gedetailleerde statusgegevens over het knooppunt, de weergave of de gegevens die u hebt geselecteerd in het deelvenster **Bereik.**
   * In het deelvenster **Handelingen** worden de bewerkingen weergegeven die u uitvoeren op het knooppunt, de weergave of de gegevens die u hebt geselecteerd in het deelvenster **Bereik.**
     
     Zie de gebruikersinterface van [StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md)voor een volledige beschrijving van de gebruikersinterface van StorSimple Snapshot Manager.
2. Klik **in** het deelvenster Bereik met de rechtermuisknop op het knooppunt **Apparaten** en klik vervolgens op **Een apparaat configureren**. Het dialoogvenster **Een apparaat configureren** wordt weergegeven.
   
    ![Een apparaat configureren](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. Selecteer in het keuzevak **Apparaat** het IP-adres van het Microsoft Azure StorSimple-apparaat of virtueel apparaat. Typ in het tekstvak **Wachtwoord** het wachtwoord Van Eenvoudige Momentopnamebeheer dat u voor het apparaat in de Azure-portal hebt gemaakt. Klik op **OK**.
4. StorSimple Snapshot Manager zoekt naar het apparaat dat u hebt geïdentificeerd. Als het apparaat beschikbaar is, voegt StorSimple Snapshot Manager een verbinding toe. U [de verbinding met het apparaat verifiëren](#to-verify-the-connection) om te controleren of de verbinding is toegevoegd.
   
    Als het apparaat om welke reden dan ook niet beschikbaar is, retourneert StorSimple Snapshot Manager een foutbericht. Klik op **OK** om het foutbericht te sluiten en klik vervolgens op **Annuleren** om het dialoogvenster **Een apparaat configureren** te sluiten.
5. Wanneer het verbinding maakt met een apparaat, importeert StorSimple Snapshot Manager elke volumegroep die voor dat apparaat is geconfigureerd, op voorwaarde dat de volumegroep bijbehorende back-ups heeft. Volumegroepen die geen bijbehorende back-ups hebben, worden niet geïmporteerd. Bovendien worden back-upbeleid dat is gemaakt voor een volumegroep niet geïmporteerd. Als u de geïmporteerde groepen wilt weergeven, klikt u met de rechtermuisknop op het bovenste **knooppunt Van de meeste volumegroepen** in het deelvenster **Bereik** en klikt u op **Geïmporteerde groepen inschakelen**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Stap 3: De verbinding met het apparaat verifiëren
Gebruik de volgende stappen om te controleren of StorSimple Snapshot Manager is verbonden met het StorSimple-apparaat.

#### <a name="to-verify-the-connection"></a>De verbinding verifiëren
1. Klik **in** het deelvenster Bereik op het knooppunt **Apparaten.**
   
    ![De status van storSimple Snapshot Manager-apparaat](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. Controleer het deelvenster **Resultaten:** 
   
   * Als er een groene indicator wordt weergegeven op het apparaatpictogram en **Beschikbaar** wordt weergegeven in de kolom **Status,** wordt het apparaat verbonden. 
   * Als er een rode indicator wordt weergegeven op het apparaatpictogram en Niet beschikbaar wordt weergegeven in de kolom **Status,** is het apparaat niet verbonden. 
   * Als **Vernieuwen** wordt weergegeven in de kolom **Status,** haalt StorSimple Snapshot Manager volumegroepen en bijbehorende back-ups voor een verbonden apparaat op.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>StorSimple Snapshot Manager upgraden of opnieuw installeren
U moet StorSimple Snapshot Manager volledig verwijderen voordat u de software upgradet of opnieuw installeert. 

Voordat u StorSimple Snapshot Manager opnieuw installeert, maakt u een back-up van de bestaande StorSimple Snapshot Manager-database op de hostcomputer. Hierdoor worden het back-upbeleid en de configuratiegegevens opgeslagen, zodat u deze gegevens eenvoudig herstellen van back-ups.

Volg deze stappen als u StorSimple Snapshot Manager upgradet of opnieuw installeert:

* Stap 1: StorSimple Snapshot Manager verwijderen 
* Stap 2: Een back-up maken van de StorSimple Snapshot Manager-database 
* Stap 3: StorSimple Snapshot Manager opnieuw installeren en de database herstellen 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Stap 1: StorSimple Snapshot Manager verwijderen
Gebruik de volgende stappen om StorSimple Snapshot Manager te verwijderen.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>StorSimple Snapshot Manager verwijderen
1. Open op de hostcomputer het **Configuratiescherm,** klik op **Programma's**en klik vervolgens op **Programma's en onderdelen**.
2. Klik in het linkerdeelvenster op **Een programma verwijderen of wijzigen.**
3. Klik met de rechtermuisknop op **StorSimple Snapshot Manager**en klik vervolgens op **Verwijderen**.
4. Hiermee wordt het StorSimple Snapshot Manager Setup-programma gestart. Klik **op Setup wijzigen**en klik vervolgens op **Verwijderen**.
   
   > [!NOTE]
   > Als er MMC-processen op de achtergrond worden uitgevoerd, zoals StorSimple Snapshot Manager of Schijfbeheer, mislukt de verwijderen en ontvangt u een bericht om alle exemplaren van MMC te sluiten voordat u probeert het programma te verwijderen. Selecteer **Toepassingen automatisch sluiten en probeer ze opnieuw op te starten nadat de installatie is voltooid**en klik op **OK**.
   > 
   > 
5. Wanneer het verwijderen is voltooid, wordt een bericht **Met installatie** weergegeven. Klik op **Sluiten**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Stap 2: Een back-up maken van de StorSimple Snapshot Manager-database
Gebruik de volgende stappen om een kopie van de StorSimple Snapshot Manager-database te maken en op te slaan.

#### <a name="to-back-up-the-database"></a>Een back-up van de database maken
1. Stop de Microsoft StorSimple-beheerservice:
   
   1. Start Serverbeheer.
   2. Selecteer Services in het dashboard Serverbeheer in **het** menu **Extra**.
   3. Selecteer op de pagina **Services** de optie **Microsoft StorSimple Management Service**.
   4. Klik in het rechterdeelvenster onder **Microsoft StorSimple Management Service**op De service **stoppen**.
      
        ![De StorSimple Device Manager-service stoppen](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. Blader naar C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData is een verborgen map.
  
3. Zoek het XML-bestand van de catalogus, kopieer het bestand en sla de kopie op een veilige locatie of in de cloud op.
   
    ![StorSimple back-upcatalogusbestand](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Start de Microsoft StorSimple-beheerservice opnieuw: 
   
   1. Selecteer Services in het dashboard Serverbeheer in **het** menu **Extra**.
   2. Selecteer **op** de pagina Services de **Microsoft StorSimple Management Service**.
   3. Klik in het rechterdeelvenster onder **Microsoft StorSimple Management Service**op De service opnieuw **starten**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Stap 3: StorSimple Snapshot Manager opnieuw installeren en de database herstellen
Als u StorSimple Snapshot Manager opnieuw wilt installeren, voert u de stappen uit [in Het installeren van een nieuwe StorSimple Snapshot Manager](#install-a-new-storsimple-snapshot-manager). Gebruik vervolgens de volgende procedure om de StorSimple Snapshot Manager-database te herstellen.

#### <a name="to-restore-the-database"></a>De database herstellen
1. Stop de Microsoft StorSimple-beheerservice:
   
   1. Start Serverbeheer.
   2. Selecteer Services in het dashboard Serverbeheer in **het** menu **Extra**.
   3. Selecteer op de pagina **Services** de optie **Microsoft StorSimple Management Service**.
   4. Klik in het rechterdeelvenster onder **Microsoft StorSimple Management Service**op De service **stoppen**.
2. Blader naar C:\ProgramData\Microsoft\StorSimple\BACatalog.
   
   > [!NOTE]
   > ProgramData is een verborgen map.
   > 
   > 
3. Verwijder het XML-bestand van de catalogus en vervang het door de versie die u eerder hebt opgeslagen.
4. Start de Microsoft StorSimple-beheerservice opnieuw: 
   
   1. Selecteer Services in het dashboard Serverbeheer in **het** menu **Extra**.
   2. Selecteer op de pagina **Services** de optie **Microsoft StorSimple Management Service**.
   3. Klik in het rechterdeelvenster onder **Microsoft StorSimple Management Service**op De service opnieuw **starten**.

## <a name="next-steps"></a>Volgende stappen
* Ga voor meer informatie over StorSimple Snapshot Manager naar [Wat is StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md).
* Ga voor meer informatie over de gebruikersinterface van StorSimple Snapshot Manager naar [de gebruikersinterface van StorSimple Snapshot Manager.](storsimple-use-snapshot-manager.md)
* Ga voor meer informatie over het gebruik van StorSimple Snapshot Manager naar [StorSimple Snapshot Manager gebruiken om uw StorSimple-oplossing te beheren.](storsimple-snapshot-manager-admin.md)

