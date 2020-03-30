---
title: Tekstparameters voor Azure Monitor-werkmappen
description: Vereenvoudig complexe rapportage met vooraf gebouwde en aangepaste geparameteriseerde werkmappen. Meer informatie over de parameters van werkmaptekst.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: c5fb585d0eb6aeb7866c2ab04b324ee31fe903ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658046"
---
# <a name="workbook-text-parameters"></a>Tekstparameters voor werkmap

Tekstvakparameters bieden een eenvoudige manier om tekstinvoer van werkmapgebruikers te verzamelen. Ze worden gebruikt wanneer het niet praktisch is om een drop-down te gebruiken om de invoer te verzamelen (bijvoorbeeld een willekeurige drempel of generieke filters). Met werkmappen kunnen auteurs de standaardwaarde van het tekstvak uit een query halen. Dit maakt interessante scenario's mogelijk, zoals het instellen van de standaarddrempel op basis van de p95 van de statistiek.

Een veelgebruikt gebruik van tekstvakken is als interne variabelen die worden gebruikt door andere werkmapbesturingselementen. Dit wordt gedaan door gebruik te maken van een query voor standaardwaarden en het invoerbesturingselement onzichtbaar te maken in de leesmodus. Een gebruiker wil bijvoorbeeld dat een drempelwaarde afkomstig is van een formule (geen gebruiker) en vervolgens de drempelwaarde in volgende query's gebruikt.

## <a name="creating-a-text-parameter"></a>Een tekstparameter maken
1. Begin met een lege werkmap in de bewerkingsmodus.
2. Kies _Parameters toevoegen_ in de koppelingen in de werkmap.
3. Klik op de blauwe _knop Parameter toevoegen._
4. Voer in het nieuwe parametervenster dat wordt weergegeven:
    1. Parameternaam:`SlowRequestThreshold`
    2. Parametertype:`Text`
    3. Vereist:`checked`
    4. Standaardwaarde ophalen uit query:`unchecked`
5. Kies 'Opslaan' op de werkbalk om de parameter te maken.

    ![Afbeelding met de aanmaak van een tekstparameter](./media/workbooks-text/text-create.png)

Zo ziet de werkmap eruit in de leesmodus.

![Afbeelding met een tekstparameter in de leesmodus](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>Een tekstparameter verwijzen
1. Voeg een querybesturingselement toe aan `Add query` de werkmap door de blauwe koppeling te selecteren en selecteer een application insights-bron.
2. Voeg in het vak KQL het volgtte:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. Door de tekstparameter met een waarde van 500 te gebruiken in combinatie met het querybesturingselement voert u de onderstaande query effectief uit:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. Query uitvoeren om de resultaten te bekijken

    ![Afbeelding met een tekstparameter waarnaar wordt verwezen in KQL](./media/workbooks-text/text-reference.png)


## <a name="setting-default-values"></a>Standaardwaarden instellen
1. Begin met een lege werkmap in de bewerkingsmodus.
2. Kies _Parameters toevoegen_ in de koppelingen in de werkmap.
3. Klik op de blauwe _knop Parameter toevoegen._
4. Voer in het nieuwe parametervenster dat wordt weergegeven:
    1. Parameternaam:`SlowRequestThreshold`
    2. Parametertype:`Text`
    3. Vereist:`checked`
    4. Standaardwaarde ophalen uit query:`checked`
5. Voeg in het vak KQL het volgtte:
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    Met deze query wordt de standaardwaarde van het tekstvak ingesteld op de 95e percentielduur voor alle aanvragen in de app.
6. Query uitvoeren om het resultaat te zien
7. Kies 'Opslaan' op de werkbalk om de parameter te maken.

    ![Afbeelding met een tekstparameter met standaardwaarde van KQL](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> Terwijl in dit voorbeeld toepassingsstatistiekengegevens worden opgevraagd, kan de aanpak worden gebruikt voor elke op logboeken gebaseerde gegevensbron - Log Analytics, Azure Resource Graph, enz.

## <a name="next-steps"></a>Volgende stappen

* [Ga aan de slag met](workbooks-visualizations.md) het leren van meer over werkmappen met veel uitgebreide visualisatiesopties.
* [Beheer](workbooks-access-control.md) en deel de toegang tot uw werkmapbronnen.
