---
title: Een PCM vervangen op uw StorSimple 8000-serie apparaat | Microsoft Documenten
description: Legt uit hoe u de energie- en koelmodule (PCM) op uw StorSimple-apparaat verwijderen en vervangen
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
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 42561570e24aec5edd33248ef1738e53175e480e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60632415"
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Stroom- en koelmodule van StorSimple-apparaat vervangen
## <a name="overview"></a>Overzicht
De Power and Cooling Module (PCM) in uw Microsoft Azure StorSimple-apparaat bestaat uit een voeding en koelventilatoren die worden bediend via de primaire en EBOD-behuizingen. Er is slechts één model PCM dat is gecertificeerd voor elke behuizing. De primaire behuizing is gecertificeerd voor een PCM van 764 W en de EBOD-behuizing is gecertificeerd voor een PCM van 580 W. Hoewel de PCM's voor de primaire behuizing en de EBOD-behuizing verschillend zijn, is de vervangingsprocedure identiek.

In deze zelfstudie wordt het volgende uitgelegd:

* Een PCM verwijderen
* Een vervangende PCM installeren

> [!IMPORTANT]
> Voordat u een PCM verwijdert en vervangt, controleert u de veiligheidsinformatie in [StorSimple-hardwarecomponentvervanging](storsimple-8000-hardware-component-replacement.md).


## <a name="before-you-replace-a-pcm"></a>Voordat u een PCM vervangt
Houd rekening met de volgende belangrijke problemen voordat u uw PCM vervangt:

* Als de voeding van de PCM uitvalt, laat u de defecte module geïnstalleerd, maar verwijdert u het netsnoer. De ventilator blijft stroom uit de behuizing ontvangen en blijft zorgen voor een goede koeling. Als de ventilator uitvalt, moet de PCM onmiddellijk worden vervangen.
* Voordat u de PCM verwijdert, koppelt u de stroom los van het PCM door de hoofdschakelaar (indien aanwezig) uit te schakelen of door het netsnoer fysiek te verwijderen. Dit geeft een waarschuwing aan uw systeem dat een stroomuitschakeling op handen is.
* Zorg ervoor dat de andere PCM functioneel is voor verdere werking van het systeem voordat u de defecte PCM vervangt. Een defecte PCM moet zo snel mogelijk worden vervangen door een volledig operationeel PCM.
* Pcm module vervanging duurt slechts enkele minuten om te voltooien, maar het moet worden voltooid binnen 10 minuten na het verwijderen van de mislukte PCM om oververhitting te voorkomen.
* Houd er rekening mee dat de vervangende 764 W PCM-modules die vanuit de fabriek worden verzonden, geen back-upbatterijmodule bevatten. U moet de batterij uit uw defecte PCM verwijderen en deze vervolgens in de vervangingsmodule plaatsen voordat u de vervanging uitvoert. Zie voor meer informatie hoe u [een back-upbatterijmodule verwijdert en invoegt.](storsimple-8000-battery-replacement.md)

## <a name="remove-a-pcm"></a>Een PCM verwijderen
Volg deze instructies wanneer u klaar bent om een Energie- en Koelmodule (PCM) van uw Microsoft Azure StorSimple-apparaat te verwijderen.

> [!NOTE]
> Controleer voordat u uw PCM verwijdert of u een juiste vervanging hebt (764 W voor de primaire behuizing of 580 W voor de EBOD-behuizing).

#### <a name="to-remove-a-pcm"></a>Een PCM verwijderen
1. Klik in de klassieke Azure-portal op **Instellingen > Monitor > Hardware-status**. Controleer de status van de PCM-componenten onder **Gedeelde componenten** om te bepalen welke PCM is mislukt:
   
   * Als een voeding in PCM 0 is mislukt, is de status van **voeding in PCM 0** rood.
   * Als een voeding in PCM 1 is mislukt, is de status van **voeding in PCM 1** rood.
   * Als de ventilator in PCM 1 is mislukt, is de status van **koeling 0 voor PCM 0** of Cooling 1 voor PCM **0** rood.
