---
title: Systeem onderwerpen in Azure Event Grid
description: Hierin worden systeem onderwerpen beschreven in Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: spelluru
ms.openlocfilehash: 67746ebd8a16eb02b8f02d238b0e3c0125989189
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84308265"
---
# <a name="system-topics-in-azure-event-grid"></a>Systeem onderwerpen in Azure Event Grid
De Azure Event Grid-Service maakt systeem onderwerpen wanneer u een eerste gebeurtenis abonnement voor een Azure-gebeurtenis bron maakt. Event Grid maakt momenteel geen systeem onderwerpen voor de Onderwerpgegevens die zijn gemaakt vóór mrt, 15, 2020. Voor alle onderwerps bronnen die u op of na deze datum hebt gemaakt, maakt Event Grid automatisch systeem onderwerpen. In dit artikel worden **systeem onderwerpen** beschreven in azure Event grid.

> [!NOTE]
> Deze functie is momenteel niet ingeschakeld voor Azure Government Cloud. 

## <a name="overview"></a>Overzicht
Wanneer u een eerste gebeurtenis abonnement maakt voor een Azure-gebeurtenis bron, zoals Azure Storage account, wordt door het inrichtings proces voor het abonnement een extra resource van het type **micro soft. EventGrid/systemTopics**gemaakt. Wanneer het laatste gebeurtenis abonnement op de Azure-gebeurtenis bron wordt verwijderd, wordt het onderwerp System automatisch verwijderd.

Systeem onderwerp is niet van toepassing op aangepaste scenario's, dat wil zeggen, Event Grid onderwerpen en Event Grid domeinen. 

## <a name="name"></a>Naam 
Wanneer u eerder een abonnement hebt gemaakt voor een gebeurtenis die door Azure-bronnen wordt gegenereerd, maakt de Event Grid-Service automatisch een systeem onderwerp met een **wille keurig gegenereerde naam**. U kunt nu een naam opgeven voor het onderwerp System tijdens het maken van het onderwerp in de Azure Portal. U kunt deze systeem onderwerp Resource gebruiken om metrische gegevens en Diagnostische logboeken te detecteren.

## <a name="location"></a>Locatie
Voor Azure-gebeurtenis bronnen die zich in een specifieke regio/locatie bevinden, wordt het systeem onderwerp gemaakt op dezelfde locatie als de Azure-gebeurtenis bron. Als u bijvoorbeeld een gebeurtenis abonnement maakt voor een Azure Blob-opslag in VS-Oost, wordt het onderwerp System gemaakt in VS-Oost. Voor algemene Azure-gebeurtenis bronnen, zoals Azure-abonnementen, resource groepen of Azure Maps, maakt Event Grid het onderwerp System op de **globale** locatie. 

## <a name="resource-group"></a>Resourcegroep 
In het algemeen wordt het systeem onderwerp gemaakt in dezelfde resource groep als waarin de Azure-gebeurtenis bron zich bevindt. Voor gebeurtenis abonnementen die zijn gemaakt bij een Azure-abonnement, wordt het onderwerp System gemaakt onder de **standaard-EventGrid**van de resource groep. Als de resource groep niet bestaat, wordt deze door Azure Event Grid gemaakt voordat het onderwerp System wordt gemaakt. 

Wanneer u de resource groep probeert te verwijderen met het opslag account, wordt het onderwerp System weer geven in de lijst met betrokken resources.  

![Resourcegroep verwijderen](./media/system-topics/delete-resource-group.png)


## <a name="next-steps"></a>Volgende stappen
Raadpleeg het volgende artikel: [systeem onderwerpen maken, weer geven en beheren](create-view-manage-system-topics.md).