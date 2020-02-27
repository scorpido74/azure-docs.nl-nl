---
title: Wat is Personalizer?
titleSuffix: Azure Cognitive Services
description: Personaler is een API-service in de Cloud waarmee u de beste ervaring kunt kiezen die aan uw gebruikers wordt weer gegeven, en leer vanuit hun realtime gedrag.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 01/21/2020
ms.author: diberry
ms.openlocfilehash: bf0710ebef21226d8d8582a920d64027bb015d34
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77622724"
---
# <a name="what-is-personalizer"></a>Wat is Personalizer?

Azure Personaler is een API-service in de cloud die uw client toepassing helpt bij het kiezen van het beste afzonderlijke _inhouds_ item om elke gebruiker weer te geven. De service selecteert het beste item, van inhouds items, op basis van collectieve real-time informatie die u over inhoud en context verstrekt.

Nadat u het inhouds item aan uw gebruiker hebt gepresenteerd, controleert uw systeem het gebruikers gedrag en meldt het een belonings Score terug naar Personaler om de mogelijkheid om de beste inhoud te selecteren op basis van de context informatie die wordt ontvangen, te verbeteren.

**Inhoud** kan elke gegevens eenheid zijn, zoals tekst, afbeeldingen, url's of e-mail berichten die u wilt selecteren om aan uw gebruiker weer te geven.

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>Hoe selecteert u het beste inhouds item in persoonlijke instellingen?

Persoonlijkere training maakt gebruik van **educatief leren** om het beste item (_actie_) te selecteren op basis van collectief gedrag en belonings scores voor alle gebruikers. Acties zijn de inhouds items, zoals nieuws artikelen, specifieke films of producten waaruit u kunt kiezen.

De **classificatie** oproep neemt het actie-item samen met de functies van de actie en context functies om het bovenste actie-item te selecteren:

* **Acties met functies** -inhouds items met specifieke functies voor elk item
* **Context functies** : functies van uw gebruikers, hun context of hun omgeving bij het gebruik van uw app

De classificatie oproep retourneert de ID van het inhouds item, de __actie__die moet worden weer gegeven voor de gebruiker in het veld **Actie-id beloning** .
De __actie__ die wordt weer gegeven voor de gebruiker, wordt gekozen met machine learning modellen, waarbij wordt geprobeerd de totale hoeveelheid beloningen in de loop van de tijd te maximaliseren.

Enkele voorbeeld scenario's zijn:

