---
title: 'Quickstart: De oplossing configureren'
description: In deze quickstart leert u hoe u uw end-to-end IoT-oplossing configureert met behulp van Azure Security Center for IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: e6ab713715cacc799d2b980c2bce2a2a15b76887
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505451"
---
# <a name="quickstart-configure-your-iot-solution"></a>Quickstart: Uw IoT-oplossing configureren

In dit artikel wordt uitgelegd hoe u een eerste configuratie van uw IoT-beveiligingsoplossing uitvoert met behulp van Azure Security Center for IoT.

## <a name="azure-security-center-for-iot"></a>Azure Security Center voor IoT

Azure Security Center for IoT biedt uitgebreide end-to-end beveiliging voor Azure-gebaseerde IoT-oplossingen.

Met Azure Security Center for IoT kunt u uw hele IoT-oplossing bewaken in één dashboard, waar al uw IoT-apparaten, IoT-platforms en back-endresources in Azure worden getoond.

Zodra Azure Security Center for IoT is ingeschakeld op uw IoT-hub, identificeert het automatisch andere Azure-services die ook verbonden zijn met uw IoT-hub en gerelateerd zijn aan uw IoT-oplossing.

Naast automatische relatiedetectie kunt u ook zelf kiezen welke andere Azure-resourcegroepen u wilt taggen als onderdeel van uw IoT-oplossing.

Uw selecties stellen u in staat hele abonnementen, resourcegroepen of afzonderlijke resources toe te voegen.

Nadat u alle resourcerelaties hebt gedefinieerd, maakt Azure Security Center for IoT gebruik van Azure Security Center om u beveiligingsaanbevelingen en -waarschuwingen voor deze resources te geven.

## <a name="add-azure-resources-to-your-iot-solution"></a>Azure-resources toevoegen aan uw IoT-oplossing

Doe het volgende om nieuwe resources toe te voegen aan uw IoT-oplossing:

1. Open uw **IoT-hub** in de Azure-portal.
1. Selecteer en open **Instellingen** in de sectie **Beveiliging** in het linkermenu, en selecteer vervolgens **Bewaakte resources**.
1. Selecteer **Bewerken** en selecteer de bewaakte resources die bij uw IoT-oplossing horen.
1. Klik op **Add**.

Gefeliciteerd. U hebt een nieuwe resourcegroep toegevoegd aan uw IoT-oplossing.

Azure Security Center for IoT bewaakt nu uw zojuist toegevoegde resourcegroepen en toont relevante beveiligingsaanbevelingen en -waarschuwingen als onderdeel van uw IoT-oplossing.

## <a name="next-steps"></a>Volgende stappen

Ga door naar het volgende artikel om te lezen hoe u beveiligingsmodules maakt.

> [!div class="nextstepaction"]
> [Beveiligingsmodules maken](quickstart-create-security-twin.md)
