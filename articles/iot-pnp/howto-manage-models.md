---
title: Preview-modellen van IoT Plug en Play beheren in de opslag plaats | Microsoft Docs '
description: De mogelijkheden voor het beheren van apparaten in de opslag plaats met behulp van de Azure Certified voor IoT-Portal, de Azure CLI en Visual Studio code.
author: Philmea
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: how-to
ms.date: 12/26/2019
ms.author: philmea
ms.openlocfilehash: 78406175090521af70381f61f4e33dfb6c35ac8c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80159146"
---
# <a name="manage-models-in-the-repository"></a>Modellen in de opslag plaats beheren

Met de opslag plaats voor IoT Plug en Play preview-modellen worden de mogelijkheden en interfaces van het apparaat ondersteund. Met de opslag plaats kunnen de modellen en interfaces worden gedetecteerd en verbruikt door ontwikkel aars van oplossingen.

Er zijn drie hulpprogram ma's die u kunt gebruiken voor het beheren van de opslag plaats:

- De Azure Certified voor IoT-Portal
- De Azure CLI
- Visual Studio Code

## <a name="model-repositories"></a>Model opslagplaatsen

Er zijn twee soorten model opslagplaatsen voor het opslaan van Capability-modellen en-interfaces:

- Er is één _open bare opslag plaats_ voor het opslaan van de hulp modellen en interfaces voor apparaten in de [Azure Certified voor IOT-apparaat Catalog](https://aka.ms/iotdevcat). Deze opslag plaats bevat ook [algemene interfaces](./concepts-common-interfaces.md) en [DCMs en interfaces die zijn gepubliceerd door micro soft-partners](./howto-onboard-portal.md). Raadpleeg de zelf studie [uw IOT-Plug en Play apparaat certificeren](./tutorial-certification-test.md)voor meer informatie over het certificeren van een apparaat en het toevoegen van het functionaliteits model van het apparaat aan de open bare opslag plaats.
- Er zijn meerdere _bedrijfs opslagplaatsen_. Er wordt automatisch een bedrijfs opslagplaats gemaakt voor uw organisatie wanneer u [de Azure Certified voor IOT-Portal onboardeert](./howto-onboard-portal.md). U kunt uw bedrijfs opslagplaats gebruiken voor het opslaan van de mogelijkheden en interfaces van uw apparaten tijdens het ontwikkelen en testen.

## <a name="azure-certified-for-iot-portal"></a>Azure Certified voor IoT-Portal

In de [Azure Certified voor IOT-Portal](https://preview.catalog.azureiotsolutions.com)kunt u de volgende taken uitvoeren:

- [Voltooi het certificerings proces voor uw IOT-apparaat](./tutorial-certification-test.md).
- Zoek IoT Plug en Play Device Capability-modellen. U kunt deze modellen gebruiken om [snel IOT-apparaten te bouwen en deze te integreren met oplossingen](./quickstart-connect-pnp-device-solution-node.md).

## <a name="azure-cli"></a>Azure CLI

De Azure CLI bevat opdrachten voor het beheren van de mogelijkheden en interfaces van de apparaten in de IoT Plug en Play Public en bedrijfs model-opslag plaatsen. Zie voor meer informatie de [Azure IOT-extensie voor Azure cli-hand leiding installeren en gebruiken](./howto-install-pnp-cli.md) .

## <a name="visual-studio-code"></a>Visual Studio Code

Als u de weer gave **model opslagplaats** wilt openen in Visual Studio code.

1. Open Visual Studio code, gebruik **CTRL + SHIFT + P**, typ en selecteer **IOT Plug en Play: open model-opslag**.

1. U kunt ervoor kiezen om de **open bare model opslagplaats te openen** of de **opslag plaats voor organisatie modellen te openen**. Voor bedrijfs model opslagplaats moet u uw model opslagplaats connection string invoeren. U vindt deze connection string in het [Azure Certified for IOT-Portal](https://preview.catalog.azureiotsolutions.com) op het tabblad **verbindings reeksen** voor de **opslag plaats**van uw bedrijf.

1. De weer gave **model opslagplaats** wordt geopend op een nieuw tabblad.

    Gebruik deze weer gave om hulp modellen en interfaces voor apparaten toe te voegen, te downloaden en te verwijderen. U kunt een filter gebruiken om specifieke items in de lijst te vinden.

1. Als u wilt scha kelen tussen uw bedrijfs model opslagplaats en de open bare model opslagplaats, gebruikt u **CTRL + SHIFT + P**, typt en selecteert u **IOT Plug en Play: model opslagplaats voor afmelden**. Gebruik vervolgens de **IOT-Plug en Play: model opslagplaats** opdracht opnieuw.

> [!NOTE]
> In VS code is de opslag plaats voor open bare modellen alleen-lezen. Micro soft-partners kunnen de open bare opslag plaats bijwerken in de [Azure Certified voor IOT-Portal](https://preview.catalog.azureiotsolutions.com).

## <a name="next-steps"></a>Volgende stappen

De voorgestelde volgende stap is informatie over het [verzenden van een IoT Plug en Play-apparaat voor certificering](tutorial-certification-test.md).
