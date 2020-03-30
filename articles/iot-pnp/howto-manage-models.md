---
title: IoT-plug- en play-voorbeeldmodellen beheren in de repository| Microsoft Docs'
description: Apparaatcapaciteitsmodellen beheren in de repository met behulp van de Azure Certified for IoT-portal, de Azure CLI en Visual Studio-code.
author: Philmea
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: how-to
ms.date: 12/26/2019
ms.author: philmea
ms.openlocfilehash: 78406175090521af70381f61f4e33dfb6c35ac8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159146"
---
# <a name="manage-models-in-the-repository"></a>Modellen beheren in de repository

Het IoT Plug and Play Preview-model slaat apparaatcapaciteitsmodellen en -interfaces op. De repository maakt de modellen en interfaces vindbaar en bruikbaar voor ontwikkelaars van oplossingen.

Er zijn drie tools die u gebruiken om de repository te beheren:

- De Azure Certified for IoT-portal
- De Azure CLI
- Visual Studio Code

## <a name="model-repositories"></a>Modelrepositories

Er zijn twee typen modelrepository voor het opslaan van apparaatcapaciteitsmodellen en -interfaces:

- Er is één _openbare opslagplaats_ die de apparaatcapaciteitsmodellen en -interfaces voor apparaten opslaat in de Azure Certified [for IoT-apparaatcatalogus.](https://aka.ms/iotdevcat) Deze repository slaat ook [gemeenschappelijke interfaces](./concepts-common-interfaces.md) en [DCM's en interfaces op die zijn gepubliceerd door Microsoft Partners.](./howto-onboard-portal.md) Zie de zelfstudie [Uw IoT Plug and Play-apparaat certificeren](./tutorial-certification-test.md)voor meer informatie over het certificeren van een apparaat en het model voor apparaatcapaciteit toevoegen aan de openbare opslagplaats.
- Er zijn meerdere _bedrijfsrepositories._ Er wordt automatisch een bedrijfsopslagplaats voor uw organisatie gemaakt wanneer u [aan boord gaat van de Azure Certified for IoT-portal.](./howto-onboard-portal.md) U uw bedrijfsrepository gebruiken om uw apparaatcapaciteitsmodellen en -interfaces op te slaan tijdens de ontwikkeling en test.

## <a name="azure-certified-for-iot-portal"></a>Azure-gecertificeerd voor IoT-portal

In de [Azure Certified for IoT-portal](https://preview.catalog.azureiotsolutions.com)u de volgende taken uitvoeren:

- [Voltooi het certificeringsproces voor uw IoT-apparaat.](./tutorial-certification-test.md)
- Zoek iot plug en play-apparaatmogelijkheden. U deze modellen gebruiken om [snel IoT-ready apparaten](./quickstart-connect-pnp-device-solution-node.md)te bouwen en ze te integreren met oplossingen.

## <a name="azure-cli"></a>Azure-CLI

De Azure CLI biedt opdrachten voor het beheren van apparaatcapaciteitsmodellen en -interfaces in de openbare en bedrijfsmodelrepositories van IoT Plug and Play. Zie de azure [IoT-extensie voor Azure CLI installeren en gebruiken](./howto-install-pnp-cli.md) voor meer informatie.

## <a name="visual-studio-code"></a>Visual Studio Code

De weergave **Modelrepository** openen in Visual Studio Code.

1. Open Visual Studio Code, gebruik **Ctrl+Shift+P,** typ en selecteer **IoT Plug and Play: Open Model Repository**.

1. U ervoor kiezen om **Public Model Repository** te openen of **Organisatiemodel Repository te openen.** Voor de opslagplaats van bedrijfsmodellen moet u de tekenreeks van uw modelrepository-verbinding invoeren. U vindt deze verbindingstekenreeks in de [Azure Certified for IoT-portal](https://preview.catalog.azureiotsolutions.com) op het tabblad **Verbindingstekenreeksen** voor uw **bedrijfsopslagplaats.**

1. Een nieuw tabblad opent de weergave **Modelopslagplaats.**

    Gebruik deze weergave om apparaatcapaciteitsmodellen en -interfaces toe te voegen, te downloaden en te verwijderen. U een filter gebruiken om specifieke items in de lijst te vinden.

1. Als u wilt schakelen tussen uw bedrijfsmodelrepository en de openbare modelopslagplaats, gebruikt u **Ctrl+Shift+P,** typt en selecteert u **IoT Plug and Play: Sign out Model Repository**. Gebruik vervolgens de opdracht **IoT Plug and Play: Open Model Repository** opnieuw.

> [!NOTE]
> In VS Code is de openbare modelrepository alleen-lezen. Microsoft Partners kunnen de openbare opslagplaats bijwerken in de [Azure Certified for IoT-portal.](https://preview.catalog.azureiotsolutions.com)

## <a name="next-steps"></a>Volgende stappen

De voorgestelde volgende stap is om te leren hoe [u een IoT Plug and Play-apparaat indient voor certificering.](tutorial-certification-test.md)
