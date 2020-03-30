---
title: Gebruikscohorten azure application insights | Microsoft Documenten
description: Verschillende sets of gebruikers, sessies, gebeurtenissen of bewerkingen analyseren die iets gemeen hebben
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 04/10/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 0c552e8f3e732c49da02b2f5704fb9cf312fb3e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671083"
---
# <a name="application-insights-cohorts"></a>Application Insights cohorten

Een cohort is een set gebruikers, sessies, gebeurtenissen of bewerkingen die iets gemeen hebben. In Azure Application Insights worden cohorten gedefinieerd door een analysequery. In gevallen waarin u een specifieke set gebruikers of gebeurtenissen herhaaldelijk moet analyseren, kunnen cohorten u meer flexibiliteit geven om precies de set uit te drukken waarin u geïnteresseerd bent.

![Deelvenster Cohorten](./media/usage-cohorts/001.png)

## <a name="cohorts-versus-basic-filters"></a>Cohorten versus basisfilters

Cohorten worden gebruikt op een manier die vergelijkbaar is met filters. Maar de definities van cohorten zijn opgebouwd uit aangepaste analysequery's, zodat ze veel flexibeler en complexer zijn. In tegenstelling tot filters u cohorten opslaan, zodat andere leden van uw team ze opnieuw kunnen gebruiken.

U een cohort van gebruikers definiëren die allemaal een nieuwe functie in uw app hebben geprobeerd. U dit cohort opslaan in uw Application Insights-bron. Het is gemakkelijk om deze opgeslagen groep specifieke gebruikers in de toekomst te analyseren.

> [!NOTE]
> Nadat ze zijn gemaakt, zijn cohorten beschikbaar via de hulpprogramma's Gebruikers, Sessies, Gebeurtenissen en Gebruikersstromen.

## <a name="example-engaged-users"></a>Voorbeeld: Betrokken gebruikers

Uw team definieert een betrokken gebruiker als iedereen die uw app vijf of meer keer in een bepaalde maand gebruikt. In deze sectie definieert u een cohort van deze betrokken gebruikers.

1. Open het gereedschap Cohorten.

2. Selecteer het tabblad **Sjabloongalerie.** U ziet een verzameling sjablonen voor verschillende cohorten.

3. Selecteer **Betrokken gebruikers op gebruikte dagen**.

    Er zijn drie parameters voor dit cohort:
    * **Activiteiten**, waarbij u kiest welke gebeurtenissen en paginaweergaven als 'gebruik' worden beschouwd.
    * **Periode**, de definitie van een maand.
    * **UsedAtLeastCustom**, het aantal keren dat gebruikers iets binnen een periode moeten gebruiken om als betrokken te tellen.

4. **Wijzig UsedAtLeastCustom** in **5+ dagen**en laat de **periode** op de standaardperiode van 28 dagen.

    ![Betrokken gebruikers](./media/usage-cohorts/003.png)

    Nu vertegenwoordigt dit cohort alle gebruikers-id's die zijn verzonden met een aangepaste gebeurtenis of paginaweergave op 5 afzonderlijke dagen in de afgelopen 28.

5. Selecteer **Opslaan**.

   > [!TIP]
   > Geef uw cohort een naam, zoals 'Betrokken gebruikers (5+ dagen).' Sla het op in 'Mijn rapporten' of 'Gedeelde rapporten', afhankelijk van of u wilt dat andere mensen die toegang hebben tot deze Application Insights-bron dit cohort kunnen zien.

6. Selecteer **Terug naar galerie**.

### <a name="what-can-you-do-by-using-this-cohort"></a>Wat u doen met behulp van deze cohort?

Open het gereedschap Gebruikers. Kies in de vervolgkeuzelijst **Weergeven** het cohort dat u hebt gemaakt onder **Gebruikers die behoren tot**.

Nu wordt het hulpprogramma Gebruikers gefilterd op dit cohort van gebruikers:

![Deelvenster Gebruikers gefilterd op een bepaald cohort](./media/usage-cohorts/004.png)

Een paar belangrijke dingen op te merken:

* U deze set niet maken via normale filters. De datumlogica is geavanceerder.
* U dit cohort verder filteren met behulp van de normale filters in het gereedschap Gebruikers. Dus hoewel het cohort is gedefinieerd op vensters van 28 dagen, u het tijdsbereik in het gereedschap Gebruikers nog steeds aanpassen op 30, 60 of 90 dagen.