|Type inhoud|**Acties (met functies)**|**Context functies**|Geretourneerde actie-ID van beloning<br>(deze inhoud weer geven)|
|--|--|--|--|
|Nieuws lijst|a. `The president...` (National, politiek, [tekst])<br>b. `Premier League ...` (globaal, sport, [tekst, afbeelding, video])<br> c. `Hurricane in the ...` (regionaal, weer, [tekst, afbeelding]|Nieuws van apparaten lezen<br>Maand of seizoen<br>|een `The president...`|
|Lijst met films|1. `Star Wars` (1977, [actie, Adventure, fictief], George Lucas)<br>2. `Hoop Dreams` (1994, [documentaire, sport], Stefan Jeroen<br>3. `Casablanca` (1942, [Romaans, drama, War], Michael Curtiz)|De film van het apparaat wordt bekeken<br>scherm grootte<br>Type gebruiker<br>|3. `Casablanca`|
|Lijst met producten|i. `Product A` (3 kg, $ $ $ $, lever in 24 uur)<br>ii. `Product B` (20 kg, $ $, 2 weken verzen ding met douane)<br>iii. `Product C` (3 kg, $ $ $, levering in 48 uur)|Het apparaat wordt in het winkel lezen<br>Bestedings categorie van gebruiker<br>Maand of seizoen|ii. `Product B`|

Persoonlijkere training gebruikt versterking van een enkele best mogelijke actie, ook wel _Actie-id_genoemd, op basis van een combi natie van:
* Getraind model-gegevens over de Personaler service ontvangen
* Actuele gegevensspecifieke acties met functies en context functies

## <a name="when-to-call-personalizer"></a>Wanneer moet u een persoonlijker gesprek doen

De **rangs** - [API](https://go.microsoft.com/fwlink/?linkid=2092082) van personaler wordt _elke keer_ dat u inhoud in realtime presenteert opgeroepen. Dit wordt aangeduid als een **gebeurtenis**met een _gebeurtenis-id_.

De **belonings** - [API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) van personaler kan in realtime worden aangeroepen of uitgesteld zodat deze beter aansluit bij uw infra structuur. U bepaalt de belonings Score op basis van uw bedrijfs behoeften. Dit kan een enkele waarde zijn, zoals 1 voor goed, en 0 voor slecht, of een getal dat is geproduceerd door een algoritme dat u maakt, gezien uw bedrijfs doelstellingen en-metrische gegevens.

## <a name="personalizer-content-requirements"></a>Vereisten voor persoonlijke inhoud

Persoonlijker gebruiken wanneer inhoud:

* Heeft een beperkte set items (Maxi maal ~ 50) waaruit kan worden geselecteerd. Als u een grotere lijst hebt, [gebruikt u een aanbevelings engine](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution) om de lijst omlaag te brengen tot 50 items.
* Bevat informatie over de inhoud die u wilt geclassificeerd: _acties met functies_ en _context functies_.
* Heeft ten minste ongeveer 1 KB/dag aan inhoud gerelateerde gebeurtenissen die persoonlijk kunnen worden toegepast. Als het vereiste minimum verkeer niet door Personaler wordt ontvangen, neemt de service meer tijd in beslag om het afzonderlijke beste inhouds item te bepalen.

Omdat persoonlijke gegevens in bijna realtime worden gebruikt om het afzonderlijke beste inhouds item te retour neren, is de service niet:
* Gebruikers profiel gegevens persistent maken en beheren
* Voor keuren of geschiedenis van afzonderlijke gebruikers vastleggen
* Gereinigde en gelabelde inhoud vereisen

## <a name="how-to-design-and-implement-personalizer-for-your-client-application"></a>Personalisatie ontwerpen en implementeren voor uw client toepassing

1. [Ontwerp](concepts-features.md) en plan inhoud, **_acties_** en **_context_** . Bepaal het belonings algoritme voor de **_belonings_** Score.
1. Elke [personaler-resource](how-to-settings.md) die u maakt, wordt beschouwd als 1 Learning-lus. De lus ontvangt zowel de rang-als belonings aanroepen voor die inhoud of gebruikers ervaring.
1. Persoonlijker toevoegen aan uw website of inhouds systeem:
    1. Voeg een **classificatie** oproep toe aan personaler in uw toepassing, website of systeem om het beste, één _inhouds_ item te bepalen voordat de inhoud wordt weer gegeven aan de gebruiker.
    1. Het beste, één _inhouds_ item weer geven, wat de geretourneerde _Actie-id_is voor de gebruiker.
    1. Pas het _algoritme_ toe op verzamelde informatie over de manier waarop de gebruiker zich heeft gereageerd, om de **belonings** score te bepalen, zoals:

        |Gedrag|Berekende belonings Score|
        |--|--|
        |Gebruiker heeft het beste, één _inhouds_ item geselecteerd (actie-id beloning)|**1**|
        |Door gebruiker geselecteerde andere inhoud|**0**|
        |De gebruiker is tijdelijk gepauzeerd en er wordt niet meer geschuifd, voordat u het beste, één _inhouds_ item selecteert (actie-id beloning)|**0,5**|

    1. Een **belonings oproep toevoegen** die een belonings score tussen 0 en 1 verzendt
        * Direct na het weer geven van uw inhoud
        * Of ergens later in een offline systeem
    1. [Evalueer uw lus](concepts-offline-evaluation.md) met een offline-evaluatie na een gebruiks periode. Met een offline-evaluatie kunt u de effectiviteit van de Personaler service testen en beoordelen zonder uw code te wijzigen of de gebruikers ervaring te beïnvloeden.

## <a name="next-steps"></a>Volgende stappen


* [Hoe Personaler werkt](how-personalizer-works.md)
* [Wat is versterking van het onderwijs?](concepts-reinforcement-learning.md)
* [Meer informatie over de functies en acties voor de rang aanvraag](concepts-features.md)
* [Meer informatie over het bepalen van de score voor de belonings aanvraag](concept-rewards.md)
* [Snelstartgidsen](sdk-learning-loop.md)
* [Zelfstudie](tutorial-use-azure-notebook-generate-loop-data.md)
* [De interactieve demo gebruiken](https://personalizationdemo.azurewebsites.net/)
