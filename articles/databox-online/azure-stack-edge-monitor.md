---
title: Uw Azure Stack edge-apparaat bewaken | Microsoft Docs
description: Hierin wordt beschreven hoe u de Azure Portal en de lokale web-UI gebruikt om uw Azure Stack rand te bewaken.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 2fc7435988a07968e65aaf265c33878c6e72e85b
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82569431"
---
# <a name="monitor-your-azure-stack-edge"></a>Uw Azure Stack rand bewaken

In dit artikel wordt beschreven hoe u uw Azure Stack Edge kunt bewaken. Als u uw apparaat wilt bewaken, kunt u Azure Portal of de lokale web-UI gebruiken. Gebruik de Azure Portal voor het weer geven van faxgebeurtenissen, het configureren en beheren van waarschuwingen en het weer geven van metrische gegevens. Gebruik de lokale webgebruikersinterface op het fysieke apparaat om de hardware-status van de verschillende onderdelen van het apparaat weer te geven.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * De gebeurtenissen van het apparaat en de bijbehorende waarschuwingen weer geven
> * De apparaatstatus van de onderdelen van apparaten weer geven
> * Metrische gegevens over capaciteit en trans acties voor uw apparaat weer geven
> * Waarschuwingen configureren en beheren

## <a name="view-device-events"></a>Faxgebeurtenissen weer geven

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Hardware-status weer geven

Voer de volgende stappen uit in de lokale web-UI om de hardware-status van de onderdelen van uw apparaat weer te geven.

1. Verbinding maken met de lokale webgebruikersinterface van uw apparaat.
2. Ga naar **onderhoud > hardware-status**. U kunt de status van de verschillende onderdelen van het apparaat weer geven. 

    ![Hardware-status weer geven](media/azure-stack-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Metrische gegevens bekijken

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>Waarschuwingen beheren

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Volgende stappen 

Meer informatie over [Bandbreedte beheren](azure-stack-edge-manage-bandwidth-schedules.md).