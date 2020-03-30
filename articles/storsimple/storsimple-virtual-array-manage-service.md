---
title: StorSimple Device Manager-service implementeren | Microsoft Documenten
description: In de Azure-portal wordt uitgelegd hoe u de StorSimple Device Manager-service maakt en verwijdert in de Azure-portal en wordt beschreven hoe u de serviceregistratiesleutel beheert.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 28499494-8c4d-4a7f-9d44-94b2b8a93c93
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 2ff987448e3900c2a533789b2f38fd3ced71ce72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267454"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>De StorSimple Device Manager-service implementeren voor StorSimple Virtual Array

## <a name="overview"></a>Overzicht

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

De StorSimple Device Manager-service wordt uitgevoerd in Microsoft Azure en maakt verbinding met meerdere StorSimple-apparaten. Nadat u de service hebt gemaakt, u deze gebruiken om de apparaten te beheren vanuit de Microsoft Azure-portal die in een browser wordt uitgevoerd. Hiermee u alle apparaten die zijn aangesloten op de StorSimple Device Manager-service controleren vanaf één centrale locatie, waardoor de administratieve lasten worden geminimaliseerd.

De veelvoorkomende taken met betrekking tot een StorSimple Device Manager-service zijn:

* Een service maken
* Een service verwijderen
* De serviceregistratiesleutel ophalen
* De serviceregistratiesleutel opnieuw genereren

In deze zelfstudie wordt beschreven hoe u elk van de voorgaande taken uitvoeren. De informatie in dit artikel is alleen van toepassing op StorSimple Virtual Arrays. Voor meer informatie over de StorSimple 8000-serie gaat u [naar de implementatie van een StorSimple Manager-service.](storsimple-manage-service.md)

## <a name="create-a-service"></a>Een service maken

Als u een service wilt maken, moet u het:

* Een abonnement met een Enterprise Agreement
* Een actief Microsoft Azure-opslagaccount
* De factureringsgegevens die worden gebruikt voor toegangsbeheer

U er ook voor kiezen om een opslagaccount te genereren wanneer u de service maakt.

Eén service kan meerdere apparaten beheren. Een apparaat kan echter niet meerdere services omvatten. Een grote onderneming kan meerdere service-exemplaren hebben om te werken met verschillende abonnementen, organisaties of zelfs implementatielocaties.

> [!NOTE]
> U hebt afzonderlijke exemplaren van de StorSimple Device Manager-service nodig om storSimple 8000-serieapparaten en StorSimple Virtual Arrays te beheren.


Voer de volgende stappen uit om een service te maken.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Een service verwijderen

Voordat u een service verwijdert, moet u ervoor zorgen dat er geen verbonden apparaten worden gebruikt. Als de service in gebruik is, schakelt u de verbonden apparaten uit. Met de deactiverenwordt de verbinding tussen het apparaat en de service verbroken, maar worden de apparaatgegevens in de cloud bewaard.

> [!IMPORTANT]
> Nadat een service is verwijderd, kan de bewerking niet worden teruggedraaid. Elk apparaat dat de service heeft gebruikt, moet in de fabriek worden gereset voordat het met een andere service kan worden gebruikt. In dit scenario gaan de lokale gegevens op het apparaat en de configuratie verloren.
 

Voer de volgende stappen uit om een service te verwijderen.

#### <a name="to-delete-a-service"></a>Een service verwijderen

1. Ga naar **Alle resources**. Zoek naar uw StorSimple Device Manager-service. Selecteer de service die u wilt verwijderen.
   
    ![Service selecteren om te verwijderen](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Ga naar uw servicedashboard om ervoor te zorgen dat er geen apparaten zijn aangesloten op de service. Als er geen apparaten zijn geregistreerd bij deze service, ziet u ook een bannerbericht met het effect. Klik **op Verwijderen**.
   
    ![Service verwijderen](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Klik op **Ja** in de bevestigingsmelding als u om bevestiging wordt gevraagd. 
   
    ![Serviceverwijdering bevestigen](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. Het kan enkele minuten duren voordat de service is verwijderd. Nadat de service is verwijderd, wordt u hiervan op de hoogte gesteld.
   
    ![Verwijdering van succesvolle service](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

De lijst met services wordt vernieuwd.

 ![Bijgewerkte lijst met services](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>De serviceregistratiesleutel ophalen
Nadat u een service hebt gemaakt, moet u uw StorSimple-apparaat registreren bij de service. Om uw eerste StorSimple-apparaat te registreren, hebt u de serviceregistratiesleutel nodig. Als u extra apparaten wilt registreren bij een bestaande StorSimple-service, hebt u zowel de registratiesleutel als de versleutelingssleutel voor servicegegevens (die tijdens de registratie op het eerste apparaat wordt gegenereerd) nodig. Zie [StorSimple security voor](storsimple-security.md)meer informatie over de versleutelingssleutel voor servicegegevens. U de registratiesleutel krijgen door toegang te krijgen tot het **sleutelsblad** voor uw service.

Voer de volgende stappen uit om de serviceregistratiesleutel op te halen.

#### <a name="to-get-the-service-registration-key"></a>De serviceregistratiesleutel opte zijn
1. Ga in het **StorSimple Device Manager-blad** naar ** &gt; Management** **Keys.**
   
   ![De blade Sleutels](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. In het **sleutelsblad** verschijnt een serviceregistratiesleutel. Kopieer de registratiesleutel met het kopieerpictogram. 

Bewaar de serviceregistratiesleutel op een veilige locatie. U hebt deze sleutel, evenals de versleutelingssleutel voor servicegegevens, nodig om extra apparaten bij deze service te registreren. Nadat u de serviceregistratiesleutel hebt verkregen, moet u uw apparaat configureren via de Windows PowerShell-interface voor StorSimple.

## <a name="regenerate-the-service-registration-key"></a>De serviceregistratiesleutel opnieuw genereren
U moet een serviceregistratiesleutel regenereren als u sleutelrotatie moet uitvoeren of als de lijst met servicebeheerders is gewijzigd. Wanneer u de sleutel regenereert, wordt de nieuwe sleutel alleen gebruikt voor het registreren van volgende apparaten. De apparaten die al zijn geregistreerd, worden niet beïnvloed door dit proces.

Voer de volgende stappen uit om een serviceregistratiesleutel opnieuw te genereren.

#### <a name="to-regenerate-the-service-registration-key"></a>De serviceregistratiesleutel regenereren
1. Ga in het **StorSimple Device Manager-blad** naar ** &gt; Management** **Keys.**
   
   ![De blade Sleutels](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. Klik **in** het blad Toetsen op **Regenereren**.
   
   ![Klik op regenereren](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. Controleer in het **sleutelblad regenereren serviceregistratie** de actie die nodig is wanneer de sleutels worden geregenereerd. Alle volgende apparaten die bij deze service zijn geregistreerd, gebruiken de nieuwe registratiesleutel. Klik **op Regenereren** om dit te bevestigen. U wordt hiervan op de hoogte gesteld nadat de inschrijving is voltooid.
   
   ![Regenererensleutel bevestigen](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Er verschijnt een nieuwe serviceregistratiesleutel.
   
    ![Regenererensleutel bevestigen](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Kopieer deze sleutel en bewaar deze voor het registreren van nieuwe apparaten met deze service.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over hoe u aan [de slag kunt](storsimple-virtual-array-deploy1-portal-prep.md) met een StorSimple Virtual Array.
* Meer informatie over het [beheren van uw StorSimple-apparaat.](storsimple-ova-web-ui-admin.md)

