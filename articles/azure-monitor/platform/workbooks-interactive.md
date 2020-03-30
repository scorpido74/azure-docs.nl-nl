---
title: Azure Monitor-werkmappen met aangepaste parameters
description: Complexe rapportage vereenvoudigen met vooraf gebouwde en aangepaste geparametereerde werkmappen
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 4d9f6e48722f01970a90a3a1d8d8b58b5d939774
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658265"
---
# <a name="interactive-workbooks"></a>Interactieve workbooks

Met werkmappen kunnen auteurs interactieve rapporten en ervaringen voor hun consumenten maken. Interactiviteit wordt op een aantal manieren ondersteund.

## <a name="parameter-changes"></a>Parameterwijzigingen
Wanneer een werkmapgebruiker een parameter bijwerkt, wordt elk besturingselement dat de parameter gebruikt, automatisch vernieuwd en opnieuw tekent om de nieuwe status weer te geven. Dit is hoe de meeste Azure-portalrapporten interactiviteit ondersteunen. Werkmappen bieden dit op een zeer rechttoe rechtaan manier met minimale inspanning van de gebruiker.

Meer informatie over [parameters in werkmappen](workbooks-parameters.md)

## <a name="grid-row-clicks"></a>Klikken op rasterrij
Met werkmappen kunnen auteurs scenario's maken waarin het klikken op een rij in een raster volgende grafieken bijwerkt op basis van de inhoud van de rij. 

Een gebruiker kan bijvoorbeeld een raster hebben dat een lijst met aanvragen weergeeft en sommige statistieken zoals falen telt. Ze kunnen het zo instellen dat het klikken op een rij die overeenkomt met een aanvraag, zal resulteren in gedetailleerde grafieken hieronder bijwerken om te filteren op alleen dat verzoek.

### <a name="setting-up-interactivity-on-grid-row-click"></a>Interactiviteit instellen op rasterrijklik
1. Schakel de werkmap over naar de bewerkingsmodus door op het werkbalkitem _Bewerken_ te klikken.
2. Gebruik de koppeling _Query toevoegen_ om een logboekquerybesturingselement toe te voegen aan de werkmap. 
3. Selecteer het querytype als _Logboek,_ resourcetype (bijvoorbeeld Toepassingsinzichten) en de resources die u wilt targeten.
4. De queryeditor gebruiken om de KQL in te voeren voor uw analyse
    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc    
    ```
5. `Run query`om de resultaten te zien
6. Klik op het pictogram _Geavanceerde instellingen_ op de queryvoettekst (het pictogram ziet eruit als een versnelling). Hiermee wordt het deelvenster geavanceerde instellingen geopend 
7. Controleer de instelling:`When an item is selected, export a parameter`
    1. Veld om uit te voeren:`Request`
    2. Parameternaam:`SelectedRequest`
    3. Standaardwaarde:`All requests`
    
    ![Afbeelding met de geavanceerde editor met instellingen voor het exporteren van velden als parameters](./media/workbooks-interactive/advanced-settings.png)

8. Klik op `Done Editing`.
9. Voeg een ander querybesturingselement toe met stap 2 en 3.
10. De queryeditor gebruiken om de KQL in te voeren voor uw analyse
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
11. `Run query`om de resultaten te zien.
12. _Visualisatie wijzigen_ in`Area chart`
12. Klik op een rij in het eerste raster. Houd er rekening mee hoe de onderstaande gebiedsgrafiek filtert op de geselecteerde aanvraag.

Het resulterende rapport ziet er als volgt uit in de bewerkingsmodus:

![Afbeelding met de creatie van een interactieve ervaring met klikken op rasterrij](./media/workbooks-interactive//grid-click-create.png)

De afbeelding hieronder toont een uitgebreider interactief rapport in leesmodus op basis van dezelfde principes. Het rapport gebruikt rasterklikken om parameters te exporteren - die op hun beurt worden gebruikt in twee grafieken en een tekstblok.

![Afbeelding met de creatie van een interactieve ervaring met klikken op rasterrij](./media/workbooks-interactive/grid-click-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>De inhoud van een hele rij exporteren
Het is soms wenselijk om de volledige inhoud van de geselecteerde rij te exporteren in plaats van alleen een bepaalde kolom. Laat in dergelijke `Field to export` gevallen de eigenschap ongedaan maken in stap 7.1 hierboven. Werkmappen exporteren de volledige rijinhoud als json naar de parameter. 

Gebruik de functie om de `todynamic` json te ontleden en toegang te krijgen tot de afzonderlijke kolommen.

 ## <a name="grid-cell-clicks"></a>Klikken op rastercel
Met werkmappen kunnen auteurs interactiviteit toevoegen via een speciaal `link renderer`type rasterkolomrenderer genaamd a . Een koppelingsrenderer converteert een rastercel in een hyperlink op basis van de inhoud van de cel. Werkmappen ondersteunen vele soorten linkrenderers - waaronder die waarmee het openen van resource-overzichtsbladen, kijkers van de vastgoedtas, app Insights-zoekopdracht, gebruik, transactietracering, enz.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>Interactiviteit instellen met klikken op rastercellen
1. Schakel de werkmap over naar de bewerkingsmodus door op het werkbalkitem _Bewerken_ te klikken.
2. Gebruik de koppeling _Query toevoegen_ om een logboekquerybesturingselement toe te voegen aan de werkmap. 
3. Selecteer het querytype als _Logboek,_ resourcetype (bijvoorbeeld Toepassingsinzichten) en de resources die u wilt targeten.
4. De queryeditor gebruiken om de KQL in te voeren voor uw analyse
    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```
