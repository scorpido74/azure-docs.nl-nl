---
title: Waar en hoe te gebruiken - Personalizer
description: Personalizer kan worden toegepast in elke situatie waarin uw toepassing het juiste item, actie of product kan selecteren om weer te geven - om de ervaring beter te maken, betere bedrijfsresultaten te behalen of de productiviteit te verbeteren.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: c562d7a1853736204a7a03262547e083bd85fb75
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617670"
---
# <a name="where-and-how-to-use-personalizer"></a>Waar en hoe personalizer te gebruiken

Gebruik Personalizer in elke situatie waarin uw toepassing de juiste actie (inhoud) moet selecteren om weer te geven - om de ervaring beter te maken, betere bedrijfsresultaten te behalen of de productiviteit te verbeteren.

Personalizer maakt gebruik van reinforcement learning om te selecteren welke actie (inhoud) de gebruiker te tonen is. De selectie kan drastisch variëren, afhankelijk van de hoeveelheid, kwaliteit en distributie van gegevens die naar de service worden verzonden.

## <a name="example-use-cases-for-personalizer"></a>Voorbeeldvanuse cases voor Personalizer

* **Intentie verduidelijking & disambiguation:** help uw gebruikers hebben een betere ervaring wanneer hun bedoeling is niet duidelijk door het verstrekken van een optie die is gepersonaliseerd.
* **Standaardsuggesties** voor menu's & opties: laat de bot het meest waarschijnlijke item op een gepersonaliseerde manier voorstellen als een eerste stap, in plaats van een onpersoonlijk menu of lijst met alternatieven te presenteren.
* **Bot eigenschappen & toon:** voor bots die kunnen variëren toon, verbosity, en schrijfstijl, overwegen het variëren van deze eigenschappen.
* **Melding & waarschuwingsinhoud:** bepaal welke tekst u wilt gebruiken voor waarschuwingen om gebruikers meer te betrekken.
* **Melding & waarschuwingstiming:** laat persoonlijk leren wanneer u meldingen naar gebruikers moet verzenden om ze meer te betrekken.


## <a name="expectations-required-to-use-personalizer"></a>Verwachtingen vereist om Personalizer te gebruiken

U Personalizer toepassen in situaties waarin u voldoet aan of de volgende richtlijnen implementeren.

|Richtlijn|Uitleg|
|--|--|
|Bedrijfsdoel|Je hebt een zakelijk of bruikbaarheidsdoel voor je sollicitatie.|
|Inhoud|Je hebt een plaats in je applicatie waar het maken van een contextuele beslissing van wat te laten zien aan gebruikers zal dat doel te verbeteren.|
|Inhoudshoeveelheid|U hebt minder dan 50 acties om per gesprek te rangschikken.|
|Geaggregeerde gegevens|De beste keuze kan en moet worden geleerd van collectief gebruikersgedrag en de totale beloningsscore.|
|Ethisch gebruik|Het gebruik van machine learning voor personalisatie volgt [op richtlijnen en keuzes](ethics-responsible-use.md) voor verantwoord gebruik die u hebt gekozen.
|Beste enkele optie|De contextuele beslissing kan worden uitgedrukt als het rangschikken van de beste optie (actie) van een beperkte set van keuzes.|
|Gescoord resultaat|Hoe goed de gerangschikte keuze werkte voor uw toepassing kan worden bepaald door het meten van een bepaald aspect van het gedrag van de gebruiker, en het uitdrukken van het in een _[beloning score](concept-rewards.md)_.|
|Relevante timing|De beloningsscore brengt niet te veel verstorende of externe factoren met zich mee. De experimentduur is laag genoeg dat de beloningsscore kan worden berekend terwijl deze nog relevant is.|
|Voldoende contextfuncties|U de context voor de rang uitdrukken als een lijst met ten minste 5 [functies](concepts-features.md) waarvan u denkt dat deze zouden helpen bij het maken van de juiste keuze, en dat omvat geen gebruikersspecifieke identificeerbare informatie.|
|Voldoende actiefuncties|Je hebt informatie over elke content keuze, _actie,_ als een lijst van ten minste 5 [functies](concepts-features.md) waarvan u denkt dat zal helpen Personalizer maken van de juiste keuze.|
|Dagelijkse gegevens|Er zijn genoeg evenementen om op de hoogte te blijven van optimale personalisatie als het probleem in de loop van de tijd afdrijft (zoals voorkeuren in nieuws of mode). Personalizer zal zich aanpassen aan continue verandering in de echte wereld, maar de resultaten zullen niet optimaal zijn als er niet genoeg gebeurtenissen en gegevens zijn om van te leren om nieuwe patronen te ontdekken en zich te vestigen. U moet kiezen voor een use case die vaak genoeg gebeurt. Overweeg op zoek naar use cases die gebeuren ten minste 500 keer per dag.|
|Historische gegevens|Uw toepassing kan gegevens lang genoeg bewaren om een geschiedenis van ten minste 100.000 interacties te verzamelen. Hierdoor kan Personalizer voldoende gegevens verzamelen om offline evaluaties en beleidsoptimalisatie uit te voeren.|

