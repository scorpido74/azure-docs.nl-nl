---
title: Gedrag van sms-waarschuwingen in actiegroepen
description: Sms-berichtindeling en reageren op sms-berichten om je af te melden, opnieuw in te schrijven of hulp te vragen.
author: dkamstra
ms.author: dukek
services: monitoring
ms.topic: conceptual
ms.date: 02/16/2018
ms.subservice: alerts
ms.openlocfilehash: b75bda626f887f1224c1b0f18a80887983a2367d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665303"
---
# <a name="sms-alert-behavior-in-action-groups"></a>Sms-waarschuwingsgedrag in actiegroepen

## <a name="overview"></a>Overzicht 
Met actiegroepen u een lijst met acties configureren. Deze groepen worden gebruikt bij het definiëren van waarschuwingen; ervoor te zorgen dat een bepaalde actiegroep wordt aangemeld wanneer de waarschuwing wordt geactiveerd. Een van de ondersteunde acties is SMS; SMS-meldingen ondersteunen bidirectionele communicatie. Een gebruiker kan reageren op een sms op:

- **Afmelden voor waarschuwingen:** Een gebruiker kan zich afmelden voor alle sms-berichten voor alle actiegroepen of één actiegroep.
- **Abonneer u opnieuw op waarschuwingen:** Een gebruiker kan zich opnieuw abonneren op alle sms-berichten voor alle actiegroepen of één actiegroep.  
- **Hulp aanvragen:** Een gebruiker kan vragen om meer informatie over de SMS. Ze worden doorgestuurd naar dit artikel.

Dit artikel behandelt het gedrag van de SMS-waarschuwingen en de reactieacties die de gebruiker kan uitvoeren op basis van de landinstelling van de gebruiker:

## <a name="receiving-an-sms-alert"></a>Een sms-melding ontvangen
Een sms-ontvanger die is geconfigureerd als onderdeel van een actiegroep, ontvangt een sms wanneer een waarschuwing wordt geactiveerd. De SMS bevat de volgende informatie:
* Shortname van de actiegroep deze waarschuwing is verzonden naar
* Titel van de waarschuwing

| REPLY | Beschrijving |
| ----- | ----------- |
| Uitschakelen`<Action Group Short name>` | Hiermee schakelt u verdere sms'en van de actiegroep uit |
| Inschakelen`<Action Group Short name>` | Maakt sms'en van de actiegroep opnieuw in gebruik |
| Stoppen | Hiermee schakelt u verdere SMS van alle actiegroepen uit |
| Start | Maakt sms'jes van ALLE actiegroepen opnieuw in gebruik |
| HELP | Er wordt een antwoord naar de gebruiker gestuurd met een link naar dit artikel. |

>[!NOTE]
>Als een gebruiker zich heeft afgemeld voor sms-berichten, maar vervolgens wordt toegevoegd aan een nieuwe actiegroep; ze ontvangen sms-berichten voor die nieuwe actiegroep, maar blijven zich afmelden voor alle vorige actiegroepen.

## <a name="next-steps"></a>Volgende stappen
Een [overzicht van waarschuwingen voor activiteitenlogboeken](alerts-overview.md) en meer informatie over hoe u wordt gewaarschuwd  
Meer informatie over [sms-tariefbeperking](alerts-rate-limiting.md)  
Meer informatie over [actiegroepen](../../azure-monitor/platform/action-groups.md)