5. `Run query`om de resultaten te zien
6. Klik _op Kolominstellingen_ om het instellingenvenster te openen.
7. Stel in de sectie _Kolommen_ het volgende in:
    1. _Voorbeeld_ - Kolomrenderer: `Link`, `Cell Details`Weergeven om te openen: , Koppelingslabel:`Sample`
    2. _Aantal_ - Kolomrenderer: `Bar`, `Blue`Kleurenpalet: , Minimumwaarde:`0`
    3. _Aanvraag_ - Kolomrenderer:`Automatic`
    4. Klik _op Opslaan en sluiten_ om wijzigingen toe te passen
8. Klik op een `Sample` van de links in het raster. Hiermee wordt een eigenschappenvenster geopend met de details van een gesamplede aanvraag.

    ![Afbeelding met de creatie van een interactieve ervaring met klikken op rastercellen](./media/workbooks-interactive/grid-cell-click-create.png)

### <a name="link-renderer-actions"></a>Rendereracties koppelen
| Koppelingsactie | Actie op klik |
|:------------- |:-------------|
| `Generic Details` | Toont de rijwaarden in een contextblad van een eigenschapsraster |
| `Cell Details` | Hiermee wordt de celwaarde in een contextblad van het eigenschappenraster weergegeven. Handig wanneer de cel een dynamisch type met informatie bevat (bijvoorbeeld json met aanvraageigenschappen zoals locatie, rolinstantie, enz.). |
| `Cell Details` | Hiermee wordt de celwaarde in een contextblad van het eigenschappenraster weergegeven. Handig wanneer de cel een dynamisch type met informatie bevat (bijvoorbeeld json met aanvraageigenschappen zoals locatie, rolinstantie, enz.). |
| `Custom Event Details` | Hiermee opent u de zoekgegevens van Application Insights met de aangepaste gebeurtenis-id (itemId) in de cel |
| `* Details` | Vergelijkbaar met aangepaste gebeurtenisdetails, behalve voor afhankelijkheden, uitzonderingen, paginaweergaven, aanvragen en traces. |
| `Custom Event User Flows` | Hiermee opent u de gebruikersstromen van Application Insights die is gedraaid op de aangepaste gebeurtenisnaam in de cel |
| `* User Flows` | Vergelijkbaar met gebruikersstromen voor aangepaste gebeurtenissen, behalve uitzonderingen, paginaweergaven en aanvragen |
| `User Timeline` | Hiermee opent u de gebruikerstijdlijn met de gebruikersnaam (user_Id) in de cel |
| `Session Timeline` | Hiermee opent u de zoekervaring Application Insights naar de waarde in de cel (bijvoorbeeld zoeken naar tekst 'abc' waarbij abc de waarde in de cel is) |
| `Resource overview` | Het overzicht van de resource openen in de portal op basis van de resource-id-waarde in de cel |

## <a name="conditional-visibility"></a>Voorwaardelijke zichtbaarheid
Met de werkmap kunnen gebruikers bepaalde besturingselementen laten verschijnen of verdwijnen op basis van waarden van de parameters. Hierdoor kunnen auteurs rapporten er anders laten uitzien op basis van de invoer van de gebruiker of de telemetriestatus. Een voorbeeld is consumenten slechts een samenvatting laten zien wanneer dingen goed zijn, maar tonen volledige details wanneer er iets mis is.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Interactiviteit instellen met voorwaardelijke zichtbaarheid
1. Volg de stappen `Setting up interactivity on grid row click` in de sectie om twee interactieve besturingselementen in te stellen.
2. Voeg bovenaan een nieuwe parameter toe:
    1. Naam:`ShowDetails`
    2. Parametertype:`Drop down`
    3. Vereist:`checked`
    4. Haal gegevens uit:`JSON`
    5. JSON-invoer:`["Yes", "No"]`
    6. Opslaan om wijzigingen door te voeren.
3. Parameterwaarde instellen op`Yes`
4. Klik in het querybesturingselement met het gebiedsdiagram op het pictogram _Geavanceerde instellingen_ (tandwielpictogram)
5. Controleer de instelling`Make this item conditionally visible`
    1. Dit item is `ShowDetails` zichtbaar `equals` als de parameterwaarde`Yes`
6. Klik _op Gereed bewerken_ om wijzigingen door te voeren.
7. Klik op _Gereed bewerken_ op de werkmap om de leesmodus in te voeren.
8. Schakel de waarde `ShowDetails` `No`van de parameter in . Merk op dat de onderstaande grafiek verdwijnt.

De afbeelding hieronder toont `ShowDetails` de zichtbare case waar`Yes`

![Afbeelding met de voorwaardelijke zichtbaarheid waar de grafiek zichtbaar is](./media/workbooks-interactive/conditional-visibility.png)

De afbeelding hieronder toont `ShowDetails` de verborgen case waar is`No`

![Afbeelding met de voorwaardelijke zichtbaarheid waar de grafiek is verborgen](./media/workbooks-interactive/conditional-invisible.png)

## <a name="next-steps"></a>Volgende stappen


* [Ga aan de slag met](workbooks-visualizations.md) het leren van meer over werkmappen met veel uitgebreide visualisatiesopties.
* [Beheer](workbooks-access-control.md) en deel de toegang tot uw werkmapbronnen.
