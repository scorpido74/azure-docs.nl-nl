---
title: Azure Monitor werkmappen met aangepaste para meters
description: Vereenvoudig complexe rapportage met vooraf samengestelde en aangepaste werkmappen met para meters
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 4d9f6e48722f01970a90a3a1d8d8b58b5d939774
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658265"
---
# <a name="interactive-workbooks"></a>Interactieve werkmappen

Met werkmappen kunnen auteurs interactieve rapporten en ervaringen maken voor hun consumenten. Interactiviteit wordt op een aantal manieren ondersteund.

## <a name="parameter-changes"></a>Parameter wijzigingen
Wanneer een werkmap gebruiker een para meter bijwerkt, wordt elk besturings element dat gebruikmaakt van de para meter automatisch vernieuwd en opnieuw getekend om de nieuwe status weer te geven. Dit is hoe de meeste van de Azure Portal-rapporten interactiviteit ondersteunen. Werkmappen bieden dit op een zeer rechte manier met minimale gebruikers inspanning.

Meer informatie over [para meters in werkmappen](workbooks-parameters.md)

## <a name="grid-row-clicks"></a>Raster rijen klikken
Met werkmappen kunnen auteurs scenario's maken waarbij de volgende grafieken worden bijgewerkt op basis van de inhoud van de rij door te klikken op een rij in een raster. 

Een gebruiker kan bijvoorbeeld een raster hebben waarin een lijst met aanvragen wordt weer gegeven en enkele statistieken, zoals fout aantallen. Ze kunnen deze zo instellen dat er wordt geklikt op een rij die overeenkomt met een aanvraag, en resulteert in gedetailleerde grafieken onder bijwerken om naar alleen die aanvraag te filteren.

### <a name="setting-up-interactivity-on-grid-row-click"></a>De rij interactiviteit op raster instellen
1. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht _bewerken_ .
2. Gebruik de koppeling _query toevoegen_ om een besturings element voor een logboek query toe te voegen aan de werkmap. 
3. Selecteer het query type als _logboek_, resource type (bijvoorbeeld Application Insights) en de resources om het doel te bereiken.
4. De query-editor gebruiken om de KQL voor uw analyse in te voeren
    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc    
    ```
5. `Run query` om de resultaten weer te geven
6. Klik op het pictogram _Geavanceerde instellingen_ op de query voet tekst (het pictogram ziet eruit als een tand wiel). Hiermee opent u het deel venster Geavanceerde instellingen 
7. Controleer de instelling: `When an item is selected, export a parameter`
    1. Te exporteren veld: `Request`
    2. Parameter naam: `SelectedRequest`
    3. Standaard waarde: `All requests`
    
    ![Afbeelding van de geavanceerde editor met instellingen voor het exporteren van velden als para meters](./media/workbooks-interactive/advanced-settings.png)

8. Klik op `Done Editing`.
9. Voeg nog een besturings element query toe met behulp van stap 2 en 3.
10. De query-editor gebruiken om de KQL voor uw analyse in te voeren
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
11. `Run query` om de resultaten weer te geven.
12. _Visualisatie_ wijzigen in `Area chart`
12. Klik op een rij in het eerste raster. U ziet hoe het vlak diagram onder filtert op de geselecteerde aanvraag.

Het resulterende rapport ziet er als volgt uit in de bewerkings modus:

![Afbeelding van het maken van een interactieve ervaring met behulp van raster rijen klikken](./media/workbooks-interactive//grid-click-create.png)

In de onderstaande afbeelding ziet u een meer uitgebreid interactief rapport in Lees modus op basis van dezelfde principes. Het rapport gebruikt raster klikken om para meters te exporteren, die op zijn beurt worden gebruikt in twee grafieken en een tekst blok.

![Afbeelding van het maken van een interactieve ervaring met behulp van raster rijen klikken](./media/workbooks-interactive/grid-click-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>De inhoud van een hele rij exporteren
Soms is het wenselijk om de volledige inhoud van de geselecteerde rij te exporteren in plaats van alleen een bepaalde kolom. In dergelijke gevallen moet u de eigenschap `Field to export` uitschakelen in stap 7,1 hierboven. Werkmappen exporteert de volledige celinhoud als een JSON-bestand naar de para meter. 

Gebruik op het verwijzende besturings element KQL de functie `todynamic` om de JSON te parseren en toegang te krijgen tot de afzonderlijke kolommen.

 ## <a name="grid-cell-clicks"></a>Klikken raster cellen
Met werkmappen kunnen auteurs interactiviteit toevoegen via een speciaal type raster kolom renderer, een `link renderer`genoemd. Met een koppelings weergave wordt een grid-cel omgezet in een Hyper link op basis van de inhoud van de cel. Werkmappen ondersteunen veel soorten koppelings beeldrenderings, inclusief de Blades voor het openen van resource overzichten, viewers van eigenschappen, app Insights Search, Usage, trans actie tracering, enzovoort.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>Interactiviteit instellen met behulp van een raster muis klik
1. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht _bewerken_ .
2. Gebruik de koppeling _query toevoegen_ om een besturings element voor een logboek query toe te voegen aan de werkmap. 
3. Selecteer het query type als _logboek_, resource type (bijvoorbeeld Application Insights) en de resources om het doel te bereiken.
4. De query-editor gebruiken om de KQL voor uw analyse in te voeren
    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```
