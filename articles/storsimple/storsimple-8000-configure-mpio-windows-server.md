---
title: MPIO configureren voor uw StorSimple-apparaat | Microsoft Documenten
description: Beschrijft hoe u Multipath I/O (MPIO) configureert voor uw StorSimple-apparaat dat is aangesloten op een host met Windows Server 2012 R2.
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
ms.workload: NA
ms.date: 03/26/2018
ms.author: alkohli
ms.openlocfilehash: eda134257edb851eea076459b44e02fc59028f46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60363247"
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>Multipath I/O configureren voor uw StorSimple-apparaat

In deze zelfstudie worden de stappen beschreven die u moet volgen om de Multipath I/O-functie (MPIO) te installeren en te gebruiken op een host met Windows Server 2012 R2 en is aangesloten op een fysiek apparaat van StorSimple. De begeleiding in dit artikel is alleen van toepassing op fysieke apparaten uit de StorSimple 8000-serie. MPIO wordt momenteel niet ondersteund op een StorSimple Cloud Appliance.

Microsoft heeft ondersteuning voor de Multipath I/O -functie (MPIO) in Windows Server gebouwd om zeer beschikbare, fouttolerante iSCSI-netwerkconfiguraties te bouwen. MPIO maakt gebruik van redundante fysieke padcomponenten - adapters, kabels en switches - om logische paden te maken tussen de server en het opslagapparaat. Als er een componentfout optreedt, waardoor een logisch pad mislukt, gebruikt multipathing-logica een alternatief pad voor I/O, zodat toepassingen nog steeds toegang hebben tot hun gegevens. Bovendien kan MPIO, afhankelijk van uw configuratie, ook de prestaties verbeteren door de belasting over al deze paden opnieuw in evenwicht te brengen. Zie [MPIO-overzicht](https://technet.microsoft.com/library/cc725907.aspx "MPIO overzicht en functies")voor meer informatie.

Voor de hoge beschikbaarheid van uw StorSimple-oplossing moet MPIO worden geconfigureerd op uw StorSimple-apparaat. Wanneer MPIO is geïnstalleerd op uw hostservers met Windows Server 2012 R2, kunnen de servers vervolgens een koppelings-, netwerk- of interfacefout verdragen.

## <a name="mpio-configuration-summary"></a>MPIO-configuratieoverzicht

MPIO is een optionele functie op Windows Server en is niet standaard geïnstalleerd. Het moet als een functie via Serverbeheer worden geïnstalleerd.

Volg de volgende stappen om MPIO te configureren op uw StorSimple-apparaat:

* Stap 1: MPIO installeren op de Windows Server-host
* Stap 2: MPIO configureren voor StorSimple-volumes
* Stap 3: Mount StorSimple volumes op de host
* Stap 4: MPIO configureren voor hoge beschikbaarheid en taakverdeling

Elk van de voorgaande stappen wordt besproken in de volgende secties.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Stap 1: MPIO installeren op de Windows Server-host

Voer de volgende procedure in om deze functie op uw Windows Server-host te installeren.

#### <a name="to-install-mpio-on-the-host"></a>MPIO installeren op de host

1. Open Serverbeheer op uw Windows Server-host. Standaard wordt Serverbeheer gestart wanneer een lid van de groep Administrators zich aanmeldt bij een computer waarop Windows Server 2012 R2 of Windows Server 2012 wordt uitgevoerd. Als Serverbeheer nog niet is geopend, klikt u op **Start > Serverbeheer**.
   
   ![Serverbeheer](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. Klik **op Serverbeheer > Dashboard > Rollen en functies toevoegen**. Hiermee wordt de wizard **Rollen en onderdelen toevoegen** gestart.
   
   ![Wizard Rollen en functies toevoegen 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. Voer in de wizard **Rollen en onderdelen toevoegen** de volgende stappen uit:
   
   1. Klik op de pagina **Voordat u begint** op **Volgende**.
   2. Accepteer op de pagina **Installatietype selecteren** de standaardinstelling voor installatie op basis van **rollen of functies.** Klik op **Volgende**.
   
       ![Wizard Rollen en functies toevoegen 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. Kies op de pagina **Doelserver selecteren** de optie **Een server selecteren in de servergroep**. Uw hostserver moet automatisch worden ontdekt. Klik op **Volgende**.
   4. Klik op de pagina **Serverfuncties selecteren** op **Volgende**.
   5. Selecteer **op** de pagina Onderdelen selecteren de optie **Multipath I/O**en klik op **Volgende**.
   
       ![Wizard Rollen en functies toevoegen 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. Bevestig op de pagina **Installatieselecties bevestigen** de selectie en selecteer **de doelserver automatisch opnieuw starten indien nodig,** zoals hieronder wordt weergegeven. Klik **op Installeren**.
   
       ![Wizard Rollen en functies toevoegen 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. U wordt op de hoogte gesteld wanneer de installatie is voltooid. Klik op **Sluiten** om de wizard te sluiten.
   
       ![Wizard Rollen en functies toevoegen 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Stap 2: MPIO configureren voor StorSimple-volumes

MPIO moet zijn geconfigureerd om StorSimple-volumes te identificeren. Voer de volgende stappen uit om MPIO te configureren om StorSimple-volumes te herkennen.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>MPIO configureren voor StorSimple-volumes

1. Open de **MPIO-configuratie**. Klik op **Serverbeheer > Dashboard >-hulpprogramma's > MPIO**.
2. Selecteer in het dialoogvenster **MPIO-eigenschappen** het tabblad **Meerdere paden ontdekken.**
3. Selecteer **Ondersteuning toevoegen voor iSCSI-apparaten**en klik op **Toevoegen**.  
   ![MPIO-eigenschappen Ontdek meerdere paden](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. Start de server opnieuw op wanneer daarom wordt gevraagd.
5. Klik in het dialoogvenster **MPIO-eigenschappen** op het tabblad **MPIO-apparaten.** Klik op **Toevoegen**.
    </br>![MPIO Properties MPIO-apparaten](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. Voer in het dialoogvenster **MPIO-ondersteuning toevoegen** onder **Device Hardware ID**het serienummer van uw apparaat in. Als u het serienummer van het apparaat wilt ontvangen, krijgt u toegang tot uw StorSimple Device Manager-service. Navigeer naar **apparaten > dashboard**. Het serienummer van het apparaat wordt weergegeven in het rechter **deelvenster Snelle blik** van het dashboard van het apparaat.
    </br>
    ![MPIO-ondersteuning toevoegen](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Start de server opnieuw op wanneer daarom wordt gevraagd.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Stap 3: Mount StorSimple volumes op de host

Nadat MPIO is geconfigureerd op Windows Server, kan volume(s) die op het StorSimple-apparaat zijn gemaakt, worden gemonteerd en vervolgens profiteren van MPIO voor redundantie. Als u een volume wilt monteren, voert u de volgende stappen uit.

#### <a name="to-mount-volumes-on-the-host"></a>Volumes op de host monteren

1. Open het venster **ISCSI-initiatoreigenschappen** op de Windows Server-host. Klik op **Serverbeheer > Dashboard > Tools > iSCSI Initiator**.
2. Klik in het dialoogvenster **ISCSI-initiatoreigenschappen** op het tabblad Detectie en klik vervolgens op **Doelportal ontdekken**.
3. Voer in het dialoogvenster **Doelportal ontdekken** de volgende stappen uit:
   
   1. Voer het IP-adres van de DATA-poort van uw StorSimple-apparaat in (voer bijvoorbeeld GEGEVENS 0 in).
   2. Klik op **OK** om terug te keren naar het dialoogvenster **ISCSI-initiatoreigenschappen.**
     
      > [!IMPORTANT]
      > **Als u een privénetwerk gebruikt voor iSCSI-verbindingen, voert u het IP-adres in van de gegevenspoort die is verbonden met het privénetwerk.**
    
4. Herhaal stap 2-3 voor een tweede netwerkinterface (bijvoorbeeld DATA 1) op uw apparaat. Houd er rekening mee dat deze interfaces moeten zijn ingeschakeld voor iSCSI. Zie [Netwerkinterfaces wijzigen voor](storsimple-8000-modify-device-config.md#modify-network-interfaces)meer informatie.
5. Selecteer het tabblad **Doelen** in het dialoogvenster **ISCSI-initiatoreigenschappen.** U moet het StorSimple-apparaatdoel IQN onder **Gedetecteerde doelen bekijken.**

   ![Tabblad Eigenschappen van iSCSI-initiators](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. Klik **op Verbinding maken** om een iSCSI-sessie in te stellen met uw StorSimple-apparaat. Er wordt een dialoogvenster **Verbinding maken met doel** weergegeven.
7. Schakel in het dialoogvenster **Verbinding maken met doel** het selectievakje Meerdere paden **inschakelen** in. Klik op **Geavanceerd**.
8. Voer in het dialoogvenster **Geavanceerde instellingen** de volgende stappen uit:
   
   1. Selecteer **Microsoft iSCSI Initiator**in de vervolgkeuzelijst **Lokale adapter** .
   2. Selecteer in de vervolgkeuzelijst **Initiator IP** het IP-adres van de host.
   3. Selecteer in de vervolgkeuzelijst **Target Portal** IP het IP-adres van de apparaatinterface.
   4. Klik op **OK** om terug te keren naar het dialoogvenster **ISCSI-initiatoreigenschappen.**
9. Klik op **Eigenschappen**. Klik in het dialoogvenster **Eigenschappen** op **Sessie toevoegen**.
10. Schakel in het dialoogvenster **Verbinding maken met doel** het selectievakje Meerdere paden **inschakelen** in. Klik op **Geavanceerd**.
11. Ga als een in het dialoogvenster **Geavanceerde instellingen:**

    1. Selecteer Microsoft iSCSI Initiator in de vervolgkeuzelijst **Lokale adapter.**
    2. Selecteer in de vervolgkeuzelijst **Initiator IP** het IP-adres dat overeenkomt met de host. In dit geval verbindt u twee netwerkinterfaces op het apparaat met één netwerkinterface op de host. Daarom is deze interface hetzelfde als die voor de eerste sessie.
    3. Selecteer in de vervolgkeuzelijst **Target Portal IP** het IP-adres voor de tweede gegevensinterface die op het apparaat is ingeschakeld.
    4. Klik op **OK** om terug te keren naar het dialoogvenster ISCSI-initiatoreigenschappen. U hebt een tweede sessie aan het doel toegevoegd.
12. Open **Computerbeheer** door te navigeren naar **Serverbeheer > Dashboard > Computermanagement**. Klik in het linkerdeelvenster op **Opslag > Schijfbeheer**. Het volume dat is gemaakt op het StorSimple-apparaat dat zichtbaar is voor deze host, wordt onder **Schijfbeheer** weergegeven als nieuwe schijf(en).
13. Initialiseer de schijf en maak een nieuw volume. Selecteer tijdens het opmaakproces een blokgrootte van 64 KB.
    
    ![Schijfbeheer](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. Klik **onder Schijfbeheer**met **de** rechtermuisknop op schijf en selecteer **Eigenschappen**.
15. Klik in het dialoogvenster StorSimple Model #### **Eigenschappen van apparaateigenschappen voor meerdere paden** op het tabblad **MPIO.**
    
    ![StorSimple 8100 Multi-Path Disk DeviceProp.](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. Klik in de sectie **DSM-naam** op **Details** en controleer of de parameters zijn ingesteld op de standaardparameters. De standaardparameters zijn:
    
    * Padverificatieperiode = 30
    * Telling opnieuw proberen = 3
    * BOB Verwijderperiode = 20
    * Interval opnieuw proberen = 1
    * Path Verify Enabled = Unchecked.

> [!NOTE]
> **Wijzig de standaardparameters niet.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Stap 4: MPIO configureren voor hoge beschikbaarheid en taakverdeling

Voor op meerdere paden gebaseerde hoge beschikbaarheid en taakverdeling moeten meerdere sessies handmatig worden toegevoegd om de verschillende beschikbare paden te declareren. Als de host bijvoorbeeld twee interfaces heeft die zijn verbonden met het iSCSI-netwerk en het apparaat twee interfaces heeft die zijn aangesloten op het iSCSI-netwerk, hebt u vier sessies nodig die zijn geconfigureerd met de juiste padpermutaties (er zijn slechts twee sessies vereist als elke DATA-interface en host interface is op een ander IP-subnet en is niet routeerbaar).

**We raden u aan ten minste 8 actieve parallelle sessies te hebben tussen het apparaat en uw toepassingshost.** Dit kan worden bereikt door 4 netwerkinterfaces op uw Windows Server-systeem in te schakelen. Gebruik fysieke netwerkinterfaces of virtuele interfaces via netwerkvirtualisatietechnologieën op hardware- of besturingssysteemniveau op uw Windows Server-host. Met de twee netwerkinterfaces op het apparaat zou deze configuratie resulteren in 8 actieve sessies. Deze configuratie helpt bij het optimaliseren van de apparaat- en clouddoorvoer.

> [!IMPORTANT]
> **Wij raden u aan geen 1 GbE- en 10 GbE-netwerkinterfaces te mixen. Als u twee netwerkinterfaces gebruikt, moeten beide interfaces van een identiek type zijn.**

In de volgende procedure wordt beschreven hoe u sessies toevoegt wanneer een StorSimple-apparaat met twee netwerkinterfaces is verbonden met een host met twee netwerkinterfaces. Dit geeft je slechts 4 sessies. Gebruik deze zelfde procedure met een StorSimple-apparaat met twee netwerkinterfaces die zijn aangesloten op een host met vier netwerkinterfaces. U moet 8 configureren in plaats van de 4 sessies die hier worden beschreven.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>MPIO configureren voor hoge beschikbaarheid en taakverdeling

1. Voer een detectie van het doel uit: klik in het dialoogvenster **iSCSI-initiatoreigenschappen** op het tabblad **Detectie** op **Portal ontdekken**.
2. Voer in het dialoogvenster **Verbinding maken met target** het IP-adres van een van de netwerkinterfaces van het apparaat in.
3. Klik op **OK** om terug te keren naar het dialoogvenster **ISCSI-initiatoreigenschappen.**
4. Selecteer in het dialoogvenster **ISCSI-initiatoreigenschappen** het tabblad **Doelen,** markeer het gedetecteerde doel en klik vervolgens op **Verbinden**. Het dialoogvenster **Verbinding maken met doel** wordt weergegeven.
5. Ga als een van de **eersten naar target:**
   
   1. Laat de standaardgeselecteerde doelinstelling voor **Deze verbinding toevoegen** aan de lijst met favoriete doelen. Hierdoor probeert het apparaat automatisch de verbinding opnieuw op te starten telkens wanneer deze computer opnieuw wordt opgestart.
   2. Schakel het selectievakje **Meerdere paden inschakelen** in.
   3. Klik op **Geavanceerd**.
6. Ga als een in het dialoogvenster **Geavanceerde instellingen:**
   
   1. Selecteer **Microsoft iSCSI Initiator**in de vervolgkeuzelijst **Lokale adapter** .
   2. Selecteer in de vervolgkeuzelijst **Initiator IP** het IP-adres dat overeenkomt met de eerste interface op de host (iSCSI-interface).
   3. Selecteer in de vervolgkeuzelijst **Target Portal IP** het IP-adres voor de eerste gegevensinterface die op het apparaat is ingeschakeld.
   4. Klik op **OK** om terug te keren naar het dialoogvenster ISCSI-initiatoreigenschappen.
7. Klik **op Eigenschappen**en klik in het dialoogvenster **Eigenschappen** op Sessie **toevoegen**.
8. Schakel in het dialoogvenster **Verbinding maken met doel** het selectievakje Meerdere paden **inschakelen** in en klik vervolgens op **Geavanceerd**.
9. Ga als een in het dialoogvenster **Geavanceerde instellingen:**
   
   1. Selecteer **Microsoft iSCSI Initiator**in de vervolgkeuzelijst **Lokale adapter** .
   2. Selecteer in de vervolgkeuzelijst **Initiator IP** het IP-adres dat overeenkomt met de tweede iSCSI-interface op de host.
   3. Selecteer in de vervolgkeuzelijst **Target Portal IP** het IP-adres voor de tweede gegevensinterface die op het apparaat is ingeschakeld.
   4. Klik op **OK** om terug te keren naar het dialoogvenster **ISCSI-initiatoreigenschappen.** Je hebt nu een tweede sessie aan het doel toegevoegd.
10. Herhaal stap 8-10 om extra sessies (paden) toe te voegen aan het doel. Met twee interfaces op de host en twee op het apparaat, u een totaal van vier sessies toevoegen.
11. Nadat u de gewenste sessies (paden) hebt toegevoegd, selecteert u in het dialoogvenster **iSCSI-initiatoreigenschappen** het doel en klikt u op **Eigenschappen**. Noteer op het tabblad Sessies van het dialoogvenster **Eigenschappen** de vier sessie-id's die overeenkomen met de mogelijke padpermutaties. Als u een sessie wilt annuleren, schakelt u het selectievakje naast een sessie-id in en klikt u op **Verbreken**.
12. Als u apparaten wilt weergeven die in sessies worden weergegeven, selecteert u het tabblad **Apparaten.** Als u het MPIO-beleid voor een geselecteerd apparaat wilt configureren, klikt u op **MPIO**. Het dialoogvenster **Apparaatdetails** wordt weergegeven. Op het tabblad **MPIO** u de juiste instellingen **voor het bebalansbeleid** selecteren. U ook het type **Actief** of **stand-bypad** bekijken.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het gebruik van de StorSimple Device Manager-service om de configuratie van uw StorSimple-apparaat te wijzigen.](storsimple-8000-modify-device-config.md)

