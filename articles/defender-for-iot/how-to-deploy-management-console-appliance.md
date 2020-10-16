---
title: Beheer console implementeren in azure Defender voor IoT
description: Meer informatie over het implementeren van de beheer console in azure Defender voor IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/14/2020
ms.author: rkarlin
ms.openlocfilehash: 6af943951b2dba0c4a11b08ad1bcd961cf1c0fc2
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094413"
---
# <a name="deploy-the-management-console"></a>De beheer console implementeren
In dit artikel wordt beschreven hoe u de on-premises beheer console van Azure Defender voor IoT implementeert.

## <a name="the-on-premises-management-console"></a>De on-premises beheer console

De on-premises beheer console bevat een geconsolideerde weer gave van alle netwerk assets en biedt een real-time weer gave van de belangrijkste IoT-en OT-risico indicatoren en-waarschuwingen over al uw faciliteiten. Nauw geïntegreerd met uw SOC-werk stromen en rapporten uitvoeren, maakt het eenvoudig om de prioriteit van oplossings activiteiten en de correlatie van bedreigingen op meerdere locaties te verbeteren.

- Holistische-Verminder complexiteit met één uniform platform voor het beheren van activa, Risico's en beveiligings problemen, en het bewaken van bedreigingen met incident reacties.

- Aggregatie en correlatie: gegevens en waarschuwingen die zijn verzameld van alle sites weer geven, verzamelen en analyseren.

- Alle Sens oren beheren: Configureer en bewaak alle Sens oren vanaf één locatie.

   ![Weer gave Azure Defender voor IoT-site beheer](media/updates/image2.png)

## <a name="deploy-the-on-premises-management-console-appliance"></a>Het apparaat van de on-premises beheer console implementeren

Voor dit proces moet u uw eigen hardware aanschaffen en software installeren. De oplossing wordt uitgevoerd op gecertificeerde apparaten. Raadpleeg de [hand leiding Azure Defender voor IOT](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) -hardwarespecificaties als referentie bij het aanschaffen van uw gecertificeerd apparaat.

Raadpleeg de [installatie handleiding voor Azure Defender voor IOT](https://aka.ms/AzureDefenderforIoTInstallSensorISO) voor meer informatie over het downloaden van de ISO-installatie kopie en het installeren van de sensor software.

**De on-premises beheer console implementeren:**

1. Navigeer naar Microsoft Azure Defender voor IoT.

2. Selecteer **on-premises beheer console**.

   ![De on-premises beheer console weergave van Azure Defender voor IoT](media/updates/image15.png)

3. Selecteer versie 3,1 in het menu **versie selecteren** .

4. Selecteer **downloaden** en sla het bestand op.

5. Nadat de software is geïnstalleerd, voert u netwerk installatie taken uit. Raadpleeg de [installatie handleiding voor Azure Defender voor IOT-netwerken](https://aka.ms/AzureDefenderForIoTNetworkSetup) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over configuratie opties gaat u naar de hand leiding voor het configureren van de module.
> [!div class="nextstepaction"]
> [Hand leiding voor module configuratie](./how-to-agent-configuration.md)