2. Zoek het mislukte PCM aan de achterkant van de primaire behuizing. Als u een 8600-model uitvoert, identificeert u de primaire behuizing door te kijken naar het systeemeenheididentificatienummer dat wordt weergegeven op het LED-display voor het voorpaneel. De standaard eenheids-id die op de primaire behuizing wordt weergegeven, is **00**, terwijl de standaard eenheids-id die op de EBOD-behuizing wordt weergegeven **01**is. In het volgende diagram en de volgende tabel wordt het voorpaneel van het LED-display uitgelegd.
   
    ![Systeem-ID op het ops-paneel aan de voorzijde](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **Figuur 1** Voorpaneel van het apparaat  
   
   | Label | Beschrijving |
   |:--- |:--- |
   | 1 |Knop Dempen |
   | 2 |Systeemvermogen |
   | 3 |Modulefout |
   | 4 |Logische fout |
   | 5 |Beeldscherm van eenheids-id |
3. De bewakingsindicator LED's aan de achterkant van de primaire behuizing kunnen ook worden gebruikt om de defecte PCM te identificeren. Zie het volgende diagram en de volgende tabel om te begrijpen hoe u de LED's gebruiken om de defecte PCM te vinden. Als de LED die overeenkomt met de **Ventilatorfail** bijvoorbeeld is aangestoken, is de ventilator mislukt. Ook als de LED die overeenkomt met **AC Fail** is verlicht, is de voeding uitgevallen. 
   
    ![Backplane van device PCM monitoring indicator LED's](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **Figuur 2** Achterkant van PCM met indicator-LED's
   
   | Label | Beschrijving |
   |:--- |:--- |
   | 1 |Wisselstroomstoring |
   | 2 |Ventilatormislukking |
   | 3 |Batterijstoring |
   | 4 |PCM OK |
   | 5 |DC-stroomuitval |
   | 6 |Batterij gezond |
4. Raadpleeg het volgende diagram van de achterkant van het StorSimple-apparaat om de mislukte PCM-module te vinden. PCM 0 is aan de linkerkant en PCM 1 is aan de rechterkant. De tabel die volgt legt de modules uit.
   
     ![Backplane van primaire behuizingsmodules van het apparaat](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **Figuur 3** Achterkant van het apparaat met plug-in modules 
   
   | Label | Beschrijving |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controller 0 |
   | 4 |Controller 1 |
5. Schakel de defecte PCM uit en koppel het stroomkabelsnoer los. U nu de PCM verwijderen.
6. Pak de vergrendeling en de zijkant van de PCM-handgreep tussen uw duim en wijsvinger en knijp ze samen om het handvat te openen.
   
    ![PCM-greep openen](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **Figuur 4** De PCM-greep openen
7. Grijp het handvat vast en verwijder de PCM.
   
    ![Apparaat PCM verwijderen](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **Figuur 5** De PCM verwijderen

## <a name="install-a-replacement-pcm"></a>Een vervangende PCM installeren
Volg deze instructies om een PCM in uw StorSimple-apparaat te installeren. Zorg ervoor dat u de back-upbatterijmodule hebt geplaatst voordat u de vervangende PCM installeert (geldt alleen voor pcm's van 764 W). Zie voor meer informatie hoe u [een back-upbatterijmodule verwijdert en invoegt.](storsimple-8000-battery-replacement.md)

#### <a name="to-install-a-pcm"></a>Een PCM installeren
1. Controleer of u over de juiste vervangingsPCM voor deze behuizing beschikt. De primaire behuizing heeft een PCM van 764 W nodig en de EBOD-behuizing heeft een PCM van 580 W nodig. U moet niet proberen om de 580 W PCM te gebruiken in de primaire behuizing, of de 764 W PCM in de EBOD-behuizing. In de volgende afbeelding ziet u waar u deze informatie identificeren op het label dat op het PCM is aangebracht.
   
    ![Apparaat PCM-label](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **Figuur 6** PCM-label
2. Controleer op schade aan de behuizing, met bijzondere aandacht voor de connectoren. 
   
   > [!NOTE]
   > **Installeer de module niet als er verbindingspennen zijn gebogen.**
   > 
   > 
3. Schuif de module in de behuizing met de PCM-handgreep in de open positie.
   
    ![Apparaat PCM installeren](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **Figuur 7** Het PCM installeren
4. Sluit handmatig de PCM-greep. U hoort een klik als de handgreep vergrendeling inschakelt.
   
   > [!NOTE]
   > Om ervoor te zorgen dat de connectorpinnen zijn ingeschakeld, u voorzichtig aan het handvat trekken zonder de vergrendeling los te laten. Als de PCM uitschuift, betekent dit dat de vergrendeling is gesloten voordat de connectoren zijn ingeschakeld.
   
5. Sluit de stroomkabels aan op de stroombron en op het PCM.
6. Zet de stamhulpbalen vast.
7. Schakel het PCM in.
8. Controleer of de vervanging is geslaagd: ga in de Azure-portal van uw StorSimple Device Manager-service naar uw apparaat en vervolgens naar **Instellingen > Monitor > Hardware-status**. Onder de **gedeelde componenten**moet de status van het PCM groen zijn.
   
   > [!NOTE]
   > Het kan enkele minuten duren voordat de vervanging PCM volledig geïnitialiseerde.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het vervangen van StorSimple-hardwarecomponenten](storsimple-8000-hardware-component-replacement.md).

