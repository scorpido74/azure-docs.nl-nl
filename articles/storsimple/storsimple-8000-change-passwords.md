---
title: Uw StorSimple-wachtwoorden wijzigen | Microsoft Documenten
description: Beschrijft hoe u de StorSimple Device Manager-service gebruiken om uw StorSimple Snapshot Manager- en apparaatbeheerderswachtwoorden te wijzigen.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: ab874bbdcd47a4bfa9abfba721afa46d0f23a338
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268026"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>Gebruik de StorSimple Device Manager-service om uw StorSimple-wachtwoorden te wijzigen

## <a name="overview"></a>Overzicht
De optie **Azure-portal-apparaatinstellingen** bevat alle apparaatparameters die u opnieuw configureren op een StorSimple-apparaat dat wordt beheerd door een StorSimple-apparaatbeheerservice. In deze zelfstudie wordt uitgelegd hoe u de optie **Beveiliging** gebruiken onder **Apparaatinstellingen** om uw apparaatbeheerder of het wachtwoord van StorSimple Snapshot Manager te wijzigen.

## <a name="change-the-device-administrator-password"></a>Het beheerderswachtwoord voor het apparaat wijzigen
Wanneer u de Windows PowerShell-interface gebruikt om toegang te krijgen tot het StorSimple-apparaat, moet u een wachtwoord voor apparaatbeheerder invoeren. Wanneer het eerste StorSimple-apparaat is geregistreerd bij een service, is het standaardwachtwoord voor deze interface *Password1*. Voor de beveiliging van uw gegevens moet u dit wachtwoord aan het einde van het registratieproces wijzigen. U het registratieproces niet verlaten zonder dit wachtwoord te wijzigen. Zie [Stap 3: Het apparaat configureren en registreren via Windows PowerShell voor StorSimple voor](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)meer informatie.

Het wachtwoord dat tijdens de registratie voor het eerst via de Windows PowerShell-interface is ingesteld, kan later worden gewijzigd via de Azure-portal. Voer de volgende stappen uit om het wachtwoord van de apparaatbeheerder te wijzigen.

#### <a name="to-change-the-device-administrator-password"></a>Het wachtwoord van de apparaatbeheerder wijzigen
1. Ga naar de StorSimple-apparaatbeheerservice en klik op **Apparaten**.

2. Selecteer en klik in de tabeltabellijst van apparaten op het apparaat waarvan u het wachtwoord wilt wijzigen.

    ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. Ga **in het** blad Instellingen naar **Apparaatinstellingen > Beveiliging**.

    ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. Klik in het blad **Beveiligingsinstellingen** op **Wachtwoord** om het wachtwoord van de apparaatbeheerder te wijzigen.

    ![](./media/storsimple-8000-change-passwords/changepwd3.png)

5. Geef in het **wachtwoordblad** een beheerderswachtwoord op dat 8 tot 15 tekens bevat. Het wachtwoord moet een combinatie zijn van 3 of meer hoofdletters, kleine letters, numerieke en speciale tekens.

6. Bevestig het wachtwoord.

    ![](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Klik **op Opslaan** en klik wanneer u om bevestiging wordt gevraagd op **Ja**.

    ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Het wachtwoord van de apparaatbeheerder moet nu worden bijgewerkt. U dit gewijzigde wachtwoord gebruiken om toegang te krijgen tot de Windows PowerShell-interface.

## <a name="set-the-storsimple-snapshot-manager-password"></a>Het wachtwoord van StorSimple Snapshot Manager instellen
De StorSimple Snapshot Manager-software bevindt zich op uw Windows-host. Met deze software kunnen beheerders de back-ups van uw StorSimple-apparaat beheren. De back-ups zijn in feite momentopnamen die lokaal en in de cloud worden opgeslagen.

Wanneer u een apparaat configureert in StorSimple Snapshot Manager, wordt u gevraagd het IP-adres en wachtwoord van het apparaat op te geven om uw opslagapparaat te verifiëren.

U het wachtwoord voor StorSimple Snapshot Manager instellen of wijzigen via de Azure-portal. Voer de volgende stappen uit om het wachtwoord van StorSimple Snapshot Manager in te stellen of te wijzigen.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>Het wachtwoord van De Momentopname Van StorSimple instellen
1. Ga naar de StorSimple-apparaatbeheerservice en klik op **Apparaten**.

2. Selecteer en klik in de tabeltabellijst van apparaten op het apparaat waarvan u het StorSimple Snapshot Manager-wachtwoord wilt instellen of wijzigen.

     ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. Ga **in het** blad Instellingen naar **Apparaatinstellingen > Beveiliging**.

     ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. Klik in het blad **Beveiligingsinstellingen** op **Wachtwoord** om het wachtwoord van StorSimple Snapshot Manager in te stellen of te wijzigen.

     ![](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. Voer in het **wachtwoordblad** een wachtwoord in dat 14 of 15 tekens is. Zorg ervoor dat het wachtwoord een combinatie van 3 of meer hoofdletters, kleine letters, numerieke en speciale tekens bevat.

6. Bevestig het wachtwoord.

     ![](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Klik **op Opslaan** en klik wanneer u om bevestiging wordt gevraagd op **Ja**.

     ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Het wachtwoord van StorSimple Snapshot Manager moet nu worden bijgewerkt.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [Beveiliging van StorSimple](storsimple-8000-security.md).
* Meer informatie over [het wijzigen van de configuratie van uw apparaat](storsimple-8000-modify-device-config.md).
* Meer informatie over [het gebruik van de StorSimple Device Manager-service om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)

