---
title: Beloningsscore - Personalizer
description: De beloningsscore geeft aan hoe goed de personalisatiekeuze, RewardActionID, voor de gebruiker heeft geresulteerd. De waarde van de beloningsscore wordt bepaald door uw bedrijfslogica, op basis van waarnemingen van gebruikersgedrag. Personalizer traint zijn machine learning-modellen door de beloningen te evalueren.
ms.date: 02/20/2020
ms.topic: conceptual
ms.openlocfilehash: 734e4d0fdcec25884f8535ec61ccd10569fa8890
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219366"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>Beloningsscores geven succes van personalisatie aan

De beloningsscore geeft aan hoe goed de personalisatiekeuze, [RewardActionID,](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/rank/rank#response)voor de gebruiker is ontstaan. De waarde van de beloningsscore wordt bepaald door uw bedrijfslogica, op basis van waarnemingen van gebruikersgedrag.

Personalizer traint zijn machine learning-modellen door de beloningen te evalueren.

Meer informatie over [het](how-to-settings.md#configure-rewards-for-the-feedback-loop) configureren van de standaardbeloningsscore in de Azure-portal voor uw personalizerbron.

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Reward API gebruiken om beloningsscore naar Personalizer te sturen

Beloningen worden naar Personalizer verzonden door de [Reward API.](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/events/reward) Een beloning is meestal een getal van 0 tot 1. Een negatieve beloning, met de waarde van -1, is mogelijk in bepaalde scenario's en mag alleen worden gebruikt als u ervaring hebt met reinforcement learning (RL). Personalizer traint het model om de hoogst mogelijke som beloningen in de loop van de tijd te bereiken.

Beloningen worden verzonden nadat het gedrag van de gebruiker is gebeurd, wat dagen later kan zijn. De maximale tijd die Personalizer zal wachten totdat een gebeurtenis wordt beschouwd als geen beloning of een standaardbeloning is geconfigureerd met de [wachttijd voor beloning](#reward-wait-time) in de Azure-portal.

Als de beloningsscore voor een gebeurtenis niet is ontvangen binnen de **wachttijd voor beloning,** wordt de **standaardbeloning** toegepast. Normaal gesproken is de **[standaardbeloning](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** geconfigureerd als nul.


## <a name="behaviors-and-data-to-consider-for-rewards"></a>Gedrag en gegevens om rekening mee te houden voor beloningen

Houd rekening met deze signalen en gedragingen voor de context van de beloningsscore:

* Directe invoer van gebruikers voor suggesties wanneer er opties zijn ("Bedoel je X?").
* Sessielengte.
* Tijd tussen de sessies.
* Sentimentanalyse van de interacties van de gebruiker.
* Directe vragen en mini-enquêtes waarbij de bot de gebruiker om feedback vraagt over nut, nauwkeurigheid.
* Reactie op waarschuwingen of vertraging op het reageren op waarschuwingen.

## <a name="composing-reward-scores"></a>Het samenstellen van beloningsscores

Een beloningsscore moet worden berekend in uw bedrijfslogica. De score kan worden weergegeven als:

* Een enkel nummer dat eenmaal is verzonden
* Een score die onmiddellijk wordt verzonden (zoals 0,8) en een extra score die later wordt verzonden (meestal 0,2).

## <a name="default-rewards"></a>Standaardbeloningen

Als er geen beloning wordt ontvangen binnen de [wachttijd voor beloning](#reward-wait-time), de duur sinds het rankgesprek, past Personalizer impliciet de **standaardbeloning** toe op die rankgebeurtenis.

## <a name="building-up-rewards-with-multiple-factors"></a>Beloningen opbouwen met meerdere factoren

Voor effectieve personalisatie u de beloningsscore opbouwen op basis van meerdere factoren.

U deze regels bijvoorbeeld toepassen voor het personaliseren van een lijst met video-inhoud:

|Gebruikersgedrag|Gedeeltelijke scorewaarde|
|--|--|
|De gebruiker klikte op het bovenste item.|+0,5 beloning|
|De gebruiker heeft de werkelijke inhoud van dat item geopend.|+0,3 beloning|
|De gebruiker keek 5 minuten van de inhoud of 30%, welke langer is.|+0,2 beloning|
|||

U de totale beloning vervolgens naar de API sturen.

## <a name="calling-the-reward-api-multiple-times"></a>Meerdere keren de Reward API aanroepen

U de Reward API ook aanroepen met dezelfde gebeurtenis-ID, die verschillende beloningsscores verzendt. Wanneer Personalizer deze beloningen krijgt, bepaalt het de uiteindelijke beloning voor die gebeurtenis door ze te aggregeren zoals gespecificeerd in de personalizerconfiguratie.

Aggregatiewaarden:

*  **Eerste:** Neemt de eerste beloning score ontvangen voor het evenement, en gooit de rest.
* **Som**: Neemt alle beloningsscores verzameld voor de eventId, en voegt ze samen.

Alle beloningen voor een evenement, die worden ontvangen na de **Reward Wait Time,** worden weggegooid en hebben geen invloed op de opleiding van modellen.

Door beloningsscores op te tellen, kan uw uiteindelijke beloning buiten het verwachte scorebereik vallen. Hierdoor mislukt de service niet.

## <a name="best-practices-for-calculating-reward-score"></a>Aanbevolen procedures voor het berekenen van de beloningsscore

* **Houd rekening met echte indicatoren van succesvolle personalisatie:** Het is gemakkelijk om te denken in termen van klikken, maar een goede beloning is gebaseerd op wat u wilt dat uw gebruikers te *bereiken* in plaats van wat je wilt dat mensen *doen*.  Het belonen van klikken kan bijvoorbeeld leiden tot het selecteren van inhoud die clickbait gevoelig is.

* **Gebruik een beloning score voor hoe goed de personalisatie werkte:** Het personaliseren van een film suggestie zou hopelijk resulteren in de gebruiker kijken naar de film en waardoor het een hoge rating. Aangezien de film rating hangt waarschijnlijk af van vele dingen (de kwaliteit van het acteren, de stemming van de gebruiker), het is niet een goede beloning signaal voor hoe goed *de personalisatie* werkte. De gebruiker kijken naar de eerste paar minuten van de film, echter, kan een beter signaal van personalisatie effectiviteit en het verzenden van een beloning van 1 na 5 minuten zal een beter signaal.

* **Beloningen zijn alleen van toepassing op RewardActionID:** Personalizer past de beloningen toe om de effectiviteit van de actie die is opgegeven in RewardActionID te begrijpen. Als u ervoor kiest om andere acties weer te geven en de gebruiker erop klikt, moet de beloning nul zijn.

* **Houd rekening met onbedoelde gevolgen:** Maak beloningsfuncties die leiden tot verantwoorde resultaten met [ethiek en verantwoord gebruik.](ethics-responsible-use.md)

* **Incrementele beloningen**gebruiken: het toevoegen van gedeeltelijke beloningen voor kleiner gebruikersgedrag helpt Personalizer om betere beloningen te bereiken. Deze incrementele beloning stelt het algoritme in staat om te weten dat het dichter bij het betrekken van de gebruiker in het uiteindelijke gewenste gedrag komt.
    * Als u een lijst met films weergeeft, u bepalen of er een aantal betrokkenheid van de gebruiker is gebeurd als de gebruiker een tijdje boven de eerste zweeft om meer informatie te zien. Het gedrag kan tellen met een beloningsscore van 0,1.
    * Als de gebruiker de pagina heeft geopend en vervolgens is afgesloten, kan de beloningsscore 0,2 zijn.

## <a name="reward-wait-time"></a>Beloon wachttijd

Personalizer zal de informatie van een Rank-gesprek correleren met de beloningen die worden verzonden in Reward-oproepen om het model te trainen. Deze kunnen op verschillende tijdstippen komen. Personalizer wacht een beperkte tijd, te beginnen wanneer de Rank-oproep is gebeurd, zelfs als de Rank-oproep is gemaakt als een inactieve gebeurtenis en later geactiveerd.

Als de **wachttijd voor beloning** verloopt en er geen beloningsinformatie is geweest, wordt een standaardbeloning toegepast op die gebeurtenis voor training. De maximale wachttijd is 6 dagen.

## <a name="best-practices-for-reward-wait-time"></a>Aanbevolen procedures voor beloningswachttijd

Volg deze aanbevelingen voor betere resultaten.

* Maak de wachttijd voor beloning zo kort als je, terwijl je genoeg tijd overlaat om feedback van gebruikers te krijgen.

* Kies geen duur die korter is dan de tijd die nodig is om feedback te krijgen. Als sommige van uw beloningen bijvoorbeeld binnenkomen nadat een gebruiker 1 minuut van een video heeft bekeken, moet de experimentlengte ten minste het dubbele zijn.

## <a name="next-steps"></a>Volgende stappen

* [Bekrachtigend leren](concepts-reinforcement-learning.md)
* [Probeer de Rank API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Probeer de Reward API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)
