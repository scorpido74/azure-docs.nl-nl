---
title: Echte gebruikersmetingen met Visual Studio Mobile Center - Azure Traffic Manager
description: Stel uw mobiele applicatie ontwikkeld met behulp van Visual Studio Mobile Center om real user measurements te sturen naar Traffic Manager
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: 3106334e1fb3e3000cbd09e00e413b34a1b55e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939185"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Echte gebruikersmetingen verzenden naar Traffic Manager met Visual Studio Mobile Center

U uw mobiele applicatie die is ontwikkeld met Visual Studio Mobile Center instellen om echte gebruikersmetingen naar Traffic Manager te verzenden door de volgende stappen te volgen:

>[!NOTE]
> Momenteel wordt het verzenden van echte gebruikersmetingen naar traffic manager alleen ondersteund voor Android.

Om echte gebruikersmetingen te configureren, moet u een sleutel en instrument van uw app met het RUM-pakket verkrijgen.

## <a name="step-1-obtain-a-key"></a>Stap 1: Een sleutel verkrijgen
    
De metingen die u uitvoert en vanuit uw clienttoepassing naar Traffic Manager stuurt, worden door de service geïdentificeerd met behulp van een unieke tekenreeks, de RUM-sleutel (Real User Measurements). U een RUM-sleutel krijgen via de Azure-portal, een REST API of via de PowerShell / CLI-interfaces.

Ga als volgt te werk om de RUM-sleutel te verkrijgen met azure portal volgens de volgende procedure:
1. Meld u vanuit een browser aan bij Azure Portal. Als u nog geen account hebt, kunt u zich registreren voor een gratis proefversie van één maand.
2. Zoek in de zoekbalk van de portal naar de naam van het Traffic Manager-profiel dat u wilt wijzigen, en klik vervolgens in de weergegeven resultaten op het Traffic Manager-profiel.
3. Klik op de profielpagina Verkeerbeheer op **Echte gebruikersmetingen** onder **Instellingen**.
4. Klik **op Sleutel genereren** om een nieuwe RUM-sleutel te maken.
        
   ![De sleutel Voor echte gebruikersmetingen genereren](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Figuur 1: Real User Measurements key generation**

5. De pagina toont de RUM-sleutel die wordt gegenereerd en een JavaScript-codefragment dat moet worden ingesloten in uw HTML-pagina.
 
   ![Javascript-code voor de sleutel voor metingen van echte gebruikers](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Figuur 2: Real User Measurements Key en Measurement JavaScript**
 
6. Klik **op** de knop Kopiëren om de RUM-sleutel te kopiëren. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>Stap 2: Instrumenter uw app met het RUM-pakket van Mobile Center SDK

Als u nieuw bent bij Visual Studio Mobile Center, bezoekt u de [website](https://mobile.azure.com). Zie Aan de slag [met de Android SDK](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android)voor gedetailleerde instructies over SDK-integratie.

Voer de volgende procedure in om echte gebruikersmetingen te gebruiken:

1.  De SDK aan het project toevoegen

    Tijdens de preview van de ATM RUM SDK moet je expliciet verwijzen naar de pakketrepository.

    Voeg in uw **app/build.gradle-bestand** de volgende regels toe:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    Voeg in uw **app/build.gradle-bestand** de volgende regels toe:

    ```groovy
    dependencies {
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. De SDK starten

    Open de hoofdactiviteitsklasse van uw app en voeg de volgende importinstructies toe:

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    Zoek naar `onCreate` de callback in hetzelfde bestand en voeg de volgende code toe:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [echte gebruikersmetingen](traffic-manager-rum-overview.md)
- Meer informatie over [hoe Traffic Manager werkt](traffic-manager-overview.md)
- Meer informatie over [Mobiel Centrum](https://docs.microsoft.com/mobile-center/)
- [Aanmelden](https://mobile.azure.com) voor Mobile Center
- Meer informatie over de [verkeersrouteringsmethoden](traffic-manager-routing-methods.md) die worden ondersteund door Traffic Manager
- Meer informatie over het [maken van een Traffic Manager-profiel](traffic-manager-create-profile.md)

