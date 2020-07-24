---
title: Eenvoudige Logboeken in Azure Monitor (preview) | Microsoft Docs
description: Met de eenvoudige Logboeken kunt u eenvoudige query's maken in Azure Monitor zonder rechtstreeks met KQL te communiceren.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 7bdb0ae813600f53df44ee1a61a4cbbb56bb0bfe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87067507"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Eenvoudige Logboeken in Azure Monitor (preview-versie)
Azure Monitor biedt een [rijke ervaring](get-started-portal.md) voor het maken van [logboek query's](log-query-overview.md) met BEhulp van de KQL-taal. U hebt mogelijk niet de volledige kracht van KQL nodig, maar u hebt de voor keur aan een vereenvoudigde ervaring voor elementaire query vereisten. Met de eenvoudige Logboeken kunt u eenvoudige query's maken zonder dat ze rechtstreeks met KQL werken. U kunt ook eenvoudige Logboeken gebruiken als leer hulpprogramma voor KQL, zoals u meer geavanceerde query's nodig hebt.

> [!NOTE]
> Eenvoudige logboeken worden momenteel alleen geïmplementeerd als test voor Cosmos DB en sleutel kluizen. Deel uw ervaring met micro soft via de [gebruikers stem](https://feedback.azure.com/forums/913690-azure-monitor) zodat wij kunnen bepalen of we deze functie zullen uitbreiden en vrijgeven.


## <a name="scope"></a>Bereik
Met de eenvoudige Logboeken kunt u gegevens ophalen uit de *AzureDiagnostics*-, *AzureMetrics*-en *AzureActivity* -tabel voor de geselecteerde resource. 

## <a name="using-simple-logs"></a>Eenvoudige Logboeken gebruiken
Navigeer naar Cosmos DB of Key Vault in uw Azure-abonnement met [Diagnostische instellingen die zijn geconfigureerd voor het verzamelen van Logboeken in een log Analytics-werk ruimte](../platform/resource-logs.md#send-to-azure-storage). Klik in het menu **controle** op **Logboeken** om de eenvoudige logboeken te openen.

![Menu](media/simple-logs/menu.png)

Selecteer een **veld** en een **operator** en geef een **waarde** op voor vergelijking. Klik **+** en specificeer **en/of** Voeg aanvullende criteria toe.

![Criteria](media/simple-logs/criteria.png)

Klik op **uitvoeren** om de query resultaten weer te geven.

## <a name="view-and-edit-kql"></a>KQL weer geven en bewerken
Selecteer **query-editor** om de KQL te openen die is gegenereerd door de eenvoudige logboeken-query in de volledige log Analytics-ervaring. 

![Query-editor](media/simple-logs/query-editor.png)

U kunt de KQL rechtstreeks bewerken en andere functies gebruiken in Log Analytics zoals filters om uw resultaten verder te verfijnen.

![KQL bewerken](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>Volgende stappen

- Maak een zelf studie over [het gebruik van log Analytics in de Azure Portal](get-started-portal.md).
- Een zelf studie over het [schrijven van logboek query's](get-started-portal.md)volt ooien.
