---
title: Apparaatcontrollers uit de StorSimple 8000-serie beheren | Microsoft Documenten
description: Meer informatie over het stoppen, opnieuw opstarten, afsluiten of opnieuw instellen van uw StorSimple-apparaatcontrollers.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2017
ms.author: alkohli
ms.openlocfilehash: ce49dcaa06288ba9e7a4d232338c727064d59685
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267766"
---
# <a name="manage-your-storsimple-device-controllers"></a>Uw StorSimple-apparaatcontrollers beheren

## <a name="overview"></a>Overzicht

In deze zelfstudie worden de verschillende bewerkingen beschreven die kunnen worden uitgevoerd op uw StorSimple-apparaatcontrollers. De controllers in uw StorSimple-apparaat zijn redundante (peer)controllers in een actief-passieve configuratie. Op een gegeven moment is slechts één controller actief en verwerkt alle schijf- en netwerkbewerkingen. De andere controller bevindt zich in een passieve modus. Als de actieve controller uitvalt, wordt de passieve controller automatisch actief.

Deze zelfstudie bevat stapsgewijze instructies om de apparaatcontrollers te beheren met behulp van:

* **Controllersblad** voor uw apparaat in de StorSimple Device Manager-service.
* Windows PowerShell voor StorSimple.

We raden u aan de apparaatcontrollers te beheren via de StorSimple Device Manager-service. Als een actie alleen kan worden uitgevoerd met Windows PowerShell voor StorSimple, maakt de zelfstudie er een notitie van.

Na het lezen van deze tutorial, zult u in staat zijn om:

* Een StorSimple-apparaatcontroller opnieuw starten of uitschakelen
* Een StorSimple-apparaat uitschakelen
* Uw StorSimple-apparaat opnieuw instellen op fabrieksstandaardinstellingen

## <a name="restart-or-shut-down-a-single-controller"></a>Een enkele controller opnieuw starten of uitschakelen
Een controller herstart of uitschakeling is niet vereist als onderdeel van de normale werking van het systeem. Shutdown bewerkingen voor een enkele apparaat controller zijn alleen gebruikelijk in gevallen waarin een mislukte apparaat hardware component moet worden vervangen. Een herstart van de controller kan ook nodig zijn in een situatie waarin de prestaties worden beïnvloed door overmatig geheugengebruik of een defecte controller. Het kan ook nodig zijn om een controller opnieuw op te starten na een succesvolle controller vervanging, als u wilt in te schakelen en de vervangen controller te testen.

Het opnieuw starten van een apparaat leidt niet tot uitval voor verbonden initiators, ervan uitgaande dat de passieve controller beschikbaar is. Als een passieve controller niet beschikbaar of uitgeschakeld is, kan het opnieuw opstarten van de actieve controller leiden tot onderbrekingen in de service en tot downtime.

