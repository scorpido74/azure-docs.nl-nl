---
title: De Azure IoT Central-apparaatbrug bouwen | Microsoft Documenten
description: Bouw de IoT Central-apparaatbrug om andere IoT-clouds (Sigfox, Particle, The Things Network, enz.) aan te sluiten op uw IoT Central-app.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: how-to
manager: peterpr
ms.openlocfilehash: 6499c9c29d10a2056b0af5499b68b5edd67d82cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158415"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Bouw de IoT Central-apparaatbrug om andere IoT-clouds aan te sluiten op IoT Central

*Dit onderwerp is van toepassing op beheerders.*

De IoT Central-apparaatbrug is een open-source oplossing die uw Sigfox, Particle, The Things Network en andere clouds verbindt met uw IoT Central-app. Of u nu assettrackingapparaten gebruikt die zijn aangesloten op sigfox's Low-Power-Wide Area Network, of met behulp van luchtkwaliteitsbewakingsapparatuur op de Particle Device Cloud, of met behulp van apparatuur voor het monitoren van bodemvocht op TTN, u direct gebruikmaken van de kracht van IoT Centraal met behulp van de IoT Central-apparaatbrug. De apparaatbrug verbindt andere IoT-clouds met IoT Central door de gegevens die uw apparaten naar de andere clouds verzenden door te sturen naar uw IoT Central-app. In uw IoT Central-app u regels bouwen en analyses uitvoeren op die gegevens, werkstromen maken in Microsoft Flow- en Azure Logic-apps, die gegevens exporteren en nog veel meer. Haal de [IoT Central-apparaatbrug](https://aka.ms/iotcentralgithubdevicebridge) van GitHub

## <a name="what-is-it-and-how-does-it-work"></a>Wat is het en hoe werkt het?
De IoT Central device bridge is een open-source oplossing in GitHub. Het is klaar om te gaan met een knop 'Implementeren naar Azure' waarmee een aangepaste Azure Resource Manager-sjabloon met verschillende Azure-bronnen wordt geïmplementeerd in uw Azure-abonnement. De middelen omvatten:
-    Azure-functie-app
-    Azure Storage-account
-    Verbruiksabonnement
-    Azure Key Vault

De functie-app is het kritieke stuk van de apparaatbrug. Het ontvangt HTTP POST verzoeken van andere IoT-platforms of aangepaste platforms via een eenvoudige webhook integratie. We hebben voorbeelden gegeven die laten zien hoe je verbinding maken met Sigfox- En Particle- en TTN-wolken. U deze oplossing eenvoudig uitbreiden om verbinding te maken met uw aangepaste IoT-cloud als uw platform HTTP POST-verzoeken naar uw functie-app kan verzenden.
De functie-app transformeert de gegevens in een formaat dat door IoT Central wordt geaccepteerd en stuurt deze door via DPS API's.

![Schermafbeelding van Azure-functies](media/howto-build-iotc-device-bridge/azfunctions.png)

Als uw IoT Central-app het apparaat herkent op apparaat-id in het doorgestuurde bericht, verschijnt er een nieuwe meting voor dat apparaat. Als de apparaat-id nog nooit is gezien door uw IoT Central-app, probeert uw functie-app een nieuw apparaat te registreren met die apparaat-id en wordt deze weergegeven als een 'Niet-gekoppeld apparaat' in uw IoT Central-app. 

## <a name="how-do-i-set-it-up"></a>Hoe stel ik het in?
De instructies worden in detail vermeld in het README-bestand in de GitHub repo. 

## <a name="pricing"></a>Prijzen
De Azure-resources worden gehost in uw Azure-abonnement. Meer informatie over prijzen vindt u in het [README-bestand.](https://aka.ms/iotcentralgithubdevicebridge)

## <a name="next-steps"></a>Volgende stappen
Nu je hebt geleerd hoe je de IoT Central-apparaatbrug bouwen, is hier de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Uw apparaten beheren](howto-manage-devices.md)
