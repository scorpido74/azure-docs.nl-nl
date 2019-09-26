---
title: Limieten, quota en drempel waarden in azure scheduler
description: Meer informatie over limieten, quota's, standaard waarden en bandbreedte drempels voor Azure scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 293cd956f8270a4863fcc657f58c970096cec1e3
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300922"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Limieten, quota en beperkings drempels in azure scheduler

> [!IMPORTANT]
> [Azure Logic apps](../logic-apps/logic-apps-overview.md) vervangt Azure scheduler, die buiten gebruik wordt [gesteld](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Als u wilt blijven werken met de taken die u in scheduler hebt ingesteld, moet u zo snel mogelijk [naar Azure Logic apps worden gemigreerd](../scheduler/migrate-from-scheduler-to-logic-apps.md) .

## <a name="limits-quotas-and-thresholds"></a>Limieten, quota en drempel waarden

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>x-MS-Request-id-header

Elke aanvraag die wordt gedaan voor de Scheduler-service retourneert een antwoord header met de naam **x-MS-Request-id**. Deze header bevat een ondoorzichtige waarde waarmee de aanvraag uniek wordt geïdentificeerd. Dus als een aanvraag consistent is mislukt en u hebt bevestigd dat de aanvraag juist is ingedeeld, kunt u de fout melden aan micro soft door de waarde voor **x-MS-Request-id** op te geven en deze gegevens op te nemen: 

* De waarde voor **x-MS-Request-id**
* De geschatte tijd waarop de aanvraag is gedaan 
* De id's voor het Azure-abonnement, de taak verzameling en de taak 
* Het type bewerking dat de aanvraag heeft gedaan

## <a name="see-also"></a>Zie ook

* [Wat is Azure Scheduler?](scheduler-intro.md)
* [Azure Scheduler-concepten, -terminologie en -entiteitenhiërarchie](scheduler-concepts-terms.md)
