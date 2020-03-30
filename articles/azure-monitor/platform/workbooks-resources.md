---
title: Parameters van Azure Monitor-werkmappenresource
description: Complexe rapportage vereenvoudigen met vooraf gebouwde en aangepaste geparametereerde werkmappen
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: cc2cde7932f783f63ee2783f0589ce4f88f248a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658099"
---
# <a name="workbook-resource-parameters"></a>Resourceparameters voor werkmap

Resourceparameters maken het mogelijk resources in werkmappen te kiezen. Dit is handig bij het instellen van het bereik waaruit de gegevens kunnen worden opgehaald. Een voorbeeld is dat gebruikers de set VM's kunnen selecteren, die de grafieken later zullen gebruiken bij het presenteren van de gegevens.

Waarden van resourcepickers kunnen afkomstig zijn uit de werkmapcontext, statische lijst of uit Azure Resource Graph-query's.

## <a name="creating-a-resource-parameter-workbook-resources"></a>Een resourceparameter maken (werkmapbronnen)
1. Begin met een lege werkmap in de bewerkingsmodus.
2. Kies _Parameters toevoegen_ in de koppelingen in de werkmap.
3. Klik op de blauwe _knop Parameter toevoegen._
4. Voer in het nieuwe parametervenster dat wordt weergegeven:
    1. Parameternaam:`Applications`
    2. Parametertype:`Resource picker`
    3. Vereist:`checked`
    4. Meerdere selecties toestaan:`checked`
5. Haal gegevens uit:`Workbook Resources`
6. Alleen resourcetypen opnemen:`Application Insights`
7. Kies 'Opslaan' op de werkbalk om de parameter te maken.

![Afbeelding met de creatie van een resourceparameter met behulp van werkmapbronnen](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>Een resourceparameter maken (Azure Resource Graph)
1. Begin met een lege werkmap in de bewerkingsmodus.
2. Kies _Parameters toevoegen_ in de koppelingen in de werkmap.
3. Klik op de blauwe _knop Parameter toevoegen._
4. Voer in het nieuwe parametervenster dat wordt weergegeven:
    1. Parameternaam:`Applications`
    2. Parametertype:`Resource picker`
    3. Vereist:`checked`
    4. Meerdere selecties toestaan:`checked`
5. Haal gegevens uit:`Query`
    1. Querytype:`Azure Resource Graph`
    2. Abonnementen:`Use default subscriptions`
    3. Voeg in het querybesturingselement dit fragment toe
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. Kies 'Opslaan' op de werkbalk om de parameter te maken.

![Afbeelding met de aanmaak van een resourceparameter met Azure Resource Graph](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> Azure Resource Graph is nog niet in alle clouds beschikbaar. Zorg ervoor dat het wordt ondersteund in uw doelcloud als u voor deze aanpak kiest.

[Documentatie voor Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)

## <a name="creating-a-resource-parameter--json-list"></a>Een resourceparameter maken (JSON-lijst)
1. Begin met een lege werkmap in de bewerkingsmodus.
2. Kies _Parameters toevoegen_ in de koppelingen in de werkmap.
3. Klik op de blauwe _knop Parameter toevoegen._
4. Voer in het nieuwe parametervenster dat wordt weergegeven:
    1. Parameternaam:`Applications`
    2. Parametertype:`Resource picker`
    3. Vereist:`checked`
    4. Meerdere selecties toestaan:`checked`
5. Haal gegevens uit:`JSON`
    1. Voeg in het inhoudsbesturingselement dit jsonfragment toe
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. Druk op de blauwe _knop Bijwerken._
6. Optioneel instellen `Include only resource types` op _Application Insights_
7. Kies 'Opslaan' op de werkbalk om de parameter te maken.

## <a name="referencing-a-resource-parameter"></a>Een resourceparameter verwijzen
1. Voeg een querybesturingselement toe aan de werkmap en selecteer een Application Insights-bron.
2. Gebruik de vervolgkeuzelijst _Application Insights_ om de parameter aan het besturingselement te binden. Als u dit doet, stelt u het bereik van de query in op de resources die door de parameter worden geretourneerd tijdens de looptijd.
4. Voeg in het Besturingselement KQL dit fragment toe
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. Voer query uit om de resultaten te bekijken. 

![Afbeelding met een resourceparameter waarnaar wordt verwezen in een querybesturingselement](./media/workbooks-resources/resource-reference.png)

> Deze benadering kan worden gebruikt om resources te binden aan andere besturingselementen, zoals metrische gegevens.

## <a name="resource-parameter-options"></a>Resourceparameteropties
| Parameter | Uitleg | Voorbeeld |
| ------------- |:-------------|:-------------|
| `{Applications}` | De geselecteerde resource-id | _/abonnementen/<sub-id->/resourceGroepen/<resourcegroep>/providers/<resourcetype>/acmeauthentication_ |
| `{Applications:label}` | Het label van de geselecteerde resource | `acmefrontend` |
| `{Applications:value}` | De waarde van de geselecteerde resource | _'/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication'_ |
| `{Applications:name}` | De naam van de geselecteerde resource | `acmefrontend` |
| `{Applications:resourceGroup}` | De resourcegroep van de geselecteerde resource | `acmegroup` |
| `{Applications:resourceType}` | Het type van de geselecteerde resource | _microsoft.insights/componenten_ |
| `{Applications:subscription}` | Het abonnement van de geselecteerde resource |  |
| `{Applications:grid}` | Een raster met de resourceeigenschappen. Handig om te renderen in een tekstblok tijdens het foutopsporing  |  |

## <a name="next-steps"></a>Volgende stappen

* [Ga aan de slag met](workbooks-visualizations.md) het leren van meer over werkmappen met veel uitgebreide visualisatiesopties.
* [Beheer](workbooks-access-control.md) en deel de toegang tot uw werkmapbronnen.
