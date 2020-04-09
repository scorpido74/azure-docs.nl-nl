---
title: Gebruikersstromen azure Application Insights analyseert navigatiestromen
description: Analyseer hoe gebruikers navigeren tussen de pagina's en functies van uw web-app.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 8622ede9e6f7fba2fde2e0b2e90eb31520a23d04
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892441"
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Gebruikersnavigatiepatronen analyseren met gebruikersstromen in toepassingsstatistieken

![Gereedschap Gebruikersstromen voor toepassingsinzichten](./media/usage-flows/00001-flows.png)

Het gereedschap Gebruikersstromen visualiseert hoe gebruikers navigeren tussen de pagina's en functies van uw site. Het is geweldig voor het beantwoorden van vragen als:

* Hoe navigeren gebruikers weg van een pagina op uw site?
* Waar klikken gebruikers op een pagina op uw site?
* Waar zijn de plaatsen die gebruikers churn meest van uw site?
* Zijn er plaatsen waar gebruikers steeds dezelfde actie herhalen?

Het gereedschap Gebruikersstromen begint vanuit een eerste paginaweergave, aangepaste gebeurtenis of uitzondering die u opgeeft. Gezien deze eerste gebeurtenis toont User Flows de gebeurtenissen die voor en na tijdens gebruikerssessies zijn gebeurd. Lijnen van verschillende dikte geven aan hoe vaak elk pad werd gevolgd door gebruikers. Met de knooppunten **Met speciale sessie gestart** wordt weergegeven waar de volgende knooppunten een sessie zijn begonnen. **Met** de knooppunten Sessie einde wordt weergegeven hoeveel gebruikers geen paginaweergaven of aangepaste gebeurtenissen hebben verzonden na het vorige knooppunt, waarbij wordt benadrukt waar gebruikers waarschijnlijk uw site hebben verlaten.

> [!NOTE]
> Uw toepassingsstatistiekenbron moet paginaweergaven of aangepaste gebeurtenissen bevatten om het gereedschap Gebruikersstromen te gebruiken. [Meer informatie over het instellen van uw app om paginaweergaven automatisch te verzamelen met de Application Insights JavaScript SDK.](../../azure-monitor/app/javascript.md)
>
>

## <a name="start-by-choosing-an-initial-event"></a>Begin met het kiezen van een eerste gebeurtenis

![Een eerste gebeurtenis kiezen voor gebruikersstromen](./media/usage-flows/00002-flows-initial-event.png)

Als u vragen wilt beantwoorden met het gereedschap Gebruikersstromen, kiest u een eerste paginaweergave, aangepaste gebeurtenis of uitzondering om als uitgangspunt voor de visualisatie te dienen:

1. Klik op de link in de **What do users do after...?** title, of klik op de knop **Bewerken.**
2. Selecteer een paginaweergave, aangepaste gebeurtenis of uitzondering in de vervolgkeuzelijst **Eerste gebeurtenis.**
3. Klik **op Grafiek maken**.

De kolom "Stap 1" van de visualisatie laat zien wat gebruikers het vaakst deden net na de eerste gebeurtenis, van boven naar beneden besteld van de meeste tot minst frequent. De "Stap 2" en de daaropvolgende kolommen laten zien wat gebruikers daarna hebben gedaan, waardoor een beeld ontstaat van alle manieren waarop gebruikers door uw site hebben genavigeerd.

Standaard worden in het gereedschap Gebruikersstromen alleen de laatste 24 uur paginaweergaven en aangepaste gebeurtenis van uw site verwijderd. U het tijdsbereik vergroten en de balans tussen prestaties en nauwkeurigheid voor willekeurige steekproeven wijzigen in het menu Bewerken.

Als sommige paginaweergaven, aangepaste gebeurtenissen en uitzonderingen niet relevant voor u zijn, klikt u op de **X** op de knooppunten die u wilt verbergen. Nadat u de knooppunten hebt geselecteerd die u wilt verbergen, klikt u op de knop **Grafiek maken** onder de visualisatie. Als u alle knooppunten wilt zien die u hebt verborgen, klikt u op de knop **Bewerken** en kijkt u vervolgens naar de sectie **Uitgesloten gebeurtenissen.**

Als paginaweergaven of aangepaste gebeurtenissen ontbreken, verwacht u dat u in de visualisatie zult zien:

* Controleer de sectie **Uitgesloten gebeurtenissen** in het menu **Bewerken.**
* Gebruik de plusknoppen op **andere** knooppunten om minder frequente gebeurtenissen in de visualisatie op te nemen.
* Als de paginaweergave of aangepaste gebeurtenis die u verwacht zelden door gebruikers wordt verzonden, probeert u het tijdsbereik van de visualisatie in het menu **Bewerken te** vergroten.
* Zorg ervoor dat de paginaweergave, aangepaste gebeurtenis of uitzondering waarvan u verwacht dat deze door de Application Insights SDK in de broncode van uw site wordt verzameld. [Meer informatie over het verzamelen van aangepaste evenementen.](../../azure-monitor/app/api-custom-events-metrics.md)

