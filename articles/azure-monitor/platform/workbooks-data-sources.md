---
title: Azure Monitor werkmappen-gegevens bronnen | Micro soft docs
description: Vereenvoudig complexe rapportage met vooraf opgebouwde en aangepaste werkmappen met para meters Azure Monitor gebaseerd op meerdere gegevens bronnen
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: mbullwin
ms.openlocfilehash: bbd231ca527b4c01509230e839b97187de29febd
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91825737"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Gegevens bronnen Azure Monitor werkmappen

Werkmappen zijn compatibel met een groot aantal gegevens bronnen. In dit artikel vindt u informatie bronnen die momenteel beschikbaar zijn voor Azure Monitor-werkmappen.

## <a name="logs"></a>Logboeken

Met werkmappen kunt u query's uitvoeren op Logboeken vanuit de volgende bronnen:

* Azure Monitor Logboeken (Application Insights resources en Log Analytics-werk ruimten.)
* Resource gerichte gegevens (activiteiten Logboeken)

Ontwerpers van werkmappen kunnen KQL-query's gebruiken waarmee de onderliggende resource gegevens worden getransformeerd om een resultatenset te selecteren die kan worden gevisualiseerd als tekst, grafieken of rasters.

![Scherm afbeelding van werkmappen rapport interface](./media/workbooks-overview/logs.png)

Ontwerpers van werkmappen kunnen eenvoudig een query uitvoeren op meerdere resources, waardoor er een echt uniforme uitgebreide rapportage ervaring wordt gemaakt.

## <a name="metrics"></a>Metrische gegevens

Azure-resources verzenden [metrische gegevens](data-platform-metrics.md) die via werkmappen kunnen worden geopend. Metrische gegevens kunnen worden geopend in werkmappen via een gespecialiseerd besturings element waarmee u de doel resources, de gewenste metrische gegevens en de aggregatie kunt opgeven. Deze gegevens kunnen vervolgens worden getekend in grafieken of rasters.

![Scherm afbeelding van metrische werkmap grafieken van CPU-gebruik](./media/workbooks-overview/metrics-graph.png)

