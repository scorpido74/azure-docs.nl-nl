---
title: Gegevensbronnen voor Azure Monitor-werkmappen | Microsoft-documenten
description: Vereenvoudig complexe rapportage met vooraf gebouwde en aangepaste geparameteriseerde Azure Monitor-werkmappen die zijn gemaakt van meerdere gegevensbronnen
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248578"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Gegevensbronnen voor Azure Monitor-werkmappen

Werkmappen zijn compatibel met een groot aantal gegevensbronnen. In dit artikel vindt u gegevensbronnen die momenteel beschikbaar zijn voor Azure Monitor-werkmappen.

## <a name="logs"></a>Logboeken

In werkmappen u logboeken opvragen uit de volgende bronnen:

* Azure Monitor-logboeken (Resources voor toepassingsinzichten en werkruimten voor logboekanalyse.)
* Resourcegerichte gegevens (activiteitslogboeken)

Auteurs van werkmapauteurs kunnen KQL-query's gebruiken die de onderliggende brongegevens transformeren om een resultaatset te selecteren die kan worden gevisualiseerd als tekst, grafieken of rasters.

![Schermafbeelding van de rapportinterface van werkmappen](./media/workbooks-overview/logs.png)

Auteurs van werkmaps kunnen eenvoudig een query uitvoeren in meerdere bronnen, waardoor een echt uniforme rijke rapportage-ervaring wordt gemaakt.

## <a name="metrics"></a>Metrische gegevens

Azure-bronnen zenden [statistieken](data-platform-metrics.md) uit die via werkmappen kunnen worden geopend. Statistieken kunnen worden geopend in werkmappen via een gespecialiseerd besturingselement waarmee u de doelbronnen, de gewenste statistieken en hun aggregatie opgeven. Deze gegevens kunnen vervolgens worden uitgezet in grafieken of rasters.

![Schermafbeelding van grafieken met werkmapstatistieken van het cpu-gebruik](./media/workbooks-overview/metrics-graph.png)

![Schermafbeelding van de interface voor werkmapstatistieken](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph 

Werkmappen ondersteunen query's voor resources en hun metagegevens met Behulp van Azure Resource Graph (ARG). Deze functionaliteit wordt voornamelijk gebruikt om aangepaste queryscopes voor rapporten te maken. De resourcescope wordt uitgedrukt via een KQL-subset die ARG ondersteunt – wat vaak voldoende is voor veelvoorkomende use cases.

Als u een querybesturingselement deze gegevensbron wilt laten gebruiken, gebruikt u de vervolgkeuzelijst Querytype om Azure Resource Graph te kiezen en selecteert u de gewenste abonnementen. Gebruik het besturingselement Query om de ARG KQL-subset toe te voegen die een interessante bronsubset selecteert.


![Schermafbeelding van De KQL-query azure resourcegraph](./media/workbooks-overview/azure-resource-graph.png)

## <a name="alerts-preview"></a>Waarschuwingen (voorbeeld)

Met werkmappen kunnen gebruikers de actieve waarschuwingen met betrekking tot hun bronnen visualiseren. Met deze functie u rapporten maken die meldingsgegevens (waarschuwing) en diagnostische informatie (statistieken, logboeken) samenbrengen in één rapport. Deze informatie kan ook worden samengevoegd om uitgebreide rapporten te maken die inzichten in deze gegevensbronnen combineren.

Als u een querybesturingselement deze gegevensbron wilt laten gebruiken, gebruikt u de vervolgkeuzelijst Querytype om Waarschuwingen te kiezen en selecteert u de abonnementen, resourcegroepen of resources die u wilt targeten. Gebruik de dropdowns van waarschuwingsfilters om een interessante subset van waarschuwingen voor uw analytische behoeften te selecteren.

![Schermafbeelding van waarschuwingenquery](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>Werkbelastingstatus (voorbeeld)

Azure Monitor heeft functionaliteit die proactief de beschikbaarheid en prestaties van Windows- of Linux-gastbesturingssystemen bewaakt. Azure Monitor modelleert belangrijke onderdelen en hun relaties, criteria voor het meten van de status van die componenten en welke onderdelen u waarschuwen wanneer een ongezonde toestand wordt gedetecteerd. Met werkmappen kunnen gebruikers deze informatie gebruiken om uitgebreide interactieve rapporten te maken.

Als u een querybesturingselement deze gegevensbron wilt laten gebruiken, gebruikt u de vervolgkeuzelijst **Querytype** om Workloadstatus te kiezen en selecteert u abonnements-, resourcegroep- of VM-resources die u wilt targeten. Gebruik de vervolgkeuzelijst voor het filter om een interessante subset van gezondheidsincidenten te selecteren voor uw analytische behoeften.

![Schermafbeelding van waarschuwingenquery](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Azure-bronstatus 

Werkmappen ondersteunen het verkrijgen van Azure-bronstatus en het combineren met andere gegevensbronnen om rijke, interactieve statusrapporten te maken

Als u een querybesturingselement deze gegevensbron wilt laten gebruiken, gebruikt u de vervolgkeuzelijst **Querytype** om Azure-status te kiezen en selecteert u de resources die u wilt targeten. Gebruik de vervolgkeuzelijst voor statusfilters om een interessante subset van resourceproblemen voor uw analytische behoeften te selecteren.

![Schermafbeelding van waarschuwingenquery](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Azure Data Explorer (voorbeeld)

Werkmappen hebben nu ondersteuning voor query's vanuit [Azure Data Explorer-clusters](https://docs.microsoft.com/azure/data-explorer/) met de krachtige [Kusto-querytaal.](https://docs.microsoft.com/azure/kusto/query/index)   

![Schermafbeelding van het queryvenster Kusto](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>Volgende stappen

* [Ga aan de slag met](workbooks-visualizations.md) het leren van meer over werkmappen met veel uitgebreide visualisatiesopties.
* [Beheer](workbooks-access-control.md) en deel de toegang tot uw werkmapbronnen.
