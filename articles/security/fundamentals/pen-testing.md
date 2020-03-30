---
title: Pentests | Microsoft Documenten
description: Het artikel geeft een overzicht van het penetratietestproces (pentest) en hoe pentest wordt uitgevoerd tegen uw apps die in azure-infrastructuur worden uitgevoerd.
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
ms.author: barclayn
ms.openlocfilehash: 3a2addce83862ef109089f1474330f3821daaed7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726705"
---
# <a name="penetration-testing"></a>Penetratietesten
Een van de voordelen van het gebruik van Azure voor het testen en implementeren van toepassingen is dat u snel omgevingen maken. U hoeft zich geen zorgen te maken over het opvragen, verwerven en "rekken en stapelen" van uw eigen on-premises hardware.

Dit is geweldig - maar je moet er nog steeds voor zorgen dat u uw normale veiligheid due diligence uit te voeren. Een van de dingen die u waarschijnlijk wilt doen, is penetratietest van de toepassingen die u implementeert in Azure.

Mogelijk weet u al dat Microsoft [penetratietesten van onze Azure-omgeving](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)uitvoert. Dit helpt azure-verbeteringen te stimuleren.

We testen uw toepassing niet voor u, maar we begrijpen wel dat u testen op uw eigen toepassingen wilt en moet uitvoeren. Dat is een goede zaak, want wanneer u de beveiliging van uw toepassingen verbetert, helpt u het hele Azure-ecosysteem veiliger te maken.

Vanaf 15 juni 2017 heeft Microsoft geen goedkeuring meer nodig om een penetratietest uit te voeren tegen Azure-resources. Klanten die aankomende penetratietestopdrachten tegen Microsoft Azure formeel willen documenteren, worden aangemoedigd om het [azure service penetration testing notification-formulier](https://portal.msrc.microsoft.com/en-us/engage/pentest)in te vullen. Dit proces is alleen gerelateerd aan Microsoft Azure en niet van toepassing op andere Microsoft Cloud Service.

>[!IMPORTANT]
>Hoewel het melden van Microsoft over pentestactiviteiten niet langer vereist is, moeten klanten nog steeds voldoen aan de [Microsoft Cloud Unified Penetration Testing Rules of Engagement.](https://technet.microsoft.com/mt784683)

Standaardtests die u uitvoeren zijn onder andere:

* Test op uw eindpunten om de [Top 10-kwetsbaarheden (Open Web Application Security Project) te](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) ontdekken
* [Fuzz testen](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) van uw eindpunten
* [Poortscannen](https://en.wikipedia.org/wiki/Port_scanner) van uw eindpunten

Een type test dat u niet uitvoeren is elke vorm [van Denial of Service (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) aanval. Dit omvat het initiëren van een DoS-aanval zelf, of het uitvoeren van gerelateerde tests die kunnen bepalen, demonstreren of simuleren elk type DoS-aanval.

## <a name="next-steps"></a>Volgende stappen

- Als u een aanstaande penetratietest formeel wilt documenteren tegen uw toepassingen die worden gehost in Microsoft Azure, gaat u naar de [penetratietestregels van engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) en vult u het testmeldingsformulier in.
