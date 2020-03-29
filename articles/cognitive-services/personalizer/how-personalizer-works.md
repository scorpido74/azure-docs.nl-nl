---
title: Hoe Personalizer werkt - Personalizer
description: De _Personalizer-lus_ maakt gebruik van machine learning om het model te bouwen dat de beste actie voor uw inhoud voorspelt. Het model is uitsluitend getraind op uw gegevens die u naar het met de Rang en Beloning oproepen verzonden.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 836c207213ac52a60e27da6fc957418187059023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623748"
---
# <a name="how-personalizer-works"></a>Hoe Personalizer werkt

De personalizer-bron, uw _leerlus,_ maakt gebruik van machine learning om het model te bouwen dat de hoogste actie voor uw inhoud voorspelt. Het model is uitsluitend getraind op uw gegevens die u naar het met de **Rang** en **Beloning** oproepen verzonden. Elke lus is volledig onafhankelijk van elkaar.

## <a name="rank-and-reward-apis-impact-the-model"></a>Rank- en Reward API's hebben invloed op het model

U verzendt _acties met functies_ en _contextfuncties_ naar de Rank API. De **Rank** API besluit te gebruiken:

* _Exploit:_ Het huidige model om de beste actie te bepalen op basis van gegevens uit het verleden.
* _Verkennen:_ Selecteer een andere actie in plaats van de bovenste actie. U [configureert dit percentage](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt) voor uw personalizerbron in de Azure-portal.

U bepaalt de beloningsscore en stuurt die score naar de Reward API. De **Reward** API:

* Verzamelt gegevens om het model te trainen door de functies en beloningsscores van elk ranggesprek vast te nemen.
* Gebruikt die gegevens om het model bij te werken op basis van de configuratie die is opgegeven in het _leerbeleid_.

## <a name="your-system-calling-personalizer"></a>Uw systeem belt personalizer

De volgende afbeelding toont de architecturale stroom van het aanroepen van de rang- en beloningsoproepen:

![alternatieve tekst](./media/how-personalizer-works/personalization-how-it-works.png "Hoe personalisatie werkt")

1. U verzendt _acties met functies_ en _contextfuncties_ naar de Rank API.

    * Personalizer beslist of het huidige model wordt gebruikt of nieuwe keuzes voor het model wil verkennen.
    * Het rankingresultaat wordt naar EventHub gestuurd.
1. De hoogste rang wordt teruggegeven aan uw systeem als _beloningsactie-ID._
    Uw systeem presenteert die inhoud en bepaalt een beloningsscore op basis van uw eigen bedrijfsregels.
1. Uw systeem geeft de beloningsscore terug aan de leerlus.
    * Wanneer Personalizer de beloning ontvangt, wordt de beloning naar EventHub gestuurd.
    * De rang en beloning zijn gecorreleerd.
    * Het AI-model wordt bijgewerkt op basis van de correlatieresultaten.
    * De gevolgtrekking motor is bijgewerkt met het nieuwe model.

## <a name="personalizer-retrains-your-model"></a>Personalizer hertraint uw model

Personalizer hertraint uw model op basis van de instelling **voor modelfrequentie-updates** op uw Personalizer-bron in de Azure-portal.

Personalizer gebruikt alle gegevens die momenteel worden bewaard, op basis van de instelling **voor gegevensbewaring** in het aantal dagen op uw Personalizer-bron in de Azure-portal.

## <a name="research-behind-personalizer"></a>Onderzoek achter Personalizer

Personalizer is gebaseerd op geavanceerde wetenschap en onderzoek op het gebied van [Reinforcement Learning,](concepts-reinforcement-learning.md) waaronder papers, onderzoeksactiviteiten en lopende onderzoeksgebieden in Microsoft Research.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [topscenario's](where-can-you-use-personalizer.md) voor personalizer