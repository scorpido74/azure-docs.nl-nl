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
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d57910ae31d4db9be17b3dc46b5920a925ab4fcf
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248578"
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

## <a name="alerts-preview"></a>Waarschuwingen (preview-versie)

Met werkmappen kunnen gebruikers de actieve waarschuwingen die betrekking hebben op hun resources visualiseren. Met deze functie kunt u rapporten maken waarmee meldings gegevens (waarschuwingen) en diagnostische informatie (metrische gegevens, Logboeken) in één rapport worden opgenomen. Deze informatie kan ook samen worden samengevoegd om uitgebreide rapporten te maken waarin inzichten over deze gegevens bronnen worden gecombineerd.

Als u wilt dat een query besturings element deze gegevens bron gebruikt, gebruikt u de vervolg keuzelijst query type om waarschuwingen te kiezen en selecteert u de abonnementen, resource groepen of bronnen om te richten. Gebruik de vervolg keuzelijst waarschuwings filter om een interessante subset van waarschuwingen voor uw analyse behoeften te selecteren.

![Scherm afbeelding van waarschuwings query's](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>Workload status (preview-versie)

Azure Monitor heeft functionaliteit waarmee de beschik baarheid en prestaties van Windows-of Linux-gast besturingssystemen proactief worden bewaakt. Azure Monitor modellen van belang rijke onderdelen en hun relaties, criteria om de status van die onderdelen te meten en welke onderdelen u waarschuwen wanneer er een onjuiste voor waarde wordt gedetecteerd. Met werkmappen kunnen gebruikers deze informatie gebruiken om uitgebreide interactieve rapporten te maken.

Als u een query besturings element wilt gebruiken deze gegevens bron, gebruikt u de vervolg keuzelijst **query type** om de werk belasting status te kiezen en selecteert u abonnement, resource groep of VM-resources om te richten. Gebruik de vervolg keuzelijst status filters om een interessante subset met status incidenten voor uw analyse behoeften te selecteren.

![Scherm afbeelding van waarschuwings query's](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Azure-resource status 

Werkmappen bieden ondersteuning voor de Azure-resource status en combi neren met andere gegevens bronnen om uitgebreide, interactieve status rapporten te maken

Als u een query besturings element wilt gebruiken deze gegevens bron, gebruikt u de vervolg keuzelijst **query type** om de Azure-status te kiezen en selecteert u de resources die u wilt instellen. Gebruik de vervolg keuzelijst status filters om een interessante subset van resource problemen voor uw analyse behoeften te selecteren.

![Scherm afbeelding van waarschuwings query's](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Azure Data Explorer (preview-versie)

Werkmappen bieden nu ondersteuning voor het uitvoeren van query's vanuit [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/) -clusters met de krachtige [Kusto](https://docs.microsoft.com/azure/kusto/query/index) -query taal.   

![Scherm opname van het Kusto-query venster](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>Volgende stappen

* [Ga](workbooks-visualizations.md) voor meer informatie over werkmappen veel uitgebreide visualisaties opties.
* De toegang tot uw werkmap resources [beheren](workbooks-access-control.md) en delen.
