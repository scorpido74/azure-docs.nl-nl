---
title: CHAP configureren voor storsimple 8000-serie apparaat | Microsoft Documenten
description: Beschrijft hoe u het CHAP (Challenge Handshake Authentication Protocol) configureert op een StorSimple-apparaat.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: TBD
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: efc116c278bfe72419800603a3b365f461fe0a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267961"
---
# <a name="configure-chap-for-your-storsimple-device"></a>CHAP configureren voor uw StorSimple-apparaat

In deze zelfstudie wordt uitgelegd hoe u CHAP configureert voor uw StorSimple-apparaat. De procedure in dit artikel is van toepassing op StorSimple 8000 serie apparaten.

CHAP staat voor Challenge Handshake Authentication Protocol. Het is een verificatieschema dat door servers wordt gebruikt om de identiteit van externe clients te valideren. De verificatie is gebaseerd op een gedeeld wachtwoord of geheim. CHAP kan een richting (unidirectioneel) of wederzijds (bidirectioneel). Een manier CHAP is wanneer het doel een initiator verifieert. In wederzijdse of omgekeerde CHAP verifieert het doel de initiator en verifieert de initiator het doel. Initiatorverificatie kan worden geïmplementeerd zonder doelverificatie. Doelverificatie kan echter alleen worden geïmplementeerd als er ook initiatieverificatie is geïmplementeerd.

Als aanbevolen toepassing raden we u aan CHAP te gebruiken om de iSCSI-beveiliging te verbeteren.

> [!NOTE]
> Houd er rekening mee dat IPSEC momenteel niet wordt ondersteund op StorSimple-apparaten.

De CHAP-instellingen op het StorSimple-apparaat kunnen op de volgende manieren worden geconfigureerd:

* Unidirectionele of eenrichtingsverificatie
* Bidirectionele of wederzijdse of omgekeerde verificatie

In elk van deze gevallen moet de portal voor het apparaat en de iSCSI-initiatorsoftware van de server worden geconfigureerd. De gedetailleerde stappen voor deze configuratie worden beschreven in de volgende zelfstudie.

## <a name="unidirectional-or-one-way-authentication"></a>Unidirectionele of eenrichtingsverificatie

Bij unidirectionele verificatie verifieert het doel de initiator. Voor deze verificatie moet u de chap-initiatorinstellingen configureren op het StorSimple-apparaat en de iSCSI Initiator-software op de host. De gedetailleerde procedures voor uw StorSimple-apparaat en Windows-host worden vervolgens beschreven.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Uw apparaat configureren voor eenrichtingsverificatie

