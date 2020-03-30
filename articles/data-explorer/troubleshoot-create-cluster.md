---
title: Problemen met het maken van Azure Data Explorer-cluster oplossen
description: In dit artikel worden stappen voor het oplossen van problemen beschreven voor het maken van een cluster in Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 863d9ecedf095e0ab284a0d7fd86363b69ae5658
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562408"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>Problemen oplossen: mislukte clustercreatie van Azure Data Explorer

Voer deze stappen uit in het onwaarschijnlijke geval dat het maken van een cluster mislukt in Azure Data Explorer.

1. Zorg ervoor dat u over de benodigde machtigingen beschikt. Als u een cluster wilt maken, moet u lid zijn van de rol *Inzender* of *Eigenaar* voor het Azure-abonnement. Werk indien nodig samen met uw abonnementsbeheerder, zodat ze u kunnen toevoegen aan de juiste rol.

1. Controleer of er geen validatiefouten zijn met betrekking tot de clusternaam die u hebt ingevoerd onder **Cluster maken** in de Azure-portal.

1. Controleer het [dashboard met de status van de Azure-service](https://azure.microsoft.com/status/). Zoek naar de status van Azure Data Explorer in het gebied waar u het cluster probeert te maken.

    Als de status niet **goed** is (groen vinkje), probeert u het cluster te maken nadat de status is verbeterd.

1. Als u nog steeds hulp nodig hebt bij het oplossen van uw probleem, opent u een ondersteuningsverzoek in de [Azure-portal.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)
