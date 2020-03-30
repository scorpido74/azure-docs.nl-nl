---
title: Batterij vervangen op microsoft Azure StorSimple 8000-serie apparaat
description: Beschrijft hoe u de back-upbatterijmodule op uw StorSimple-apparaat verwijderen, vervangen en onderhouden.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: f21bbf4777aa74e84ffb8c1af903f90608d5551f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255013"
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Noodaccumodule van StorSimple-apparaat vervangen

## <a name="overview"></a>Overzicht
De primaire energie- en koelmodule (PCM) van de behuizing op uw Microsoft Azure StorSimple-apparaat heeft een extra batterijpakket. Dit pakket levert stroom, zodat het StorSimple-apparaat gegevens kan opslaan als er een verlies van wisselstroom is voor de primaire behuizing. Dit accupakket wordt de *back-upbatterijmodule*genoemd. De back-up batterijmodule bestaat alleen voor de primaire behuizing in uw StorSimple-apparaat (de EBOD-behuizing bevat geen back-upbatterijmodule).

In deze zelfstudie wordt het volgende uitgelegd:

* De back-upbatterijmodule verwijderen
* Een nieuwe back-upbatterijmodule installeren
* De back-upbatterijmodule onderhouden

> [!IMPORTANT]
> Voordat u een back-upbatterijmodule verwijdert en vervangt, controleert u de veiligheidsinformatie in de [vervanging van StorSimple-hardwareonderdelen.](storsimple-8000-hardware-component-replacement.md)


## <a name="remove-the-backup-battery-module"></a>De back-upbatterijmodule verwijderen
De back-up batterijmodule voor uw StorSimple-apparaat is een veldvervangbare eenheid. Voordat deze in het PCM wordt geïnstalleerd, moet de batterijmodule in de originele verpakking worden opgeslagen. Voer de volgende stappen uit om de back-upbatterij te verwijderen.

#### <a name="to-remove-the-backup-battery-module"></a>De back-upbatterijmodule verwijderen
1. Ga in de Azure-portal naar uw StorSimple Device Manager-serviceblade. Ga naar **Apparaten** en selecteer uw apparaat in de lijst met apparaten. Navigeer naar **De** > **hardwarestatus controleren**. Bekijk **onder Gedeelde componenten**de status van de batterij.
2. Identificeer het PCM waarin de batterij is uitgevallen. Figuur 1 toont de achterkant van het StorSimple-apparaat.
   
    ![Backplane van primaire behuizingsmodules voor apparaten](./media/storsimple-battery-replacement/IC740994.png)
   
    **Figuur 1** Achterkant van het primaire apparaat met PCM- en controllermodules
   
   | Label | Beschrijving |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controller 0 |
   | 4 |Controller 1 |
   
    Zoals aangegeven door nummer 3 in figuur 2, moet de bewakingsindicator LED op PCM 0 die overeenkomt met **batterijfout** worden verlicht.
   
    ![Backplane of Device PCM Monitoring Indicator LED's](./media/storsimple-battery-replacement/IC740992.png)
   
    **Figuur 2** Achterkant van PCM met de controle-indicator LED's
   
   | Label | Beschrijving |
   |:--- |:--- |
   | 1 |Wisselstroomstoring |
   | 2 |Ventilatormislukking |
   | 3 |Batterijstoring |
   | 4 |PCM OK |
   | 5 |DC-stroomuitval |
   | 6 |Batterij gezond |
3. Als u de PCM met een defecte batterij wilt verwijderen, voert u de stappen uit in [Een PCM verwijderen.](storsimple-8000-power-cooling-module-replacement.md#remove-a-pcm)
4. Als de PCM is verwijderd, tilt en draai de handgreep van de batterijmodule naar boven, zoals aangegeven in de volgende figuur, en trek deze omhoog om de batterij te verwijderen.
   
    ![Batterij verwijderen uit PCM](./media/storsimple-battery-replacement/IC741019.png)
   
    **Figuur 3** De batterij uit het PCM verwijderen
5. Plaats de module in de veldvervangbare eenheidsverpakking.
6. Breng het defecte apparaat terug naar Microsoft voor goed onderhoud en onderhoud.

## <a name="install-a-new-backup-battery-module"></a>Een nieuwe back-upbatterijmodule installeren
Voer de volgende stappen uit om de vervangende batterijmodule in de PCM in de primaire behuizing van uw StorSimple-apparaat te installeren.

#### <a name="to-install-the-battery-module"></a>De batterijmodule installeren
1. Plaats de back-upbatterijmodule in de juiste richting in het PCM.
2. Druk de handgreep van de batterijmodule helemaal ingedrukt om de connector te plaatsen.
3. Vervang de PCM in de primaire behuizing door de richtlijnen te volgen in [Een stroom- en koelmodule vervangen op uw StorSimple-apparaat.](storsimple-8000-power-cooling-module-replacement.md)
4. Nadat de vervanging is voltooid, gaat u naar uw apparaat en gaat u naar**De status Hardware** **controleren** > in de Azure-portal. Controleer de status van de batterij om te controleren of de installatie is geslaagd. Een groene status geeft aan dat de batterij gezond is.

## <a name="maintain-the-backup-battery-module"></a>De back-upbatterijmodule onderhouden
In uw StorSimple-apparaat levert de back-upbatterijmodule stroom aan de controller tijdens een gebeurtenis op het gebied van stroomuitval. Hiermee kan het StorSimple-apparaat kritieke gegevens opslaan voordat deze op een gecontroleerde manier wordt afgesloten. Met twee volledig opgeladen batterijen in de PCM's kan het systeem twee opeenvolgende verliesgebeurtenissen verwerken.

In de Azure-portal geeft de **status Hardware** onder het **monitorblad** aan of de batterij defect is of het einde van de levensduur nadert. De batterijstatus wordt aangegeven door **Batterij in PCM 0** of Batterij in PCM **1** onder **Gedeelde componenten.** Dit mes zal een **gedegradeerde** toestand voor het einde van de levensduur nadert, en **mislukt** voor het einde van de levensduur bereikt.

> [!NOTE]
> De batterij kan **niet-defecten** melden wanneer deze gewoon moet worden opgeladen.


Als de **gedegradeerde** status wordt weergegeven, raden we de volgende actieaan:

* Het systeem kan een recent stroomverlies hebben ondervonden of de batterijen kunnen periodiek onderhoud ondergaan. Let 12 uur op het systeem voordat u verdergaat.
  
  * Als de status na 12 uur continue verbinding met de stroomkabels nog steeds is **afgebroken** met de controllers en pcm's die worden uitgevoerd, moet de batterij worden vervangen. Neem [contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md) voor een vervangende back-upbatterijmodule.
  * Als de toestand na 12 uur in orde is, is de batterij operationeel en had deze alleen een onderhoudslading nodig.
* Als er geen sprake is van een bijbehorend verlies van wisselstroom en de PCM is ingeschakeld en is aangesloten op wisselstroom, moet de batterij worden vervangen. [Neem contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md) om een vervangende back-upbatterijmodule te bestellen.

> [!IMPORTANT]
> Gooi de defecte batterij weg volgens nationale en regionale regelgeving.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het vervangen van StorSimple-hardwarecomponenten](storsimple-8000-hardware-component-replacement.md).

