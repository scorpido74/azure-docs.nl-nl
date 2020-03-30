---
title: Systeemonderwerpen in Azure Event Grid
description: Beschrijft systeemonderwerpen in Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 35025bf1592f1293b9326d643f76322b4af590c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501192"
---
# <a name="system-topics-in-azure-event-grid"></a>Systeemonderwerpen in Azure Event Grid
De Azure Event Grid-service maakt systeemonderwerpen wanneer u een eerste gebeurtenisabonnement maakt voor een Azure-gebeurtenisbron. Momenteel maakt Event Grid geen systeemonderwerpen voor onderwerpbronnen die zijn gemaakt vóór 15 maart 2020. Voor alle onderwerpbronnen die u op of na deze datum hebt gemaakt, maakt Gebeurtenisraster automatisch systeemonderwerpen. In dit artikel worden **systeemonderwerpen** beschreven in Azure Event Grid.

> [!NOTE]
> Deze functie is momenteel niet ingeschakeld voor azure government cloud. 

## <a name="overview"></a>Overzicht
Wanneer u een eerste gebeurtenisabonnement maakt voor een Azure-gebeurtenisbron, zoals Azure Storage-account, wordt bij het inrichtingsproces voor het abonnement een extra bron gemaakt van het type **Microsoft.EventGrid/systemTopics**. Wanneer het laatste gebeurtenisabonnement op de Azure-gebeurtenisbron wordt verwijderd, wordt het systeemonderwerp automatisch verwijderd.

Het systeemonderwerp is niet van toepassing op aangepaste onderwerpscenario's, dat wil zeggen gebeurtenisrasteronderwerpen en gebeurtenisrasterdomeinen. 

## <a name="location"></a>Locatie
Voor Azure-gebeurtenisbronnen die zich in een specifieke regio/locatie bevinden, wordt het systeemonderwerp gemaakt op dezelfde locatie als de Azure-gebeurtenisbron. Als u bijvoorbeeld een gebeurtenisabonnement maakt voor een Azure blob-opslag in Oost-VS, wordt het systeemonderwerp gemaakt in Oost-VS. Voor algemene Azure-gebeurtenisbronnen, zoals Azure-abonnementen, resourcegroepen of Azure Maps, maakt Event Grid het systeemonderwerp op **algemene** locatie. 

## <a name="resource-group"></a>Resourcegroep 
In het algemeen wordt het systeemonderwerp gemaakt in dezelfde resourcegroep waarin de Azure-gebeurtenisbron zich bevindt. Voor gebeurtenisabonnementen die zijn gemaakt op Azure-abonnementsbereik, wordt het systeemonderwerp gemaakt onder de resourcegroep **Default-EventGrid**. Als de resourcegroep niet bestaat, maakt Azure Event Grid deze voordat u het systeemonderwerp maakt. 

Wanneer u de brongroep met het opslagaccount probeert te verwijderen, ziet u het systeemonderwerp in de lijst met getroffen bronnen.  

![Resourcegroep verwijderen](./media/system-topics/delete-resource-group.png)

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen: 

- [Aangepaste onderwerpen](event-sources.md#custom-topics)
- [Domeinen](event-domains.md)