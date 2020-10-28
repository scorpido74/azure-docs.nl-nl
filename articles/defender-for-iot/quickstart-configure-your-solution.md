---
title: 'Quickstart: De oplossing configureren'
description: In deze quickstart leert u hoe u uw end-to-end IoT-oplossing configureert met behulp van Azure Defender for IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: f949d52d4ba461da94153cbbd17ca5fb3eb214d7
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92092083"
---
# <a name="quickstart-configure-your-azure-defender-for-iot-solution"></a>Quickstart: Configureer uw Azure Defender for IoT

In dit artikel wordt uitgelegd hoe u een eerste configuratie van uw IoT-beveiligingsoplossing uitvoert met behulp van Defender for IoT.

## <a name="what-is-defender-for-iot"></a>Wat is Defender for IoT?

Defender for IoT biedt uitgebreide end-to-end beveiliging voor Azure-gebaseerde IoT-oplossingen.

Met Defender for IoT kunt u uw hele IoT-oplossing bewaken in één dashboard, waar al uw IoT-apparaten, IoT-platforms en back-endresources in Azure worden getoond.

Zodra Defender for IoT is ingeschakeld op uw IoT-hub, identificeert het automatisch andere Azure-services die ook verbonden zijn met uw IoT-hub en gerelateerd zijn aan uw IoT-oplossing.

Naast automatische relatiedetectie kunt u ook zelf kiezen welke andere Azure-resourcegroepen u wilt taggen als onderdeel van uw IoT-oplossing.

Uw selecties stellen u in staat hele abonnementen, resourcegroepen of afzonderlijke resources toe te voegen.

Nadat u alle resourcerelaties hebt gedefinieerd, maakt Defender for IoT gebruik van Defender om u beveiligingsaanbevelingen en -waarschuwingen voor deze resources te geven.

## <a name="add-azure-resources-to-your-iot-solution"></a>Azure-resources toevoegen aan uw IoT-oplossing

Doe het volgende om nieuwe resources toe te voegen aan uw IoT-oplossing:

1. Open uw **IoT-hub** in de Azure-portal.
1. Onder **Beveiliging** selecteert u **Overzicht** gevolgd door **Instellingen** > Selecteer vervolgens **Bewaakte resources** .
1. Selecteer **Bewerken** en selecteer de bewaakte resources die bij uw IoT-oplossing horen.
1. Klik op **Add** .

Gefeliciteerd. U hebt een nieuwe resourcegroep toegevoegd aan uw IoT-oplossing.

Defender for IoT bewaakt nu uw zojuist toegevoegde resourcegroepen en toont relevante beveiligingsaanbevelingen en -waarschuwingen als onderdeel van uw IoT-oplossing.

## <a name="next-steps"></a>Volgende stappen

Ga door naar het volgende artikel om te lezen hoe u beveiligingsmodules maakt.

> [!div class="nextstepaction"]
> [Beveiligingsmodules maken](quickstart-create-security-twin.md)
