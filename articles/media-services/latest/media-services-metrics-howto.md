---
title: Metrische gegevens weer geven met Azure Monitor
description: In dit artikel wordt uitgelegd hoe u metrische gegevens kunt controleren met behulp van de Azure Portal grafieken en Azure CLI.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80382933"
---
# <a name="monitor-media-services-metrics"></a>Metrische gegevens voor Media Services controleren

Met [Azure monitor](../../azure-monitor/overview.md) kunt u metrische gegevens en Diagnostische logboeken bewaken die u helpen begrijpen hoe uw toepassingen worden uitgevoerd. Zie [Media Services metrische gegevens en Diagnostische logboeken controleren](media-services-metrics-diagnostic-logs.md)voor een gedetailleerde beschrijving van deze functie en om te zien waarom u Azure Media Services metrische gegevens en Diagnostische logboeken wilt gebruiken.

Azure Monitor biedt verschillende manieren om te communiceren met metrische gegevens, zoals het maken van grafieken in de portal, het openen ervan via de REST API of het opvragen van query's met behulp van Azure CLI. In dit artikel wordt uitgelegd hoe u metrische gegevens kunt controleren met behulp van de Azure Portal grafieken en Azure CLI.

## <a name="prerequisites"></a>Vereisten

- [Een Media Services-account maken](create-account-cli-how-to.md)
- Controleren [Media Services metrische gegevens en Diagnostische logboeken](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Metrische gegevens weer geven in Azure Portal

1. Meld u aan bij Azure Portal op https://portal.azure.com.
1. Navigeer naar uw Azure Media Services-account en selecteer **metrische gegevens**.
1. Klik op het vak **resource** en selecteer de resource waarvoor u de metrische gegevens wilt bewaken.

    Het venster **een resource selecteren** wordt weer gegeven aan de rechter kant met de lijst met beschik bare resources. In dit geval ziet u:

    * &lt;Media Services-account naam&gt;
    * &lt;Naam van Media Services&gt;/&lt;account naam van streaming-eind punt&gt;
    * &lt;naam van opslag account&gt;

    Selecteer de resource en druk op **Toep assen**. Zie [Media Services metrische gegevens controleren](media-services-metrics-diagnostic-logs.md)voor meer informatie over ondersteunde bronnen en metrische gegevens.

    ![Metrische gegevens](media/media-services-metrics/metrics02.png)

    > [!NOTE]
    > Als u wilt scha kelen tussen resources waarvoor u de metrische gegevens wilt controleren, klikt u nogmaals op het vak **resource** en herhaalt u deze stap.
1. (Optioneel) Geef uw grafiek een naam (Bewerk de naam door op het potlood bovenaan te drukken).
1. Voeg metrische gegevens toe die u wilt weer geven.

    ![Metrische gegevens](media/media-services-metrics/metrics03.png)
1. U kunt uw grafiek vastmaken aan uw dash board.

## <a name="view-metrics-with-azure-cli"></a>Metrische gegevens weer geven met Azure CLI

Voer de volgende `az monitor metrics` opdracht uit om de metrische gegevens van ' uitgang ' met Azure CLI te verkrijgen:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Als u andere metrische gegevens wilt ophalen, vervangt u ' uitgang ' door de naam van de metrische gegevens die u wilt gebruiken.

## <a name="see-also"></a>Zie ook

* [Azure Monitor metrische gegevens](../../azure-monitor/platform/data-platform.md)
* [Metrische waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](../../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Volgende stappen

[Diagnostische logboeken](media-services-diagnostic-logs-howto.md)