5. `Run query` om de resultaten weer te geven
6. Klik op _kolom instellingen_ om het deel venster instellingen te openen.
7. Stel in het gedeelte _kolommen_ het volgende in:
    1. Voor _beeld_ -kolom renderer: `Link`, weer geven om te openen: `Cell Details`, koppelings Label: `Sample`
    2. _Aantal_ -kolom renderer: `Bar`, kleuren palet: `Blue`, minimum waarde: `0`
    3. _Aanvraag_ -kolom weergave: `Automatic`
    4. Klik op _opslaan en sluiten_ om de wijzigingen toe te passen
8. Klik op een van de `Sample` koppelingen in het raster. Hiermee opent u een eigenschappen venster met de details van een voor beeld van een aanvraag.

    ![Afbeelding van het maken van een interactieve ervaring met klikken op raster cellen](./media/workbooks-interactive/grid-cell-click-create.png)

### <a name="link-renderer-actions"></a>Koppeling renderer-acties
| Koppelings actie | Actie bij klikken |
|:------------- |:-------------|
| `Generic Details` | Hiermee worden de rijwaarden in een eigenschappen raster context blad weer gegeven |
| `Cell Details` | Toont de celwaarde in een eigenschappen raster context blad. Dit is handig wanneer de cel een dynamisch type bevat met informatie (bijvoorbeeld JSON met aanvraag eigenschappen zoals locatie, rolinstantie, enzovoort). |
| `Cell Details` | Toont de celwaarde in een eigenschappen raster context blad. Dit is handig wanneer de cel een dynamisch type bevat met informatie (bijvoorbeeld JSON met aanvraag eigenschappen zoals locatie, rolinstantie, enzovoort). |
| `Custom Event Details` | Hiermee opent u de Application Insights Zoek gegevens met de aangepaste gebeurtenis-ID (itemId) in de cel. |
| `* Details` | Vergelijkbaar met details van aangepaste gebeurtenissen, met uitzonde ring van afhankelijkheden, uitzonde ringen, pagina weergaven, aanvragen en traceringen. |
| `Custom Event User Flows` | Hiermee opent u de Application Insights Gebruikersstromen ervaring die in de cel is gedraaid op de aangepaste gebeurtenis naam |
| `* User Flows` | Vergelijkbaar met aangepaste gebeurtenis Gebruikersstromen, behalve voor uitzonde ringen, pagina weergaven en aanvragen |
| `User Timeline` | Hiermee opent u de gebruikers tijdlijn met de gebruikers-ID (user_Id) in de cel. |
| `Session Timeline` | Hiermee opent u de Application Insights Zoek ervaring voor de waarde in de cel (bijvoorbeeld zoek naar tekst ' ABC ' waarbij ABC de waarde in de cel is) |
| `Resource overview` | Open de resource-overzicht in de portal op basis van de resource-ID-waarde in de cel |

## <a name="conditional-visibility"></a>Voorwaardelijke zicht baarheid
Met werkmap kunnen gebruikers bepaalde besturings elementen weer geven of verbergen op basis van de waarden van de para meters. Hierdoor kunnen auteurs rapporten zien op basis van de gebruikers invoer of de telemetrie-status. Een voor beeld is het weer geven van alleen een samen vatting wanneer dingen goed zijn, maar de volledige details worden weer gegeven wanneer er iets mis gaat.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Interactiviteit instellen met behulp van voorwaardelijke zicht baarheid
1. Volg de stappen in de sectie `Setting up interactivity on grid row click` om twee interactieve besturings elementen in te stellen.
2. Voeg aan de bovenkant een nieuwe para meter toe:
    1. Naam: `ShowDetails`
    2. Parameter type: `Drop down`
    3. Vereist: `checked`
    4. Gegevens ophalen uit: `JSON`
    5. JSON-invoer: `["Yes", "No"]`
    6. Opslaan om wijzigingen door te voeren.
3. Parameter waarde instellen op `Yes`
4. Klik in het besturings element query met het vlak diagram op het pictogram _Geavanceerde instellingen_ (tandwiel pictogram)
5. Controleer de instelling `Make this item conditionally visible`
    1. Dit item wordt weer gegeven als `ShowDetails` parameter waarde `equals` `Yes`
6. Klik op _gereed bewerken_ om wijzigingen door te voeren.
7. Klik op _gereed bewerken_ op de werk balk van de werkmap om de Lees modus in te voeren.
8. Schakel de waarde van para meter `ShowDetails` naar `No`. U ziet dat de onderstaande grafiek verdwijnt.

In de onderstaande afbeelding ziet u het zicht bare geval waar `ShowDetails` is `Yes`

![Afbeelding van de voorwaardelijke zicht baarheid waar de grafiek wordt weer gegeven](./media/workbooks-interactive/conditional-visibility.png)

In de onderstaande afbeelding ziet u de verborgen situatie waarbij `ShowDetails` is `No`

![Afbeelding van de voorwaardelijke zicht baarheid waar de grafiek is verborgen](./media/workbooks-interactive/conditional-invisible.png)

## <a name="next-steps"></a>Volgende stappen


* [Ga](workbooks-visualizations.md) voor meer informatie over werkmappen veel uitgebreide visualisaties opties.
* De toegang tot uw werkmap resources [beheren](workbooks-access-control.md) en delen.
