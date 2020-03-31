---
title: Vervolgkeuzeparameters voor Azure Monitor Workbook
description: Complexe rapportage vereenvoudigen met vooraf gebouwde en aangepaste geparameteriseerde werkmappen met vervolgkeuzeparameters
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: f3220a363025d80fd7636dbfc3af3d2d9d7bc040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658279"
---
# <a name="workbook-drop-down-parameters"></a>Vervolgparameters voor de vervolgkeuzelijst van werkmap

Met vervolgkeuzewaarden kan de gebruiker een of meer invoerwaarden uit een bekende set verzamelen (selecteer bijvoorbeeld een van de verzoeken van uw app). Drop downs bieden een gebruiksvriendelijke manier om willekeurige ingangen van gebruikers te verzamelen. Drop downs zijn vooral handig bij het inschakelen van filtering in uw interactieve rapporten. 

De eenvoudigste manier om een vervolgkeuzelijst op te geven, is door een statische lijst op te geven in de parameterinstelling. Een meer interessante manier is om de lijst dynamisch te krijgen via een KQL query. Met parameterinstellingen u ook opgeven of het één of multi-select is, en als het multi-select is, hoe de resultaatset moet worden opgemaakt (scheidingsteken, offerte, enz.).

## <a name="creating-a-static-drop-down-parameter"></a>Een statische vervolgkeuzeparameter maken

1. Begin met een lege werkmap in de bewerkingsmodus.
2. Kies _Parameters toevoegen_ in de koppelingen in de werkmap.
3. Klik op de blauwe _knop Parameter toevoegen._
4. Voer in het nieuwe parametervenster dat wordt weergegeven:
    1. Parameternaam:`Environment`
    2. Parametertype:`Drop down`
    3. Vereist:`checked`
    4. Toestaan `multiple selection`:`unchecked`
    5. Haal gegevens uit:`JSON`
5. Voeg in het tekstblok JSON Input dit jsonfragment in:
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. Druk op `Update` de blauwe knop.
7. Kies 'Opslaan' op de werkbalk om de parameter te maken.
8. De parameter Milieu is een vervolgkeuzelijst met de drie waarden.

    ![Afbeelding die de verwezenlijking van een statische verdrinkt toont](./media/workbook-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>Een statische vervolgkeuzelijst maken met groepen items
Als uw queryresultaat/json een veld 'groep' bevat, worden in de vervolgkeuzelijst waardengroepen weergegeven. Volg het bovenstaande voorbeeld, maar gebruik in plaats daarvan de volgende json:
```json
[
    { "value":"dev", "label":"Development", "group":"Development" },
    { "value":"dev-cloud", "label":"Development (Cloud)", "group":"Development" },
    { "value":"ppe", "label":"Pre-production", "group":"Test" },
    { "value":"ppe-test", "label":"Pre-production (Test)", "group":"Test" },
    { "value":"prod1", "label":"Prod 1", "selected":true, "group":"Production" },
    { "value":"prod2", "label":"Prod 2", "group":"Production" }
]
```
    ![Image showing an example of a grouped dropdown](./media/workbook-dropdowns/grouped-dropDown.png)


## <a name="creating-a-dynamic-drop-down-parameter"></a>Een dynamische vervolgkeuzeparameter maken
1. Begin met een lege werkmap in de bewerkingsmodus.
2. Kies _Parameters toevoegen_ in de koppelingen in de werkmap.
3. Klik op de blauwe _knop Parameter toevoegen._
4. Voer in het nieuwe parametervenster dat wordt weergegeven:
    1. Parameternaam:`RequestName`
    2. Parametertype:`Drop down`
    3. Vereist:`checked`
    4. Toestaan `multiple selection`:`unchecked`
    5. Haal gegevens uit:`Query`
5. Voeg in het tekstblok JSON Input dit jsonfragment in:

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. Druk op `Run Query` de blauwe knop.
2. Kies 'Opslaan' op de werkbalk om de parameter te maken.
3. De parameter RequestName is een vervolgkeuzelijst voor alle aanvragen in de app.

    ![Afbeelding met de creatie van een dynamische vervolgkeuzelijst](./media/workbook-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>Vervolgkeuzeparameter Verwijzen naar de vervolgkeuzelijst
### <a name="in-kql"></a>In KQL
1. Voeg een querybesturingselement toe aan de werkmap en selecteer een Application Insights-bron.
2. Voer in de KQL-editor dit fragment in

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. Dit wordt uitgebreid op de evaluatietijd van query's om:

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. Voer query uit om de resultaten te bekijken. Optioneel u het weergeven als een grafiek.

    ![Afbeelding met een vervolgkeuzelijst waarnaar wordt verwezen in KQL](./media/workbook-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>Parameterwaarde, label, selectie en groep
De query die wordt gebruikt in de dynamische vervolgkeuzeparameter hierboven, retourneert alleen een lijst met waarden die getrouw worden weergegeven in de vervolgkeuzelijst. Maar wat als u een andere weergavenaam wilde, of als een van deze zou worden geselecteerd? Drop down parameters maken dit mogelijk via de waarde, het label, de selectie en de groep kolommen.

In het onderstaande voorbeeld ziet u hoe u een lijst met afhankelijkheidsfavorieten van Application Insights krijgen waarvan de weergavenamen zijn gestyled met een emoji, de eerste is geselecteerd en is gegroepeerd op operatienamen.

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```
    ![Image showing a drop-down parameter using value, label, selection and group options](./media/workbook-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>Parametersopties voor vervolgzetten
| Parameter | Uitleg | Voorbeeld |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | De geselecteerde waarde | GET fabrikamaccount |
| `{DependencyName:label}` | Het geselecteerde label | 🌐 KRIJG fabrikamaccount |
| `{DependencyName:value}` | De geselecteerde waarde | GET fabrikamaccount |

## <a name="multiple-selection"></a>Meervoudige selectie
In de voorbeelden tot nu toe wordt de parameter expliciet ingesteld op slechts één waarde in de vervolgkeuzelijst. Drop down parameters `multiple selection` ook ondersteuning - waardoor `Allow multiple selection` dit is net zo eenvoudig als het controleren van de optie. 

De gebruiker heeft ook de mogelijkheid om het formaat `delimiter` `quote with` van het resultaat set via de en instellingen. De standaardwaarde retourneert alleen de waarden als verzameling in dit formulier: 'a', 'b', 'c'. Ze hebben ook de mogelijkheid om het aantal selecties te beperken.

De KQL die verwijst naar de parameter moet worden gewijzigd om te werken met de indeling van het resultaat. De meest voorkomende manier om `in` het mogelijk te maken is via de operator.

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

Hier is een voorbeeld voor multi-select vervolgkeuzelijst op het werk:

![Afbeelding met een parameter voor een multi-select-vervolgkeuzelijst](./media/workbook-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>Volgende stappen

* [Ga aan de slag met](workbooks-visualizations.md) het leren van meer over werkmappen met veel uitgebreide visualisatiesopties.
* [Beheer](workbooks-access-control.md) en deel de toegang tot uw werkmapbronnen.
