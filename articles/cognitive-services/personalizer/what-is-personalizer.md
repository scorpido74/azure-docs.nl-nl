---
title: Wat is Personalizer?
titleSuffix: Azure Cognitive Services
description: Personalizer is een cloudgebaseerde API-service waarmee u de beste ervaring kiezen om uw gebruikers te laten zien en te leren van hun realtime gedrag.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 01/21/2020
ms.author: diberry
ms.openlocfilehash: 850ab0ee89ee167886d8747a0c721bb643529e14
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80052070"
---
# <a name="what-is-personalizer"></a>Wat is Personalizer?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Personalizer is een api-service in de cloud waarmee uw clienttoepassing het beste, enige _inhoudsitem_ kan kiezen om elke gebruiker weer te geven. De service selecteert het beste item, uit inhoudsitems, op basis van collectieve realtime informatie die u verstrekt over inhoud en context.

Nadat u het inhoudsitem aan uw gebruiker hebt aangeboden, controleert uw systeem het gedrag van gebruikers en rapporteert u een beloningsscore terug naar Personalizer om de mogelijkheid te verbeteren om de beste inhoud te selecteren op basis van de contextinformatie die het ontvangt.

**Inhoud** kan elke eenheid van informatie zijn, zoals tekst, afbeeldingen, url's of e-mails waaruit u wilt selecteren om aan uw gebruiker te worden weergegeven.

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>Hoe selecteert Personalizer het beste contentitem?

Personalizer maakt gebruik **van reinforcement learning** om het beste item _(actie)_ te selecteren op basis van collectief gedrag en beloningsscores voor alle gebruikers. Acties zijn de inhouditems, zoals nieuwsartikelen, specifieke films of producten om uit te kiezen.

Met de **rangaanroep** wordt het actieitem, samen met de functies van de actie en contextfuncties, aangenomen om het hoogste actieitem te selecteren:

* **Acties met functies** - inhoudsitems met functies die specifiek zijn voor elk item
* **Contextfuncties** - functies van uw gebruikers, hun context of hun omgeving wanneer ze uw app gebruiken

De rangaanroep retourneert de id van welk __inhoudsitem, actie__, om aan de gebruiker te laten zien, in het veld **Actie-id belonen.**
De __actie__ die aan de gebruiker wordt getoond, wordt gekozen met machine learning-modellen, in een poging om het totale aantal beloningen in de loop van de tijd te maximaliseren.

Verschillende voorbeeldscenario's zijn:

