---
title: Transactiediagnostiek voor Azure Application Insights | Microsoft Documenten
description: Application Insights end-to-end transactiediagnostiek
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.openlocfilehash: 39c4c51a1bc84e06efac3674b1ee5b487f9e6729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671134"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Uniforme transcomponenttransactiediagnostiek

De uniforme diagnostische ervaring correleert telemetrie aan de serverzijde van alle bewaakte toepassingsinzichten in één weergave. Het maakt niet uit of u meerdere bronnen hebt met afzonderlijke instrumentatietoetsen. Application Insights detecteert de onderliggende relatie en stelt u in staat om eenvoudig de toepassingscomponent, afhankelijkheid of uitzondering te diagnosticeren die een transactievertraging of -fout heeft veroorzaakt.

## <a name="what-is-a-component"></a>Wat is een component?

Componenten zijn onafhankelijk inzetbare onderdelen van uw distributed/microservices-toepassing. Ontwikkelaars en operationele teams hebben zichtbaarheid op codeniveau of toegang tot telemetrie gegenereerd door deze toepassingscomponenten.

* Componenten verschillen van "waargenomen" externe afhankelijkheden zoals SQL, EventHub etc. waartoe uw team/organisatie mogelijk geen toegang heeft (code of telemetrie).
* Onderdelen worden uitgevoerd op een willekeurig aantal server-/rol-/container-exemplaren.
* Componenten kunnen afzonderlijke instrumentatiesleutels voor Application Insights zijn (zelfs als abonnementen verschillend zijn) of verschillende rollen die rapporteren aan één instrumentatiesleutel voor Application Insights. De nieuwe ervaring toont details over alle componenten, ongeacht hoe ze zijn ingesteld.