**Gebruik personalizer niet** wanneer het gepersonaliseerde gedrag niet iets is dat voor alle gebruikers kan worden gedetecteerd. Bijvoorbeeld, met behulp van Personalizer om een eerste pizza bestelling suggereren uit een lijst van 20 mogelijke menu-items is nuttig, maar die contact te bellen van de lijst met gebruikers contact wanneer die hulp met kinderopvang (zoals "Oma") is niet iets dat is personaliseerbaar in uw user base.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Personalizer gebruiken in een webtoepassing

Het toevoegen van een leerlus aan een webtoepassing omvat:

* Bepaal welke ervaring u wilt personaliseren, welke acties en functies u hebt, welke contextfuncties u wilt gebruiken en welke beloning u instelt.
* Voeg een verwijzing naar de Personalisatie SDK toe in uw toepassing.
* Bel de Rank API wanneer u klaar bent om te personaliseren.
* Sla de eventId op. U stuurt later een beloning met de Reward API.
1. Bel Activeren voor het evenement zodra u zeker weet dat de gebruiker uw gepersonaliseerde pagina heeft gezien.
1. Wacht op de selectie van gerangschikte inhoud door de gebruiker.
1. Call Reward API om aan te geven hoe goed de uitvoer van de Rank API deed.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Hoe personalizer te gebruiken met een chatbot

In dit voorbeeld ziet u hoe u Personalisatie gebruiken om een standaardsuggestie te doen in plaats van de gebruiker telkens een reeks menu's of keuzes naar beneden te sturen.

* Haal de [code](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) voor dit voorbeeld.
* Stel uw botoplossing in. Zorg ervoor dat u uw LUIS-toepassing publiceert.
* Beheren Rank en Reward API calls voor bot.
    * Voeg code toe om luis-intent processing te beheren. Als de **Geen** wordt geretourneerd als de hoogste intentie of de score van de hoogste intentie lager is dan de drempel van uw bedrijfslogica, stuurt u de intentieslijst naar Personalizer om de intenties te rangschikken.
    * Toon intentielijst aan de gebruiker als selecteerbare koppelingen met de eerste intentie de hoogste intentie van rank API-respons.
    * Leg de selectie van de gebruiker vast en verzend deze in de Reward API-aanroep.

### <a name="recommended-bot-patterns"></a>Aanbevolen botpatronen

* Maak Personalizer Rank API-aanroepen elke keer dat een disambiguation nodig is, in tegenstelling tot caching resultaten voor elke gebruiker. Het resultaat van disambiguating intentie kan veranderen in de tijd voor een persoon, en waardoor de Rank API om varianties te verkennen zal versnellen algehele leren.
* Kies een interactie die gebruikelijk is bij veel gebruikers, zodat u voldoende gegevens hebt om te personaliseren. Inleidende vragen kunnen bijvoorbeeld beter passen dan kleinere verduidelijkingen diep in de gespreksgrafiek die slechts een paar gebruikers kunnen krijgen.
* Gebruik Rank API-aanroepen om gesprekken met 'eerste suggestie is goed' in te schakelen, waarbij de gebruiker wordt gevraagd "Wilt u X?" of "Bedoelde je X?" en de gebruiker kan gewoon bevestigen; in tegenstelling tot het geven van opties aan de gebruiker waar ze moeten kiezen uit een menu. Bijvoorbeeld, Gebruiker:"Ik wil graag een kopje koffie bestellen" Bot:"Wilt u een dubbele espresso?". Op deze manier is het beloningssignaal ook sterk omdat het direct betrekking heeft op de ene suggestie.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Personalizer gebruiken met een aanbevelingsoplossing

