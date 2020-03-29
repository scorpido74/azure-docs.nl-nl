---
title: Actieve en inactieve evenementen - Personalizer
description: In dit artikel wordt het gebruik van actieve en inactieve gebeurtenissen binnen de Personalizer-service besproken.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: a8f27542208965e2b820b9fc45cfcc5353a7f193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624251"
---
# <a name="active-and-inactive-events"></a>Actieve en inactieve gebeurtenissen

Een **actieve** gebeurtenis is een call-to-rank waarbij u weet dat u het resultaat aan de klant gaat laten zien en de beloningsscore bepaalt. Dit is de standaardinstelling.

Een **inactieve** gebeurtenis is een call-to-rank waarbij u niet zeker weet of de gebruiker ooit de aanbevolen actie te zien krijgt, vanwege bedrijfslogica. Hiermee u het evenement verwijderen, zodat Personalizer niet is getraind met de standaardbeloning. Inactieve gebeurtenissen mogen de Reward API niet aanroepen.

Het is belangrijk dat de leerlus het eigenlijke type gebeurtenis kent. Een inactieve gebeurtenis heeft geen Beloningsgesprek. Een actieve gebeurtenis moet een Beloningsaanroep hebben, maar als de API-aanroep nooit wordt uitgevoerd, wordt de standaardbeloningsscore toegepast. Wijzig de status van een gebeurtenis van inactief naar actief zodra u weet dat deze de gebruikerservaring zal beïnvloeden.

## <a name="typical-active-events-scenario"></a>Normaal scenario voor actieve gebeurtenissen

Wanneer uw toepassing de Rank API aanroept, ontvangt u de actie, die de toepassing moet weergeven in het veld **rewardActionId.**  Vanaf dat moment verwacht Personalizer een Reward-gesprek met een beloningsscore met dezelfde eventId. De beloningsscore wordt gebruikt om het model te trainen voor toekomstige Rank-gesprekken. Als er geen Reward call is ontvangen voor de gebeurtenisId, wordt een standaardbeloning toegepast. [Standaardbeloningen](how-to-settings.md#configure-rewards-for-the-feedback-loop) worden ingesteld op uw Personalizer-bron in de Azure-portal.

## <a name="other-event-type-scenarios"></a>Andere scenario's voor gebeurtenistype

In sommige scenario's moet de toepassing mogelijk Rank aanroepen voordat deze weet of het resultaat wordt gebruikt of weergegeven aan de gebruiker. Dit kan bijvoorbeeld gebeuren in situaties waarin bijvoorbeeld het renderen van uitgelichte inhoud door een marketingcampagne wordt overschreven. Als het resultaat van de Rang-oproep nooit is gebruikt en de gebruiker het nooit heeft gezien, stuur dan geen bijbehorende Reward-oproep.

Doorgaans worden deze scenario's gemaakt wanneer:

* U maakt de gebruikersinterface vooraf die de gebruiker wel of niet te zien krijgt.
* Uw toepassing doet voorspellende personalisatie waarbij Rank-gesprekken worden gevoerd met weinig realtime context en de toepassing de uitvoer al dan niet gebruikt.

Gebruik in deze gevallen Personalizer om Rank te bellen en de gebeurtenis inactief te _vragen._ Personalizer verwacht geen beloning voor dit evenement en past geen standaardbeloning toe.

Als de toepassing de informatie uit de Rang-aanroep later in uw bedrijfslogica gebruikt, _activeert u_ de gebeurtenis. Zodra het evenement actief is, verwacht Personalizer een evenementbeloning. Als er geen expliciete aanroep wordt gedaan naar de Reward API, past Personalizer een standaardbeloning toe.

## <a name="inactive-events"></a>Inactieve gebeurtenissen

Als u training voor een evenement `learningEnabled = False`wilt uitschakelen, belt u Rang met behulp van .

Voor een inactieve gebeurtenis wordt leren impliciet geactiveerd als u een beloning `activate` voor de gebeurtenisId verzendt of de API voor die gebeurtenisId aanroept.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het bepalen van de beloningsscore en welke gegevens u moet overwegen.](concept-rewards.md)
