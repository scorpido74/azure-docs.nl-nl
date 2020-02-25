---
title: Problemen met het maken van Azure Data Explorer-clusters oplossen
description: In dit artikel worden de stappen beschreven voor het oplossen van problemen met het maken van een cluster in azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 863d9ecedf095e0ab284a0d7fd86363b69ae5658
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562408"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>Problemen oplossen: het maken van een cluster met Azure Data Explorer is mislukt

In het onwaarschijnlijke geval dat het maken van een cluster mislukt in azure Data Explorer, voert u de volgende stappen uit.

1. Zorg ervoor dat u over de benodigde machtigingen beschikt. Als u een cluster wilt maken, moet u lid zijn van de rol *Inzender* of *eigenaar* voor het Azure-abonnement. Als dat nodig is, kunt u samen werken met uw abonnements beheerder zodat ze u kunnen toevoegen aan de juiste rol.

1. Zorg ervoor dat er geen validatie fouten zijn gerelateerd aan de cluster naam die u hebt opgegeven onder **cluster maken** in de Azure Portal.

1. Controleer het [dashboard met de status van de Azure-service](https://azure.microsoft.com/status/). Zoek naar de status van de Azure-Data Explorer in de regio waarin u het cluster wilt maken.

    Als de status niet **goed** is (groen vinkje), probeert u het cluster te maken nadat de status is verbeterd.

1. Als u nog hulp nodig hebt bij het oplossen van uw probleem, opent u een ondersteunings aanvraag in de [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
