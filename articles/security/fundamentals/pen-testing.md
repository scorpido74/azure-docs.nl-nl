---
title: Pen testen | Microsoft Docs
description: In dit artikel vindt u een overzicht van het proces van indringings tests (Pentest) en hoe u Pentest kunt uitvoeren op uw apps die worden uitgevoerd in de Azure-infra structuur.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2018
ms.author: terrylan
ms.openlocfilehash: 413ec8b121838a4ffac4119421ec3266e141618b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80549255"
---
# <a name="penetration-testing"></a>Indringings tests
Een van de voor delen van het gebruik van Azure voor het testen en implementeren van toepassingen is dat u snel omgevingen kunt maken. U hoeft zich geen zorgen te maken over uw eigen on-premises hardware voor het aanvragen van opdrachten, voor het verkrijgen en ' rekken en stapelen '.

Dit is geweldig, maar u moet er wel zeker van zijn dat u uw normale beveiligings toelaat. Een van de dingen die u waarschijnlijk wilt doen, is de toepassingen die u in azure implementeert, door penetratie testen.

Mogelijk weet u dat micro soft de [Azure-omgeving indringings tests](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)uitvoert. Dit helpt Azure-verbeteringen te stimuleren.

Uw toepassing wordt niet door indringing geprotesteerd, maar we willen wel weten dat u uw eigen toepassingen wilt testen. Het is een goed idee, omdat wanneer u de beveiliging van uw toepassingen verbetert, u het volledige Azure-ecosysteem beter kunt beveiligen.

Vanaf 15 juni 2017 heeft micro soft niet langer vooraf-goed keuring nodig voor het uitvoeren van een indringings test op Azure-resources. Klanten die aanstaande indringings tests op het Microsoft Azure willen documenteren, worden aangemoedigd om het formulier voor het testen van de [Azure service-Indringings melding](https://portal.msrc.microsoft.com/en-us/engage/pentest)in te vullen. Dit proces is alleen gerelateerd aan Microsoft Azure en is niet van toepassing op een andere Microsoft Cloud service.

>[!IMPORTANT]
>Hoewel het niet meer nodig is dat micro soft op de hoogte wordt gebracht van de test activiteiten van de pen, moeten de klanten nog steeds voldoen aan de [Microsoft Cloud gecombineerde Indringings test regels van engagement](https://technet.microsoft.com/mt784683).

U kunt onder andere de volgende standaard tests uitvoeren:

* Testen op uw eind punten om de [OWASP (open Web Application Security project) van de Top 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) van beveiligings problemen op te slaan
* [Fuzzy tests](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) van uw eind punten
* [Poort scans](https://en.wikipedia.org/wiki/Port_scanner) van uw eind punten

Een soort test die u niet kunt uitvoeren, is een [DOS-aanval (Denial of service)](https://en.wikipedia.org/wiki/Denial-of-service_attack) . Dit omvat het initiëren van een DoS-aanval zelf of het uitvoeren van gerelateerde tests die het type DoS-aanval kunnen bepalen, demonstreren of simuleren.

## <a name="next-steps"></a>Volgende stappen

- Als u een aanstaande indringings test wilt documenteren voor uw toepassingen die worden gehost in Microsoft Azure, gaat u naar de [Indringings test regels van betrokkenheid](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) en vult u het formulier test melding in.