Deze filters ondersteunen meer geavanceerde vragen die onmogelijk kunnen worden uitte drukken via de querybouwer. Een voorbeeld hiervan _zijn mensen die de afgelopen 28 dagen betrokken waren. Hoe gedroegen diezelfde mensen zich de afgelopen 60 dagen?_

## <a name="example-events-cohort"></a>Voorbeeld: Gebeurtenissencohort

U ook cohorten van gebeurtenissen maken. In deze sectie definieert u een cohort van de gebeurtenissen en paginaweergaven. Dan zie je hoe ze te gebruiken van de andere tools. Dit cohort kan een reeks gebeurtenissen definiëren die uw team als _actief gebruik_ beschouwt of een set die gerelateerd is aan een bepaalde nieuwe functie.

1. Open het gereedschap Cohorten.

2. Selecteer het tabblad **Sjabloongalerie.** U ziet een verzameling sjablonen voor verschillende cohorten.

3. Selecteer **Gebeurteniskiezer**.

    ![Schermafbeelding van de kiezer voor gebeurtenissen](./media/usage-cohorts/006.png)

4. Selecteer in de vervolgkeuzelijst **Activiteiten** de gebeurtenissen die u in het cohort wilt zijn.

5. Sla het cohort en geef het een naam.

## <a name="example-active-users-where-you-modify-a-query"></a>Voorbeeld: actieve gebruikers waarbij u een query wijzigt

De vorige twee cohorten werden gedefinieerd met behulp van vervolgkeuzevakken. Maar u ook cohorten definiëren door analysequery's te gebruiken voor totale flexibiliteit. Om te zien hoe u een cohort van gebruikers uit het Verenigd Koninkrijk wilt maken.

![Geanimeerd beeld dat door gebruik van hulpmiddel Cohorten loopt](./media/usage-cohorts/cohorts0001.gif)

1. Open het gereedschap Cohorten, selecteer het tabblad **Sjabloongalerie** en selecteer **het cohort Lege gebruikers**.

    ![Cohort lege gebruikers](./media/usage-cohorts/001.png)

    Er zijn drie secties:
   * Een tekstsectie markeren, waarin u het cohort voor anderen in uw team in meer detail beschrijft.

   * Een sectie parameters, waarin u uw eigen parameters maakt, zoals **activiteiten** en andere vervolgkeuzevakken uit de vorige twee voorbeelden.

   * Een querysectie, waarin u het cohort definieert met behulp van een analysequery.

     In de querysectie schrijft u [een analysequery](/azure/kusto/query). De query selecteert de bepaalde set rijen die het cohort beschrijven dat u wilt definiëren. Het gereedschap Cohorten voegt vervolgens impliciet een "| samenvatten door user_Id" clausule aan de query. Deze gegevens worden onder de query in een tabel weergegeven, zodat u zorgen dat uw query resultaten retourt.

     > [!NOTE]
     > Als u de query niet ziet, probeert u het formaat van de sectie te wijzigen om deze groter te maken en de query weer te geven. De geanimeerde .gif aan het begin van deze sectie illustreert het formaat gedrag.

2. Kopieer en plak de volgende tekst in de queryeditor:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Selecteer **Query uitvoeren**. Als u gebruikersnamen niet in de tabel ziet verschijnen, gaat u over naar een land/regio waar uw toepassing gebruikers heeft.

4. Sla en noem het cohort.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

_Ik heb een cohort van gebruikers uit een bepaald land / regio gedefinieerd. Als ik dit cohort in de gebruikerstool vergelijk met alleen het instellen van een filter op dat land/regio, zie ik verschillende resultaten. Waarom?_

Cohorten en filters zijn verschillend. Stel dat u een cohort van gebruikers uit het Verenigd Koninkrijk hebt (gedefinieerd zoals het vorige voorbeeld) en u de resultaten ervan vergelijkt met het instellen van het filter 'Land of regio = Verenigd Koninkrijk'.

* De cohortversie toont alle gebeurtenissen van gebruikers die een of meer gebeurtenissen vanuit het Verenigd Koninkrijk hebben verzonden in het huidige tijdsbereik. Als u splitst per land of regio, ziet u waarschijnlijk veel landen en regio's.
* De filtersversie toont alleen gebeurtenissen uit het Verenigd Koninkrijk. Maar als u splitst per land of regio, ziet u alleen het Verenigd Koninkrijk.

## <a name="learn-more"></a>Meer informatie

* [Querytaal voor analytische gegevens](https://go.microsoft.com/fwlink/?linkid=856587)
* [Gebruikers, sessies, evenementen](usage-segmentation.md)
* [Gebruikersstromen](usage-flows.md)
* [Overzicht van gebruik](usage-overview.md)