Veel bedrijven gebruiken aanbevelingsengines, marketing- en campagnetools, doelgroepsegmentatie en clustering, collaboratieve filtering en andere middelen om producten uit een grote catalogus aan klanten aan te bevelen.

De [Microsoft Recommenders GitHub-repository](https://github.com/Microsoft/Recommenders) bevat voorbeelden en aanbevolen procedures voor het bouwen van aanbevelingssystemen, geleverd als Jupyter-notitieblokken. Het biedt werkvoorbeelden voor het voorbereiden van gegevens, het bouwen van modellen, het evalueren, afstemmen en operationaliseren van de aanbevelingsengines, voor veel voorkomende benaderingen, waaronder xDeepFM, SAR, ALS, RBM, DKN.

Personalizer kan werken met een aanbevelingsengine wanneer deze aanwezig is.

* Aanbevelingsengines nemen grote hoeveelheden artikelen (bijvoorbeeld 500.000) en bevelen een subset (zoals de top 20) aan van honderden of duizenden opties.
* Personalizer neemt een klein aantal acties met veel informatie over hen en rangschikt ze in real time voor een bepaalde rijke context, terwijl de meeste aanbevelingsengines slechts een paar attributen gebruiken over gebruikers, producten en hun interacties.
* Personalizer is ontworpen om de gebruikersvoorkeuren voortdurend autonoom te verkennen, wat betere resultaten oplevert wanneer inhoud snel verandert, zoals nieuws, live-evenementen, live community-inhoud, inhoud met dagelijkse updates of seizoensgebonden inhoud.

Een gemeenschappelijk gebruik is om de output van een aanbevelingsmotor (bijvoorbeeld de top 20-producten voor een bepaalde klant) te gebruiken en dat te gebruiken als invoeracties voor Personalizer.

## <a name="adding-content-safeguards-to-your-application"></a>Inhoudsbeveiligingen toevoegen aan uw toepassing

Als uw toepassing grote verschillen in inhoud toestaat die aan gebruikers worden weergegeven en sommige van die inhoud mogelijk onveilig of ongepast is voor sommige gebruikers, moet u vooruit plannen om ervoor te zorgen dat de juiste beveiligingen aanwezig zijn om te voorkomen dat uw gebruikers onaanvaardbare inhoud zien. Het beste patroon om waarborgen te implementeren is:
    * De lijst met acties verkrijgen om te rangschikken.
    * Filter degenen die niet levensvatbaar zijn voor het publiek.
    * Rangschik deze levensvatbare acties alleen.
    * Geef de hoogste actie weer aan de gebruiker.

In sommige architecturen kan de bovenstaande volgorde moeilijk te implementeren zijn. In dat geval is er een alternatieve benadering voor de uitvoering van waarborgen na de rangschikking, maar er moet een voorziening worden getroffen, zodat acties die buiten de beveiliging vallen niet worden gebruikt om het Personalizer-model te trainen.

* Verkrijg de lijst met acties die moeten worden gerangschikt, waarbij leren is gedeactiveerd.
* Rangacties.
* Controleer of de bovenste actie levensvatbaar is.
    * Als de bovenste actie levensvatbaar is, activeert u het leren voor deze rang en laat u deze vervolgens aan de gebruiker zien.
    * Als de topactie niet levensvatbaar is, activeer dan niet het leren voor deze rangschikking en bepaal via uw eigen logica of alternatieve benaderingen wat u aan de gebruiker laten zien. Zelfs als u de op één na best gerangschikte optie gebruikt, activeert u het leren niet voor deze rangschikking.


## <a name="next-steps"></a>Volgende stappen

[Ethiek & verantwoord gebruik.](ethics-responsible-use.md)
