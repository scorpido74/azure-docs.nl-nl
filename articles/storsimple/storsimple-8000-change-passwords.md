---
title: Uw StorSimple-wacht woorden wijzigen | Microsoft Docs
description: Hierin wordt beschreven hoe u de StorSimple Apparaatbeheer-service gebruikt om uw StorSimple Snapshot Manager en de beheerders wachtwoorden van uw apparaat te wijzigen.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79268026"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>De StorSimple Apparaatbeheer-service gebruiken om uw StorSimple-wacht woorden te wijzigen

## <a name="overview"></a>Overzicht
De optie Azure Portal **Apparaatinstellingen** bevat alle para meters die u opnieuw kunt configureren op een StorSimple-apparaat dat wordt beheerd door een StorSimple Apparaatbeheer-service. In deze zelf studie wordt uitgelegd hoe u de optie **beveiliging** kunt gebruiken onder **Apparaatinstellingen** om uw StorSimple te wijzigen Snapshot Manager wacht woord.

## <a name="change-the-device-administrator-password"></a>Het beheerderswachtwoord voor het apparaat wijzigen
Wanneer u de Windows Power shell-interface gebruikt voor toegang tot het StorSimple-apparaat, moet u een beheerders wachtwoord voor het apparaat opgeven. Wanneer het eerste StorSimple-apparaat is geregistreerd bij een service, is het standaard wachtwoord voor deze interface *Wachtwoord1*. Voor de beveiliging van uw gegevens moet u dit wacht woord wijzigen aan het einde van het registratie proces. U kunt niet afsluiten vanuit het registratie proces zonder dit wacht woord te wijzigen. Zie voor meer informatie [stap 3: het apparaat configureren en registreren via Windows PowerShell voor StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Het wacht woord dat voor het eerst is ingesteld via de Windows Power shell-interface tijdens de registratie, kan later worden gewijzigd via de Azure Portal. Voer de volgende stappen uit om het beheerders wachtwoord van het apparaat te wijzigen.

#### <a name="to-change-the-device-administrator-password"></a>Het beheerders wachtwoord voor het apparaat wijzigen
1. Ga naar de StorSimple-apparaatbeheerservice en klik op **Apparaten**.

2. Selecteer in de lijst in tabel vorm van apparaten de optie en klik op het apparaat waarvan u het wacht woord wilt wijzigen.

    ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. Ga op de Blade **instellingen** naar **Apparaatinstellingen > beveiliging**.

    ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. Klik in de Blade **beveiligings instellingen** op **wacht woord** om het beheerders wachtwoord van het apparaat te wijzigen.

    ![](./media/storsimple-8000-change-passwords/changepwd3.png)

5. Geef op de Blade **wacht woord** een beheerders wachtwoord op dat uit 8 tot 15 tekens bestaat. Het wacht woord moet een combi natie zijn van drie of meer hoofd letters, kleine letters, cijfers en speciale tekens.

6. Bevestig het wachtwoord.

    ![](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Klik op **Opslaan** en klik op **Ja**om de bevestiging te bevestigen.

    ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Het beheerders wachtwoord voor het apparaat moet nu worden bijgewerkt. U kunt dit gewijzigde wacht woord gebruiken om toegang te krijgen tot de Windows Power shell-interface.

## <a name="set-the-storsimple-snapshot-manager-password"></a>Het wacht woord voor StorSimple Snapshot Manager instellen
De StorSimple Snapshot Manager-software bevindt zich op uw Windows-host. Met deze software kunnen beheerders de back-ups van uw StorSimple-apparaat beheren. De back-ups zijn in feite momentopnamen die lokaal en in de cloud worden opgeslagen.

Wanneer u een apparaat configureert in StorSimple Snapshot Manager, wordt u gevraagd om het IP-adres en wacht woord van het apparaat op te geven voor verificatie van uw opslag apparaat.

U kunt het wacht woord voor StorSimple Snapshot Manager instellen of wijzigen via de Azure Portal. Voer de volgende stappen uit om het wacht woord voor StorSimple Snapshot Manager in te stellen of te wijzigen.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>Het wacht woord voor StorSimple Snapshot Manager instellen
1. Ga naar de StorSimple-apparaatbeheerservice en klik op **Apparaten**.

2. Selecteer in de lijst in tabel vorm van apparaten de optie en klik op het apparaat waarvan u de StorSimple Snapshot Manager het wacht woord dat u wilt instellen of wijzigen.

     ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. Ga op de Blade **instellingen** naar **Apparaatinstellingen > beveiliging**.

     ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. Klik op de Blade **beveiligings instellingen** op **wacht woord** om het wacht woord voor de StorSimple Snapshot Manager in te stellen of te wijzigen.

     ![](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. Voer een wacht woord van 14 of 15 tekens in op de Blade **wacht** woord. Zorg ervoor dat het wacht woord een combi natie van drie of meer hoofd letters, kleine letters, cijfers en speciale tekens bevat.

6. Bevestig het wachtwoord.

     ![](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Klik op **Opslaan** en klik op **Ja**om de bevestiging te bevestigen.

     ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Het Snapshot Manager wacht woord voor StorSimple moet nu worden bijgewerkt.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [StorSimple-beveiliging](storsimple-8000-security.md).
* Meer informatie over [het wijzigen van de](storsimple-8000-modify-device-config.md)apparaatconfiguratie.
* Meer informatie over [het gebruik van de StorSimple Apparaatbeheer-service voor het beheren van uw StorSimple-apparaat](storsimple-8000-manager-service-administration.md).

