---
title: Tariefbeperking voor sms, e-mails, pushmeldingen
description: Begrijpen hoe Azure het aantal mogelijke sms-, e-mail-, Azure-app-push- of webhookmeldingen van een actiegroep beperkt.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 3/12/2018
ms.subservice: alerts
ms.openlocfilehash: 61e6cc22171815b15b865dd6ed5670bd9c446ead
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114334"
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>Beperking van de snelheid voor spraak-, sms-berichten, e-mails, pushmeldingen van Azure-apps en webhook-berichten
Tariefbeperking is een opschorting van meldingen die optreedt wanneer er te veel naar een bepaald telefoonnummer, e-mailadres of apparaat worden verzonden. Tariefbeperking zorgt ervoor dat waarschuwingen beheersbaar en bruikbaar zijn.

De drempelwaarden voor de tariefgrens zijn:

- **SMS**: Niet meer dan 1 SMS per 5 minuten.
- **Stem**: Niet meer dan 1 spraakoproep om de 5 minuten.
- **E-mail:** Niet meer dan 100 e-mails in een uur.
 
  Andere acties zijn niet beperkt.

## <a name="rate-limit-rules"></a>Regels voor tarieflimiet
- Een bepaald telefoonnummer of e-mail is beperkt wanneer het meer berichten ontvangt dan de drempelwaarde toestaat.
- Een telefoonnummer of e-mail kan deel uitmaken van actiegroepen voor veel abonnementen. Tariefbeperking geldt voor alle abonnementen. Het is van toepassing zodra de drempel is bereikt, zelfs als berichten worden verzonden vanaf meerdere abonnementen.
- Wanneer een e-mailadres beperkt is, wordt een extra melding verzonden om de tariefbeperking te communiceren. In de e-mail wordt vermeld wanneer de tariefbeperking verloopt.

## <a name="next-steps"></a>Volgende stappen ##
* Meer informatie over [sms-waarschuwingsgedrag](alerts-sms-behavior.md).
* Krijg een [overzicht van waarschuwingen voor activiteitenlogboeken](alerts-overview.md)en ontdek hoe u waarschuwingen ontvangt.  
* Meer informatie over het [configureren van waarschuwingen wanneer een servicestatusmelding wordt geplaatst.](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)

