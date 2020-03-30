---
title: Maak Support ticket of case voor StorSimple 8000 serie
description: Meer informatie over het aanmelden van ondersteuningsaanvragen en het starten van een ondersteuningssessie op uw StorSimple 8000-serie apparaat.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 99de3a6fbbbb1c4324df1712a5e24fd334ca4977
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254987"
---
# <a name="contact-microsoft-support"></a>Neem contact op met Microsoft Ondersteuning

De StorSimple Device Manager biedt de mogelijkheid om **een nieuwe ondersteuningsaanvraag in** te loggen in het serviceoverzichtsblad. Als u problemen ondervindt met uw StorSimple-oplossing, u een serviceaanvraag voor technische ondersteuning maken. In een online sessie met uw ondersteuningstechnicus moet u mogelijk ook een ondersteuningssessie starten op uw StorSimple-apparaat. Dit artikel leidt je door:

* Een ondersteuningsaanvraag maken.
* Hoe u de levenscyclus van een ondersteuningsaanvraag vanuit de portal beheren.
* Een ondersteuningssessie starten in de Windows PowerShell-interface van uw StorSimple-apparaat.

Bekijk de Sla's en informatie van de [StorSimple 8000-serie ondersteuning](https://msdn.microsoft.com/library/mt433077.aspx) voordat u een ondersteuningsaanvraag maakt.

## <a name="create-a-support-request"></a>Een ondersteuningsaanvraag maken

Afhankelijk van uw [ondersteuningsplan](https://azure.microsoft.com/support/plans/)u ondersteuningstickets voor een probleem op uw StorSimple-apparaat rechtstreeks vanaf het overzichtsblad van de StorSimple Device Manager-service maken. Voer de volgende stappen uit om een ondersteuningsaanvraag te maken:

#### <a name="to-create-a-support-request"></a>Een ondersteuningsverzoek maken

1. Ga naar uw StorSimple-apparaatbeheerservice. Ga in de instellingen van het serviceoverzichtsblad naar de sectie **ONDERSTEUNING + PROBLEEMOPLOSSING** en klik vervolgens op **Nieuw ondersteuningsverzoek**.
     
    ![Neem contact op met MS Support via een nieuwe portal](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. Selecteer **basics**in het blad **Nieuw ondersteuningsverzoek** . Ga in het **blad Basics** de volgende stappen uit:
   1. Selecteer In de vervolgkeuzelijst **Probleemtype** de optie **Technisch**.
   2. Het huidige **abonnement,** **servicetype** en de **Resource** (StorSimple Device Manager-service) worden automatisch gekozen. 
   3. Selecteer een **ondersteuningsabonnement** in de vervolgkeuzelijst als u meerdere abonnementen aan uw abonnement hebt gekoppeld. U hebt een betaald ondersteuningsplan nodig om technische ondersteuning in te schakelen.
   4. Klik op **Volgende**.

       ![Neem contact op met MS Support via een nieuwe portal](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. Selecteer **stap 2-probleem**in het blad **Nieuw ondersteuningsverzoek** . Ga in het **probleemblad** de volgende stappen uit:
    
    1. Kies de **ernst**.
    2. Geef op of het probleem gerelateerd is aan het toestel of de StorSimple Device Manager-service.
    3. Kies een **rubriek** voor dit probleem en geef meer **details** over het probleem.
    4. Geef de begindatum en tijd voor het probleem op.
    5. Klik **in het uploaden van bestanden**op het mappictogram om naar uw ondersteuningspakket te bladeren.
    6. Controleer **diagnostische gegevens delen**.
    7. Klik op **Volgende**.

       ![Neem contact op met MS Support via een nieuwe portal](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. Klik in het mes Van het **nieuwe ondersteuningsverzoek** op **Contactgegevens van stap 3**. Ga in het blad **Contactgegevens** de volgende stappen uit:

   1. Geef in de **contactopties**de contactmethode (telefoon of e-mail) en de taal op. De reactietijd wordt automatisch geselecteerd op basis van uw abonnement.
   2. Geef in de contactgegevens uw naam, e-mail, optionele contactpersoon, land/regio op. Schakel het selectievakje **Wijzigingen in contactpersonen opslaan voor toekomstige ondersteuningsaanvragen** in.
   3. Klik **op Maken**.
   
       ![Neem contact op met MS Support via een nieuwe portal](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

      Microsoft Support gebruikt deze informatie om contact met u op te nemen voor meer informatie, diagnose en oplossing.
      Nadat u uw aanvraag hebt ingediend, neemt een ondersteuningstechnicus zo snel mogelijk contact met u op om verder te gaan met uw verzoek.

## <a name="manage-a-support-request"></a>Een ondersteuningsverzoek beheren

Nadat u een ondersteuningsticket hebt gemaakt, kunt u de voortgang van het ticket vanuit de portal beheren.

#### <a name="to-manage-your-support-requests"></a>Uw ondersteuningsverzoeken beheren

1. Als u naar de help- en ondersteuningspagina wilt gaan, navigeert u **naar Bladeren > Help + ondersteuning.**

    ![Ondersteuningsaanvragen beheren](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. Een tabeltabel met alle ondersteuningsaanvragen wordt weergegeven in het **ondersteuningsblad Help + ondersteuning.**

    ![Ondersteuningsaanvragen beheren](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Selecteer en klik op een ondersteuningsverzoek. U de status en de details van dit verzoek bekijken. Klik **+ Nieuw bericht** als u dit verzoek wilt opvolgen.

    ![Ondersteuningsaanvragen beheren](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Een ondersteuningssessie starten in Windows PowerShell voor StorSimple

Als u eventuele problemen met het StorSimple-apparaat wilt oplossen, moet u contact opnemen met het Microsoft Support-team. Microsoft Support moet mogelijk een ondersteuningssessie gebruiken om zich aan te melden bij uw apparaat.

Voer de volgende stappen uit om een ondersteuningssessie te starten:

#### <a name="to-start-a-support-session"></a>Een ondersteuningssessie starten

1. Rechtstreeks toegang tot het apparaat via de seriële console of via een telnet-sessie vanaf een externe computer. Volg hiervoor de stappen in [PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat.](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)
2. Druk in de sessie die wordt geopend op **de** enter-toets om een opdrachtprompt te krijgen.
3. Selecteer in het menu van de seriële console optie 1, **Log in met volledige toegang**.
4. Typ bij de prompt het volgende wachtwoord:
   
    `Password1`
5. Typ achter de prompt de volgende opdracht:
   
    `Enable-HcsSupportAccess`
6. Een gecodeerde tekenreeks wordt aan u gepresenteerd. Kopieer deze tekenreeks naar een teksteditor zoals Kladblok.
7. Sla deze tekenreeks op en stuur deze in een e-mailbericht naar Microsoft Support.

> [!IMPORTANT]
> U ondersteuningstoegang `Disable-HcsSupportAccess`uitschakelen door het uitvoeren van. Het StorSimple-apparaat probeert ook de ondersteuningstoegang 8 uur na de start van de sessie uit te schakelen. Het is een aanbevolen procedure om uw StorSimple-apparaatreferenties te wijzigen na het starten van een ondersteuningssessie.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [diagnosticeren en oplossen van problemen met betrekking tot uw StorSimple 8000-serie apparaat](storsimple-8000-troubleshoot-deployment.md)