> [!NOTE]
> * **Mis je de gerelateerde itemlinks?** Alle gerelateerde telemetrie bevinden zich in de [bovenste](#cross-component-transaction-chart) en [onderste](#all-telemetry-with-this-operation-id) delen van de linkerkant. 

## <a name="transaction-diagnostics-experience"></a>Ervaring met transactiediagnostiek
Deze weergave bevat vier belangrijke onderdelen: resultatenlijst, een transactiediagram voor meerdere componenten, een tijdreekslijst met alle telemetrie die verband houdt met deze bewerking en het detailvenster voor een geselecteerd telemetrieitem aan de linkerkant.

![Belangrijkste onderdelen](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Transactiediagram voor componenten

Deze grafiek biedt een tijdlijn met horizontale balken voor de duur van aanvragen en afhankelijkheden tussen componenten. Eventuele uitzonderingen die worden verzameld, worden ook gemarkeerd op de tijdlijn.

* De bovenste rij in deze grafiek vertegenwoordigt het ingangspunt, de binnenkomende aanvraag voor de eerste component die in deze transactie wordt aangeroepen. De duur is de totale tijd die nodig is om de transactie te voltooien.
* Oproepen naar externe afhankelijkheden zijn eenvoudige niet-inklapbare rijen, met pictogrammen die het afhankelijkheidstype vertegenwoordigen.
* Oproepen naar andere componenten zijn inklapbare rijen. Elke rij komt overeen met een specifieke bewerking die bij het onderdeel wordt aangeroepen.
* Standaard wordt de aanvraag, afhankelijkheid of uitzondering die u hebt geselecteerd aan de rechterkant weergegeven.
* Selecteer een rij om de details aan de rechterkant te [zien.](#details-of-the-selected-telemetry) 

> [!NOTE]
> Aanroepen naar andere componenten hebben twee rijen: één rij vertegenwoordigt de uitgaande aanroep (afhankelijkheid) van de bellercomponent en de andere rij komt overeen met de binnenkomende aanvraag bij de aangeroepen component. Het toonaangevende pictogram en de afzonderlijke styling van de duurbalken helpen elkaar te onderscheiden.

## <a name="all-telemetry-with-this-operation-id"></a>Alle telemetrie met deze operation-id

In deze sectie ziet u de platte lijstweergave in een tijdsreeks van alle telemetrie die verband houdt met deze transactie. Het toont ook de aangepaste gebeurtenissen en traces die niet worden weergegeven in de transactiegrafiek. U deze lijst filteren op telemetrie gegenereerd door een specifieke component/oproep. U elk telemetrie-item in deze lijst selecteren om de bijbehorende details aan de rechterkant te [zien.](#details-of-the-selected-telemetry)

![Tijdvolgorde van alle telemetrie](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Details van de geselecteerde telemetrie

In dit inklapbare deelvenster worden de details van een geselecteerd item uit de transactiegrafiek of de lijst weergegeven. 'Alles weergeven' bevat alle standaardkenmerken die worden verzameld. Alle aangepaste kenmerken worden afzonderlijk weergegeven onder de standaardset. Klik op de "..." onder het venster stacktrace om een optie te krijgen om het spoor te kopiëren. "Open profiler traces" of "Open debug snapshot" toont code niveau diagnostiek in overeenkomstige detail vensters.

![Uitzonderingsdetail](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Zoekresultaten

In dit inklapbare deelvenster worden de andere resultaten weergegeven die voldoen aan de filtercriteria. Klik op een resultaat om de respectievelijke details van de 3 hierboven genoemde secties bij te werken. We proberen monsters te vinden die waarschijnlijk de details van alle componenten beschikbaar hebben, zelfs als de bemonstering in een van deze componenten van kracht is. Deze worden weergegeven als "voorgestelde" monsters.

![Zoekresultaten](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>De buggervan profiler en momentopname

[Applicatieinsights profiler](../../azure-monitor/app/profiler.md) of [snapshot debugger](snapshot-debugger.md) helpen met code-level diagnostiek van prestaties en fout problemen. Met deze ervaring u profilersporen of momentopnamen van elk onderdeel met één klik zien.

Als profiler niet aan de slag kon, neem dan contact op met **serviceprofilerhelp\@microsoft.com**

Als je Snapshot Debugger niet aan de slag krijgen, neem dan contact op met **snapshothelp\@microsoft.com**

![Profiler-integratie](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>Veelgestelde vragen

*Ik zie een enkel onderdeel op de grafiek, en de anderen worden alleen weergegeven als externe afhankelijkheden zonder enig detail van wat er gebeurd is binnen deze componenten.*

Mogelijke redenen:

* Zijn de andere componenten geinstrumenteerd met Application Insights?
* Maken ze gebruik van de nieuwste stabiele Application Insights SDK?
* Als deze componenten afzonderlijke Application Insights-bronnen zijn, hebt u dan toegang nodig tot hun telemetrie?

Als je wel toegang hebt en de componenten zijn uitgerust met de nieuwste Application Insights SDKs, laat het ons dan weten via het rechtsboven-feedbackkanaal.

*Ik zie dubbele rijen voor de afhankelijkheden. Wordt dit verwacht?*

Op dit moment tonen we de uitgaande afhankelijkheidsoproep, gescheiden van de binnenkomende aanvraag. Doorgaans zien de twee gesprekken er identiek uit, waarbij alleen de duurwaarde verschilt als gevolg van de retourvlucht van het netwerk. Het toonaangevende pictogram en de afzonderlijke styling van de duurbalken helpen elkaar te onderscheiden. Is deze presentatie van de gegevens verwarrend? Geef ons uw feedback!

*Hoe zit het met klok scheeftrekkingen over verschillende component exemplaren?*

Tijdlijnen worden aangepast voor klokscheeftrekkingen in het transactiediagram. U de exacte tijdstempels zien in het detailvenster of met Behulp van Analytics.

*Waarom mist de nieuwe ervaring de meeste gerelateerde itemsquery's?*

Dit is standaard. Alle gerelateerde items, voor alle componenten, zijn al beschikbaar aan de linkerkant (bovenste en onderste secties). De nieuwe ervaring heeft twee gerelateerde items die de linkerkant niet dekt: alle telemetrie van vijf minuten voor en na deze gebeurtenis en de gebruikerstijdlijn.
