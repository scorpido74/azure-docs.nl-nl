---
title: Eenvoudige logboekenervaring in Azure Monitor (Preview) | Microsoft Documenten
description: Met de eenvoudige logboeken-ervaring u basisquery's maken in Azure Monitor zonder direct contact te hebben met KQL.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 59bcb42edaf7d46498a3514b4f1c919c6e8cc0c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660254"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Eenvoudige logboekenervaring in Azure Monitor (Voorbeeld)
Azure Monitor biedt een [uitgebreide ervaring](get-started-portal.md) voor het maken van [logboekquery's](log-query-overview.md) met behulp van de KQL-taal. U hebt echter niet de volledige kracht van KQL nodig en geeft de voorkeur aan een vereenvoudigde ervaring voor basisqueryvereisten. Met de eenvoudige logboeken-ervaring u basisquery's maken zonder direct contact te hebben met KQL. U eenvoudige logboeken ook gebruiken als leermiddel voor KQL, omdat u meer geavanceerde query's nodig hebt.

> [!NOTE]
> Simple Logs is momenteel geïmplementeerd als een test voor alleen Cosmos DB en Key Vaults. Deel uw ervaring met Microsoft via [User Voice](https://feedback.azure.com/forums/913690-azure-monitor) om ons te helpen bepalen of we deze functie zullen uitbreiden en vrijgeven.


## <a name="scope"></a>Bereik
Met de ervaring Eenvoudige logboeken worden gegevens opgehaald uit de tabel *AzureDiagnostics,* *AzureMetrics*en *AzureActivity* voor de geselecteerde bron. 

## <a name="using-simple-logs"></a>Eenvoudige logboeken gebruiken
Navigeer naar een Cosmos DB of Key Vault in uw Azure-abonnement met [diagnostische instellingen die zijn geconfigureerd om logboeken te verzamelen in een werkruimte voor Log Analytics.](../platform/resource-logs-collect-storage.md) Klik **op Logboeken** in het menu **Controle** om de eenvoudige logboekenervaring te openen.

![Menu](media/simple-logs/menu.png)

Selecteer een **veld** en een **operator** en geef een **waarde** voor vergelijking op. Klik **+** op en geef **en/of op** om aanvullende criteria toe te voegen.

![Criteria](media/simple-logs/criteria.png)

Klik **op Uitvoeren** om de queryresultaten weer te geven.

## <a name="view-and-edit-kql"></a>KQL weergeven en bewerken
Selecteer **Query-editor** om de KQL te openen die wordt gegenereerd door de query Eenvoudige logboeken in de volledige loganalyse-ervaring. 

![Query-editor](media/simple-logs/query-editor.png)

U de KQL rechtstreeks bewerken en andere functies in Log Analytics gebruiken, zoals filters om uw resultaten verder te verfijnen.

![KQL bewerken](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>Volgende stappen

- Voer een zelfstudie in over [het gebruik van Log Analytics in de Azure-portal.](get-started-portal.md)
- Voltooi een zelfstudie over [het schrijven van logquery's.](get-started-portal.md)
