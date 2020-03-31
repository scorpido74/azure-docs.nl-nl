---
title: Statistieken weergeven met Azure Monitor
description: In dit artikel ziet u hoe u statistieken controleren met de Azure-portaldiagrammen en Azure CLI.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: c230e1e950bb924631032940642a6202acf4ade8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382933"
---
# <a name="monitor-media-services-metrics"></a>Metrische gegevens voor Media Services controleren

[Azure Monitor](../../azure-monitor/overview.md) stelt u in staat om statistieken en diagnostische logboeken te controleren die u helpen te begrijpen hoe uw toepassingen presteren. Zie Statistieken van [Monitor Media Services en diagnostische logboeken](media-services-metrics-diagnostic-logs.md)voor een gedetailleerde beschrijving van deze functie en om te zien waarom u azure media services-statistieken en diagnostische logboeken wilt gebruiken.

Azure Monitor biedt verschillende manieren om te communiceren met statistieken, waaronder het in kaart brengen ervan in de portal, toegang tot deze gegevens via de REST API of het opvragen ervan met Azure CLI. In dit artikel ziet u hoe u statistieken controleren met de Azure-portaldiagrammen en Azure CLI.

## <a name="prerequisites"></a>Vereisten

- [Een Media Services-account maken](create-account-cli-how-to.md)
- [Monitor Media Services-statistieken en diagnostische logboeken bekijken](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Statistieken weergeven in Azure-portal

1. Meld u aan bij Azure Portal op https://portal.azure.com.
1. Navigeer naar uw Azure Media Services-account en selecteer **Statistieken**.
1. Klik op het vak **RESOURCE** en selecteer de resource waarvoor u statistieken wilt controleren.

    Het **venster Een resource selecteren** wordt aan de rechterkant weergegeven met de lijst met bronnen die voor u beschikbaar zijn. In dit geval zie je:

    * &lt;Naam Media Services-account&gt;
    * &lt;Naam&gt;/&lt;van Media Services-accountstreaming&gt;
    * &lt;naam van het opslagaccount&gt;

    Selecteer de resource en druk op **Toepassen**. Zie [Statistieken van MediaServices controleren](media-services-metrics-diagnostic-logs.md)voor meer informatie over ondersteunde bronnen en statistieken.

    ![Metrische gegevens](media/media-services-metrics/metrics02.png)

    > [!NOTE]
    > Als u wilt schakelen tussen resources waarvoor u statistieken wilt controleren, klikt u nogmaals op het vak **RESOURCE** en herhaalt u deze stap.
1. (Optioneel) geef uw grafiek een naam (bewerk de naam door op het potlood bovenaan te drukken).
1. Voeg statistieken toe die u wilt weergeven.

    ![Metrische gegevens](media/media-services-metrics/metrics03.png)
1. U uw grafiek vastmaken aan uw dashboard.

## <a name="view-metrics-with-azure-cli"></a>Statistieken weergeven met Azure CLI

Als u 'Egress'-statistieken met Azure CLI `az monitor metrics` wilt krijgen, voert u de volgende opdracht uit:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Als u andere statistieken wilt krijgen, vervangt u 'Uitgang' voor de metrische naam waarin u geïnteresseerd bent.

## <a name="see-also"></a>Zie ook

* [Azure-monitorstatistieken](../../azure-monitor/platform/data-platform.md)
* [Metrische waarschuwingen maken, weergeven en beheren met Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Volgende stappen

[Diagnostische logboeken](media-services-diagnostic-logs-howto.md)
