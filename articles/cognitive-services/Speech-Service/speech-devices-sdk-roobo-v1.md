---
title: Speech-apparaten SDK roobo Smart audio dev kit v1-Speech Service
titleSuffix: Azure Cognitive Services
description: Vereisten en instructies voor het aan de slag gaan met de SDK voor spraak apparaten, roobo Smart audio dev kit v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 9add5b063b67ddcc4cd5bf93e7f5b570b004e5ca
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815590"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Apparaat: roobo Smart audio dev kit

Dit artikel bevat apparaatspecifieke informatie voor de roobo Smart audio dev kit.

## <a name="set-up-the-development-kit"></a>Instellen van de development kit

1. De Development Kit heeft twee micro USB-connectors. De linker connector is de Power Development Kit om te zetten en is gemarkeerd als stroom in de onderstaande afbeelding. De juiste is om deze te beheren en is gemarkeerd als debug in de installatie kopie.

    ![de dev kit verbinding te maken](media/speech-devices-sdk/qsg-1.png)

1. Schakel de Development Kit uit met behulp van een micro USB-kabel om de voedings poort te verbinden met een PC of een stroom adapter. Een groene stroom indicator is afhankelijk van het bovenste bord.

1. Als u de Development Kit wilt beheren, verbindt u de poort voor fout opsporing met een computer met een tweede micro USB-kabel. Het is essentieel dat u een kabel van hoge kwaliteit gebruikt om betrouw bare communicatie te garanderen.

1. Uw development kit voor de configuratie van cirkelvormige of lineaire plaatsen.

    |Development kit configuratie|Afdrukstand|
    |-----------------------------|------------|
    |Kringverwijzing|Rechtop, met microfoons geconfronteerd met het maximum|
    |Lineair|Aan de zijde met microfoons gericht u (weergegeven in de volgende afbeelding)|

    ![lineaire dev kit afdrukstand](media/speech-devices-sdk/qsg-2.png)

1. Installeer de certificaten en stel de machtigingen van het geluids apparaat in. Typ de volgende opdrachten in een opdrachtpromptvenster:

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Deze opdrachten gebruiken de brug Android foutopsporing `adb.exe`, deze maakt deel uit van de Android Studio-installatie. Dit hulpprogramma bevindt zich in C:\Users\[gebruikersnaam] \AppData\Local\Android\Sdk\platform-hulpprogramma's. U kunt deze map toevoegen aan het pad naar het eenvoudig om aan te roepen kunnen `adb`. Anders moet u het volledige pad naar uw installatie van adb.exe in elke opdracht die wordt aangeroepen `adb`.
    >
    > Als er een fout `no devices/emulators found`, controleert u of de USB-kabel is aangesloten en is de kabel van een hoge kwaliteit. U kunt `adb devices` gebruiken om te controleren of uw computer kan communiceren met de Development Kit, omdat er een lijst met apparaten wordt geretourneerd.
    >
    > [!TIP]
    > De microfoon van uw PC en de spreker om ervoor te zorgen dat u werkt met de development kit microfoons dempen. Op deze manier wordt niet u per ongeluk het apparaat met audio van de PC activeren.

1. Als u een spreker wilt toevoegen aan de dev kit, kunt u deze verbinden met de audio lijn uit. U moet een goede luid spreker met een analoge stekker van 3,5 mm kiezen.

    ![Vysor audio](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Ontwikkelings informatie

Zie de [roobo Development Guide (Engelstalig](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)) voor meer informatie over ontwikkel aars.

## <a name="audio"></a>Audio

Roobo biedt een hulp programma waarmee alle audio wordt vastgelegd in Flash-geheugen. Het kan helpen bij het oplossen van problemen met audio. Een versie van het hulpprogramma is opgegeven voor de configuratie van elke development kit. Selecteer uw apparaat op de [roobo-site](https://ddk.roobo.com/)en selecteer vervolgens de koppeling **roobo extra** onder aan de pagina.

## <a name="next-steps"></a>Volgende stappen

* [De Android-voor beeld-app uitvoeren](speech-devices-sdk-android-quickstart.md)
