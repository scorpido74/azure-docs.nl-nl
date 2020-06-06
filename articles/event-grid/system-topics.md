---
title: Systeem onderwerpen in Azure Event Grid
description: Hierin worden systeem onderwerpen beschreven in Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: spelluru
ms.openlocfilehash: c34ada2c7437d8415b52c68fb66103ec3aa81d95
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84456926"
---
# <a name="system-topics-in-azure-event-grid"></a>Systeem onderwerpen in Azure Event Grid
Systeem onderwerpen zijn de onderwerpen die zijn gemaakt voor Azure-Services, zoals Azure Storage en Azure Event Hubs. U kunt systeem onderwerpen maken met behulp van de sjabloon Azure Portal, Power shell, CLI of Azure Resource Manager.  

> [!NOTE]
> Deze functie is momenteel niet ingeschakeld voor Azure Government Cloud. 

## <a name="create-system-topics"></a>Systeem onderwerpen maken
U kunt op twee manieren een systeem onderwerp maken: 

- Maak een systeem onderwerp voor een Azure-resource en maak vervolgens een gebeurtenis abonnement voor dat systeem onderwerp.
- Maak een gebeurtenis abonnement op een Azure-resource die intern een systeem onderwerp voor u maakt.

Wanneer u de eerste benadering gebruikt, wordt het onderwerp System niet automatisch verwijderd wanneer het laatste gebeurtenis abonnement voor dat systeem onderwerp wordt verwijderd. Wanneer u de tweede benadering gebruikt, wordt het onderwerp System automatisch verwijderd wanneer het laatste gebeurtenis abonnement wordt verwijderd. 

Raadpleeg de volgende artikelen voor gedetailleerde instructies voor het maken van systeem onderwerpen met behulp van Azure Portal, Power shell of CLI:

- [Systeem onderwerpen maken, weer geven en beheren met behulp van Azure Portal](create-view-manage-system-topics.md).
- [Event Grid systeem onderwerpen maken, weer geven en beheren met behulp van Azure CLI](create-view-manage-system-topics-cli.md)
- [Event Grid systeem onderwerpen maken met behulp van Azure Resource Manager sjablonen](create-view-manage-system-topics-arm.md)

## <a name="system-topic-name"></a>Naam van het systeem onderwerp
Event Grid geen systeem onderwerpen hebt gemaakt voor Azure-bronnen (Azure Storage, Azure Event Hubs, enzovoort) die zijn gemaakt vóór mrt, 15, 2020. Als u een abonnement hebt gemaakt voor een gebeurtenis die wordt veroorzaakt door Azure-bronnen tussen 3/15/2020 en 6/2/2020, wordt door de Event Grid-Service automatisch een systeem onderwerp met een **wille keurig gegenereerde naam**gemaakt. Na 6/2/2020 kunt u een naam opgeven voor het onderwerp System tijdens het maken van een gebeurtenis abonnement voor een Azure-bron. 

## <a name="location-and-resource-group"></a>Locatie en resource groep
Voor Azure-gebeurtenis bronnen die zich in een specifieke regio/locatie bevinden, wordt het systeem onderwerp gemaakt op dezelfde locatie als de Azure-gebeurtenis bron. Als u bijvoorbeeld een gebeurtenis abonnement maakt voor een Azure Blob-opslag in VS-Oost, wordt het onderwerp System gemaakt in VS-Oost. Voor algemene Azure-gebeurtenis bronnen, zoals Azure-abonnementen, resource groepen of Azure Maps, maakt Event Grid het onderwerp System op de **globale** locatie. 

In het algemeen wordt het systeem onderwerp gemaakt in dezelfde resource groep als waarin de Azure-gebeurtenis bron zich bevindt. Voor gebeurtenis abonnementen die zijn gemaakt bij een Azure-abonnement, wordt het onderwerp System gemaakt onder de **standaard-EventGrid**van de resource groep. Als de resource groep niet bestaat, wordt deze door Azure Event Grid gemaakt voordat het onderwerp System wordt gemaakt. 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen: 

- [Systeem onderwerpen maken, weer geven en beheren met behulp van Azure Portal](create-view-manage-system-topics.md).
- [Event Grid systeem onderwerpen maken, weer geven en beheren met behulp van Azure CLI](create-view-manage-system-topics-cli.md)
- [Event Grid systeem onderwerpen maken met behulp van Azure Resource Manager sjablonen](create-view-manage-system-topics-arm.md)