![Scherm afbeelding van de interface voor metrische gegevens van werkmap](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph

Werkmappen bieden ondersteuning voor het uitvoeren van query's voor resources en hun meta gegevens met behulp van Azure resource Graph (ARG). Deze functie wordt hoofd zakelijk gebruikt voor het bouwen van aangepaste query bereiken voor-rapporten. Het resource bereik wordt weer gegeven via een KQL die ARG ondersteunt, wat vaak voldoende is voor algemene use-cases.

Als u een query besturings element wilt maken met deze gegevens bron, gebruikt u de vervolg keuzelijst query type om Azure resource Graph te kiezen en selecteert u de abonnementen die u wilt instellen. Gebruik het besturings element query om de ARG KQL-subset toe te voegen waarmee een interessante resource deel verzameling wordt geselecteerd.

![Scherm opname van KQL-query van Azure resource Graph](./media/workbooks-overview/azure-resource-graph.png)

## <a name="azure-resource-manager"></a>Azure Resource Manager

Werkmap ondersteunt Azure Resource Manager REST-bewerkingen. Zo kunt u een query uitvoeren op management.azure.com-eind punt zonder dat u uw eigen autorisatie header-token hoeft op te geven.

Als u een query besturings element wilt maken met deze gegevens bron, gebruikt u de vervolg keuzelijst gegevens bron om Azure Resource Manager te kiezen. Geef de juiste para meters op, zoals HTTP-methode, URL-pad, headers, URL-para meters en/of hoofd tekst.

> [!NOTE]
> Alleen `GET` , `POST` en `HEAD` bewerkingen worden momenteel ondersteund.

## <a name="azure-data-explorer"></a>Azure Data Explorer

Werkmappen bieden nu ondersteuning voor het uitvoeren van query's vanuit [Azure Data Explorer](/azure/data-explorer/) -clusters met de krachtige [Kusto](/azure/kusto/query/index) -query taal.   

![Scherm opname van het Kusto-query venster](./media/workbooks-overview/data-explorer.png)

## <a name="workload-health"></a>Werk belasting status

Azure Monitor heeft functionaliteit waarmee de beschik baarheid en prestaties van Windows-of Linux-gast besturingssystemen proactief worden bewaakt. Azure Monitor modellen van belang rijke onderdelen en hun relaties, criteria om de status van die onderdelen te meten en welke onderdelen u waarschuwen wanneer er een onjuiste voor waarde wordt gedetecteerd. Met werkmappen kunnen gebruikers deze informatie gebruiken om uitgebreide interactieve rapporten te maken.

Als u een query besturings element wilt gebruiken deze gegevens bron, gebruikt u de vervolg keuzelijst **query type** om de werk belasting status te kiezen en selecteert u abonnement, resource groep of VM-resources om te richten. Gebruik de vervolg keuzelijst status filters om een interessante subset met status incidenten voor uw analyse behoeften te selecteren.

![Scherm afbeelding van waarschuwings query's](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Azure-resource status

Werkmappen bieden ondersteuning voor de Azure-resource status en combi neren met andere gegevens bronnen om uitgebreide, interactieve status rapporten te maken

Als u een query besturings element wilt gebruiken deze gegevens bron, gebruikt u de vervolg keuzelijst **query type** om de Azure-status te kiezen en selecteert u de resources die u wilt instellen. Gebruik de vervolg keuzelijst status filters om een interessante subset van resource problemen voor uw analyse behoeften te selecteren.

![Scherm afbeelding van waarschuwings query's](./media/workbooks-overview/resource-health.png)

## <a name="json"></a>JSON

Met de JSON-provider kunt u een query resultaat maken op basis van statische JSON-inhoud. Het wordt meestal gebruikt in para meters voor het maken van vervolg keuzelijst parameters van statische waarden. Eenvoudige JSON-matrices of-objecten worden automatisch geconverteerd naar raster rijen en-kolommen.  Voor meer specifiek gedrag kunt u het tabblad resultaten en JSONPath-instellingen gebruiken om kolommen te configureren.

## <a name="alerts-preview"></a>Waarschuwingen (preview)

> [!NOTE]
> De voorgestelde manier om informatie over Azure-waarschuwingen op te vragen, is door de gegevens bron van de [Azure-resource grafiek](#azure-resource-graph) te gebruiken door een query uit te stellen op de `AlertsManagementResources` tabel.
>
> Zie de [Naslag informatie over de tabel van Azure resource Graph](../../governance/resource-graph/reference/supported-tables-resources.md)of de [sjabloon waarschuwingen](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Azure%20Resources/Alerts/Alerts.workbook) voor voor beelden.
>
> De gegevens bron van waarschuwingen blijft gedurende een bepaalde tijd beschikbaar terwijl auteurs overstappen op het gebruik van ARG. Het gebruik van deze gegevens bron in sjablonen wordt afgeraden. 

Met werkmappen kunnen gebruikers de actieve waarschuwingen die betrekking hebben op hun resources visualiseren. Beperkingen: de gegevens bron voor waarschuwingen vereist Lees toegang tot het abonnement om resources te kunnen opvragen en kan geen nieuwere soorten waarschuwingen weer geven. 

Om ervoor te zorgen dat een query besturings element deze gegevens bron gebruikt, gebruikt u de vervolg keuzelijst _gegevens bron_ om waarschuwingen te kiezen _(preview)_ en selecteert u de abonnementen, resource groepen of bronnen om te richten. Gebruik de vervolg keuzelijst waarschuwings filter om een interessante subset van waarschuwingen voor uw analyse behoeften te selecteren.

## <a name="custom-endpoint"></a>Aangepast eind punt

Werkmappen ondersteunen het ophalen van gegevens uit een externe bron. Als uw gegevens zich buiten Azure bevinden, kunt u deze naar werkmappen brengen met behulp van dit gegevens bron type.

Als u een query besturings element wilt maken met deze gegevens bron, gebruikt u de vervolg keuzelijst _gegevens bron_ om een _aangepast eind punt_te kiezen. Geef de juiste para meters `Http method` op, zoals,, `url` `headers` `url parameters` en/of `body` . Zorg ervoor dat uw gegevens bron [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) ondersteunt, anders mislukt de aanvraag.

Om te voor komen dat u automatisch aanroepen naar niet-vertrouwde hosts maakt wanneer u sjablonen gebruikt, moet de gebruiker de gebruikte hosts markeren als vertrouwd. U kunt dit doen door te klikken op de knop _toevoegen als vertrouwd_ of door deze toe te voegen als een vertrouwde host in de werkmap instellingen. Deze instellingen worden opgeslagen in browsers die ondersteuning bieden voor IndexDb met Web Workers, meer informatie [hier](https://caniuse.com/#feat=indexeddb).

> [!NOTE]
> Schrijf geen geheimen in een van de velden (,, `headers` `parameters` `body` , `url` ), omdat ze zichtbaar zijn voor alle gebruikers van de werkmap.

## <a name="next-steps"></a>Volgende stappen

* [Ga](workbooks-visualizations.md) voor meer informatie over werkmappen veel uitgebreide visualisaties opties.
* De toegang tot uw werkmap resources [beheren](workbooks-access-control.md) en delen.
* [Tips voor het optimaliseren van query's Log Analytics](../log-query/query-optimization.md)