Als u meer stappen in de visualisatie wilt zien, gebruikt u de vervolgkeuzestappen **Vorige stappen** en **Vervolgstappen** boven de visualisatie.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>Waar gaan gebruikers na een bezoek aan een pagina of functie naartoe en waar klikken ze op?

![Gebruikersstromen gebruiken om te begrijpen waar gebruikers op klikken](./media/usage-flows/00003-flows-one-step.png)

Als uw eerste gebeurtenis een paginaweergave is, is de eerste kolom ("Stap 1") van de visualisatie een snelle manier om te begrijpen wat gebruikers onmiddellijk na het bezoeken van de pagina hebben gedaan. Probeer uw site te openen in een venster naast de visualisatie gebruikersstromen. Vergelijk uw verwachtingen over de manier waarop gebruikers met de pagina communiceren met de lijst met gebeurtenissen in de kolom Stap 1. Vaak kan een UI-element op de pagina dat onbelangrijk lijkt voor uw team een van de meest gebruikte op de pagina zijn. Het kan een geweldig uitgangspunt zijn voor ontwerpverbeteringen aan uw site.

Als uw eerste gebeurtenis een aangepaste gebeurtenis is, wordt in de eerste kolom weergegeven wat gebruikers hebben gedaan vlak na het uitvoeren van die actie. Net als bij paginaweergaven moet u overwegen of het waargenomen gedrag van uw gebruikers overeenkomt met de doelstellingen en verwachtingen van uw team. Als uw geselecteerde eerste gebeurtenis bijvoorbeeld 'Toegevoegd item aan winkelwagentje' is, moet u kijken of 'Ga naar afhandeling' en 'Voltooide aankoop' kort daarna in de visualisatie worden weergegeven. Als het gedrag van gebruikers verschilt van uw verwachtingen, gebruikt u de visualisatie om te begrijpen hoe gebruikers worden "gevangen" door het huidige ontwerp van uw site.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Waar zijn de plaatsen die gebruikers churn meest van uw site?

Kijk uit voor **sessieknooppunten die** hoog in een kolom in de visualisatie worden weergegeven, vooral vroeg in een stroom. Dit betekent dat veel gebruikers waarschijnlijk gekarnd van uw site na het volgen van de voorgaande pad van pagina's en UI interacties. Soms wordt churn verwacht - na het voltooien van een aankoop op een e-commercesite, bijvoorbeeld - maar meestal is churn een teken van ontwerpproblemen, slechte prestaties of andere problemen met uw site die kunnen worden verbeterd.

Houd er rekening mee dat **sessieknooppunten** alleen gebaseerd zijn op telemetrie die wordt verzameld door deze Application Insights-bron. Als Application Insights geen telemetrie ontvangt voor bepaalde gebruikersinteracties, kunnen gebruikers nog steeds op die manieren met uw site hebben samengewerkt nadat de sessie is beëindigd nadat de sessie is beëindigd.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Zijn er plaatsen waar gebruikers steeds dezelfde actie herhalen?

Zoek naar een paginaweergave of aangepaste gebeurtenis die door veel gebruikers wordt herhaald in de volgende stappen in de visualisatie. Dit betekent meestal dat gebruikers repetitieve acties uitvoeren op uw site. Als u herhaling vindt, denk dan na over het wijzigen van het ontwerp van uw site of het toevoegen van nieuwe functionaliteit om herhaling te verminderen. Bijvoorbeeld bulkbewerkingsfunctionaliteit toevoegen als u gebruikers ziet die repetitieve acties uitvoeren op elke rij van een tabelelement.

## <a name="common-questions"></a>Veelgestelde vragen

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>Vertegenwoordigt de eerste gebeurtenis de eerste keer dat de gebeurtenis in een sessie wordt weergegeven of wanneer deze in een sessie wordt weergegeven?

De eerste gebeurtenis op de visualisatie vertegenwoordigt alleen de eerste keer dat een gebruiker die paginaweergave of aangepaste gebeurtenis tijdens een sessie heeft verzonden. Als gebruikers de eerste gebeurtenis meerdere keren in een sessie kunnen verzenden, wordt in de kolom Stap 1 alleen weergegeven hoe gebruikers zich gedragen na de *eerste* instantie van de eerste gebeurtenis, niet alle exemplaren.

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>Sommige knooppunten in mijn visualisatie zijn te hoog niveau. Bijvoorbeeld een knooppunt met de tekst 'Button Clicked'. Hoe kan ik het opsplitsen in meer gedetailleerde knooppunten?

Gebruik de opties **Splitsen op** in het menu **Bewerken:**

1. Kies de gebeurtenis die u wilt opsplitsen in het **menu Evenement.**
2. Kies een dimensie in het menu **Dimensie.** Als u bijvoorbeeld een gebeurtenis hebt met de naam 'Button Clicked', probeert u een aangepaste eigenschap met de naam 'Button Name'.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van gebruik](usage-overview.md)
* [Gebruikers, sessies en gebeurtenissen](usage-segmentation.md)
* [Bewaartermijn](usage-retention.md)
* [Aangepaste gebeurtenissen toevoegen aan uw app](../../azure-monitor/app/api-custom-events-metrics.md)
