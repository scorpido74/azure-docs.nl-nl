---
title: Problemen met azure Data Explorer-clusterverbindingsfouten oplossen
description: In dit artikel worden stappen voor het oplossen van problemen beschreven voor het maken van verbinding met een cluster in Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: c71af799f614e9cd28221d79634666cbc3b2c987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60827033"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>Problemen oplossen: geen verbinding maken met een cluster in Azure Data Explorer

Als u geen verbinding kunt maken met een cluster in Azure Data Explorer, voert u de volgende stappen uit.

1. Zorg ervoor dat de verbindingsreeks juist is. Het moet in de `https://<ClusterName>.<Region>.kusto.windows.net`vorm: , zoals `https://docscluster.westus.kusto.windows.net`het volgende voorbeeld: .

1. Zorg ervoor dat u over de benodigde machtigingen beschikt. Als dat niet zo is, krijgt u de reactie*dat* u niet gemachtigd bent.

    Zie [Databasemachtigingen beheren](manage-database-permissions.md) voor meer informatie over machtigingen. Als dat nodig is, vraagt u aan de clusterbeheerder of de juiste rol voor u kan worden toegevoegd.

1. Controleer of het cluster niet is verwijderd. Bekijk hiervoor het activiteitenlogboek in uw abonnement.

1. Controleer het [dashboard met de status van de Azure-service](https://azure.microsoft.com/status/). Zoek naar de status van Azure Data Explorer in de regio waarin u verbinding probeert te maken met een cluster.

    Als de status niet **goed** is (groen vinkje), probeert u verbinding te maken met het cluster nadat de status is verbeterd.

1. Als u nog steeds hulp nodig hebt bij het oplossen van uw probleem, opent u een ondersteuningsverzoek in de [Azure-portal.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)