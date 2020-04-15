---
title: 'Snelstart: maak een beveiligingsmodule'
description: In deze snelle start leert u hoe u een Azure Security Center for IoT-moduletwee maakt voor gebruik met Azure Security Center for IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c782692e-1284-4c54-9d76-567bc13787cc
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: e9c4470ea16f1840274a6fa8613822b20d6772b3
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310864"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Snelstart: maak een azureiotsecurity module twin

In deze quickstart wordt uitgelegd hoe u afzonderlijke _azureiotsecurity-moduletwins_ maken voor nieuwe apparaten of hoe batchmoduletwins maken voor alle apparaten in een IoT-hub.

## <a name="understanding-azureiotsecurity-module-twins"></a>Inzicht in azureiotsecurity module tweelingen

Voor IoT-oplossingen die in Azure zijn ingebouwd, spelen apparaattweelingen een belangrijke rol in zowel apparaatbeheer als procesautomatisering.

Azure Security Center for IoT biedt volledige integratie met uw bestaande IoT-apparaatbeheerplatform, zodat u de beveiligingsstatus van uw apparaat beheren en gebruik maken van bestaande apparaatbeheermogelijkheden.
Azure Security Center voor IoT-integratie wordt bereikt door gebruik te maken van het IoT Hub twin-mechanisme.

Zie [IoT Hub-moduletweelingen](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) voor meer informatie over het algemene concept van moduletweelingen in Azure IoT Hub.

Azure Security Center for IoT maakt gebruik van het tweepersoonsmechanisme voor de module en onderhoudt een beveiligingsmodule met de naam _azureiotsecurity_ voor elk van uw apparaten.

De beveiligingsmodule twee bevat alle informatie die relevant is voor de beveiliging van apparaten voor elk van uw apparaten.

Als u volledig gebruik wilt maken van Azure Security Center for IoT-functies, moet u deze beveiligingsmoduletwins maken, configureren en gebruiken voor elk apparaat in de service.

## <a name="create-azureiotsecurity-module-twin"></a>Azureiotsecurity module twin maken

_azureiotsecurity_ module twins kunnen op twee manieren worden gemaakt:

1. [Module batch script](https://aka.ms/iot-security-github-create-module) - maakt automatisch module twin voor nieuwe apparaten of apparaten zonder een module twin met behulp van de standaard configuratie.
1. Het handmatig bewerken van elke module twin individueel met specifieke configuraties voor elk apparaat.

>[!NOTE]
> Met behulp van de batchmethode zal niet overschrijven bestaande azureiotsecurity module tweelingen. Met behulp van de batch methode alleen creëert nieuwe module tweelingen voor apparaten die nog niet over een beveiligingsmodule twin.

Zie [agentconfiguratie](how-to-agent-configuration.md) voor meer informatie over het wijzigen of wijzigen van de configuratie van een bestaande moduletwee.

Gebruik de volgende instructies om handmatig een nieuwe _azureiotsecuritymoduletwin_ voor een apparaat te maken:

1. Zoek en selecteer in uw IoT-hub het apparaat waarvoor u een beveiligingsmoduletwin wilt maken.
1. Klik op uw apparaat en vervolgens op **Module-identiteit toevoegen**.
1. Voer **azureiotsecurity**in in het veld **Naam van moduleidentiteit** .

1. Klik op **Opslaan**.

## <a name="verify-creation-of-a-module-twin"></a>De creatie van een moduletwee controleren

Ga als volgt te werk om te controleren of er een beveiligingsmoduletwee bestaat voor een specifiek apparaat:

1. Selecteer **IoT-apparaten** in uw Azure IoT-hub in het menu **Explorers.**
1. Voer de apparaat-id in of selecteer een optie in het **veld Queryapparaat** en klik op **Queryapparaten**.
    ![Queryapparaten](./media/quickstart/verify-security-module-twin.png)
1. Selecteer het apparaat of dubbelklik erop om de pagina Apparaatdetails te openen.
1. Selecteer het menu **Identiteiten module** en bevestig het bestaan van de **azureiotsecurity-module** in de lijst met moduleidentiteiten die aan het apparaat zijn gekoppeld.
    ![Modules die aan een apparaat zijn gekoppeld](./media/quickstart/verify-security-module-twin-3.png)

Zie [Agent-configuratie](how-to-agent-configuration.md)voor meer informatie over het aanpassen van eigenschappen van Azure Security Center voor IoT-moduletwins.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het configureren van aangepaste waarschuwingen...

> [!div class="nextstepaction"]
> [Aangepaste waarschuwingen configureren](quickstart-create-custom-alerts.md)
