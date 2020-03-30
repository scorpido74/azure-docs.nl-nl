---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 852a3b00e8513f9ce68c5aae54c974505d0c9af6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176533"
---
1. Start **Keychain Access**op je Mac. Open **Mijn certificaten**op de linkernavigatiebalk onder **Rubriek**. Zoek het SSL-certificaat dat u in de vorige sectie hebt gedownload en maak de inhoud ervan bekend. Selecteer alleen het certificaat (selecteer niet de privésleutel). Exporteer [het](https://support.apple.com/kb/PH20122?locale=en_US)dan.
2. Selecteer in de [Azure-portal](https://portal.azure.com/) **Bladeren door Alle** > **App-services**. Selecteer vervolgens de back-end van mobiele apps. 
3. Selecteer **onder Instellingen**de optie App Service **Push**. Selecteer vervolgens de naam van de meldingshub. 
4. Ga naar **het Uploadcertificaat voor PushNotification Services** > **van**Apple. Upload het .p12-bestand en selecteer de juiste **modus** (afhankelijk van of uw client SSL-certificaat van vroeger productie of sandbox is). Eventuele wijzigingen opslaan.

Uw service is nu geconfigureerd om te werken met pushmeldingen op iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
