---
title: bestand opnemen
description: bestand opnemen
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 03/30/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: be8aae6308e712449402b002576974743bc125ef
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986686"
---
Deze voorbeelden laten zien hoe u Azure Monitor kunt gebruiken om waarschuwingen te maken voor abonnementen waarvoor het beheer van gedelegeerde Azure-resources is vrijgegeven.

| **Sjabloon** | **Beschrijving** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) | Voert een query uit op activiteit van de afgelopen dag in een beheerde tenant en [rapporteert over toegevoegde of verwijderde delegaties](../articles/lighthouse/how-to/monitor-delegation-changes.md) (of pogingen die niet succesvol waren).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/alert-using-actiongroup) | Met deze sjabloon maakt u een Azure-waarschuwing en maakt u verbinding met een bestaande actiegroep.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/multiple-loganalytics-alerts) | Hiermee maakt u meerdere waarschuwingen voor een logboek op basis van Kusto-query's.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegation-alert-for-customer) | Hiermee wordt een waarschuwing in een tenant geïmplementeerd wanneer een gebruiker een abonnement naar een beheertenant delegeert.|
