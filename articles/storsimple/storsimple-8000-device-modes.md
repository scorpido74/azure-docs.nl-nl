---
title: StorSimple-apparaatmodus wijzigen | Microsoft Documenten
description: Beschrijft de storSimple-apparaatmodi en legt uit hoe u Windows PowerShell voor StorSimple gebruiken om de apparaatmodus te wijzigen.
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
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: e55964beff48df6ce24d99c01975d39b662f1612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60576086"
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>De apparaatmodus op uw StorSimple-apparaat wijzigen

Dit artikel geeft een korte beschrijving van de verschillende modi waarin uw StorSimple-apparaat kan werken. Uw StorSimple-apparaat kan in drie modi functioneren: normaal, onderhoud en herstel.

Na het lezen van dit artikel, zult u weten:

* Wat de StorSimple-apparaatmodi zijn
* Hoe om erachter te komen in welke modus de StorSimple apparaat is in
* Hoe over te schakelen van de normale naar de onderhoudsmodus en *vice versa*

De bovenstaande beheertaken kunnen alleen worden uitgevoerd via de Windows PowerShell-interface van uw StorSimple-apparaat.

## <a name="about-storsimple-device-modes"></a>Over StorSimple-apparaatmodi

Uw StorSimple-apparaat kan werken in de normale, onderhouds- of herstelmodus. Elk van deze modi wordt hieronder kort beschreven.

### <a name="normal-mode"></a>Normale modus

Dit wordt gedefinieerd als de normale operationele modus voor een volledig geconfigureerd StorSimple-apparaat. Standaard moet uw apparaat in de normale modus staan.

### <a name="maintenance-mode"></a>Onderhoudsmodus

Soms moet het StorSimple-apparaat in de onderhoudsmodus worden geplaatst. Met deze modus u onderhoud uitvoeren op het apparaat en storende updates installeren, zoals updates die betrekking hebben op de firmware van de schijf.

U het systeem alleen in de onderhoudsmodus zetten via de Windows PowerShell voor StorSimple. Alle I/O-aanvragen worden onderbroken in deze modus. Ook services zoals niet-vluchtig random access memory (NVRAM) of de clusterservice worden gestopt. Beide controllers worden opnieuw gestart wanneer u deze modus in- of uitsluit. Wanneer u de onderhoudsmodus verlaat, worden alle services hervat en moeten ze gezond zijn. Dit kan enkele minuten duren.

> [!NOTE]
> **De onderhoudsmodus wordt alleen ondersteund op een goed functionerend apparaat. Het wordt niet ondersteund op een apparaat waarin een of beide controllers niet functioneren.**


### <a name="recovery-mode"></a>Herstelmodus

Herstelmodus kan worden omschreven als "Windows Safe Mode with network support". De herstelmodus schakelt het Microsoft Support-team in en stelt hen in staat om diagnoses op het systeem uit te voeren. Het primaire doel van de herstelmodus is het ophalen van de systeemlogboeken.

Als uw systeem in de herstelmodus wordt gebruikt, moet u contact opnemen met Microsoft Support voor de volgende stappen. Ga voor meer informatie naar [Microsoft Support](storsimple-8000-contact-microsoft-support.md).

> [!NOTE]
> **U het apparaat niet in de herstelmodus plaatsen. Als het apparaat zich in een slechte staat bevindt, probeert de herstelmodus het apparaat in een toestand te krijgen waarin microsoft-ondersteuningspersoneel het kan onderzoeken.**

## <a name="determine-storsimple-device-mode"></a>De StorSimple-apparaatmodus bepalen

#### <a name="to-determine-the-current-device-mode"></a>De huidige apparaatmodus bepalen

1. Meld u aan bij de seriële console van het apparaat door de stappen te volgen in [PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Kijk naar het bannerbericht in het seriële consolemenu van het apparaat. Dit bericht geeft expliciet aan of het apparaat zich in de onderhouds- of herstelmodus bevindt. Als het bericht geen specifieke informatie bevat met betrekking tot de systeemmodus, bevindt het apparaat zich in de normale modus.

## <a name="change-the-storsimple-device-mode"></a>De StorSimple-apparaatmodus wijzigen

U het StorSimple-apparaat in de onderhoudsmodus plaatsen (vanuit de normale modus) om onderhoudsupdates uit te voeren of onderhoudsmodus-updates te installeren. Voer de volgende procedures uit om de onderhoudsmodus in of uit te sluiten.

> [!IMPORTANT]
> Controleer voordat u de onderhoudsmodus invoert of beide apparaatcontrollers in orde zijn door toegang te krijgen tot de **status apparaatinstellingen > Hardware** voor uw apparaat in de Azure-portal. Als een of beide controllers niet in orde zijn, neemt u contact op met Microsoft Support voor de volgende stappen. Ga voor meer informatie naar [Microsoft Support](storsimple-8000-contact-microsoft-support.md).
 

#### <a name="to-enter-maintenance-mode"></a>De onderhoudsmodus invoeren

1. Meld u aan bij de seriële console van het apparaat door de stappen te volgen in [PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Kies in het menu van de seriële console optie 1, **Log in met volledige toegang**. Geef het wachtwoord van de **apparaatbeheerder**op wanneer u daarom wordt gevraagd. Het standaardwachtwoord `Password1`is: .
3. Typ bij de opdrachtprompt 
   
    `Enter-HcsMaintenanceMode`
4. U ziet een waarschuwingsbericht waarin staat dat de onderhoudsmodus alle I/O-aanvragen verstoort en de verbinding met de Azure-portal wordt verbroken en dat u om bevestiging wordt gevraagd. Type **Y** om de onderhoudsmodus in te voeren.
5. Beide controllers worden opnieuw opgestart. Wanneer de herstart is voltooid, geeft de seriële consolebanner aan dat het apparaat in de onderhoudsmodus staat. Hieronder ziet u een voorbeeld van de uitvoer.

```
    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Controller0>Enter-HcsMaintenanceMode
    Checking device state...

    In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>

```

#### <a name="to-exit-maintenance-mode"></a>De onderhoudsmodus afsluiten

1. Meld u aan bij de seriële console van het apparaat. Controleer in het bannerbericht of uw apparaat in de onderhoudsmodus staat.
2. Typ het volgende na de opdrachtprompt:
   
    `Exit-HcsMaintenanceMode`
3. Er verschijnt een waarschuwingsbericht en een bevestigingsbericht. Type **Y** om de onderhoudsmodus af te sluiten.
4. Beide controllers worden opnieuw opgestart. Wanneer de herstart is voltooid, geeft de seriële consolebanner aan dat het apparaat zich in de normale modus bevindt. Hieronder ziet u een voorbeeld van de uitvoer.

```
    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0
    ---------------------------------------------------------------

    Controller0>Exit-HcsMaintenanceMode
    Checking device state...

    Before exiting maintenance mode, ensure that any updates that are required on both controllers have been applied. Failure to install on each controller could result in data corruption. Exiting maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to exit maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Active
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [toepassen van updates in de normale en onderhoudsmodus](storsimple-update-device.md) op uw StorSimple-apparaat.

