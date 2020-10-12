---
title: Frequentie limiet voor SMS, e-mails, Push meldingen
description: Krijg inzicht in de manier waarop Azure het aantal mogelijke SMS-, e-mail-, Azure-app push-of webhook-meldingen beperkt van een actie groep.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 3/12/2018
ms.subservice: alerts
ms.openlocfilehash: 39b9fd8396601d777aa802a147bee3acc4a22cd0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87045297"
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>Frequentie limiet voor spraak, SMS, e-mails Azure-app push meldingen en webhook-berichten
Frequentie beperking is het opschorten van meldingen die optreden wanneer te veel wordt verzonden naar een bepaald telefoon nummer, e-mail adres of apparaat. Als u de frequentie beperkt, zorgt u ervoor dat waarschuwingen kunnen worden beheerd en wat actie kan worden uitgevoerd.

De drempel waarden voor frequentie limieten zijn:

- **SMS**: niet meer dan 1 SMS om de vijf minuten.
- **Voice**: Maxi maal 1 telefoon oproep om de vijf minuten.
- **E-mail**: maxi maal 100 e-mail berichten in een uur.
 
  Andere acties zijn geen tarieven beperkt.

## <a name="rate-limit-rules"></a>Frequentie limiet regels
- Een bepaald telefoon nummer of e-mail adres is beperkt wanneer er meer berichten worden ontvangen dan de drempel waarde toestaat.
- Een telefoon nummer of e-mail adres kan deel uitmaken van actie groepen in veel abonnementen. De frequentie beperking is van toepassing op alle abonnementen. Deze is van toepassing zodra de drempel waarde is bereikt, zelfs als berichten worden verzonden vanuit meerdere abonnementen.
- Wanneer een e-mail adres een beperkt aantal is, wordt er een extra melding verzonden om de frequentie limiet te communiceren. De e-mail status wanneer de frequentie limiet verloopt.

## <a name="next-steps"></a>Volgende stappen ##
* Meer informatie over het [gedrag van SMS-waarschuwingen](alerts-sms-behavior.md).
* Een [overzicht van waarschuwingen voor het activiteitenlogboek](alerts-overview.md) en meer informatie over het ontvangen van waarschuwingen.  
* Meer informatie over het [configureren van waarschuwingen wanneer een service status melding wordt geplaatst](../../service-health/alerts-activity-log-service-notifications-portal.md).
