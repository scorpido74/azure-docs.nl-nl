---
title: Spraakapparaten SDK Roobo Smart Audio Dev Kit v1 - Spraakservice
titleSuffix: Azure Cognitive Services
description: Voorwaarden en instructies om aan de slag te gaan met de Speech Devices SDK, Roobo Smart Audio Dev Kit v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 9add5b063b67ddcc4cd5bf93e7f5b570b004e5ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74815590"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Apparaat: Roobo Smart Audio Dev Kit

Dit artikel bevat apparaatspecifieke informatie voor de Roobo Smart Audio Dev Kit.

## <a name="set-up-the-development-kit"></a>De ontwikkelkit instellen

1. De development kit heeft twee micro USB-aansluitingen. De linker connector is om de macht van de ontwikkeling kit en wordt gemarkeerd als Macht in de afbeelding hieronder. De juiste is om het te controleren, en is gemarkeerd Foutopsporing in de afbeelding.

    ![De dev-kit aansluiten](media/speech-devices-sdk/qsg-1.png)

1. Power the development kit by using a micro USB cable to connect the power port to a PC or power adapter. Een groene stroomindicator zal onder het bovenbord oplichten.

1. Als u de ontwikkelingskit wilt bedienen, sluit u de foutopsporingspoort aan op een computer met behulp van een tweede micro-USB-kabel. Het is essentieel om een kabel van hoge kwaliteit te gebruiken om betrouwbare communicatie te garanderen.

1. Oriënteer uw ontwikkelkit voor de circulaire of lineaire configuratie.

    |Configuratie van de ontwikkelingskit|Afdrukstand|
    |-----------------------------|------------|
    |Circulaire|Rechtop, met microfoons naar het plafond gericht|
    |Lineair|Aan de zijkant, met microfoons naar u toe (weergegeven in de volgende afbeelding)|

    ![Lineaire dev kit oriëntatie](media/speech-devices-sdk/qsg-2.png)

1. Installeer de certificaten en stel de machtigingen van het geluidsapparaat in. Typ de volgende opdrachten in een opdrachtpromptvenster:

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Deze opdrachten maken gebruik van `adb.exe`de Android Debug Bridge, die deel uitmaakt van de Android Studio-installatie. Deze tool bevindt zich\[in C:\Gebruikers gebruikersnaam]\AppData\Local\Android\Sdk\platform-tools. U deze map aan uw pad toevoegen `adb`om het gemakkelijker te maken om een beroep te doen. Anders moet u het volledige pad naar uw installatie van adb.exe opgeven in elke opdracht die aanroept. `adb`
    >
    > Als u een `no devices/emulators found` fout ziet, controleer dan of uw USB-kabel is aangesloten en een kabel van hoge kwaliteit is. U kunt `adb devices` gebruiken om te controleren of uw computer kan praten met de ontwikkeling kit als het zal een lijst van apparaten terug.
    >
    > [!TIP]
    > Demp de microfoon en luidspreker van uw pc om er zeker van te zijn dat u met de microfoons van de ontwikkelkit werkt. Op deze manier activeert u het apparaat niet per ongeluk met audio van de pc.

1. Als u een luidspreker aan de dev-kit wilt bevestigen, u deze aansluiten op de audiolijn. U moet kiezen voor een luidspreker van goede kwaliteit met een 3,5 mm analoge stekker.

    ![Vysor audio](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Ontwikkelingsinformatie

Zie voor meer informatie over ontwikkeling de [Roobo-ontwikkelingsgids.](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)

## <a name="audio"></a>Audio

Roobo biedt een tool die alle audio vastlegt op flashgeheugen. Het kan u helpen audioproblemen op te lossen. Voor elke configuratie van de ontwikkelkit is een versie van de tool beschikbaar. Selecteer op de [Roobo-site](https://ddk.roobo.com/)uw apparaat en selecteer vervolgens de link **Roobo Tools** onderaan de pagina.

## <a name="next-steps"></a>Volgende stappen

* [De Voorbeeld-app Voor Android uitvoeren](speech-devices-sdk-android-quickstart.md)