1. Ga in de Azure-portal naar uw StorSimple Device Manager-service. Klik **op Apparaten** en selecteer en klik op een apparaat waarvoor u CHAP wilt configureren. Ga naar **Apparaatinstellingen > Beveiliging**. Klik in het blad **Beveiligingsinstellingen** op **CHAP**.
   
    ![CHAP-initiator](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. In het **CHAP-blad** en in de sectie **CHAP Initiator:**
   
   1. Geef een gebruikersnaam op voor uw CHAP-initiator.
   2. Geef een wachtwoord voor uw CHAP-initiator.
      
      > [!IMPORTANT]
      > De CHAP-gebruikersnaam moet minder dan 233 tekens bevatten. Het CHAP-wachtwoord moet tussen de 12 en 16 tekens liggen. Een langere gebruikersnaam of wachtwoord resulteert in een verificatiefout op de Windows-host.
   
   3. Bevestig het wachtwoord.

       ![CHAP-initiator](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. Klik op **Opslaan**. Er wordt een bevestigingsbericht weergegeven. Klik op **OK** om de wijzigingen op te slaan.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Eenrichtingsverificatie configureren op de Windows-hostserver
1. Start de iSCSI-initiator op de Windows-hostserver.
2. Voer in het venster **eigenschappen van iSCSI-initiators** de volgende stappen uit:
   
   1. Klik op het tabblad **Detectie.**
      
       ![Eigenschappen iSCSI-initiator](./media/storsimple-configure-chap/IC740944.png)
   2. Klik **op Portal ontdekken**.
3. Ga als een in het dialoogvenster **Doelportal ontdekken:**
   
   1. Geef het IP-adres van uw apparaat op.
   2. Klik op **Geavanceerd**.
      
       ![Doelportal ontdekken](./media/storsimple-configure-chap/IC740945.png)
4. Ga als een in het dialoogvenster **Geavanceerde instellingen:**
   
   1. Schakel **het selectievakje CHAP-logboek inschakelen in.**
   2. Geef in het veld **Naam** de gebruikersnaam op die u hebt opgegeven voor de CHAP-initiator in de Azure-portal.
   3. Geef in het **veld Geheim doel** het wachtwoord op dat u hebt opgegeven voor de CHAP-initiator in de Azure-portal.
   4. Klik op **OK**.
      
       ![Algemene geavanceerde instellingen](./media/storsimple-configure-chap/IC740946.png)
5. Op het tabblad **Doelen** van het venster **ISCSI-initiatoreigenschappen** moet de apparaatstatus worden weergegeven als **Verbonden**. Als u een StorSimple 1200-apparaat gebruikt, wordt elk volume gemonteerd als een iSCSI-doel. Daarom moeten stap 3-4 voor elk volume worden herhaald.
   
    ![Volumes die als afzonderlijke doelstellingen zijn gemonteerd](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Als u de iSCSI-naam wijzigt, wordt de nieuwe naam gebruikt voor nieuwe iSCSI-sessies. Nieuwe instellingen worden pas gebruikt voor bestaande sessies als u zich afmeldt en zich opnieuw aanmeldt.

Ga voor meer informatie over het configureren van CHAP op de Windows-hostserver naar [Aanvullende overwegingen](#additional-considerations).

## <a name="bidirectional-or-mutual-authentication"></a>Bidirectionele of wederzijdse verificatie

Bij bidirectionele verificatie verifieert het doel de initiator en verifieert de initiator het doel. Deze procedure vereist dat de gebruiker de CHAP-initiatorinstellingen, de CHAP-instellingen op het apparaat omkeren en iSCSI Initiator-software op de host. In de volgende procedures worden de stappen beschreven om wederzijdse verificatie op het apparaat en op de Windows-host te configureren.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Uw apparaat configureren voor wederzijdse verificatie

1. Ga in de Azure-portal naar uw StorSimple Device Manager-service. Klik **op Apparaten** en selecteer en klik op een apparaat waarvoor u CHAP wilt configureren. Ga naar **Apparaatinstellingen > Beveiliging**. Klik in het blad **Beveiligingsinstellingen** op **CHAP**.
   
    ![CHAP-doel](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Scroll naar beneden op deze pagina en in de sectie **CHAP Target:**
   
   1. Geef een **Reverse CHAP-gebruikersnaam** op voor uw apparaat.
   2. Geef een **Reverse CHAP-wachtwoord** voor uw apparaat.
   3. Bevestig het wachtwoord.
3. In de sectie **CHAP Initiator:**
   
   1. Geef een **gebruikersnaam** op voor uw apparaat.
   2. Geef een **wachtwoord** op voor uw apparaat.
   3. Bevestig het wachtwoord.

       ![CHAP-initiator](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. Klik op **Opslaan**. Er wordt een bevestigingsbericht weergegeven. Klik op **OK** om de wijzigingen op te slaan.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Bidirectionele verificatie configureren op de Windows-hostserver

1. Start de iSCSI-initiator op de Windows-hostserver.
2. Klik in het venster **EIGENSCHAPPEN van iSCSI-initiator** op het tabblad **Configuratie.**
3. Klik **op CHAP**.
4. Ga als een in het dialoogvenster **Mutual CHAP Secret van de iSCSI-initiator:**
   
   1. Typ het **Reverse CHAP-wachtwoord** dat u hebt geconfigureerd in de Azure-portal.
   2. Klik op **OK**.
      
       ![iSCSI initiator wederzijds CHAP-geheim](./media/storsimple-configure-chap/IC740949.png)
5. Klik op het tabblad **Doelen.**
6. Klik op de knop **Verbinding maken.** 
7. Klik **in** het dialoogvenster Verbinding maken met doel op **Geavanceerd**.
8. Ga als een van de meest **inziens over geavanceerde eigenschappen:**
   
   1. Schakel **het selectievakje CHAP-logboek inschakelen in.**
   2. Geef in het veld **Naam** de gebruikersnaam op die u hebt opgegeven voor de CHAP-initiator in de Azure-portal.
   3. Geef in het **veld Geheim doel** het wachtwoord op dat u hebt opgegeven voor de CHAP-initiator in de Azure-portal.
   4. Schakel het selectievakje **Wederzijdse verificatie uitvoeren** in.
      
       ![Wederzijdse verificatie van geavanceerde instellingen](./media/storsimple-configure-chap/IC740950.png)
   5. Klik op **OK** om de CHAP-configuratie te voltooien

Ga voor meer informatie over het configureren van CHAP op de Windows-hostserver naar [Aanvullende overwegingen](#additional-considerations).

## <a name="additional-considerations"></a>Aanvullende overwegingen

De **Quick Connect-functie** biedt geen ondersteuning voor verbindingen die CHAP hebben ingeschakeld. Wanneer CHAP is ingeschakeld, moet u ervoor zorgen dat u de knop **Verbinding** maken gebruikt die beschikbaar is op het tabblad **Doelen** om verbinding te maken met een doel.

![Verbinding maken met doel](./media/storsimple-configure-chap/IC740947.png)

Schakel in het dialoogvenster **Verbinding maken met doel** dat wordt gepresenteerd het selectievakje Deze verbinding toevoegen aan de lijst met favoriete **doelen** in. Deze selectie zorgt ervoor dat elke keer dat de computer opnieuw wordt opgestart, wordt geprobeerd om de verbinding met de favoriete iSCSI-doelen te herstellen.

## <a name="errors-during-configuration"></a>Fouten tijdens de configuratie

Als uw CHAP-configuratie onjuist is, ziet u waarschijnlijk een foutbericht **voor verificatiefouten.**

## <a name="verification-of-chap-configuration"></a>Verificatie van CHAP-configuratie

U controleren of CHAP wordt gebruikt door de volgende stappen uit te voeren.

#### <a name="to-verify-your-chap-configuration"></a>Uw CHAP-configuratie verifiëren
1. Klik op **Favoriete doelen**.
2. Selecteer het doel waarvoor u verificatie hebt ingeschakeld.
3. Klik op **Details**.
   
    ![favoriete doelen voor iSCSI-initiatoreigenschappen](./media/storsimple-configure-chap/IC740951.png)
4. Noteer in het dialoogvenster **Favoriete doelgegevens** de vermelding in het veld **Verificatie.** Als de configuratie succesvol was, moet er **CHAP**.
   
    ![Favoriete doeldetails](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Beveiliging van StorSimple](storsimple-8000-security.md).
* Meer informatie over [het gebruik van de StorSimple Device Manager-service om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)

