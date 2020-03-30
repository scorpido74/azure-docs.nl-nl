---
title: APNS VOIP-meldingen verzenden met Azure Notification Hubs
description: Meer informatie over het verzenden van APNS VOIP-meldingen via Azure Notification Hubs (niet officieel ondersteund).
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80146886"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>APNS VOIP gebruiken via Meldingshubs (niet officieel ondersteund)

Het is mogelijk om APNS VOIP-meldingen te gebruiken via Azure Notification Hubs; er is echter geen officiële steun voor dit scenario.

## <a name="considerations"></a>Overwegingen

Als u er nog steeds voor kiest om APNS VOIP-meldingen via Notification Hubs te verzenden, moet u rekening houden met de volgende beperkingen:

- Voor het verzenden van `apns-topic` een VOIP-melding moet de `.voip` koptekst worden ingesteld op de toepassingsbundel-id + het achtervoegsel. Voor een voorbeeld-app met de `com.microsoft.nhubsample`bundel-id moet de `apns-topic` koptekst bijvoorbeeld worden ingesteld op`com.microsoft.nhubsample.voip.`

   Deze methode werkt niet goed met Azure Notification Hubs, omdat de bundel-id van de app moet worden geconfigureerd als onderdeel van de APNS-referenties van de hub en de waarde niet kan worden gewijzigd. Ook betekent Notification Hubs niet dat `apns-topic` de waarde van de koptekst wordt overschreven tijdens runtime.

   Als u VOIP-meldingen wilt verzenden, moet `.voip` u een afzonderlijke meldingshub configureren met de app-bundel-id.

- Voor het verzenden van `apns-push-type` een VOIP-melding `voip`moet de koptekst worden ingesteld op de waarde .

   Om klanten te helpen bij de overgang naar iOS 13, `apns-push-type` probeert Notification Hubs de juiste waarde voor de header af te leiden. De gevolgtrekking logica is opzettelijk eenvoudig, in een poging om te voorkomen dat het breken van standaard meldingen. Helaas veroorzaakt deze methode problemen met VOIP-meldingen, omdat Apple VOIP-meldingen behandelt als een speciaal geval dat niet dezelfde regels volgt als standaardmeldingen.

   Als u VOIP-meldingen wilt verzenden, `apns-push-type` moet u een expliciete waarde voor de koptekst opgeven.

- Notification Hubs beperkt APNS-payloads tot 4 KB, zoals gedocumenteerd door Apple. Voor VOIP-meldingen staat Apple payloads tot 5 KB toe. Notification Hubs maakt geen onderscheid tussen standaard- en VOIP-meldingen; daarom zijn alle meldingen beperkt tot 4 KB.

   Als u VOIP-meldingen wilt verzenden, mag u de limiet voor de grootte van het laadvermogen van 4 KB niet overschrijden.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende koppelingen voor meer informatie:

- [Documentatie `apns-topic` voor `apns-push-type` en headers en waarden, inclusief de speciale gevallen voor VOIP-meldingen.](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)

- [Documentatie voor de limiet voor de grootte van het laadvermogen](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification).

- [Updaten van Notification Hubs voor iOS 13](push-notification-updates-ios-13.md#apns-push-type).