> [!IMPORTANT]
> * **Een draaiende controller mag nooit fysiek worden verwijderd, omdat dit zou resulteren in een verlies van redundantie en een verhoogd risico op downtime.**
> * De volgende procedure is alleen van toepassing op het fysieke storsimple-apparaat. Zie [Werken met het cloudtoestel](storsimple-8000-cloud-appliance-u2.md#work-with-the-storsimple-cloud-appliance)voor informatie over het starten, stoppen en opnieuw starten van het StorSimple Cloud Appliance.

U één apparaatcontroller opnieuw opstarten of afsluiten via de Azure-portal van de StorSimple Device Manager-service of Windows PowerShell voor StorSimple.

Voer de volgende stappen uit om uw apparaatcontrollers vanuit de Azure-portal te beheren.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Een controller opnieuw starten of afsluiten in Azure-portal
1. Ga in uw StorSimple Device Manager-service naar **Apparaten**. Selecteer uw apparaat in de lijst met apparaten. 

    ![Kies een apparaat](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Ga naar **Instellingen > Controllers**.
   
    ![Controleer of StorSimple-apparaatcontrollers gezond zijn](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. Controleer in het blade **controllers** of de status van beide controllers op uw apparaat **in orde**is. Selecteer een controller, klik met de rechtermuisknop en selecteer **Opnieuw starten** of **afsluiten**.

    ![StorSimple-apparaatcontrollers opnieuw starten of afsluiten selecteren](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. Er wordt een taak gemaakt om de controller opnieuw te starten of af te sluiten en u krijgt eventueel toepasselijke waarschuwingen te zien. Als u het opnieuw opstarten of afsluiten wilt controleren, gaat u naar **Service > Activiteitslogboeken** en filtert u op parameters die specifiek zijn voor uw service. Als een controller is uitgeschakeld, moet u op de aan/uit-knop drukken om de controller in te schakelen om deze in te schakelen.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Een controller opnieuw starten of uitschakelen in Windows PowerShell voor StorSimple
Voer de volgende stappen uit om één controller op uw StorSimple-apparaat uit te schakelen of opnieuw op te starten vanuit de Windows PowerShell voor StorSimple.

1. Toegang tot het apparaat via de seriële console of een telnet-sessie vanaf een externe computer. Als u verbinding wilt maken met Controller 0 of Controller 1, voert u de stappen uit in [PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat.](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)
2. Kies in het menu van de seriële console optie 1, **Log in met volledige toegang**.
3. Noteer in het bannerbericht de controller waarmee u bent verbonden (Controller 0 of Controller 1) en of het de actieve of passieve (stand-by) controller is.
   
   * Typ bij de prompt één controller om één controller uit te schakelen:
     
       `Stop-HcsController`
     
       Hiermee wordt de controller uitgeschakeld waarmee u bent verbonden. Als u de actieve controller stopt, mislukt het apparaat niet naar de passieve controller.

   * Typ op de prompt het thema:
     
       `Restart-HcsController`
     
       Hiermee wordt de controller waarmee u verbinding hebt gemaakt, opnieuw opgestart. Als u de actieve controller opnieuw start, wordt deze niet overnaar de passieve controller voor de herstart.

## <a name="shut-down-a-storsimple-device"></a>Een StorSimple-apparaat uitschakelen

In dit gedeelte wordt uitgelegd hoe u een lopend of mislukt StorSimple-apparaat vanaf een externe computer afsluit. Een apparaat wordt uitgeschakeld nadat beide apparaatcontrollers zijn uitgeschakeld. Een apparaat wordt afgesloten wanneer het apparaat fysiek wordt verplaatst of buiten gebruik wordt genomen.

> [!IMPORTANT]
> Controleer voordat u het apparaat uitschakelt de status van de apparaatonderdelen. Navigeer naar uw apparaat en klik vervolgens op **Instellingen > Hardware-status**. Controleer in het **blad Status en hardwarestatus** of de LED-status van alle onderdelen groen is. Alleen een gezond apparaat heeft een groene status. Als uw apparaat wordt afgesloten om een defect onderdeel te vervangen, ziet u een mislukte (rode) of een gedegradeerde (gele) status voor de betreffende component(en).


#### <a name="to-shut-down-a-storsimple-device"></a>Een StorSimple-apparaat uitschakelen

1. Gebruik de [procedure voor het opnieuw starten of afsluiten van een controller](#restart-or-shut-down-a-single-controller) om de passieve controller op uw apparaat te identificeren en uit te schakelen. U deze bewerking uitvoeren in de Azure-portal of in Windows PowerShell voor StorSimple.
2. Herhaal de bovenstaande stap om de actieve controller uit te schakelen.
3. Je moet nu naar het achtervlak van het apparaat kijken. Nadat de twee controllers volledig zijn uitgeschakeld, moeten de status-LED's op beide controllers rood knipperen. Als u het apparaat op dit moment volledig moet uitschakelen, draait u de aan/uit-schakelaars op zowel power- als koelmodules (PCM's) naar de UIT-positie. Dit moet het apparaat uitschakelen.

## <a name="reset-the-device-to-factory-default-settings"></a>Het apparaat terugzetten naar de fabrieksinstellingen

> [!IMPORTANT]
> Neem contact op met Microsoft Support als u uw apparaat opnieuw wilt instellen in de standaardinstellingen van de fabriek. De onderstaande procedure mag alleen worden gebruikt in combinatie met Microsoft Support.

In deze procedure wordt beschreven hoe u uw Microsoft Azure StorSimple-apparaat resetten naar standaardinstellingen in de fabriek met Windows PowerShell voor StorSimple.
Als u een apparaat opnieuw instelt, worden standaard alle gegevens en instellingen uit het hele cluster verwijderd.

Voer de volgende stappen uit om uw Microsoft Azure StorSimple-apparaat opnieuw in te stellen naar de standaardinstellingen in de fabriek:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Het apparaat opnieuw instellen op standaardinstellingen in Windows PowerShell voor StorSimple
1. Toegang tot het apparaat via de seriële console. Controleer het bannerbericht om ervoor te **Active** zorgen dat u bent verbonden met de Active-controller.
2. Kies in het menu van de seriële console optie 1, **Log in met volledige toegang**.
3. Typ bij de prompt de volgende opdracht om het hele cluster opnieuw in te stellen en alle instellingen voor gegevens, metagegevens en controllerte verwijderen:
   
    `Reset-HcsFactoryDefault`
   
    Als u in plaats daarvan één controller wilt resetten, `-scope` gebruikt u de cmdlet [Reset-HcsFactoryDefault](https://technet.microsoft.com/library/dn688132.aspx) met de parameter.)
   
    Het systeem wordt meerdere keren opnieuw opgestart. U wordt op de hoogte gesteld wanneer de reset is voltooid. Afhankelijk van het systeemmodel kan het 45-60 minuten duren voor een 8100-apparaat en 60-90 minuten voor een 8600 om dit proces te voltooien.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Vragen en antwoorden over het beheren van apparaatcontrollers
In deze sectie hebben we een aantal van de veelgestelde vragen over het beheren van StorSimple-apparaatcontrollers samengevat.

**V:** Wat gebeurt er als beide controllers op mijn apparaat gezond zijn en ingeschakeld en ik de actieve controller opnieuw start of afsluit?

**A.** Als beide controllers op uw apparaat in orde zijn en zijn ingeschakeld, wordt u om bevestiging gevraagd. U ervoor kiezen om:

* **Start de actieve controller opnieuw** op : u krijgt een melding dat het opnieuw opstarten van een actieve controller ervoor heeft gezorgd dat het apparaat niet naar de passieve controller is overgelaten. De controller wordt opnieuw opgestart.
* **Een actieve controller uitschakelen** – U krijgt een melding dat het afsluiten van een actieve controller leidt tot downtime. U moet ook op de aan/uit-knop op het apparaat drukken om de controller in te schakelen.

**V:** Wat gebeurt er als de passieve controller op mijn apparaat niet beschikbaar is of uitgeschakeld en ik de actieve controller opnieuw start of afsluit?

**A.** Als de passieve controller op uw apparaat niet beschikbaar is of is uitgeschakeld, kiest u ervoor om:

* **Start de actieve controller** opnieuw op : u krijgt een melding dat voortzetting van de bewerking zal leiden tot een tijdelijke onderbreking van de service en dat u om bevestiging wordt gevraagd.
* **Een actieve controller uitschakelen** – U krijgt een melding dat het voortzetten van de bewerking leidt tot downtime. U moet ook op de aan/uit-knop op een of beide controllers drukken om het apparaat in te schakelen. U wordt om bevestiging gevraagd.

**V:** Wanneer wordt de controller opnieuw opgestart of afgesloten?

**A.** Het opnieuw opstarten of uitschakelen van een controller kan mislukken als:

* Er wordt een apparaatupdate uitgevoerd.
* Er is al een herstart van de controller aan de gang.
* Er is al een controller-uitschakeling aan de gang.

**V:** Hoe u erachter komen of een controller opnieuw is opgestart of uitgeschakeld?

**A.** U de status van de controller controleren op het controllerblad. De status van de controller geeft aan of een controller bezig is met het opnieuw opstarten of afsluiten. Bovendien bevat het **zwaard van Waarschuwingen** een informatieve waarschuwing als de controller opnieuw wordt opgestart of wordt afgesloten. De bewerkingen voor het opnieuw opstarten en afsluiten van de controller worden ook geregistreerd in de activiteitslogboeken. Ga voor meer informatie over activiteitslogboeken naar [De activiteitslogboeken weergeven](storsimple-8000-service-dashboard.md#view-the-activity-logs).

**V:** Is er enige impact op de I / O als gevolg van controller failover?

**A.** De TCP-verbindingen tussen initiators en actieve controller worden gereset als gevolg van een mislukte controller, maar worden hersteld wanneer de passieve controller de werking ervan aanneemt. Er kan een tijdelijke (minder dan 30 seconden) pauze zijn in I/O-activiteit tussen de initiators en het apparaat tijdens deze bewerking.

**V:** Hoe kan ik mijn controller weer in gebruik nemen nadat deze is afgesloten en verwijderd?

**A.** Als u een controller wilt weer in gebruik nemen, moet u deze in het chassis invoegen zoals beschreven in [Een controllermodule vervangen op uw StorSimple-apparaat.](storsimple-8000-controller-replacement.md)

## <a name="next-steps"></a>Volgende stappen
* Als u problemen ondervindt met uw StorSimple-apparaatcontrollers die u niet oplossen met behulp van de procedures in deze zelfstudie, [neemt u contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Ga voor meer informatie over het gebruik van de StorSimple Device Manager-service naar [De StorSimple Device Manager-service gebruiken om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)

