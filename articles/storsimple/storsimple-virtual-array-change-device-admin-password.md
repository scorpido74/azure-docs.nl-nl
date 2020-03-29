---
title: Het wachtwoord voor apparaatbeheerders van StorSimple Virtual Array wijzigen | Microsoft Documenten
description: Beschrijft hoe u de Azure-portal of de StorSimple Virtual Array-webgebruikersinterface gebruikt om het wachtwoord van de apparaatbeheerder te wijzigen.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 11490814-d9fd-4dc7-9c3b-55dd2c23eaf1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5308badf439254062a8aefca1840eb21bc234ace
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60580365"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>Het wachtwoord voor de beheerder van het StorSimple-apparaat apparaat wijzigen via StorSimple Device Manager

## <a name="overview"></a>Overzicht

Wanneer u de Windows PowerShell-interface gebruikt om toegang te krijgen tot de StorSimple Virtual Array, moet u een wachtwoord voor apparaatbeheerder invoeren. Wanneer het StorSimple-apparaat voor het eerst is ingericht en gestart, is het standaardwachtwoord *Password1*. Voor de beveiliging van uw gegevens verloopt het standaardwachtwoord de eerste keer dat u zich aanmeldt en moet u dit wachtwoord wijzigen.

U ook de lokale webgebruikersinterface of de Azure-portal gebruiken om het wachtwoord van de apparaatbeheerder te wijzigen op elk gewenst moment nadat het apparaat in uw productieomgeving is geïmplementeerd. Elk van deze procedures wordt beschreven in dit artikel.

 ![apparaten blad](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>De Azure-portal gebruiken om het wachtwoord te wijzigen

Voer de volgende stappen uit om het wachtwoord van de apparaatbeheerder te wijzigen via de Azure-portal.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Het wachtwoord van de apparaatbeheerder wijzigen via de Azure-portal

1. Selecteer op de bestemmingspagina van de service uw service, dubbelklik op de servicenaam en klik vervolgens in de sectie **Beheer** op **Apparaten**. Hiermee wordt het **apparaatblad** geopend met al uw StorSimple Virtual Array-apparaten.

2. Dubbelklik in het blade **apparaten** op het apparaat waarvoor een wachtwoordwijziging vereist is.

3. Klik in het **pagina-pagina-instellingen** voor uw apparaat op **Beveiliging**.

4. Ga als volgt te werk in het blad **Beveiligingsinstellingen:**
   
   1. Schuif omlaag naar de sectie **Wachtwoord apparaatbeheerder.** Geef een beheerderswachtwoord op dat 8 tot 15 tekens bevat.
   2. Bevestig het wachtwoord.
   3. Klik op **Opslaan** boven aan de blade.

Het wachtwoord van de apparaatbeheerder is nu bijgewerkt. U dit gewijzigde wachtwoord gebruiken om lokaal toegang te krijgen tot het apparaat.

![Blad van beveiligingsinstellingen](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>De lokale webgebruikersinterface gebruiken om het wachtwoord te wijzigen

Voer de volgende stappen uit om het wachtwoord van de apparaatbeheerder te wijzigen via de lokale webgebruikersinterface.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Het wachtwoord van de apparaatbeheerder wijzigen via de lokale webgebruikersinterface

1. Klik in de lokale webgebruikersinterface op **Wijziging onderhoudswachtwoord** > **Password change** voor uw apparaat.
   
    ![wachtwoord wijzigen1](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Voer het **huidige wachtwoord in**.
3. Geef een **nieuw wachtwoord op.** Het wachtwoord moet ten minste 8 tekens lang zijn. Het moet 3 van de 4 van de volgende bevatten: hoofdletters, kleine letters, numerieke en speciale tekens.
   
    Houd er rekening mee dat uw wachtwoord niet hetzelfde kan zijn als de laatste 24 wachtwoorden.
4. Voer het wachtwoord opnieuw in ter bevestiging.
   
    ![wachtwoord wijzigen2](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. Klik onder aan de pagina op **Toepassen.** Het nieuwe wachtwoord wordt nu toegepast. Als de wachtwoordwijziging niet is gelukt, ziet u de volgende fout:
   
    ![wachtwoordfout](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    Nadat het wachtwoord is bijgewerkt, wordt u hiervan op de hoogte gesteld. U dit gewijzigde wachtwoord vervolgens gebruiken om lokaal toegang te krijgen tot het apparaat.


## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [beheren van uw StorSimple Virtual Array.](storsimple-ova-web-ui-admin.md)