|Inhoudstype|**Acties (met functies)**|**Contextfuncties**|Geretourneerde reward-actie-id<br>(geef deze inhoud weer)|
|--|--|--|--|
|Nieuwslijst|a. `The president...`(nationaal, politiek, [tekst])<br>b. `Premier League ...`(globaal, sport, [tekst, beeld, video])<br> c. `Hurricane in the ...`(regionaal, weer, [tekst,afbeelding]|Apparaatnieuws wordt gelezen van<br>Maand, of seizoen<br>|A`The president...`|
|Lijst met films|1. `Star Wars` (1977, [actie, avontuur, fantasie], George Lucas)<br>2. `Hoop Dreams` (1994, [documentaire, sport], Steve James<br>3. `Casablanca` (1942, [romaans, drama, oorlog], Michael Curtiz)|Apparaatfilm wordt bekeken vanaf<br>schermgrootte<br>Type gebruiker<br>|3.`Casablanca`|
|Productenlijst|i. `Product A`(3 kg, $$$$, leveren in 24 uur)<br>ii. `Product B`(20 kg, $$, 2 weken verzending bij de douane)<br>iii. `Product C`(3 kg, $$$, levering in 48 uur)|Apparaat winkelen wordt gelezen uit<br>Bestedingslaag van de gebruiker<br>Maand, of seizoen|ii. `Product B`|

Personalizer gebruikte reinforcement learning om de beste actie te selecteren, bekend als _reward action ID,_ op basis van een combinatie van:
* Opgeleid model - informatie uit het verleden die de personalizerservice heeft ontvangen
* Huidige gegevens - Specifieke acties met functies en contextfuncties

## <a name="when-to-call-personalizer"></a>Wanneer personalizer bellen

De Rank **Rank** [API](https://go.microsoft.com/fwlink/?linkid=2092082) van Personalizer wordt _elke keer_ dat je content presenteert in real-time aangeroepen. Dit staat bekend als een **gebeurtenis**, genoteerd met een _gebeurtenis-ID_.

De Reward **Reward** [API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) van Personalizer kan in realtime worden aangeroepen of vertraagd om beter bij uw infrastructuur te passen. U bepaalt de beloningsscore op basis van uw bedrijfsbehoeften. Dat kan een enkele waarde zijn, zoals 1 voor goed, en 0 voor slecht, of een getal dat wordt geproduceerd door een algoritme dat u maakt, rekening houdend met uw zakelijke doelen en statistieken.

## <a name="personalizer-content-requirements"></a>Vereisten voor gepersonaliseerde inhoud

Gebruik Personalizer wanneer uw inhoud:

* Heeft een beperkte set items (max van ~ 50) om uit te kiezen. Als u een grotere lijst hebt, [gebruikt u een aanbevelingsengine](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution) om de lijst terug te brengen tot 50 items.
* Bevat informatie die de inhoud beschrijft die u wilt rangschikken: _acties met functies_ en _contextfuncties._
* Heeft een minimum van ~ 1k/dag content-gerelateerde evenementen voor Personalizer effectief te zijn. Als Personalizer niet het vereiste minimale verkeer ontvangt, duurt het langer om het beste inhoudsitem te bepalen.

Aangezien Personalizer collectieve informatie in bijna realtime gebruikt om het beste inhoudsitem terug te sturen, hoeft de service niet:
* Gebruikersprofielgegevens blijven beheren en beheren
* Voorkeuren of geschiedenis van individuele gebruikers registreren
* Inhoud met een schone en gelabelde inhoud vereisen

## <a name="how-to-design-and-implement-personalizer-for-your-client-application"></a>Hoe personalre te ontwerpen en te implementeren voor uw clientapplicatie

1. [Ontwerp](concepts-features.md) en plan voor inhoud, **_acties_** en **_context_**. Bepaal het beloningsalgoritme voor de **_beloningsscore._**
1. Elke [personalizerresource](how-to-settings.md) die u maakt, wordt beschouwd als 1 leerlus. De lus ontvangt de zowel de Rang en Beloning oproepen voor die inhoud of gebruikerservaring.
1. Personaliser toevoegen aan uw website of inhoudssysteem:
    1. Voeg een **Rang-gesprek** toe aan Personalizer in uw toepassing, website of systeem om het beste, enige _inhoudsitem_ te bepalen voordat de inhoud aan de gebruiker wordt getoond.
    1. Geef het beste item met één _inhoud_ weer, dat is de geretourneerde _beloningsactie-ID_, aan de gebruiker.
    1. Pas _algoritme_ toe op verzamelde informatie over hoe de gebruiker zich gedroeg, om de **beloningsscore** te bepalen, zoals:

        |Gedrag|Berekende beloningsscore|
        |--|--|
        |Gebruiker geselecteerd beste, single _content_ item (reward action ID)|**1**|
        |Gebruiker geselecteerde andere inhoud|**0**|
        |Gebruiker onderbroken, scrollen rond besluiteloos, voordat u de beste, single _content_ item (beloning actie-ID)|**0.5**|

    1. Een **beloningsgesprek** toevoegen met een beloningsscore tussen 0 en 1
        * Onmiddellijk na het tonen van uw inhoud
        * Of ergens later in een offline systeem
    1. [Evalueer uw lus](concepts-offline-evaluation.md) met een offline evaluatie na een periode van gebruik. Met een offline evaluatie u de effectiviteit van de Personalizer Service testen en beoordelen zonder uw code te wijzigen of de gebruikerservaring te beïnvloeden.

## <a name="next-steps"></a>Volgende stappen


* [Hoe Personalizer werkt](how-personalizer-works.md)
* [Wat is Reinforcement Learning?](concepts-reinforcement-learning.md)
* [Meer informatie over functies en acties voor de rangaanvraag](concepts-features.md)
* [Meer informatie over het bepalen van de score voor het beloningsverzoek](concept-rewards.md)
* [Snel gestart](sdk-learning-loop.md)
* [Zelfstudie](tutorial-use-azure-notebook-generate-loop-data.md)
* [Gebruik de interactieve demo](https://personalizationdemo.azurewebsites.net/)
