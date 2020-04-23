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
1. Start de **sleutel keten toegang**op uw Mac. Open op de navigatie balk aan de **Category**linkerkant onder categorie **de map Mijn certificaten**. Zoek het SSL-certificaat dat u in de vorige sectie hebt gedownload en geef de inhoud ervan op. Selecteer alleen het certificaat (niet de persoonlijke sleutel selecteren). [Exporteer het](https://support.apple.com/kb/PH20122?locale=en_US)vervolgens.
2. Selecteer in de [Azure Portal](https://portal.azure.com/) **Bladeren alle** > **app Services**. Selecteer vervolgens uw Mobile Apps back-end. 
3. Selecteer **app Service push**onder **instellingen**. Selecteer vervolgens de naam van de notification hub. 
4. Ga naar **Apple Push Notification Services** > **Upload certificaat**. Upload het. P12-bestand, waarbij u de juiste **modus** selecteert (afhankelijk van of uw client-SSL-certificaat van eerdere productie of sandbox is). Sla de wijzigingen op.

Uw service is nu geconfigureerd om te werken met push meldingen op iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
